# User Interface Options

2 main interfaces for configuring Junos

- Junos CLI
- J-Web

## Logging In

Fresh Junos device has just the root account. You log in with `username: root` and no password.  
All non-root users log into the operational mode straight away.

The hostname of a fresh device is `Amnesiac`

## JUNOS CLI

The Junos CLI defaults to VT100 terminal type. There are a lot of key bindings to assist movement of the cursor in the terminal.

![Keyboard Sequences](../images/Module_2/keyboard_sequences.png)

### Context Sensitive Help

Use the `?` for help

### Topical help

- help topic  
The help topic displays usage guidelines for the statement.
Example:  
`user@router> help topic interfaces ?`  
`user@router> help topic interfaces address`  
![help topic](../images/Module_2/help_topic_cmd.png)

- help reference
Displays summary information for the referenced config statment.  
`user@router> help reference interfaces address`
![help reference](../images/Module_2/help_reference_cmd.png)

- help apropos  
Displays contexts (typically set commands) that reference a specified variable  
`user@router> help apropos archive`
![help apropos](../images/Module_2/help_apropos_cmd.png)

### Command Completion

This helps enter commands faster by reducing keystrokes and preventing typos and errors. When there are more than one command, the system displays the available options.

- Spacebar completion:  
Use the spacebar to complete commands. To disable spacebar completion: `user@router> set cli complete-on-space off`

- Tab completion:  
Use the tab key to complete commands and variables. Variables include policy names, AS Paths, Community Names and IP addresses.

### Using the pipe (|)

The pipe `|` is used to filter or manipulate command output.

## CLI Modes

### Operational Mode

Used for monitoring and troubleshooting.  
Identified by `>`  
The operational mode provides key capabilities such as:

- Entering configuration mode
- Controlling the CLI environment
- Monitor and troubleshooting: ping, traceroute, clear, show, test and mtrace.
- Copy files
- Restart software processes
- Perform system-level operations

### Configuration Mode

For configuration of all properties of the device.  
Identified by `#`

## Active Configuration Vs Candidate Configuration

Junos configuration modes has two types:

- Active configuration
- Candidate configuration

### **Active Configuration**

This is the configuration currently active on the system and is the configuration which the system loads during its boot sequence. Kinda like startup-config

### **Candidate Configuration**

This is a temporary configuration that might become an active configuration. When you configure a device, the device creates a candidate config file and populates it with the active configuration. You then modify the candidate configuration and commit your changes. This causes the candidate configuration to become the active configuration.  
The commit command checks the modified configuration for syntax errors. If there's an error, it does not save the changes.  

You can restore previous configuration using the `rollback` command. The system stores up to 50 previous configs with the 1st one starting from 0.  

You enter the configuration mode by entering `configure` in the operational mode. After entering configure, you will be in the configuration mode like this:

```text
[edit]
user@router#
```

In case there's another user in the configuration mode, you will be prompted about the user and the part of the config file he/she is editing.  

#### Configure Exclusive: `configure exclusive`

This is used to enable only a single user to modify the candidate configuration at a time. It excludes other users from editing the configs. All changes are lost if they are not committed before leaving the `configure exclusive` mode unlike using the standard configure command.

#### Configure Private: `configure private`

This enables multiple users to edit private copies of the configuration concurrently. Just like configure exclusive, all changes are discarded if not committed before leaving the mode. However, the changes are merged into the global configuration when a commit is made.

Whenever a user does`rollback 0` in the configure private mode, the software discards only their changes.

When two users make conflicting changes, the first commit from user 1 takes effect. The second commit from user 2 will give an error and no change will be effected. User 2 must commit again to override user 1's commit.

In chassis clustering, `configure private` is automated and users will have to leave the configure private mode before other users can make changes

#### Configuration Hierarchy

Configuration is hierarchical and has many levels. Use the following to move through the hierarchy:

- `edit`: Functions like change directory statement. Use edit to move to the desired level or to create a level and move to that level.
- `up`: Moves up one level from the current position.
- `up n`: Moves up 'n' levels or top of the hierarchy if the levels are fewer than n.
- `top`: Moves quickly to the top of the hierarcy.
- `exit`: Returns the user to the top level hierarchy of the current level not the root level. However, when `exit` is used at the root level, the user leaves the configuration mode. `exit configuration-mode` can be used to exit config mode from any level of the hierachy.

#### Adding Configuration Statements

The `set` command is used to add config statements.

```text
[edit system services]
user@router# set ftp
```

#### Deleting Configuration Statements

The `delete` command is used to add config statements.

```text
[edit system services]
user@router# delete ftp
```

The delete command can also be used to delete a whole hierarchy. This will delete every config under the hierarchy.

`wildcard delete` can also be used to delete multiple items. Eg:

```text
[edit]
user@router# wildcard delete interfaces ge-1/*
matched: ge-1/0/0
matched: ge-1/0/1
Delete 2 objects? [yes, no] (no)
```

#### Helpful commands in the Config Mode

`deactivate` can also be used to ignore the config statement while still keeping the config lines in the file. Eg: `deactivate interfaces <interface name>`

To reactivate the lines, use `activate` command. Eg: `activate interfaces <interface name>`

`annotate` is used to comment a config statement.

![helpful commands](../images/Module_2/helpful_cmds.png)

#### Viewing the configuration file

`show` command is used to display the current candidate configs including the changes. The show command shows the config statements in a hierarchy, as such using it at the top level will show everything whiles using it within a lower level will only show statements under that hierarchy. The command can also be used by adding the hierarchy level. Eg: `show system services`

The command `show | display set` can be used to show the `set` commands used to build the config file.

#### Committing the configuration file

Junos does not automatically apply configuration statements as they are being added. Hence, the configuration will have to be saved before they are activated. This is done using the `commit` command.

`commit synchronize` is used to synchronize configs to devices with different routing engines.
The `set system commit synchronize` can be used to let the system automatically synchronize when the standard commit command is used.

`commit check` is used to validate the syntax of the candidate configs without actually activating the configs.

`commit confirmed` is used to temporarily activate rollback to complete the commit sequence on your behalf. The candidate config is activated and rolled back to rollback 0 after a defined number of minutes (default: 10mins). Your config file is saved to rollback 1 so you can check your 'mistake'. This command has saved a lot of jobs.

`commit at <time>` is used to schedule commits. You can view pending commits with `show system commit` and clear pending commits with `clear system commit`.

`commit comment "comment string"` is used to add a log entry to the commit.

`commit and-quit` allows you to activate your candidate config and exit configuration mode in a single command.

#### Comparing Active and Candidate Configs

The `compare` command is used to compare files. The `show | compare` is used to compare the candidate config and the active config (rollback 0). The command output shows only the difference in the files. The '+' indicates the additions and the '-' indicates deleted lines. Eg:

```text
[edit system services]
user@router# show | compare
[edit system services]
+ ftp;
- telnet;
```

#### Comparing Active and Historical Configs

In operational mode, you can use the following to compare historical configs or an arbitrary file stored on the system. Junos stores 50 configs - rollback 0 - 49.

`show configuration | compare rollback n`  
`show configuration | compare filename`

#### Compare arbitrary files

The command below can be used to compare files on the system.  
`file compare files filename_1 filename_2`

#### **Life of the Configuration File**

![Life of a config file](../images/Module_2/life_of_config_file.png)

## Backing Out of Configs

Junos stores up to 50 historical configuration files. The current version which is the active configuration is version 0 whiles the oldest is version 49. In config mode, the command `rollback version` can be used to load a particular config file in history. The changes are only loaded and will have to be committed before it is activated.

On some smaller devices, there are less configuration files that are stored by default. This can be changed using the command `set max-configuration-on-flash ?`.

## Saving Configs

`save filename` saves the candidate config to the file(filename). If no location is specified, the file is automatically saved in the user's home directory (/var/home/username/). The file can also be saved directly to a remote location. Eg:  
`save ftp://user:password@router/path/filename`  
`save scp://user@router/path/filename`

## Loading Configs

`load` can be used to load configs from:

- local file
- file on a remote machine
- the terminal's capture buffer

![Load Command Arguments](../images/Module_2/load_cmd_arguments.png)
![Load Command Arguments 2](../images/Module_2/load_cmd_arguments_2.png)

## Run Command

`run` command enables you to run operational mode commands in configuration mode by simply prefixing the command with run. Eg:

```text
[edit interfaces ge-0/0/12]
user@router# run ping 10.250.0.49 count 3
```
