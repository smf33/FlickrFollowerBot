# Flickr Follower Bot

Bot for Flickr, in .Net Core, using a Chrome client and Selenium for command it.

Main functions :
- Follow users whose follow you
- Unfollow users whose doesn't follow you and are not tagged as friend or family
- Find contacts in Flickr'Explore
- Can work with a remote Selenium grid and/or in docker

*Tags	: Flickr, Chrome, Selenium, C#, .Net, Core, bot, robot*

## Requirement

- Windows, Linux or Mac
- .Net Core SDK 3.1 : https://dotnet.microsoft.com/download/dotnet-core/3.1
- Chrome, or a Selenium server with Chrome clients

## Usage

### DotNet run
![.NET Core](https://github.com/smf33/FlickrFollowerBot/workflows/.NET%20Core/badge.svg)

Download the sources and run donet sdk command in the folder of your Windows, Linux or Mac.

- Run with default (users settings have been set in the .json or the environnement variable) :
```
dotnet run
```

- Follow back users whose follow you in a permanent loop :
```
dotnet run BotTasks=DetectFollowBack,DoFollow,Wait,Loop BotUserEmail=you@dom.com BotUserPassword=Passw0rd
```

- On a daily base, find users in today Explore, fav theirs pictures then follow them :
```
dotnet run BotTasks=DetectExplored,DoFav,DoFollow BotUserEmail=you@dom.com BotUserPassword=Passw0rd
```

- On a daily base, unfollow users whose doesn't follow you :
```
dotnet run BotTasks=DetectUnfollowBack,DoUnfollow BotUserEmail=you@dom.com BotUserPassword=Passw0rd
```

- Follow and fav photo of a specific user :
```
dotnet run BotUserEmail=you@dom.com BotUserPassword=Passw0rd AddContactsToFollow=https://www.flickr.com/photos/jc173/ AddContactsToFav=https://www.flickr.com/photos/jc173/ BotTasks=DoContactsFollow,DoContactsFav
```

- On a daily base, fav photos of users that you already follow :
```
dotnet run BotTasks=DetectRecentContactPhotos,DoPhotosFav BotUserEmail=you@dom.com BotUserPassword=Passw0rd
```

- On a daily base, search keywords and follow+fav of users in the search resulf :
```
dotnet run BotSearchKeywords="Paris,France" BotTasks=SearchKeywords,DoContactsFollow,DoPhotosFav BotUserEmail=you@dom.com BotUserPassword=Passw0rd
```

- Using a remote selenium hub :
```
dotnet run SeleniumRemoteServer=http://seleniumhubhostname:4444/wd/hub BotUserEmail=you@dom.com BotUserPassword=Passw0rd
```

### Docker run
![Docker](https://github.com/smf33/FlickrFollowerBot/workflows/Docker/badge.svg)

- Build and Run default BotTasks with Docker with a remote Selenium Hub (here another docker) :
Exemple with Z:\FlickrFollowerBot as the source path, on a Windows system
```
docker build -f Z:\FlickrFollowerBot\Dockerfile -t flickrfollowerbot Z:\FlickrFollowerBot
docker run --name seleniumContainer --detach --publish 4444:4444 selenium/standalone-chrome --volume /dev/shm:/dev/shm 
docker run --link seleniumContainer:seleniumhost flickrfollowerbot BotUserEmail=you@dom.com BotUserPassword=Passw0rd SeleniumRemoteServer=http://seleniumhost:4444/wd/hub
```

### Docker Compose run
![Docker Compose](https://github.com/smf33/FlickrFollowerBot/workflows/Docker%20Compose/badge.svg)

- Build and Run default BotTasks with Docker and an standalone Selenium

Exemple with BotUserEmail&BotUserPassword provided in the FlickrFollowerBot.json or in the "environment:" of the docker-compose.yml
```
docker-compose up
```

## Configuration
- Main settings :
Settings may be read in command line parameter, else in environnement variable, else in FlickrFollowerBot.json.
Only BotUserEmail and BotUserPassword won't have default working values from the initial configuration file.
BotUserPassword may be set to null in debug mode (the user will be able to insert the password himself)

| Parameter | Description |
| :-------- | :---------- |
| BotUserEmail | Email for auto-login and filename of the session file |
| BotUserPassword | Password for auto-login, may be set to null if session file already created |
| BotUsePersistence | Will create a file for the user session and cookies |
| SeleniumRemoteServer | Url of the Selenium Hub web service |
| BotTasks | Tasks to do, separatedd by a comma |
| BotUserSaveFolder | Where user informations (like cookie) are stored |
| AddPhotosToFav | Next DoPhotosFav will fav this picture, multiple separated with a comma, format : https://www.flickr.com/photos/{USERID}/{PHOTOID}  |
| AddContactsToFollow | Next DoContactsFollow will follow this user, multiple separated with a comma, format : https://www.flickr.com/photos/{USERID}/  |
| AddContactsToFav | Next DoContactsFav will fav pictures of this user, multiple separated with a comma, format : https://www.flickr.com/photos/{USERID}/ |

- Taks :
Task name is case insensitive
A lot of settings in order to randomize or limit the batch, in the Bot.Json

| Name | Description |
| :--- | :---------- |
| DetectContactsFollowBack | Push contacts for DoContactsFollow and DoContactsFav |
| DetectContactsUnfollowBack | Push contacts for DoContactsUnfollow |
| DetectExplored | Push contacts for DoContactsFollow, DoContactsFav and push photos to DoPhotosFav |
| SearchKeywords | Search BotSearchKeywords and find contacts for DoContactsFollow, DoContactsFav and push photos to DoPhotosFav |
| DetectRecentContactPhotos | Push photos for DoPhotosFav |
| DoContactsUnfollow | Pop elements that DetectContactsUnfollowBack have send to this queue |
| DoContactsFollow | Pop elements that others tasks have send to this queue |
| DoContactsFav | Pop elements that others tasks have send to this queue |
| DoPhotosFav | Pop elements that others tasks have send to this queue ||
| Save | Update the session file |
| Wait | Pause the worker |
| Loop | Restart from first task |

## Notes
- Selenium Chrome Driver must have the same version than your Chrome (change the lib version in the project if required)
- Flickr Web Api isn't used because doesn't allow main actions
- Don't be evil, else Flick will delete your spamming account
- The solution is micro-service oriented, but Flickr will detected the spamming account if the bot is too fast
- If you want to publish without remote Selenium, add _PUBLISH_CHROMEDRIVER in the DefineConstants of the .csproj

## TODO :
- Unfav all
- Exception list for unfollow
- Explore : target a number of result instead of a number of scrool
- Smarter fav : detect already faved pict without going to the pict
- In some case, fix the Content Security Policy issue on some retail/headless mode first auth
- Resume when Chrome crash ?
- Fork for Instagram
