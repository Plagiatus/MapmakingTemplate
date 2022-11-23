# Mapmaking Template

A mapmaking template with (optionally) integrated ftp and RCON GitHub Actions.

![](https://media.discordapp.net/attachments/256539745583104001/821182549220261918/unknown.png)

## How to set up

### Singleplayer

To use this template for your development, just follow the following steps for an optimal setup.

1. This repository is a template. That means you can use it as a starting point for your development. Just click the "Use this template" button at the top of this repository, [click here](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/creating-a-repository-from-a-template#creating-a-repository-from-a-template) if you need help with setting up your repository that way. Don't clone it to your PC just yet.

2. Create the world that you want to develop in in singleplayer.

3. Clone the repository in a way where the world folder is the parent folder for all the files in the repository.  
	```
	.minecraft/saves/world
	├ .git
	├ advancements
	├ data
	├ datapacks
	├ ....
	├ resourcepack
	├ .gitignore
	├ level.dat
	├ README.md
	└ ...
	```

	Alternatively you can also clone it to a different location, which means you need to set up a system link for both the datapack and the resourcepack, see Step 4.

4. Set up a [system link] from your minecraft resourcepacks folder to the resourcepack folder of the repository (repeat for datapack if your repository is not inside your world).  
This allows you to have all the files in one place, but make your PC pretend it's also in other places so that minecraft can find and use them without the need to copy things back and forth all the time.  
Command (run in terminal) to create link (windows): `mklink /J <name/path of new "fake" folder> <path of the folder it points to>`  
Example for resourcepack: `mklink /J "C:\Users\<you>\AppData\Roaming\.minecraft\resourcepacks\map_pack" "C:\Users\<you>\AppData\Roaming\.minecraft\saves\NewMap\resourcepack"`  
Example for datapack: `mklink /J "C:\Users\<you>\AppData\Roaming\.minecraft\saves\NewMap\datapacks" "D:/Data/Projects/NewProject/datapacks"`

And you're ready to work. Now you can just open the whole world folder in your favorite editor (I use VSCode), change both datapacks and resourcepack and are only a `/reload` or `F3+T` away from getting your changes into your world.  
Or the changes from anyone else working on the map at the same time. Just pull the changes and `/reload` / `F3+T`.

**If you don't intend to work on a server, delete the `.github` folder!**

### Server

Sometimes we have the luxury to be able to work on a shared server at the same time. It's often a hassle to move files over through ftp all the time though, or if you work directly on the server you loose the benefits of git / version control. Thanks to github actions however, we can integrate a server into our development cycle.

**💡 Make sure you've followed all the steps for the singleplayer setup first to have a local work environment. If you _only_ work on a server you can skip step 2 and just keep the repository somewhere else, but I'd recommend still setting it up as described.**

**❗ This server part only needs to be set up once by someone in the team, while the singleplayer/local part should be done by every collaborator! ❗**

1. Create a new FTP file access account for your server or use your own. This example is set up for standard Port 21 and authentication via password. If you have SSH authentication, please see the [FTP Action](https://github.com/marketplace/actions/ftp-action) Documentation for what you need to change in the [main.yml](./.github/workflows/main.yml) file.

2. Enable RCON on the server. I recommend setting up a password for it, too. Make sure the RCON port is accessible from the internet! (You can try [mcrcon](https://github.com/Tiiffi/mcrcon/releases) to test that.) 

3. Store the following values as [repository secrets](https://docs.github.com/en/actions/reference/encrypted-secrets)

|secret identitfier|content|example|
|-|-|-|
|ftp_server| the ip address / url of the ftp access | 196.145.2.132|
|ftp_username| the username of the ftp access	| github_user|
|ftp_password| the password of the ftp access | da2%mda$&.P*|
|rcon_server| the ip address / url of the minecraft server itself | 196.145.2.132|
|rcon_port| the port of the RCON access of the server | 25575|
|rcon_password|the password of the RCON connection|l?kTB.63*dK|

_rcon and ftp server might be identical_

4. If your ftp's root access does point directly to the world directory, remove the `remoteDir` input from the `with` list in the `Sync Files` step. If not, make sure it's pointing to the (world) folder you want the files to be copied to.

5. You can adjust the `options` input from the same `with` list as in #4. It uses the linux `mirror` command, so you can give the same options as [that command](https://www.cyberciti.biz/faq/lftp-mirror-example/). The default is set up to copy `--only-newer` files so it doesn't copy the entire folder every time, only what changed.   
**Do not modify if you don't know what you're doing!**  
_For example, using `--delete` sounds good at first, until you realize that it will delete your world folder when you use it._

6. You are now ready to deploy. The actions will now sync the files on the main branch to the ftp server after every push, inform you about the new push and run `/reload` ingame to load the new files. If you want to change that, you can modify the [main.yml](./.github/workflows/main.yml) file. Please see the [RCON Action](https://github.com/Plagiatus/RCON-Action/) repository for futher information and documentation on how to use the options from the RCON action.

## The issue with world files

If you're developing in singleplayer and want to share a world to develop in, you will quickly notice that you'll have an issue: The world isn't tracked by this set up.  
That is because the world is stored in binary files which are very hard to properly track with git. Then to be able to properly pull the changes you'd have to make sure you're leaving the world every time to be sure, which definitely disrupts the workflow.  

### Suggested Solution

I recommend cumulating the world changes in manual commits. That's what the `world_files` folder in this template is in here for:  
Take all the files and folders in the world, that **are not part of this template** and put them into a zip file. Then put the zip file into the `world_files` folder. Name it in a way where you know what is the latest version (e.g. [semver](https://semver.org/) or just an increasing number) and add this folder to your commit. I suggest marking the commit in a way that clearly communicates at a glance that a world change has occured.  

Then to apply the world changes to your singleplayer world, make sure to leave the world, then copy the newest world folder to the singleplayer world folder and unzip it there. **⚠️ This will overwrite your local changes!**

**Be aware that this method means that for world changes you need to coordinate with the other contributors, as merging world changes is basically impossible.**

## Contributions / Issues / Suggestions
If you have any questions, ran into issues or have suggestions, please post an [issue](https://github.com/Plagiatus/MapmakingTemplate/issues).

Contributions/PRs welcome.

