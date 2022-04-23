# ansible-issue-77616
This a test for [Ansible issue #77616](https://github.com/ansible/ansible/issues/77616) (meta: flush_handlers. Wrong conditional behaviour).

## usage
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
