This is an example playbook that includes the role `my_role` conditional on the variable `run_my_role`.
The purpose is to test in which format the conditional variable needs to be provided in order for the role to be included.

The use case behind that test is that the role includes a variable that is not defined - which should be detected when the playbook is run in check mode.

### Bug?
I suspect a bug in that variables that are passed in key/value format are not recognized for evaluating the conditional on the role inclusion.

### Ansible version

Tested on 
* Mac OSX Yosemite (10.10.2) with ansible version 1.8.2
* CentOS with ansible version 1.6.2


### Steps to reproduce
Specify the variable `run_my_role` in key/value format, try check mode and actual run

     ansible-playbook my_playbook.yml -i env-local -e "run_my_role=true" -v -C
     
     ansible-playbook my_playbook.yml -i env-local -e "run_my_role=true" -v 

### Expected outcome
Either way (if check mode or actual run) the expected outcome is, that the role is included and the undefined variable is reported:

    TASK: [my-role | use a non existent variable] *********************************
    fatal: [localhost] => One or more undefined variables: 'this_is_not_defined' is undefined


### Actual outcome
Either way (if check mode or actual run) the role is not included and the check mode does not detect missing variable definition:

    TASK: [my-role | use a non existent variable] *********************************
    skipping: [localhost]


### Work around
Specify the variable `run_my_role` in json format, and even the check mode reports the missing variable:

     ansible-playbook my_playbook.yml -i env-local -e '{"run_my_role": true}' -v -C