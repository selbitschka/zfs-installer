# zfs-installer
This is a fork of https://github.com/saveriomiroddi/zfs-installer to make some enhancements.

Please follow the main instructions, enhancements are documented below.

Tested with Mint 20 only, so please do not use in production with other distributions.

# Encancements
### Creation of a main dataset per pool
Instead of putting all the data directly into the pools (bpool, rpool), the script creates
a parent dataset per pool. This gives you more flexibility by using snapshots during upgrades etc. 

```console
bpool                            xxxM   xxxM      xxxK  /
bpool/boot                       xxxM   xxxM      xxxM  legacy
rpool                            xxxG   xxxG      xxxK  /
rpool/root                       xxxG   xxxG      xxxG  /
```

You can use the parameters ZFS_BOOT_SET_NAME and ZFS_ROOT_SET_NAME to choose the name of the datasets. 

### Dataset for encryption
Instead of encrypting the root pool (rpool) directly an encrypted base dataset (encrypted) is generated.
This allows the creation of unencrypted dataset within the root pool if needed.

If you enable encryption your datasets look like:

```console
rpool                            xxxG   xxxG      xxxK  /
rpool/encrypted                  xxxG   xxxG      xxxK  none
rpool/encrypted/root             xxxG   xxxG      xxxG  /
```

You can use the parameter ZFS_ENCRYPTION_SET_NAME to choose the name of the datasets. 

### Dataset for /home
Optionally the script creates separate datasets for /home, to allow better snapshot backup support.
The 'home' datasets is created beside your systems 'root' dataset directly below 'rpool' or 'encrypted' 
in case of encryption. So you can backup your 'home' data without backing up the whole system.  
```console
rpool                            xxxG   xxxG      xxxK  /
rpool/encrypted                  xxxG   xxxG      xxxK  none
rpool/encrypted/home             xxxG   xxxG      xxxG  /home
```
You can use the parameter ZFS_HOME_SET to enable the creation of a separate dataset and
ZFS_HOME_SET_NAME to choose the name of the datasets. 

### Datasets for /var
Many ZFS guides recommend creating multiple dataset for /var and directories below -
this script do separate some of them.
 
If you enable the separation of /var following datasets are generated:
```console
rpool/encrypted/root/var         xxxM   xxxG      xxxK  /var
rpool/encrypted/root/var/cache   xxxM   xxxG      xxxM  /var/cache
rpool/encrypted/root/var/lib     xxxK   xxxG      xxxK  /var/lib
rpool/encrypted/root/var/log     xxxM   xxxG      xxxM  /var/log
rpool/encrypted/root/var/tmp     xxxK   xxxG      xxxK  /var/tmp
```

```.../var/cache``` and ```.../var/tmp``` are created with option ```-o com.sun:auto-snapshot=false``` 
to disable snapshots.

You can enable the separation, by setting ZFS_VAR_SET to a nonempty value.

### Datasets for /opt
If you plan to have data in ```/opt``` its possible to create an own dataset for it.
```console
rpool/encrypted/root/opt         xxxM   xxxG      xxxM  /opt
```

You can enable the separation, by setting ZFS_OPT_SET to a nonempty value.

### Minor improvements
* ```show_datasets``` function prints the created pools and dataset during installation.
* ```sleep $v_swap_size``` added to swap generation to give the system enough time to create the zvol.