# Storage

## Current phase

The NAS is intentionally simple in the current phase.

A Debian LXC provides Samba shares backed by the storage available to the HomeLab. The design favors easy migration over premature RAID or pool complexity.

## Current goals

- central file access;
- simple SMB interoperability;
- predictable permissions;
- clear separation between configuration and user data;
- easy migration when additional disks are added.

## Future direction

The storage architecture will be revisited when multiple disks are available. Possible areas of evaluation include:

- redundancy strategy;
- filesystem / pool choice;
- SMART monitoring;
- backup policy;
- media and personal-data separation;
- recovery testing.

No RAID or redundancy claim should be inferred from the current single-disk phase.

## Git policy

Never commit:

- NAS contents;
- Samba password databases;
- filesystem snapshots;
- disk serial numbers unless specifically needed and sanitized;
- backup archives;
- mount credentials.
