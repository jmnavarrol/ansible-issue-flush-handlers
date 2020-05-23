# ansible-issue-41313
This a test for [Ansible issue #41313](https://github.com/ansible/ansible/issues/41313).

It's been tested using the Python *virtualenv* defined on [ansible.requirements](./ansible.requirements) and Python 2.7.13 (but I don't think using Python 3 instead makes any difference).

## usage
1. Create and load a Python virtualenv with [ansible.requirements](./ansible.requirements) (I used [virtualenvwrapper](https://virtualenvwrapper.readthedocs.io)).
1. Run ansible on this repo's root (result *as-is*):
    ```console
    (ansible) user@host:~/ansible-issue-41313$ ansible-playbook ansible/playbook.yml
    [WARNING]: No inventory was parsed, only implicit localhost is available
    [WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

    PLAY [A test for #41313] ***************************************************************************************************************************************************************************

    TASK [Gathering Facts] *****************************************************************************************************************************************************************************
    ok: [localhost]

    TASK [Shows Ansible version] ***********************************************************************************************************************************************************************
    ok: [localhost] => {
        "msg": "Playbook running Ansible version 2.9.9"
    }

    TASK [First task] **********************************************************************************************************************************************************************************
    changed: [localhost]
    [WARNING]: flush_handlers task does not support when conditional

    RUNNING HANDLER [my_handler] ***********************************************************************************************************************************************************************
    ok: [localhost] => {
        "msg": "Handler triggered from first_task.  This should appear AFTER the last task message!!!"
    }

    TASK [Last task] ***********************************************************************************************************************************************************************************
    ok: [localhost] => {
        "msg": "This is the last task. This should appear BEFORE the triggered handler!!!"
    }

    PLAY RECAP *****************************************************************************************************************************************************************************************
    localhost                  : ok=5    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

    (ansible) user@host:~/ansible-issue-41313$
    ```

**See how tasks don't appear in the expected order!!!**

