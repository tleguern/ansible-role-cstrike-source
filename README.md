# Ansible Role: Counter-Strike: Source

[![builds.sr.ht status](https://builds.sr.ht/~tleguern/ansible-role-cstrike-source.svg)](https://builds.sr.ht/~tleguern/ansible-role-cstrike-source?)

An Ansible role that installs and configures a Counter-Strike: Source dedicated server.

The game server is downloaded thought Steam and exposed as a systemd service for easier management.
Using this role it is possible to publish a minimalist server with custom maps.
For more advanced configuration it is necessary to install [Metamod:Source](https://github.com/tleguern/ansible-role-metamod-source) and [SourceMod](https://github.com/tleguern/ansible-role-sourcemod).

Automatic testing is provided using molecule's delegated driver and https://builds.sr.ht.

## Requirements

An ansible role dedicated to the installation of SteamCMD such as [ansible-steamcmd](https://github.com/tleguern/ansible-steamcmd).

## Role Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `steamcmd_user` | User name for steamcmd | `steam` |
| `steamcmd_bin` | Path to the steamcmd executable | `/usr/games/steamcmd` |
| `cstrike_source_motd` | HTML text for the server's MOTD | See below |
| `cstrike_source_mapcycle` | List of maps for the rotation | See below |
| `cstrike_source_server_cfg` | Server configuration | See below |
| `cstrike_source_port` | Network port | `27015` |
| `cstrike_source_ip` | IP address to listen on | `0.0.0.0` |
| `cstrike_source_extra_mapcycles` | Configuration of extra mapcycle | See below |
| `cstrike_source_extra_maps_directory` | Directory containing extra bsp and nav files | `""` |

### `cstrike_source_motd`

The MOTD is a welcome text displayed by the server during the first connexion of a player.
It is formatted in HTML.

Default value:

```html
  <html>
  <head>
    <title>Counter-Strike: Source</title>
  </head>
  <body scroll="no">
    <pre>Who needs Global Offensive anyway?</pre>
  </body>
  </html>
```

### `cstrike_source_mapcycle`

The mapcycle file is a simple list of maps loaded on the server.

There is no default value.

Example:

```
cs_italy
de_dust2
de_aztec
cs_office
de_piranesi
```

### `cstrike_source_server_cfg`

The server.cfg file is the main configuration file for the server.
It holds the server name, the administrator password as well as various rules.

There is no default value.

Example:

```
hostname "My Counter-Strike Server"
rcon_password mypassword
mp_friendlyfire 1
mp_allowNPCs 0
mp_autoteambalance 1
```

### `cstrike_source_extra_mapcycles`

A list of hashes containing two keys: `name` and `content`.
The first will be used as a prefix to form a new mapcycle file named `mapcycle_{{ name }}.txt` and containing `{{ content }}`.

Example:

```
cstrike_source_extra_mapcycles:
  - name: deathrun
    content: |
      deathrun_temple
      deathrun_goldfever
  - name: dustonly
    content: |
      de_dust
      de_dust2
```

These files can later be used with rcon, using the `mapcyclefile` CVAR.

# Dependencies

The `acl` package should be installed on the server.

# Example Playbook

```yaml
- hosts: game
  vars:
    cstrike_source_extra_maps_directory: "files/cstrike-source/maps"
    cstrike_source_server_cfg: |
      hostname "My Server"
      sv_password password
      rcon_password rconpassword
    cstrike_source_mapcycle: |
      de_dust2
      de_aztec
      cs_office
  pre_tasks:
    - package:
        name: acl
        state: present
  roles:
    - role: ansible-steamcmd
    - role: ansible-role-cstrike-source
```

# License

ISC

## Contributing

Either send [send GitHub pull requests](https://github.com/tleguern/ansible-role-cstrike-source) or [send patches on SourceHut](https://lists.sr.ht/~tleguern/misc).

# Author Information

Tristan Le Guern <tleguern@bouledef.eu>
