# PHP 5 (`php5`)

Master:

Develop:

Installs PHP 5

**Part of the BAS Ansible Role Collection (BARC)**

## Overview

* Installs the latest stable version of PHP 5 from non-system, or optionally, from system only sources

# TODO (Remove)

php-opcache ?
php-xdebug ?
blackfire profiler ?

Explain why php5-cli is installed and not just php5 (installs Apache)

CI?

## Quality Assurance

This role uses manual and automated testing to ensure the features offered by this role work as advertised. 
See `tests/README.md` for more information.

## Dependencies

* None

## Requirements

* None

## Limitations

* The PHP version varies depending on which operating system is used and if non-system package sources are permitted

As the package policy varies between system and non-system package sources, and between operating systems, the version 
of PHP installed is variable between supported operating systems.

For Ubuntu machines, a non-system PPA is installed resulting in more recent versions of PHP 5 to be installed. On CentOS 
machines, a non-system repository is installed resulting in more recent versions of PHP 5 to be installed.

It is a convention of BARC roles to use the latest version of packages. Where a suitable non-system package source is 
available it will be used. Otherwise system packages will be used. Suitable non-system packages require a reputable,
maintainer, typically a company or well respected individual. Where non-system packages are used, the variable 
*BARC_use_non_system_package_sources* can be set to `false` to always use system packages if this is needed.

_This limitation is **NOT** considered to be significant. Solutions will **NOT** be actively pursued._ 
_Pull requests addressing this limitation will be considered.*_

See [BARC-]() for further details.

* On Ubuntu where only system packages are permitted, the 'php5-cli' package is selected, rather than the 'php5' package

The `php5` meta package has dependencies which will, by default, install the Apache web-server. Though this is likely convenient for some, as a generic role, this is completely unsuitable. Other roles can be used to integrate PHP with 
a web-server if that is desired.

See [this source](http://serverfault.com/questions/243297/how-do-you-install-php5-without-installing-apache-in-ubuntu) 
for further details.

See [BARC-]() for further details.

## Usage

### PHP version

Depending on the operating system used, the version of PHP installed will different, though it will be at least PHP 
*5.4*, and not greater than the latest PHP 5 release. The table below hopes to clarify the version you can expect:

| Operating System | Non-System Package Sources Permitted | PHP Version | Notes |
| ---------------- | ------------------------------------ | ----------- | ----- |
| Ubuntu           | Yes                                  | *5.6*       | -     |
| Ubuntu           | No                                   | *5.5*       | -     |
| CentOS           | Yes                                  | *5.6*       | -     |
| CentOS           | No                                   | *5.4*       | -     |

Because the exact version installed cannot be guaranteed by this role, you should be careful if using depending on this 
role in another role or a project that relies on PHP. If any version of PHP greater than 5.4 and less than 7.0, is 
acceptable this role is suitable, however where you depend on some feature added to minor releases (e.g. *5.5*) this 
role is unsuitable.

This ambiguity is considered a limitation, see the *limitations* section for more information.

### Non-system packages

It is a convention of BARC roles to use the latest version of packages. Where a suitable non-system package source is 
available it will be used. Suitable non-system packages require a reputable, maintainer, typically a company or well 
respected individual. If this is for some reason unsuitable, it is possible to only use system packages by setting the 
*BARC_use_non_system_package_sources* variable to `false`.

Note: As the package policy varies between system and non-system package sources, and between operating systems, the 
version of installed packages is variable.

Note: On Ubuntu when using system packages only, the `php5-cli` package is selected rather than the `php5` package.
This is considered a limitation, see the *limitations* section for more information.

### Typical playbook

```yaml
---

- name: install php 5
  hosts: all
  become: yes
  vars: []
  roles:
    - BARC.php5
```

### Tags

BARC roles use standardised tags to control which aspects of an environment are changed by roles. Where relevant, tags
will be applied at a role, or task(s) level, as indicated below.

This role uses the following tags, for various tasks:

* [**BARC_CONFIGURE**](https://antarctica.hackpad.com/BARC-Standardised-Tags-AviQxxiBa3y#:h=BARC_CONFIGURE)
* [**BARC_CONFIGURE_PACKAGES**](https://antarctica.hackpad.com/BARC-Standardised-Tags-AviQxxiBa3y#:h=BARC_CONFIGURE_PACKAGE)
* [**BARC_INSTALL_PACKAGES**](https://antarctica.hackpad.com/BARC-Standardised-Tags-AviQxxiBa3y#:h=BARC_INSTALL_PACKAGE)

### Variables

#### *BARC_use_non_system_package_sources*

* **MAY** be specified
* Specifies whether non-system sources can be used to install packages
* Note: This variable is scoped to all other BARC roles which install packages from non-system sources
* Values MUST use one of these options, as determined by Ansible:
    * `true`
    * `false`
* Values **SHOULD NOT** be quoted to prevent Ansible coercing values to a string
* Where not specified, a value of `true` will be assumed
* Default: `true`

## Developing

### Issue tracking

Issues, bugs, improvements, questions, suggestions and other tasks related to this package are managed through the 
[BAS Ansible Role Collection](https://jira.ceh.ac.uk/projects/BARC) (BARC) project on Jira.

This service is currently only available to BAS or NERC staff, although external collaborators can be added on request.
See our contributing policy for more information.

### Source code

All changes should be committed, via pull request, to the canonical repository, which for this project is:

`ssh://git@stash.ceh.ac.uk:7999/barc/php5.git`

A mirror of this repository is maintained on GitHub. Changes are automatically pushed from the canonical repository to
this mirror, in a one-way process.

`git@github.com:antarctica/ansible-php5.git`

Note: The canonical repository is only accessible within the NERC firewall. External collaborators, please make pull 
requests against the mirrored GitHub repository and these will be merged as appropriate.

### Contributing policy

This project welcomes contributions, see `CONTRIBUTING.md` for our general policy.

The [Git flow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow/) 
workflow is used to manage the development of this project:

* Discrete changes should be made within feature branches, created from and merged back into develop 
(where small changes may be made directly)
* When ready to release a set of features/changes, create a release branch from develop, update documentation as 
required and merge into master with a tagged, semantic version (e.g. v1.2.3)
* After each release, the master branch should be merged with develop to restart the process
* High impact bugs can be addressed in hotfix branches, created from and merged into master (then develop) directly

## Licence

Copyright 2015 NERC BAS.

Unless stated otherwise, all documentation is licensed under the Open Government License - version 3. All code is
licensed under the MIT license.

Copies of these licenses are included within this role.
