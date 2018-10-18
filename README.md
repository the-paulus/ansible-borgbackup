Backup Borg
=========

Install borgbackup and create a cron job to schedule backups using borgbackup.

Requirements
------------

There are not requirements for this role. If you plan to backup remotely then that must be configured prior to running the tasks in the role.

Role Variables
--------------

This and one of the password environment variables are the only variables that is absolutely required.
```yml
backup_borg_reposoitory: /srv/backup
```

Although the encryption mode is technically required, a default is provided.
```yml
backup_borg_encryption: keyfile-blake2
```

If you don't want to create a cron job then define **backup_borg_cron** as `[]`
```yml
backup_borg_cron:
  minute: 0
  hour: 3
  day: '*'
  weekday: '*'
  month: '*'
```

In order to create a cron job that will create backups the **BORG_REPO** and either the **BORG_PASSPHRASE** or **BORG_PASSCOMMAND** need to be set. See the [Environment Variables](https://borgbackup.readthedocs.io/en/stable/man_intro.html#environment-variables).
```yml
backup_borg_envars:
  BORG_REPO: "{{ backup_borg_reposoitory }}"
  BORG_PASSPHRASE: "N27N87aC6WNfNM9B"
  BORG_PASSCOMMAND: ""
  BORG_NEW_PASSPHRASE: ""
  BORG_HOSTNAME_IS_UNIQUE: "no"
  BORG_HOST_ID: ""
  BORG_RSH: ""
  BORG_REMOTE_PATH: ""
  BORG_FILES_CACHE_TTL: "20"
  BORG_SHOW_SYSINFO: "yes"
  TMPDIR: ""
  BORG_UNKNOWN_UNENCRYPTED_REPO_ACCESS_IS_OK: "no"
  BORG_RELOCATED_REPO_ACCESS_IS_OK: "no"
  BORG_CHECK_I_KNOW_WHAT_I_AM_DOING: "NO"
  BORG_DELETE_I_KNOW_WHAT_I_AM_DOING: "NO"
  BORG_RECREATE_I_KNOW_WHAT_I_AM_DOING: "NO"
```

The following variables tell borg which files to exclude in the backup. There is no restriction on which ones can and cannot be used in conjunction.
```yml
backup_borg_exclude_file: ""
backup_borg_exclude_files:
  - '*/lost+found'
  - '*/.local/share/Trash'

backup_borg_exclude_patterns_file: ""
backup_borg_exclude_patterns: []
backup_borg_exclude_if_present: ""
```

The command options for the create and prune subcommands.
```yml
backup_borg_create_options: "--verbose --filter AME --list --stats --compression auto,zlib,9 --exclude-caches --exclude-nodump"
backup_borg_prune_options: "--list --prefix '{hostname}' --keep-daily 7 --keep-weekly 4 --keep-monthly 6"
```

The name of each archive that is created.
```yml
backup_borg_archive_name: "{hostname}-{now:%Y-%m-%dT%H:%M:%S}"
```

What directories to backup.
```yml
backup_borg_paths: "/etc /root /home"
```

Dependencies
------------

None

Example Playbook
----------------

```yml
  - hosts: servers
    vars:
      backup_borg_reposoitory: "ssh://backup@192.168.1.2:/srv/backup"
      backup_borg_envars:
        BORG_REPO: "{{ backup_borg_reposoitory }}"
        backup_borg_exclude_files:
          - '*/lost+found'
          - '*/.local/share/Trash'
          - '/home/*/Downloads'
    roles:
      - the-paulus.backup-borg
```

License
-------

BSD
