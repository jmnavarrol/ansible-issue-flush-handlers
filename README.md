# ansible-issue-41313
This a test for [Ansible issue #41313](https://github.com/ansible/ansible/issues/41313) (meta: flush_handlers doesn't honor when clause).

## usage
This repository's development environment is managed by means of my [ansible-issues](https://github.com/jmnavarrol/ansible-issues) project, so for easy use I recommend cloning/forking it.

Otherwise is up to you to configure your running environment as you please.

Run ansible on this repo's root against [the test playbook](./playbook.yml):
```console
(ansible-issue-41313) user@host:~/ansible-issue-41313$ ansible-playbook playbook.yml
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
