# storage

Ansible role to setup and maintain storage configuration.

## Requirements

Target systems with Logical Volume Manager (LVM) available.

## Role Variables

- storage_conf
  Defines the storage configuration (see example below)
  - mandatory variables :
    - _vg_name_ : Volume Group name. Will be created if inexistent.
    - _lv_name_ : Logical Volume name.
    - _lv_size_ : Logical Volume size. LV and underlying FS (filesystem) will
      be resized if value is greater than current size.
  - non mandatory variables :
    - _pvs_ : Physical VolumeS.
      yaml list or List of comma-separated devices to use as physical devices
      in the volume group. PV is created if necessary via the Ansible module
      'lvg'.
    - _fs_type_ : FS type
    - _fs_src : The filesystem source (UUID=..., LABEL=..., PARTUUID=...).
      If undefined, defaults to `/dev/mapper/vg_name-lv_name`.
    - _fs_mountpoint_ : The mountpoint for this FS.
    - _fs_mountoptions_ : The mount options for this FS.
    - _fs_dump_ : The dump number for this FS (0 by default).
    - _fs_passno_ : The passno number for this FS (0 by default).
    - _state_ : The state of the volume / filesystem.
      default is present. If 'absent', force is mandatory.
    - _force_ : set to true to remove a volume / filesystem with 'state: absent'.

Examples :

    storage_conf:
      - pvs: /dev/disk/by-id/scsi-0QEMU_QEMU_HARDDISK_drive-scsi0
        vg_name: 'sys'
        lv_name: 'root'
        lv_size: '12G'
        fs_src: 'UUID=098bc92e-f20f-425b-b73e-a161aa0c0e9e'
        fs_type: 'ext4'
        fs_mountpoint: '/'
      - pvs:
          - /dev/sdb
          - /dev/sdc
        vg_name: 'data'
        lv_name: 'mydata'
        lv_size: '16G'
        fs_type: 'ext4'
        fs_mountpoint: '/srv/stuff'
      # here we don't care about the PV distribution
      - vg_name: 'data'
        lv_name: 'ftp'
        lv_size: '11G'
        fs_type: 'xfs'
        fs_mountpoint: '/srv/ftp'
        fs_mountoptions: 'noatime,nosuid'
      # here we just create an unformated and unmounted LV
      - vg_name: 'data'
        lv_name: 'bigdata'
        lv_size: '800G'
      # Delete a volume / filesystem
      - lv_name: mydb
        vg_name: data
        lv_size: 5G
        fs_mountpoint: /srv/mydb
        state: absent
        force: true

## Dependencies

None.

## Install this role as submodule in a git repository

`git submodule add https://git.sekoya.org/mb/storage.git roles/storage`

## Example Playbook

    - hosts: servers
      roles:
         - storage
or

    - hosts: servers
      roles:
         - { role: storage, when: storage_conf is defined }

## License

GPLv3

## Author Information

http://www.sekoya.org
