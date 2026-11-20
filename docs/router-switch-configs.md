# Router and Switch configurations

## Basic Device Setup
Applied to all routers and switches. Only the hostname differs per device:

```
enable
config terminal
hostname hostname
no ip domain-lookup
enable secret LabPass123
banner motd #Authorized access only#
line console 0
 logging sync
 exit
end
write memory
```

| Command | Description |
|---|---|
| `enable` | Moves from user mode (`>`) to privileged mode (`#`), where configuration commands are allowed |
| `config terminal` | Enters global configuration mode, where changes to the device are made |
| `hostname ISP-R1` | Sets the device name, matching the topology diagram |
| `no ip domain-lookup` | Stops the device from trying to look up mistyped commands as hostnames, which causes long freezes |
| `enable secret LabPass123` | Sets the password required to enter privileged mode. Stored in encrypted form in the config |
| `banner motd #Authorized access only#` | Sets a warning message shown to anyone who logs in. The `#` marks the start and end of the message |
| `line console 0` | Enters the settings for the console port (the direct cable connection) |
| `logging sync` | Stops system messages from interrupting a command while it is being typed |
| `exit` | Goes back one level (here, from the console line settings to global configuration mode) |
| `end` | Returns directly to privileged mode from any configuration level |
| `write memory` | Saves the running config to the startup config so it survives a restart |
