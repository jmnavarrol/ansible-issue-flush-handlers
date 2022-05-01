# ansible-issue-77616
This a test for [Ansible issue #77616](https://github.com/ansible/ansible/issues/77616) (meta: flush_handlers. Wrong conditional behaviour).
1. [Usage](#usage)
1. [Workaround](#workaround)

## usage<a name="usage"></a>
This repository's development environment is managed by means of my [ansible-issues](https://github.com/jmnavarrol/ansible-issues) project, so for easy use I recommend cloning/forking it.

Otherwise is up to you to configure your running environment as you please.

Run ansible on this repo's root against [the test playbook](./playbook.yml):
```console
(ansible-issues) user@host:~/flush-handlers$ ansible-playbook playbook.yml
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [A test for 'flush_handlers' action. See https://github.com/ansible/ansible/issues/77616] *****************************************************************

TASK [Gathering Facts] *****************************************************************************************************************************************
ok: [localhost]

TASK [Shows involved versions] *********************************************************************************************************************************
ok: [localhost] => {
    "msg": [
        "Python version: '3.9.2'.",
        "Ansible version '2.12.4'.",
        "This is always going to be the First Message.",
        "See tasks' sorting below..."
    ]
}

TASK [First task (it triggers 'my_handler')] *******************************************************************************************************************
changed: [localhost]

TASK [This 'flush_handlers' should NEVER be triggered as the 'when' clause ALWAYS evaluates to False!] *********************************************************
[WARNING]: flush_handlers task does not support when conditional

RUNNING HANDLER [my_handler] ***********************************************************************************************************************************
ok: [localhost] => {
    "msg": [
        "This was triggered from 'first_task'.",
        "If 'flush_handlers' worked as intended, this would be the LAST message!!!"
    ]
}

TASK [Last task] ***********************************************************************************************************************************************
ok: [localhost] => {
    "msg": [
        "This is the last task.",
        "If 'flush_handlers' worked as intended, this would appear BEFORE the triggered handler!!!"
    ]
}

PLAY RECAP *****************************************************************************************************************************************************
localhost                  : ok=5    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

(ansible-issues) user@host:~/flush-handlers$
```

**See how tasks don't appear in the expected order!!!**

## WORKAROUND<a name="workaround"></a>
The basic rationale is that *"if you can't add a *when* clause to 'flush_handlers', add it to another artifact that can"*.

In this vein, Ansible supports *includes* so if I conditionally include a file that only runs the *'flush_handlers'* task, *"it should work, right?"*.  Well, almost...

There are two kinds of includes in Ansible: *static* and *dynamic*.  Since the problem here is that Ansible's interpreter in the controller will irrespectively run *meta* tasks whenever it sees them and *static* includes do parse the code, *static* includes will fail.  On the other hand, *dynamic* includes only load the code once they reach it at runtime, provided it should; in other words, the interpreter will only *"see"* the *meta* directive if and only conditions are met, so *'flush_handlers'* will work as expected in this conditions.

I added two other playbooks to show my case (no inventory, so just run `ansible-playbook workaround.yml` or `ansible-playbook non-working-workaround.yml`):
1. **[workaround.yml](./workaround.yml):** it uses *include_tasks*, which is dynamic and, thus, it works as expected.
1. **[non-working-workaround.yml](./non-working-workaround.yml):** it uses *import_tasks*, which is static and, thus, still exhibits this bug.

I provide these two playbooks because it's not always easy to tell at a glance when an include will be dynamic or static while loading files, or roles, or role tasks, etc. just to make clear the difference so helping you to find a valid combination for your particular case.
