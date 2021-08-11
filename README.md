# ansible-issue-41313
This a test for [Ansible issue #41313](https://github.com/ansible/ansible/issues/41313).

It's been tested using the Python *virtualenv* defined on [the provided requirements file](./python-virtualenvs/ansible-issue-41313.requirements) and Python 3.7.3.

## usage
This repository takes advantage of [*Bash Magic Enviro*](https://github.com/jmnavarrol/bash-magic-enviro/blob/main/README.md) so, if you already configured your Bash console to use it, your environment will be automatically configured once you enter your sandbox' root directory.

For *"manual"* configuration, you need to create and load a *Python3 virtualenv* using [the provided requirements file](./python-virtualenvs/ansible-issue-41313.requirements).

Run ansible on this repo's root against [the test playbook](./ansible/playbook.yml) (result *as-is*):
```console
(ansible-issue-41313) user@host:~/ansible-issue-41313$ ansible-playbook ansible/playbook.yml
[DEPRECATION WARNING]: Ansible will require Python 3.8 or newer on the controller starting with Ansible 2.12. Current version: 3.7.3 (default, Jan 22 2021, 20:04:44) 
[GCC 8.3.0]. This feature will be removed from ansible-core in version 2.12. Deprecation warnings can be disabled by setting deprecation_warnings=False in ansible.cfg.
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [A test for #41313. See https://github.com/ansible/ansible/issues/41313] ******************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************************************
ok: [localhost]

TASK [Shows Ansible version] *******************************************************************************************************************************************
ok: [localhost] => {
    "msg": [
        "Playbook running Ansible version '2.11.3'",
        "This is always going to be the First Message.",
        "See tasks' sorting below..."
    ]
}

TASK [First task (it triggers 'my_handler')] ***************************************************************************************************************************
changed: [localhost]

TASK [This 'flush_handlers' should NEVER be triggered as the 'when' clause ALWAYS evaluates to False!] *****************************************************************
[WARNING]: flush_handlers task does not support when conditional

RUNNING HANDLER [my_handler] *******************************************************************************************************************************************
ok: [localhost] => {
    "msg": [
        "This comes from 'first_task'.",
        "If 'flush_handlers' worked as it should, this would be the LAST message!!!"
    ]
}

TASK [Last task] *******************************************************************************************************************************************************
ok: [localhost] => {
    "msg": [
        "This is the last task.",
        "If 'flush_handlers' worked as it should, this would appear BEFORE the triggered handler!!!"
    ]
}

PLAY RECAP *************************************************************************************************************************************************************
localhost                  : ok=5    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

(ansible-issue-41313) user@host:~/ansible-issue-41313$
```

**See how tasks don't appear in the expected order!!!**
