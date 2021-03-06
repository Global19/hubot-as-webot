# Mebot

## About

Webot is a chat bot built on the Hubot framework. It was initially generated by
generator-hubot.

This project is a tying together of a yo generated hubot and several published
packages for the purpose of taking an individual's presence in one room and
replicating this onto a different service.

It was written by Andrew Lucas for resin.io's internal communication
streamlining.

## License

* Parts published by GitHub under an unrestricted license.
* Parts authored by Andrew Lucas (sqweelygig), owned by resin.io, and published
  under Apache-2.0.

## Installation

### Resin.io

1. Get an app &amp; device
    1. Create a new application in [https://resin.io](resin.io) targeted for
       your hardware
    2. Download ResinOS, flash and boot to make your IoT device cloud
       manageable
2. Set up environment
    1. Read the Environment Variables section of this document
    2. Use the Environment Variables page of your application or device
3. Deploy the repo
    1. `git clone https://github.com/resin-io/hubot-as-mebot` then `cd` into it
    2. Add the remote using the provided command in the top right of resin.io
    3. `git push resin` &amp; see the unicorn

### Heroku

1. Get an app
    1. Visit heroku.com, get an account
    2. Create a new app using the top right button
2. Set up environment
    1. Read the Environment Variables section of this document
    2. Visit the settings tab of Heroku
    2. Use the Config Variables section on Heroku to configure how you wish
3. Fork the repo
    1. This is optional if you have access to and trust an organisation that
       has already forked this repo (eg resin.io)
    2. Fork [hubot-as-mebot](https://github.com/resin-io/hubot-as-mebot)
4. Link the repo
    1. Visit the deploy Tab on Heroku
    2. Enable Automatic deploys from the master branch
5. Activate the worker
    1. Visit the resources tab on Heroku
    2. Edit the web Dyno so it is not running
    3. Edit the worker Dyno so it is running
6. Restart, just in case
    1. My experience is that once this is all set up it's best to restart
    2. Using the More dropdown (top right) restart all Dynos

#### Deployment without Trust

So, you don't trust the repo maintainers and want to follow the principle of
least privilege. Good for you. You've got two easy options, either way you're
going to have to take over the review and deploy yourself:
* Create and maintain your own Fork in step (3). More work, more secure.
* Leave deployment as manual in step (4). Less work, less secure.

### Windows

1. Get node.js
    1. I use whatever the latest ^6.x is.
    2. Plenty of guides exist for this, I'm not going to replicate or
       recommend.
2. Clone the repo
    1. `git clone https://github.com/resin-io/hubot-as-mebot`
3. Set up environment
    1. Read the Environment Variables section of this document
    2. I quite like using `SET` commands in a .bat file for this
4. Run the code
    1. `cd` into the code directory
    2. `npm run windows`
    3. I quite like using a `CALL` in the .bat file for this

### Environment Variables

* `HUBOT_ADAPTOR` - Always set to `flowdock-listen-to-self`. Sets up which
  service to listen to. Runtime processes should force this.
* `HUBOT_FLOWDOCK_API_TOKEN` - Personal API token from
  [www.flowdock.com/account/tokens](https://www.flowdock.com/account/tokens).
  eg `43542ab245098dade5098f12430bcdfa`.
* `HUBOT_FLOWDOCK_LISTEN_TO_SELF` - Always set to `1`. This tells the adaptor
  to listen to it's own account. This modifications made in
  flowdock-attend-own-account. Runtime processes should force this.
* `HUBOT_GITTER_API_TOKEN` - Personal Access Token from
  [developer.gitter.im/apps](https://developer.gitter.im/apps). eg
  `342d5aacbe45098ade243578de435fc425ad23ee`.
* `HUBOT_GITTER_ROOM` - Name of the Gitter conversation to replicate to. eg
  `resin-io/sandbox`. This defaults to `resin-io/public`.
* `HUBOT_IGNORE_PREFIX` - Ignores lines based on a prefix. eg `:`. This
  defaults to `£`.
* `HUBOT_MONITOR_ROOM` - ID of the Flowdock flow to replicate from, currently
  awkward to access.  The utility command 'monitor log' will put this to the
  robot logger. eg `bed6fa5a-6a31-4a31-9b3b-9184b158c2b8` (r/sandbox). This defaults to
  `3febc696-3aaf-4a83-9ddd-3a267abf4212` (public/chat).
* `HUBOT_NAME` - Set to your own nickname in Flowdock. Tells Hubot what account
  it is using. eg `sqweelygig`.

## Program Flow

1. Connect
    1. because of `HUBOT_ADAPTOR` the `flowdock-listen-to-self` adaptor
       connects to flowdock using the `HUBOT_FLOWDOCK_API_TOKEN` and lists the
       channels it is joined to.  This pays attention to the account's output
       because of `HUBOT_LISTEN_TO_SELF`.
2. Receive
    1. `flowdock-ignore-by-prefix` intercepts all comments and stops the
       processing of any beginning with `HUBOT_IGNORE_PREFIX`.
    2. `hubot-room-select` intercepts all comments and stops any that are
       not from `HUBOT_MONITOR_ROOM` and `HUBOT_NAME` (this module seems to
       have two purposes because it was coded to have monitor off|me|all, but
       our use case configures it into one monitor me).
3. Listen
    1. `hubot-gitter-echo` hears all comments and echos them to
       `HUBOT_GITTER_ROOM` using `HUBOT_GITTER_API_TOKEN`.
    2. `hubot-rules` listens for "what are the three rules" and echoes Asimov's
       three rules. In the project because it is a good debug script.
