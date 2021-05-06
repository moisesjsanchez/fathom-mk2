# Fathom Mk-2

Fathom Mk-2 the successor to the original Fathom-Chan movie bot. 

### Purpose/Abilities
  * Display anime movie events from the Fathom site monthly
  * Reminders for all movie events the day before they air
  * Command for displaying movie 

### What Changed and Why Rewrite?
  * Rewritten in Node.js to take advantage of the discord.js package and NPM init
  * Addressees issues with the bot such as lack of autonomy for task such as reminding
  * Hosted using Google Cloud Platform so the bot can be other other bots in a single VM instance vs original one bot per Heroku app
  * Docker image written for easier transport and isolation from other bot instances running on VM
  * Save $7 a month on Heroku cost since GCP is based on usage

### API Design

class Scraper:

  * -url: string
  * -movie_data: object
  * +writeToDb(): write to database
  * +cleanObject(): cleans object

class Fathom:
  * +remindMe(date): returns boolean
  * +displayEvents(): returns event objects from Fathom page + Others
  * +printReminder(date): returns movie day before happening (optimal can set own date up to 14 days in advance)
  * +refreshEvents(): returns movies automatic on first of month (cron: 0 0 1 * *)

### Database Schema

class Event:
  * -title: string
  * -date: string
  * -image: string
  * -date_created: int

### High Level Overview

At a high level users of my discord chat room would be able to see updates to events either waiting for the "refresh" or by directly calling it. Furthermore users are able to set reminders for the bot for the entire discord group. Our service will get this information from various sites' scrapped data being held in a Google Cloud Store which Fathom Mk 2 fetches data from. 

![image](https://user-images.githubusercontent.com/36614348/117245153-34d38b00-adef-11eb-9ff4-909db470407b.png)

### Component Design (WIP)

##### Scraper
For the scrapping portion of the service I decided to go with using Pub/Sub + Scheduler + Cloud Function to do the heavy lifting vs simply opening another linux VM (or using the same one) and running a multiplexer for a scrapper running a cronjob. The reason behind this decision is that I was trying to stay within the free tier to avoid extra cost from the bot, and opening another VM would have resulted in me being charged. Also by using GCP's system I can monitor the scrapper application as a near separate entity from the main discord bot.
