# APAD MVP infrastructure

----------

## Content

**[Creating new infrastructure](#creating-new-infrastructure)**
- [MANUAL](#manual)
- [AUTOMATED Mk1](#automated-mk1)
- [AUTOMATED Mk2](#automated-mk2)
- [CONFIGURATION](#configuration)

**[Updating existing infrastructure](#updating-existing-infrastructure)**

**[Deleting existing infrastructure](#deleting-existing-infrastructure)**

**[Under the hood](#under-the-hood)**

**[How To](#how-to)**

----------

## Creating new infrastructure

### MANUAL

* Run **code-pipeline-infrastructure-builder.cfn.yml** manually via CF Console

*N.B!:* ensure your stack name is finished with '**-pipeline**' postfix

* Upload this repository to newly created CodeCommit repository

----------

### AUTOMATED Mk1

* Use bash script helper, **build-pipeline.sh**

* By default, install method defaults to '**cli**'.

Usage:
```sh
$ sh build_pipeline.sh 'project_name' 'environment_name' 'aws_profile' 'aws_region'
```

Example:
```sh
$ sh build-pipeline.sh apad mvp sandbox eu-west-1
```

##### Upload this repository to newly created CodeCommit repository

----------

### AUTOMATED Mk2

However, new nice option added for **build-pipeline.sh**: '**ansible**'!

* Use bash script helper, **build-pipeline.sh** but with '**ansible**' as '**install_method**' option

Usage:
```sh
$ sh build_pipeline.sh 'project_name' 'environment_name' 'aws_profile' 'aws_region' ['install_method']
```

Example:
```sh
$ sh build-pipeline.sh apad mvp sandbox eu-west-1 ansible
```

It will apply the same pipeline using the same configuration file stored in `config` folder but also **automates** transfer of this repository to target CodeCommit repo via **ssh** by default.

Highly recommended.

----------

### CONFIGURATION

CF parameters configuration is stored in `config/cf` folder.

Script reads the configuration based on  `${project_name}/${environment_name}` subfolders.

Check bash script code for details.

----------

### Updating existing infrastructure

* Commit to respective branch to CodeCommit repository.
Code Pipeline will automatically carry on the update procedures

----------

### Deleting existing infrastructure

* Remove manually master stack via CloudFormation Console
* Remove manually pipeline stack via CloudFormation Console

----------

### Under the hood

* **modules** folder contains **master.cfn.yml** file that defines how infrastructure should be build as well as all templates required to build default MVP infrastructure

* **config** folder defines all-in-one configuration file in json format that contains all variables you might want to change in all modules.
It is passed to **master.cfn.yml** template later on automatically

* **roles** folder and **playbook.yml** are the part of Ansible workflow, which job is to normalize **master.cfn.yml** template and populate with correct defined earlier in **code-pipeline-infrastructure-builder.cfn.yml** Code Pipeline variables

* **buildspec.yml** file defines how CodeBuild should normalize and upload all modules and master file to S3 bucket

----------

### How To

1) create EC2 -> KeyPair you want to use in current environment

2) upload IAM -> SSH public key used for CodeCommit **or** create HTTPS Git credentials for AWS CodeCommit

3) create config/cf/'project'-'region'.json used via Pipeline to create infrastructure

4) default *modules/db* used -> PostgreSQL. Choose between MySQL and PostgreSQL and fix master file

5) create CF Pipeline:
- manual via CF
- using script (**do not forget to create** config/cf/'project'/'env'/cf_parameters.json)

5.1) upload repository to 'branch' you want to use ('master' by default)
*N.B!*: 'ansible' method uploads repository automatically
