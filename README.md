# SFTP Revived for VSCode

**This was originally a fork from [liximomo's SFTP plugin](https://github.com/liximomo/vscode-sftp.git).**

## Description

Syncs your local directory with a remote server directory. Allows you to optionally edit upload a file
to the remote directory after it saves locally. This allows you to edit more or less directly on the
server similar to WinScp or other similar programs.

Very powerful, with smart features. Very simple, requires just three lines of config! Very fast, finished in a blink.

- Features
  - [Browser remote with Remote Explorer](#remote-explorer)
  - Diff local and remote
  - Sync directory
  - Upload/Download
  - Upload on save
  - File Watcher
  - Multiple configs
  - Switchable profiles
- [Commands](https://github.com/liximomo/vscode-sftp/wiki/Command)
- [Debug](#debug)

## Documentation

- [Commands](./docs/commands.md)
- [Alt commands](./docs/alt_commands.md)
- [Common configuration](./docs/common_config.md)
- [SFTP configuration](./docs/sftp_config.md)
- [FTP confriguration](./docs/ftp_config.md)
- [Settings](./docs/settings.md)

## Usage

If your latest files are on the server, you can start with an empty local folder, then download your project,
and from that point sync.

1. In `VS Code`, open a local directory you wish to sync to the remote server (or create an empty
  directory that you wish to first download the contents of a remote server folder in order to edit locally).
2. `Ctrl+Shift+P` on Windows/Linux or `Cmd+Shift+P` on Mac open command palette, run `SFTP: config` command.
3. A basic configuration file will appear named `sftp.json` under the `.vscode` directory. Edit the
  parameters to match your setup.

For instance:

```json
{
    "name": "Profile Name",
    "host": "name_of_remote_host",
    "protocol": "ftp",
    "port": 21,
    "secure": true,
    "username": "username",
    "remotePath": "/public_html/project",
    "password": "password",
    "uploadOnSave": true
}
```

 `remotePath` in the above example is the path which will be downloaded if you "Download Project".

The password parameter is optional but if you don't add it, you will be constantly prompted for a password.
Note that you must escape any backslashes and other special characters with a backslash.

There are other Example Configs below.

1. Save and close the `sftp.json` file.
2. `Ctrl+Shift+P` on Windows/Linux or `Cmd+Shift+P` on Mac open command palette.
3. Type "sftp" and you'll now see a number of other comands.
4. A good one to start with if you want to start with a remote folder is `SFTP: Download Project`.
  This will download the directory shown in the `remotePath` setting in `sftp.json` to your local open directory.
5. Done - you can now edit locally and after each save it will upload to your remote file.
6. Enjoy !

For detailed usage. Please go to [wiki](https://github.com/liximomo/vscode-sftp/wiki).

## Example Configs

You can see the full config [here](https://github.com/liximomo/vscode-sftp/wiki/config).

- [SFTP Revived for VSCode](#sftp-revived-for-vscode)
  - [Description](#description)
  - [Documentation](#documentation)
  - [Usage](#usage)
  - [Example Configs](#example-configs)
    - [Simple](#simple)
    - [Profiles](#profiles)
    - [Multiple Context](#multiple-context)
    - [Connection Hopping](#connection-hopping)
      - [Single Hop](#single-hop)
      - [Multiple Hop](#multiple-hop)
    - [Config in User Setting](#config-in-user-setting)
  - [Remote Explorer](#remote-explorer)
  - [Debug](#debug)

### Simple

```json
{
  "host": "host",
  "username": "username",
  "remotePath": "/remote/workspace"
}
```

### Profiles

```json
{
  "username": "username",
  "password": "password",
  "remotePath": "/remote/workspace/a",
  "watcher": {
    "files": "dist/*.{js,css}",
    "autoUpload": false,
    "autoDelete": false
  },
  "profiles": {
    "dev": {
      "host": "dev-host",
      "remotePath": "/dev",
      "uploadOnSave": true
    },
    "prod": {
      "host": "prod-host",
      "remotePath": "/prod"
    }
  },
  "defaultProfile": "dev"
}
```

_Note：_ `context` and `watcher` are only available at root level.

Use `SFTP: Set Profile` to switch profile.

### Multiple Context

The context must **not be same**.

```json
[
  {
    "name": "server1",
    "context": "project/build",
    "host": "host",
    "username": "username",
    "password": "password",
    "remotePath": "/remote/project/build"
  },
  {
    "name": "server2",
    "context": "project/src",
    "host": "host",
    "username": "username",
    "password": "password",
    "remotePath": "/remote/project/src"
  }
]
```

_Note：_ `name` is required in this mode.

### Connection Hopping

You can connect to a target server through a proxy with ssh protocol.

_Note：_ **Variable substitution is not working in a hop config.**

#### Single Hop

local -> hop -> target

```json
{
  "name": "target",
  "remotePath": "/path/in/target",
  "host": "hopHost",
  "username": "hopUsername",
  "privateKeyPath": "/Users/localUser/.ssh/id_rsa",
  "hop": {
    "host": "targetHost",
    "username": "targetUsername",
    "privateKeyPath": "/Users/hopUser/.ssh/id_rsa"
  }
}
```

#### Multiple Hop

local -> hopa -> hopb -> target

```json
{
  "name": "target",
  "remotePath": "/path/in/target",
  "host": "hopAHost",
  "username": "hopAUsername",
  "privateKeyPath": "/Users/hopAUsername/.ssh/id_rsa",
  "hop": [
    {
      "host": "hopBHost",
      "username": "hopBUsername",
      "privateKeyPath": "/Users/hopaUser/.ssh/id_rsa"
    },
    {
      "host": "targetHost",
      "username": "targetUsername",
      "privateKeyPath": "/Users/hopbUser/.ssh/id_rsa"
    }
  ]
}
```

### Config in User Setting

You can use `remote` to tell sftp to get the config from [remote-fs](https://github.com/liximomo/vscode-remote-fs).

In User Setting:

```json
"remotefs.remote": {
  "dev": {
    "scheme": "sftp",
    "host": "host",
    "username": "username",
    "rootPath": "/path/to/somewhere"
  },
  "projectX": {
    "scheme": "sftp",
    "host": "host",
    "username": "username",
    "privateKeyPath": "/Users/xx/.ssh/id_rsa",
    "rootPath": "/home/foo/some/projectx"
  }
}
```

In sftp.json:

```json
{
  "remote": "dev",
  "remotePath": "/home/xx/",
  "uploadOnSave": true,
  "ignore": [".vscode", ".git", ".DS_Store"]
}
```

## Remote Explorer

![remote-explorer-preview](https://raw.githubusercontent.com/bthompson90/vscode-sftp-revived/master/assets/showcase/remote-explorer.png)

Remote Explorer lets you explore files in remote. You can open Remote Explorer by:

1. Run Command `View: Show SFTP`.
2. Click SFTP view in Activity Bar.

You can only view a files content with Remote Explorer. Run command `SFTP: Edit in Local` to edit it in local.

_Note：_ You need to manually refresh the parent folder after you **delete** a file to make the explorer updated.

## Debug

1. Open User Settings.
   - On Windows/Linux - `File > Preferences > Settings`
   - On macOS - `Code > Preferences > Settings`
2. Set `sftp.debug` to `true` and reload vscode.
3. View the logs in `View > Output > sftp`.
