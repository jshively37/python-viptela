# Cisco Viptela vManage Python CLI/SDK

This repo contains a Python SDK for Cisco Viptela vManage as well as a CLI for exercising that SDK

## Requirements
* Python 3.6+

## Installation

### Creating Python virtual environment

```bash
python3 -m venv env
. env/bin/activate
```

### Installation from PyPI

```bash
pip install viptela
```

### Installation from github 

```bash
pip install git+https://github.com/CiscoDevNet/python-viptela.git@specific_template
```

### Installation from within repo

```bash
git clone https://github.com/CiscoDevNet/python-viptela.git
cd python-viptela
pip install -e .
```

## Environment Variables

IP/DNS and credentials for the vManage server can be set for the SDK via environment variable

* `VMANAGE_HOST`
* `VMANAGE_USERNAME`
* `VMANAGE_PASSWORD`

## vManage Command Line Interface

```bash
$ vmanage --help
Usage: vmanage [OPTIONS] COMMAND [ARGS]...

Options:
  --host TEXT      vManage Host (env: VMANAGE_HOST)  [required]
  --username TEXT      vManage Username (env: VMANAGE_USERNAME)  [required]
  --password TEXT  vManage Password (env: VMANAGE_PASSWORD)  [required]
  --help           Show this message and exit.

Commands:
  export  Export commands
  import  Import commands
  show    Show commands
```

vManage host and credentials can be also specified via command line options.  The
command line options override the environment variables. If no password is specified,
the user will be prompted for one.

### Importing and exporting of templates and policy

#### Data file format

All template information is exported to a single file with to lists: `feature_templates` and `device_templates`.  Each list contains dictionaries as pulled from the vManage API with a few
additions and modifications.  First, a list of attached devices and a list of input variables
is added to the device template.  Then instance IDs are translated into name so that the templates
can be imported into another vManage.

All policy template information is exported to a single file with four lists: `policy_lists`,
`policy_definitions`, `central_policies`, `local_policies`.  Each list contains dictionaries as
pulled from the vManage API with the instance IDs converted to names so that policies can be
imported into another vManage.

#### Import Options

* `--check`: Just check. No changes. (default=False)
* `--update`: Update if exists (default=False)
* `--diff`: Show diffs (default=False)

The CLI/SDK will import templates and policy idempotently and as non-destructivly as possible.
It will only add templates and policies that are not there.  When a template or policy does exist
but is different than what is in the import file, it will not he updates unless the `--update`
option is given.

#### Export Templates

##### Export all templates to a file

```bash
vmanage export templates --file vmanage-templates.json
```

##### Export templates from a specific vManange

```bash
vmanage --host=192.133.178.54 export templates --file vmanage-templates.json
```

##### Export specific device templates

```bash
vmanage export templates --type=device --file vmanage-templates.json --name=isr4331 --name=ISR1111-8P
```

##### Export just feature templates

```bash
vmanage export templates --type=feature --file vmanage-templates.json
```

#### Import Templates

##### Import all templates

```bash
vmanage import templates --file vmanage-templates.json
```

##### Import feature templates

```bash
vmanage import templates --type=feature --file vmanage-templates.json
```

##### Import specific device templates

```bash
vmanage import templates --type=device --file vmanage-templates.json --name=isr4331 --name=ISR1111-8P
```

#### Export Policies

```bash
vmanage export policies --file vmanage-policies.json
```

#### Import Policies

```bash
vmanage import policies --file vmanage-policies.json
```

### Show Information

#### The following show commands are available

* control - Show control information
  * connections
  * connections-history
* device - Show device information
  * config
  * status
* omp - Show OMP information
  * peers
* policy - Show policy information
  * central
  * local
  * definition
  * list
* template - Show template information

#### Examples

##### Diff two templates

```
vmanage show template g0/0/0-R1 --diff g0/0/0-R2
[ ( 'change',
    'templateDefinition.tunnel-interface.color.value.vipType',
    ('ignore', 'constant')),
  ( 'change',
    'templateDefinition.tunnel-interface.color.value.vipValue',
    ('default', 'custom2')),
  ( 'add',
    'templateDefinition.tunnel-interface.color.restrict',
    [('vipVariableName', 'vpn_if_tunnel_color_restrict')]),
  ( 'change',
    'templateId',
    ( '2a0481f4-a5b2-44bf-baad-59de4d3b4e99',
      '1dc123db-7a14-40f5-9653-1c87ccd5eaa2')),
  ('change', 'createdOn', (1569072320921, 1569072320260)),
  ('change', '@rid', (843, 839)),
  ( 'change',
    'templateDescription',
    ( 'G0/0/0 Interface for R1 router with TLOC extension, adds NAT',
      'G0/0/0 Interface for R2 router with TLOC extension, adds NAT, changes '
      'color to custom2')),
  ('change', 'lastUpdatedOn', (1569072320921, 1569072320260))]
```

## License

CISCO SAMPLE CODE LICENSE