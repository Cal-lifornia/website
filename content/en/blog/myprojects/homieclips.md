---
title: Homieclips
weight: 5
date: 2025-01-04
description: >
 Homie Clips was an app that I was working on, it's an app my friends will be able to use to upload and view their short clips that they have captured from games or other content. Since we don't have a central app for that. 
---

## Goals
### Fundamental
- Allow my friends to upload short clips that they have created 
- Be able to stream the videos from any device quickly and easily
- A pleasing user experience
- Hosting clips in an object storage solution like S3
- Discord authentication and sign in 
- Automatic transcoding of uploaded files to HLS including multiple video qualities
- When uploading games naming the game they belong to
- Only those I choose can sign in via Discord and access the site
### Future
- Use the [IGDB](https://www.igdb.com/) to provide the list of games to select as well as use the cover art for categorising
- Include playlists for viewing
- Have the ability to download the clips
## Services
### Main Backend & Frontend: Homie Clips
*Repository Link: https://github.com/Cal-lifornia/homieclips*

This is both the main frontend and backend of the Homie Clips app which is being built in Golang. 

The backend is an Api built in Golang with the [Gin](https://github.com/gin-gonic/gin) library. The main purpose of the backend is to handle the CRUD actions between the frontend and the main Mongo database. These include:
- Creating, deleting, updating and reading of the clips data from the database
- Authentication via Auth0
- Login and logout logic

The frontend of the app is also served via Gin and the frontend is built with vanilla javascript and [Tailwind CSS](https://tailwindcss.com/). 

To stream videos the [Video.js](https://videojs.com/) library is being used as it can natively stream HLS and has lots of customisable options. For that to work the data for the main home page is fetched and the link for the video is fetched by adding the unique object id onto the S3 url for the video.

The frontend still requires a fair amount of work, with the ability to upload clips being the main missing feature as I am aiming to have the ability to use multipart uploads directly from the client to S3 so that the main server isn't being used for that. This is because the main server will be hosted at home.

#### HSL Transcoder 
*Github Link: https://github.com/Cal-lifornia/homieclips-hsl-transcoder*

The HSL Transcoder service is a background service that utilises FFMPEG, AWS S3 and SQS libraries to automatically transcode any files that have been uploaded to the S3 Homie Clips bucket to HSL fragments in multiple resolutions. Those fragments are then uploaded to the S3 bucket and marked as complete in the MongoDB, the original uploaded file is then copied to another location in the bucket so that it is archived.

Currently the service works as intended however two main features are missing:
- Transcoding will only really work for 4K and QHD, this is because a function to detect the resolution of the original clip and then use the correct FFMPEG command needs to be added.
- Automatic thumbnail generation and upload using FFMPEGs thumbnail filter

#### Database
The database for Homie Clips uses MongoDB, originally Mongo was only being used for testing as I hadn't used it before, but I found that it was very useful and since I don't need it to be lighting quick it is suitable for the project.

The key part of the database is the Clips collection, which has each clips object_id, this id is used to name the clip that is uploaded to the S3 Homie Clips bucket, so it allows the clip to be easily found and used in the frontend without having to use a separate S3 library. In the future this will also allow changes to happen in the database and as long the object_id remains, base functionality will stay.
