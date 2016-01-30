# PHP 5 (`php5`)

Master:

Develop:

Installs and configures PHP 5

**Part of the BAS Ansible Role Collection (BARC)**

## Overview

* Installs the latest stable version of PHP 5 from non-system, or optionally, from system only sources
* Configures the PHP configuration file for the CLI SAPI using recommended settings to improve security
* Optionally, installs and enables the Zend OpCache extension, this is enabled by default

# TODO (Remove)

php-opcache ?
php-xdebug ?
blackfire profiler ?

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

*This limitation is **NOT** considered to be significant. Solutions will **NOT** be actively pursued.*
*Pull requests addressing this limitation will be considered.*

See [BARC-]() for further details.

* On Ubuntu where only system packages are permitted, the 'php5-cli' package is selected, rather than the 'php5' package

The `php5` meta package has dependencies which will, by default, install the Apache web-server. Though this is likely convenient for some, as a generic role, this is completely unsuitable. Other roles can be used to integrate PHP with 
a web-server if that is desired.

See [this source](http://serverfault.com/questions/243297/how-do-you-install-php5-without-installing-apache-in-ubuntu) 
for further details.

*This limitation is considered to be significant. A workaround is in place to mitigate this limitation, pending a*
*permanent resolution by Ubuntu. Pull requests addressing this limitation will be considered.*

See [BARC-]() for further details.

* On CentOS, the PHP configuration file is the same for all SAPIs

CentOS uses a single configuration file, `/etc/php.ini`, for all SAPIs, there is no CLI SAPI specific configuration,
unlike on Ubuntu.

CentOS does support loading configuration files from a directory of configuration files, specifically, 
`/etc/php.d/*.ini`, but these configuration files are included by the main configuration file, and so used by all SAPIs.

*This limitation is considered to be significant. Solutions will be actively pursued. Pull requests to address this* 
*will be gratefully considered and given priority.*

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

### PHP configuration files

PHP uses a configuration file to set various options, such as which modules are enabled and how these are configured, 
e.g. which time-zone to use.

This configuration file uses the INI format, which consists of sections containing options with a value. In PHP this
configuration file can be a single file (e.g. `php.ini`) and or a directory of files (e.g. `php.d/*.ini`).

E.g. Core configuration options can be set in a main `php.ini` file, with module specific configuration contained in
per-module configuration files included in the main configuration file.

In addition, configuration options can be set on a per SAPI (Server API) basis. For example settings may differ between
the `cli` SAPI (i.e. the Command Line) and the `apache` SAPI (i.e. a Web Server).

The combination of these various configuration options differ by Operating System, and package used to install PHP.
This variation is considered a limitation, see the *limitations* section for more information.

This role, which configures the `cli` SAPI only, will only configure options relevant to the `cli` SAPI specifically 
(such as a longer execution time). However, on CentOS there is no configuration file limited to the `cli` SAPI, and 
the options set by this role therefore apply to *all* SAPIs. This is considered a limitation, see the *limitations* 
section for more information.

Settings for other SAPIs, such as web-servers, **SHOULD** be set in other roles, or project/purpose specific playbooks.
The tasks used in this role can be used as a template for setting additional options. Providing the Ansible INI module
is used, setting additional INI options will not cause conflicts with this role and so preserve idempotency.

The configuration options set by this role are considered suitably generic and applicable to all situations such that 
they are safe to be used as defaults - however it is accepted there are situations they would not be suitable. They 
include options for:

* Maximum execution time and memory limits
* Default timezone - localised to *Europe/London*
* Error reporting and logging
* Concealing the PHP version for security purposes

See the *php5_sapi_cli_options* variable in the role defaults file (`defaults/main.yml`) for the specific options set.
Where any of these options are unsuitable, override this variable with a copy of these defaults, omitting the unsuitable
options.

### PHP extensions

#### Zend OpCache

"OPcache improves PHP performance by storing precompiled script bytecode in shared memory, thereby removing the need for PHP to load and parse scripts on each request. This extension is bundled with PHP 5.5.0 and later"

Source: http://php.net/manual/en/book.opcache.php

This extension is enabled by default - it is controlled by the *php5_use_opcache* variable.
Currently this role does not configure options for this extension, however it is safe to do this outside this role.

Note: As the version of PHP installed is inconsistent, this role ensures the OpCache extension is present and enabled in 
all versions of PHP on all supported Operating Systems, where this desired.

Note: If this extension is disabled, in this role, it will not *actively* be disabled.
I.e. on Ubuntu this extension is enabled by default, setting the *php5_use_opcache* variable to 'false' will not make 
this role disable this extension. However on CentOS, where this extension is not-enabled by default, setting the 
*php5_use_opcache* variable to 'false' will cause this to change.

If you wish to *actively* disable this extension you will need to add your own tasks. This role will not present a 
conflict in this situation.

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

#### *php5_sapi_cli_options*

**MAY** be specified.
Specifies configuration options for the CLI (Command Line) SAPI

Note: On some Operating Systems, these options may apply beyond the scope of the CLI SAPI. See the *Limitations*
section for more information

Structured as a list of items, with each item having the following properties

* *section*
    * **MUST** be specified
    * Specifies the section of the INI configuration file options for this item belong to
    * Values **MUST** be valid section names as determined by the INI configuration format
* *options*
    * **MAY** be specified
    * Specifies the list of options, and values, to be set within the section set by the *section* item
    * Structured as a list of sub-items, with each sub-item having the following properties
        * *option*
            * **MUST** be specified if any part of the sub-item is specified
            * Specifies the *option* of the INI option/value pair
            * Values **MUST** be valid option names as determined by the configuration format and **MUST** be valid
            option names by PHP
        * *value*
            * *MUST** be specified if any part of the sub-item is specified
            * Specifies the *value* of the INI option/value pair
            * Values **MUST** be valid values as determined by the configuration format and **MUST** be valid values by 
            PHP

Example:

```yml
php5_sapi_cli_options:
  - section: "Resource Limits"
    options:
      - option: max_execution_time  # (seconds)
        value: 30
```

Default: *See role defaults*

#### *php5_use_opcache*

* **MAY** be specified
* Specifies whether the Zend OpCache extension should be installed to improve performance
* This variable is used as a 'feature flag' for whether tasks related to the OpCache extension will be applied
* See the *Usage* section for more information on this feature
* Values **MUST** use one of these options, as determined by Ansible:
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
