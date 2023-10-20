# Role spreadcat.fule

Ansible role to either create a new Ansible role with a new molecule setup, or
add a molecule configuration to an existing role.

The role will create the molecule configuration required and can in addition:

* Update the file `meta/main.yml`
* Add a basic `.yamllint.yml` file
* Add a template for a Changelog file.
* Add more scenarios to the molecule configuration.
* Update the `README.md` file.
* Remove or update the `.travis.yml` file.
* Add a basic task structure for the role.

## Requirements

* Ansible must be installed on the host to run this role.
* Two variables must be provided:
   * `fule_role_name`
   * `fule_working_dir`
* The role required `gathered_facts: true` to be executed.

* Additionally the platforms that shall be used in molecule must be provided.
   * `fule_role_platforms: []`

## Role Variables

```yaml
fule_manage_changelog_file: bool
```

* If set to true a template for a changelog file will be deployed when missing.
  Existing files will not be overwritten.

```yaml
fule_manage_yamllint_configuration_file: bool
```

* If set to `true` a basic yamllint configuration file will be crated in the
  root directory of the role.

```yaml
fule_modify_role_structure: bool
```

* If set to true, the role structure will be altered the following way:

```yaml
fule_remove_default_travis_file: bool
```

* If set to `true`, the file `.travis` in the root directory of the role will be
  removed as longs as it's the default file.

```yaml
fule_replace_meta_file: bool
```

* If set to true, the file `/meta/main.yml` will be updated information about

```yaml
fule_role_author: str
```

* Default author for the role

```yaml
fule_role_company: str
```

* Default role company.

```yaml
fule_role_license: str
```

* Default license for the role.

```yaml
fule_role_name: str
```

* doca tag:role Name of the Ansible role in the format namespace.rolename.

```yaml
fule_update_default_readme_file: bool
```

* If set to true, the default README.md will be replace with a template file.

```yaml
fule_update_default_travis_file: bool
```

* If set to `true`, the file `.travis` in the root directory will be updated to
  pass the linting tests.

```yaml
fule_working_dir: str
```

* defaults file for fule Directory in which to create the Ansible role with
  molecule.

```yaml
fule_yamllint_configuration_file: str
```

* Sets the name of the yamllint configuration file.

## Other variables

```yaml
fule_changelog_file: str
```

* Sets the name of the changelog file.

```yaml
fule_platform_names: dict
```

* Dict with the assignment for Linux distributions and their names with Ansible
  Galaxy.

```yaml
fule_platform_releases: dict
```

* Dict with the assignment for Linux distributions and their nicknames. Ansible
  galaxy does not accept the release number for e.g. Ubuntu releases. This
  variables creates the assignment between the release version number and the
  nicknames expected by Ansible galaxy.

```yaml
fule_readme_checksum: str
```

* Checksum to identify the default README.md file from the ansible-galaxy init
  command.

```yaml
fule_readme_template: str
```

* Path to the template used to replace the README.md when
  `fule_update_default_readme_file: true`.

```yaml
fule_role_minimum_ansible_version: str
```

* Minimum Ansible version required to run the role.

```yaml
fule_travis_checksum: list
```

* Checksum to identify the default .travis.yml file from the ansible-galaxy init
  command.

## Dependencies

* None

### Features

#### General behaviour

The role tries to be at least intrusive as possible.
The goal is to be able to add molecule support to any existing role without destroying anything.

As a general principle any file that has been edited and/or is not identical to the default file created by `ansible-galaxy role init` will not be overwritten or changed.

Files that are missing but are requested by enabling a feature will be created again.

#### Meta information

The role can update the galaxy meta-information file placed in `meta/main.yml` with information like

* Author
* License
* Platforms
* Company
* Namespace
* Role name

The author, license and company is retrieved from the variables:

* author:
* company:
* license:

The namespace and role name is automatically retrieved from `fule_role_name`.

The list of supported platforms is evaluated from the molecule configuration provided to this role.

Any existing change in the file `meta/main.yml` is overwritten.

Use `fule_replace_meta_file: true` to enable this feature.

#### Platforms

The platform definition is the source of multiple configuration in this role:

1. The file `molecule/<scenario>/molecule.yml` is created according to the
   molecule specifications.
1. The file `meta/main.yml` (if enabled) contains the platforms listed in this
   definition.

If no custom configuration is provided like in the example below, the default
configuration with CentOS 8 is being set up in molecule.

```yaml
fule_molecule_platforms:
  - name: Fedora
    release: 37
    image: "geerlingguy/docker-fedora37-ansible:latest"
    provisioner:
      hostvars:
        ansible_user: ansible
    specs:
      tmpfs: ['/run', '/tmp']
      volumes: ['/sys/fs/cgroup:/sys/fs/cgroup:ro']
      capabilities: ['SYS_ADMIN']
      command: '/lib/systemd/systemd'
      pre_build_image: true
```

#### Readme

The role can update the file `README.md` in the root directory and provide a basic template to use.

If the file has been already edited, no changes will be performed.

If the file is missing, a new file will be created.

References like the role name, author, the license and other information will be used automatically.

Set `fule_update_default_readme_file: true` to enable this feature.

#### Tasks

The role can provide a basic task structure from the start.

The file `tasks/main.yml` will be replaced and include the following additional files (in this order):

* `tasks/install.yml`
* `tasks/config.yml`
* `tasks/service.yml`.

If the file `tasks/main.yml` has been edited, it will not be updated. The
additional files will also not be deployed then.

If any file already exists, the role will not update the file.

Use `fule_modify_role_structure: true` to enable this feature.

#### Travis

Depending on the Ansible version used, new roles can contain a file `.travis.yml` in the root directory of the role.

If you are not using [Travis CI](https://www.travis-ci.com/) this file does not have any meaning for you.

You can either remove the file by setting the variable `fule_remove_default_travis_file: true` or keep it.

If you set `fule_update_default_travis_file: true` (default) the role will fix minor linting issues within the default file.

If both Travis CI variables have been set, the file will be updated and then removed.
Removal takes precedence.

Existing `.travis.yml` files which have been modified, will neither be updated or removed.

#### Yamllint

The role can create a basic [yamllint][#yamllint] configuration file in the root directory.

Since [yamllint][#yamllint] supports multiple variants of naming the file, the
filename can be set by using `fule_yamllint_configuration_file: ".yamllint.yml"`
(default).

Set `fule_manage_yamllint_configuration_file: true` for creating a configuration
file. Existing files will not be overwritten or touched in any way.

#### Fule custom variables

In order to support role specific variables fule supports a role specific variable file.
When creating a role with molecule using fule, an (almost) empty variable file
will be placed at `molecule/fule_variables.yml`.

Fule variables that are specific for a certain existing role, can be defined
here.

Running fule again that variable again will then honor the variable being loaded
there.

The variabes can be overwritten using facts, include_role parameters and
extra-vars on the CLI.

```bash
# cat molecule/fule_variables.yml
fule_molecule_scenarios:
  - scen1
```

Running fule on a role again, then will create an additional scenario for
molecule.

```bash
tree -d molecule/
molecule/
├── default
└── scen1
```

## Example execution

### Minimal Example

This is a minimal example that creates a role with molecule support in place.

While the role is fully functionaly, molecule will require some additional
adjustments in order to function.
If you want a fully working example, please see the next example.

```bash
ansible \
  --inventory localhost, all \
  --connection local \
  --module-name include_role --args name=spreadcat.fule \
  --extra-vars fule_working_dir=/tmp \
  --extra-vars fule_role_name=spreadcat.newrole
```

### Working Example

The following example creates a minimum working example. The update of the
metafile has be included, since the default file is missing entries like the
namespace, which then leads to molecule refusing to start up. Setting
`fule_replace_meta_file: true` mitigates this issue.

This creates a working ansible role with molecule in the location of
`/tmp/spreadcat.newrole`.

```bash
ansible \
  --inventory localhost, all \
  --connection local \
  --module-name include_role --args name=spreadcat.fule \
  --extra-vars fule_working_dir=/tmp \
  --extra-vars fule_role_name=spreadcat.newrole \
  --extra-vars fule_replace_meta_file=true
```

After running this, molecule will list only one default scenario, but will be
ready to start up.

```bash
$ cd /tmp/spreadcat.newrole
$ spreadcat.newrole: mc list
INFO     Running default > list
                ╷             ╷                  ╷               ╷         ╷
  Instance Name │ Driver Name │ Provisioner Name │ Scenario Name │ Created │ Converged
╶───────────────┼─────────────┼──────────────────┼───────────────┼─────────┼───────────╴
  centos8       │ podman      │ ansible          │ default       │ false   │ false
                ╵             ╵                  ╵               ╵         ╵
```

## Example Playbook

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

```yaml
---
- hosts: all
  gather_facts: true
  become: false
  vars:
    fule_role_name: spreadcat.testrole
    fule_working_dir: /data/ansible/roles
  roles:
     - role: spreadcat.fule
```

## Limitations

* Ubuntu 20.04 is only partially supported. Out of the box Python 3.9 is required to run ansible-lint in the current version which would required to install it in the test container. Creating the structure works fine so far, just the linting test for that platform is being skipped.
* Lintin has been removed from molecule. This role therefore removes support from it from molecule as well.

## License

MIT

## Author Information

* This role is written an maintained by DBV.

[#yamllint]: https://yamllint.readthedocs.io/en/stable/
