# rsync.nvim

Asynchronously transfer your files with `rsync` on save.

Fork of [kusnezoff-alexander/rsync.nvim](https://github.com/kusnezoff-alexander/rsync.nvim) with additional options for directional-only sync.

![output](https://github.com/kusnezoff-alexander/rsync.nvim/assets/53407525/c5c402bd-98ac-4899-9ce0-ebf27db28d29)

## Dependencies

- [cargo](https://www.rust-lang.org/tools/install)
- rsync

## Installation

```lua
-- packer.nvim
use {
    'kusnezoff-alexander/rsync.nvim',
    run = 'make',
    requires = {'nvim-lua/plenary.nvim'},
    config = function()
        require("rsync").setup()
    end
}
-- lazy.nvim
{
    'kusnezoff-alexander/rsync.nvim',
    build = 'make',
    dependencies = 'nvim-lua/plenary.nvim',
    config = function()
        require("rsync").setup()
    end,
}
```

## Usage

**rsync.nvim** looks for `.nvim/rsync.toml` file by default in the root of your
project. The path can also be set with the `project_config_path` key in the
plugin configuration.

The current options available:

```toml
# this is the path to the remote. Can be either a local/remote filepath.
remote_path = "../copy/"
# or if using ssh
remote_path = "user@host:/home/user/path/"

# specifying a file(s) which should be synced "down" but are on ignore files.
# this is a workaround to sync down files which are included on ignore files.
remote_includes = "build.log"
# or using an array if multiple files are needed.
remote_includes = ["build.log", "build/generated.json"]

# specifying an gitignore file(s). Files matching patterns in ignore files are
# excluded from "SyncUp" and "SyncDown" except ones specified in `remote_includes`.
# For example, to exclude file(s) in the global gitignore and the project gitignore:
ignorefile_paths = ["~/.gitignore", ".gitignore"]

# Directories (relative to the project root) that should ONLY be synced from
# the local host to the remote. `RsyncDown` will not pull them back. Useful
# for source/config directories you want to push to a cluster but never
# overwrite locally with whatever is on the remote.
host_to_remote_only = ["src/", ".nvim/"]

# Directories (relative to the project root) that should ONLY be synced from
# the remote back to the local host. `RsyncUp` will not push them up. Useful
# for build output or experiment results produced on the remote that should
# flow back to the host but never the other way.
remote_to_host_only = ["data/", "build/"]
```

## Commands

| Name               | Action                                                                                    |
| ------------------ | ----------------------------------------------------------------------------------------- |
| RsyncDown          | Sync all files from remote\* to local folder.                                             |
| RsyncDownFile      | Sync specified or current file from remote to local folder.                               |
| RsyncUp            | Sync all files from local\* to remote folder.                                             |
| RsyncUpFile        | Sync specified or current file from local to remote. This requires rsync version >= 3.2.3 |
| RsyncLog           | Open log file for rsync.nvim.                                                             |
| RsyncConfig        | Print out user config.                                                                    |
| RsyncProjectConfig | Print or reload current project config.                                                   |
| RsyncSaveSync      | Temporarily disable/enable/toggle sync when saving.                                       |

\*: Files which are excluded are, everything in .gitignore and .nvim folder.

## Configuration

Global configuration settings with the default values

```lua
---@type RsyncConfig
{
    -- triggers `RsyncUp` when fugitive thinks something might have changed in the repo.
    fugitive_sync = false,
    -- triggers `RsyncUp` when you save a file.
    sync_on_save = true,
    -- the path to the project configuration
    project_config_path = ".nvim/rsync.toml",
    -- called when the rsync command exits, provides the exit code and the used command
    on_exit = function(code, command)
    end,
    -- called when the rsync command prints to stderr, provides the data and the used command
    on_stderr = function(data, command)
    end,
}
```

## Similar projects

- [coffebar/transfer.nvim](https://github.com/coffebar/transfer.nvim)
- [KenN7/vim-arsync](https://github.com/KenN7/vim-arsync)
