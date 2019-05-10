# User-Defined Module Collections

HPC systems at the Oak Ridge Leadership Computing Facility use [Lmod](https://lmod.readthedocs.io/en/latest/) to manage modules. Lmod has many features that improve the user experience this tutorial will focus on one in particular: ***module collections***

In short, module collections allow a set of modules to saved and easily restored. 

This tutorial will cover:

- Creating custom module collections
- Setting your own default modules with collections (the correct way!)
- Best practices for managing and naming collections

<hr>
***Note:***
If you're unfamiliar with using Environment Modules (or Lmod) to dynamically modify your environment, this tutorial probably isn't for you. A [basic understanding of modules](https://en.wikipedia.org/wiki/Environment_Modules_(software)) and [how they're used at the OLCF](https://www.olcf.ornl.gov/for-users/system-user-guides/summit/summit-user-guide/#shell-&-programming-environments) should be considered a prerequisite for this tutorial.
<hr>

## Creating custom module collections

Lmod's interface to module collections is simple:

| Command                           | Description                                                                 |
|-----------------------------------|-----------------------------------------------------------------------------|
| module save \<collection_name>     | Save the currently loaded modules into a collection named \<collection_name> |
| module restore \<collection_name>  | Load the collection \<collection_name>                                       |
| module describe \<collection_name> | List the modules included in \<collection_name>                              |
| module savelist                   | List available module collections                                           |
| module disable \<collection_name>  | Disable the \<collection_name> collection                                    |

Let's consider an example where we want to define a collection `MyMods` to include HDF5, htop, and Allinea Forge tools. This collection will be specific to Summit.

We'll first load the desired modules, then save them.

```
## From a fresh login to Summit
summit$ module load hdf5 htop forge
summit$ module save MyMods
Saved current collection of modules to: "MyMods", for system: "summit"
```

OLCF systems set the `$LMOD_SYSTEM_NAME` environment variable, which handles the separation of machines for you automatically. Notice that the output from `module save` above in specific to `system: "summit"`.

Similarly, when listing our saved collections, Lmod will only tell us of the collections for the current system:

```
summit$ module savelist
Named collection list (For LMOD_SYSTEM_NAME = "summit"):
  1) MyMods
```


## Setting your own default modules
By default, there's a set of modules already loaded when you first log in to an OLCF machine. These are listed in a module `DefApps`. 

For example, a fresh login to Summit may have:

```
summit$ module -t list
xl/16.1.1-1
spectrum-mpi/10.2.0.11-20190201
hsi/5.0.2.p5
xalt/1.1.3
lsf-tools/2.0
darshan-runtime/3.1.7
DefApps

```

<hr>
***Tip:***
When listing modules, it's often easier to read them one module per line. The `-t` / `--terse` flag does just this. Terse formatting is also much preferable for copy-paste!
<hr>

The system-wide default modules are a determined to be reasonable starting place, but they can be set on a per-user level, too. Let's now look at how this is achieved. 

For this example, our goal is to set the default login modules to include the GNU compilers, as well as `git/2.13.0` and `python/3.5.2`.

As before, we'll first load the desired set of modules.

```
summit$ module load git/2.13.0
summit$ module load python/3.5.2
summit$ module load gcc
```

To make this collection the standard set of modules, we'll simply leave the name off of the `module save` command.

```
summit$ module save
Saved current collection of modules to: "default", for system: "summit"
```

Notice that Lmod has given this collection a name of `default` for the machine `summit`. When logging into Summit, Lmod will first look for the existance of a `default` collection. If a `default` collection is found, it's loaded automatically. 

We can verify this by exiting and logging in again:

```
summit$ exit
logout
Connection to summit closed.
localhost$ ssh summit
****************************************************************************
                          NOTICE TO USERS

This is a Federal computer system and is the property of the United States
Government.  It is for authorized use only.  Users (authorized or
unauthorized) have no explicit or implicit expectation of privacy.

Any or all uses of this system and all files on this system may be
intercepted, monitored, recorded, copied, audited, inspected, and disclosed
to authorized site, Department of Energy, and law enforcement personnel, as
well as authorized officials of other agencies, both domestic and foreign.
By using this system, the user consents to such interception, monitoring,
recording, copying, auditing, inspection, and disclosure at the discretion
of authorized site or Department of Energy personnel.

Unauthorized or improper use of this system may result in administrative
disciplinary action and civil and criminal penalties.  By continuing to use
this system you indicate your awareness of and consent to these terms and
conditions of use.  LOG OFF IMMEDIATELY if you do not agree to the
conditions stated in this warning.
****************************************************************************
Last login: Fri May 10 09:40:41 2019 from hub.ccs.ornl.gov

summit$ module -t list
hsi/5.0.2.p5
xalt/1.1.3
lsf-tools/2.0
darshan-runtime/3.1.7
DefApps
gcc/6.4.0
spectrum-mpi/10.2.0.11-20190201
git/2.13.0
python/3.5.2
```

This approach is much safer (and easier) than using the `module` command in shell initialization files like `.bashrc`, `.bash_profile`, `.cshrc`, etc. 

## Module Collection Naming
It is not recommended to create collections with the name `default`. Lmod will use the name `default` automatically when the `module save` command is issued, and this should always refer to the modules loaded at time of login. 

As mentioned above, OLCF systems share a home filesystem, and set the `$LMOD_SYSTEM_NAME` environment variable so collections get a `.<system>` suffix. On systems where `$LMOD_SYSTEM_NAME` is not set, care should be taken to name collections appropriately. It's also common to name collections by application, or even branch build of a particular project. 

Good examples of collection names:

- `MyCode-dev-2.6.summit`
- `MyCode-master-2.5.summit`
- `MyCode-x86-build.rhea`

Bad examples of collection names:

- `modules`
- `default` (unless intended to be loaded at time of login)
- `MyCode-modules`
- `testing`


## Other Resources & Further Reading
- User-Defined Module Collections with Lmod ([Video](https://vimeo.com/293582400))
- [Summit User Guide: Shell & Programming Environments](https://www.olcf.ornl.gov/for-users/system-user-guides/summit/summit-user-guide/#shell-&-programming-environments)
- [Lmod ReadTheDocs](https://lmod.readthedocs.io/en/latest/)
