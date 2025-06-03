# Thinkbox Deadline

Repository setup with Remote Connection and Webserver, Client setup

## Table of Contents

- Repository
- Client
- Remote Connection
- Webserver

## Host setup

Currently the setup for the host is we have Proxmox installed on our server machine, and it hosts two running VM's. On these VM's we have installed Windows Server 2025, with us splitting out features and tools between the two. We will be installing Deadline on our second VM, called `VM-02`.

## Repository

### Installation

At the point of this article, the latest version of Thinkbox Deadline is `10.4.1.8` and it comes with compiler issues. We'll go into more details further down.

Official Guide: [10.4](#https://docs.thinkboxsoftware.com/products/deadline/10.4/1_User%20Manual/manual/install-db-repo.html)

When downloading the Deadline installers, we have three installers to choose from.

```bash
AWSPortalLink-1.4.0.0-windows-installer         # Currently not applicable
DeadlineClient-10.4.1.8-windows-installer       # Deadline Monitor, Pulse, Webserver, etc.
DeadlineRepository-10.4.1.8-windows-installer   # The repository + MongoDB
```

For now, let's run the Repository first. 

<!-- ![Alt text](./images/01_01_repository.png)
 -->
<img src="./images/01_01_repository_init.png" width="400">
<img src="./images/01_02_repository_init.png" width="400">


Once we come against where the setup asks us to specify the Installation Directory, I tend to move towards a more structured hierarchy, and will set this to:

```
C:\Custom\Thinkbox\DeadlineRepository10
```

Making sure to check the `Set full read/write access for files for all users` option.

<img src="./images/01_03_repository_dir.png" width="400">

Then I specify to use `MongoDB` as the type of database, and simply download a new MongoDB via the internet, accepting the EULA's as I go.

<img src="./images/01_04_repository_mongodb.png" width="400">
<img src="./images/01_05_repository_mongodb.png" width="400">
<img src="./images/01_06_repository_mongodb.png" width="400">
<img src="./images/01_07_repository_mongodb.png" width="400">

Now it will ask me to specify where to install the MongoDB Directory, as well as the Hostname and ports.

I like to also change the installation directory to one that mimicks the above repository, like so:

```
C:\Custom\Thinkbox\DeadlineDatabase10
```

<img src="./images/01_08_repository_mongodb.png" width="400">

Then I'll leave the defaults for the certification. Always remember to turn on and use the client certification, as it will alleviate pains later on.

Certificate Directory:

```
C:\Custom\Thinkbox\DeadlineDatabase10\certs
```

I will be adding to this folder directly later on, however this could be handled more elegantly, such as:

```
C:\Custom\Thinkbox\DeadlineDatabase10\certs\local_certs
```

This just specifies the directory where the shared `DeadlineClient10.pfx` file should live.

<img src="./images/01_09_repository_mongodb.png" width="400">


Finally, enable Deadline Secrets Management. For this example, I've used the following:

```
Admin Username: FormationAdmin
Admin Password: password01!
```

<img src="./images/01_10_repository_secrets.png" width="400">
<img src="./images/01_11_repository_secrets.png" width="400">
<img src="./images/01_12_repository_secrets.png" width="400">

### Permissions

This assumes that you have created a local user account, called `deadlineuser`. The documentation is best used from the [official Microsoft page](#https://support.microsoft.com/en-us/help/4026923/windows-10-create-a-local-user-or-administrator-account)

Onto setting up user permissions. Navigate to where you've installed the Deadline repository, for me this is:

```
C:\Custom\Thinkbox\DeadlineRepository10
```

Right click on the folder, and select `Properties`.

<img src="./images/02_01_repository_perms.png" width="400">

From here, navigate to `Security`, click `Advanced`, and then `Change permissions`


<img src="./images/02_02_repository_perms.png" height="300">
<img src="./images/02_03_repository_perms.png" height="300">

Remove `Everyone` and `Authenticated Users`, then Add a user. From here, click on `Select a principal` and search for `deadlineuser`, then hit `OK`. Put the `Type` to `Allow`, the `Applies to` to `This folder, subfolders and files`, and select ONLY `Read & Execute`, `List folder contents`, and `Read`.

<img src="./images/02_04_repository_perms.png" width="400">
<img src="./images/02_05_repository_perms.png" width="400">
<img src="./images/02_06_repository_perms.png" width="400">
<img src="./images/02_07_repository_perms.png" width="400">

This should now overwrite permissions to all the folders below. Then we will have to cherry-pick 3 distinct folders to give full access permissions too. This will be `jobs`, `jobsArchived`, and `reports`.

<img src="./images/02_08_repository_perms.png" height="300">
<img src="./images/02_09_repository_perms.png" width="400">

Finally, we have to setup permissions for the certificate. Right click, select `properties`, then the `security` tab, and `advanced`. From here, select the `Owner` and hit `Change`, type in `Everyone` and hit `OK`. Then apply, and you're done! The repository is now fully setup.

<img src="./images/02_10_repository_perms.png" width="400">
<img src="./images/02_11_repository_perms.png" height="300">
<img src="./images/02_12_repository_perms.png" width="400">
<img src="./images/02_13_repository_perms.png" width="400">

---

## Client

### Installation

Setting up the client is relatively easy compared to the repository, but it still has some gotchas.