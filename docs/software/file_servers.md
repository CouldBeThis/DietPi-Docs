# File Servers

## Overview

- [**ProFTPD - Simple, efficient, lightweight FTP file server**](#proftpd)
- [**Samba - Feature rich file server**](#samba)
- [**vsftpd - Feature rich FTP file server**](#vsftpd)
- [**NFS - Network file system server**](#nfs)

??? info "How do I run **DietPi-Software** and install **optimised software** ?"
    To install any of the **DietPi optimised software** listed below run from the command line:

    ```
    dietpi-software
    ```
    
    Choose **Software Optimised** and select one or more items. Finally click on `Install`. DietPi will do all the necessary steps to install and start these software items.
    
    ![DietPi software](../assets/images/dietpi-software.jpg)
    
    To see all the DietPi configurations options, review [DietPi Tools](../../dietpi_tools) section.

[Return to the **Optimised Software list**](../../software/)

## FTP Common

### permissions

If folders or mounted volumnes are created by `root`, and you log into FTP with `dietpi`, then you will not have permission to edit the contents and you will get an error if you try to do anything other than read. 

A simple solution would be to change ownership of the folder. You would need to be logged in as root to do this (or use sudo)

```bash
chown -R dietpi:dietpi /path/to/folder
```

?? Problems

## ProFTPD

ProFTPD gives you access to files/music/downloads etc. on your DietPi system quickly and efficiently with minimal overhead.

![DietPi file server software ProFTPD](../assets/images/dietpi-software-fileservers-proftpd.png)


=== "Accessing"

    Same as `vsftpd`. See instructions below.

=== "Destination directory"

    The destination directory can be changed by replacing **/Path/To/Directory** to your target directory:
    
    ```sh
    systemctl stop proftpd
    sed -i '/DefaultRoot /c\DefaultRoot /Path/To/Directory' /etc/proftpd/proftpd.conf
    systemctl start proftpd
    ```

=== "Jailing"

    Jailing means to lock users to their home folders.
    
    *Jailing* can be enabled via
    
    ```sh
    systemctl stop proftpd
    sed -i '/DefaultRoot /c\DefaultRoot ~' /etc/proftpd/proftpd.conf
    systemctl start proftpd
    ```

***

Wikipedia: <https://wikipedia.org/wiki/ProFTPD>

## vsftpd

Very secure FTP file server with feature rich security options.

![DietPi file server software vsftpd](../assets/images/dietpi-software-fileservers-vsftpd.png)

For all of the below examples,

 * Username = `dietpi` by default
 * Password = The same as your **root login** password (not the default application password, like most other things). Default is `dietpi`.
 * IP-Address = Your DietPi's IP address
 * Port = `21` by default

=== "Configuration"

    Options are set in the file `/etc/vsftpd.conf`
	
	To change which directory is shared, edit the line begining `local_root`
	
	The [manual page for `vsftpd.conf`](https://www.mankier.com/5/vsftpd.conf) is a comprehensive reference.

=== "Access via Windows"

    The access to ftp with **Windows** is achieved as follows:
    
    - Go to My Computer (Windows Explorer).
    - In the address bar at the top type `ftp://Username:Password@IP-Address` press enter.
    - Example: `ftp://dietpi:Password@192.168.0.100`
	
	Read a [tutorial](https://www.dummies.com/computers/operating-systems/windows-xp-vista/how-to-access-an-ftp-server-in-windows-explorer/) about using the native Windows FTP client.

=== "Access via any FTP client"

    Use the credentials above with your choice of FTP client.
	
	If you are looking for a client
	
	- [AlternativeTo has a list](https://alternativeto.net/browse/search?q=ftp+client) that can be sorted by platform, liscence, cost and features. 
	- Wikipedia has handy charts: [Comparison of FTP client software](https://en.wikipedia.org/wiki/Comparison_of_FTP_client_software). Mind the column "Latest stable release" as it includes antique programs.

=== "Access via web browser"

    In the past, it was possible to work with FTP from most browsers. However, [Firefox has removed it](https://www.slashgear.com/firefox-88-disables-ftp-support-no-new-addons-for-android-15668630/), [as has Chrome](https://www.chromestatus.com/feature/6246151319715840).  

    If you find web browser a convenient way to work with files, you might want to consider installing [Filestash](https://www.filestash.app/) ([github](https://github.com/mickael-kerjean/filestash)) which creates a modern web UI ontop of the FTP as served. Not supported by DietPi!

***

Wikipedia: <https://wikipedia.org/wiki/Vsftpd>


## Samba

The Samba server lets you share files on your DietPi system with ease based on the well known SMB networking protocol.

![DietPi file server software samba](../assets/images/dietpi-software-fileservers-samba.png)

=== "Access to Samba"

    The access to the Samba file server is achieved as follows:
    
    - Address = `\\192.168.0.100\dietpi`
    - Username = `dietpi`
    - Password = `<your global application password>` (default: `dietpi`)
	
	Samba is supported on major platforms:
	
	- Linux: Gnome has [native support](https://www.addictivetips.com/ubuntu-linux-tips/connect-to-servers-with-gnome-file-manager/), KDE supports it [via smb4k](https://docs.kde.org/stable5/en/smb4k/smb4k/index.html), or one of the [many other options](https://www.samba.org/samba/GUI/) on the official samba website
	- Mac OS has [native support](https://ag.montana.edu/it/support/smb-macs.html)
	- Windows has [native support](https://www.techrepublic.com/article/how-to-connect-to-linux-samba-shares-from-windows-10/)
	- Android, with a free app such as [Material Files](https://play.google.com/store/apps/details?id=me.zhanghai.android.files)
	- iOS has [native support](https://osxdaily.com/2019/11/04/how-connect-smb-share-iphone-ipad-files-app/)

=== "Change the Samba password"

    The Samba password can be changed with
    
    ```sh
    smbpasswd -a dietpi
    ```

=== "Add/change the valid user"

    To add/change the valid user do the following steps:
    
    - Edit `/etc/samba/smb.conf`
        - Search the entry `[dietpi]`, change `valid users = username_i_require`
        - Add the user to Samba with `smbpasswd -a username_i_require`
    - Restart services with `systemctl restart nmbd smbd`
    
    You can now connect to the samba server with the username and password you entered above.

=== "Destination directory"

    The destination directory can be changed by replacing **/Path/To/Directory** to your target directory:
    
    ```sh
    sed -i '/path = /c\path = /Path/To/Directory' /etc/samba/smb.conf
    systemctl restart nmbd smbd
    ```

***

Wikipedia: <https://wikipedia.org/wiki/Samba_(software)>

YouTube video tutorial (German language): [Raspberry Pi als Datei-Server - einfache Installation eines Fileservers Samba unter DietPi](https://www.youtube.com/watch?v=XB2F_Gyjw0s).

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/XB2F_Gyjw0s" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


## 

## NFS

Network file system server.

![DietPi file server software NFS](../assets/images/dietpi-software-fileservers-nfs.png)

=== "Access to an NFS share"

    The access to the NFS share with an **NFS client** is achieved as follows:
    
    - Address = IP address of your DietPi system (e.g.: 192.168.0.100)
    - Port = 2049

=== "Access configuration"

    The NFS access configuration is done via **export files**.  
    You can edit the `/etc/exports` file as well as adding further export files within the `/etc/exports.d` directory.
    
    **Explanations of the export file format** are available in the internet or can be read in the man pages (use `man exports`, therefore the package **man** needs to be installed).
    
    After changing the access configuration, the export information can be re-read via command
    
    ```
    exportfs -ra
    ```
    
    Alternatively you can restart the service (`systemctl restart nfs-kernel-server`).
    
    The actual access configuration can be displayed with the command
    
    ```
    exportfs
    ```
    
    On the client side you can query the mountable exports with the command
    
    ```
    showmount -e <NFS_SERVER>
    ```

=== "Default configuration / increase security"

    By default the DietPi NFS installation exports the directory `/mnt/dietpi_userdata` for everyone. This is configured in `/etc/exports.d/dietpi.exports`. You can edit this file to restrict the access.
    
    E.g. you could limit the access to the NFS share by setting a IP address range:
    
    - Edit the following file: `/etc/exports.d/dietpi.exports`
    - To only allow users access with an IP address range of 192.168.0.1-255
    
        ```
        /mnt/dietpi_userdata 192.168.0.*(rw,async,no_root_squash,fsid=0,crossmnt,no_subtree_check)
        ```
    
    - Activate the new configuration (`systemctl restart nfs-kernel-server` or `exportfs -ra`)

***

Wikipedia: <https://wikipedia.org/wiki/Network_File_System>

[Return to the **Optimised Software list**](../../software/)
