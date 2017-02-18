Instructions:

download heroku belt from https://cli-assets.heroku.com/branches/stable/heroku-windows-amd64.exe
download nodejs
download git

heroku login
git clone https://github.com/heroku/node-js-getting-started.git
cd node-js-getting-started

heroku create <nodejs app name>
git push heroku master
heroku ps:scale web=1

**run cmd as administrator**
npm install npm --global
npm init
npm install express request body-parser --save

**change index.js codes**
var express = require('express')
var bodyParser = require('body-parser')
var request = require('request')
var app = express()

app.set('port', (process.env.PORT || 5000))

// Process application/x-www-form-urlencoded
app.use(bodyParser.urlencoded({extended: false}))

// Process application/json
app.use(bodyParser.json())

// Index route
app.get('/', function (req, res) {
    res.send('Hello world, I am a chat bot')
})

// for Facebook verification
app.get('/webhook/', function (req, res) {
    if (req.query['hub.verify_token'] === 'my_voice_is_my_password_verify_me') { 
        res.send(req.query['hub.challenge'])
    }
    res.send('Error, wrong token')
})

// Spin up the server
app.listen(app.get('port'), function() {
    console.log('running on port', app.get('port'))
})

var token = "EAAD4Oiv9NJgBAIjzA6vXP8dDAMc6PLrv0mVZBHXmW76qHh4PmmwKCCgvJf3Es2RKAUBOfzX2XvZAsnRB3R53hUYQ4FngbEyjXprOgZArCvQJ6NNZAxbL0KgLjZCMChWMdITnlbly4jPwsdTAbXWTB6ZCmlZCU0I54mAvi8m6cp01AZDZD";

app.post('/webhook/', function (req, res) {
    messaging_events = req.body.entry[0].messaging
    for (i = 0; i < messaging_events.length; i++) {
        event = req.body.entry[0].messaging[i]
        sender = event.sender.id
		/*
        if (event.message && event.message.text) {
            text = event.message.text            
			sendGenericMessage(sender)
			continue                        
        }
        if (event.postback) {
            text = event.postback["payload"]
            sendTextMessage(sender, text, token)
            continue
        }
		*/
		var returnText = "";
		var randomInt = 1;
		
		if (event.message || event.message.text) {
			text = event.message.text.toLowerCase();
			
			// greetings
			if(text.indexOf("hi") != -1 || text.indexOf("hello") != -1 || text.indexOf("hey") != -1) {
				returnText = "Hello! How're you feeling today?";
			} else if(text.indexOf("help") != -1 || text.indexOf("bad day") != -1) {
				returnText = "What happened? Care to share?";
			} else if(text.indexOf("thank") != -1) {
				returnText = "Hey no worries! Just talk to me when you feel like doing so.";
			} else {
				randomInt = Math.floor((Math.random() * 6) + 1); // random int between numbers inclusive
			
				if(randomInt == 1) {
					returnText = "Sometimes things can be really hard. You can even feel like you're at the bottom and no one is there or able to pull you out of that dark place. Don't give up however. Remind yourself that pain is only temporal.";
				} else if(randomInt == 2) {
					returnText = "If a certain someone or a certain something is causing you grief or pain, tell yourself that your life has so many other aspects worth living for. You've only one life, but one life can make a difference. Don't waste it alrights?";
				} else if(randomInt == 3) {
					returnText = "It's ok to feel sad. Cry it out if you've to. You'll feel better. A battle is always not without losses and life is like a battle. However, don't give up and don't give in regardless because this will make previous losses meaningless. Continue to live. It's painful and the pain may never completely disappear, but along the way you'll find others to hold your hand and accompany you.";
				} else if(randomInt == 4) {
					returnText = "There're times that I feel overwhelmed too and I question myself what did I do to deserve this. Still, it's times like these that I remind myself I'm mere mortal. I'm born into this world with nothing but a life. I've gained things along the way, but I certainly haven't truly lost anything. My basic responsibility is to continue to live. Everything else is either a bonus or an obstacle.";
				} else if(randomInt == 5) {
					returnText = "Life can seem unpleasant at the moment but it'll get better and those are not mere empty words. I used to think life was nothing but pain but I persisted on, refusing to bow down to the tragic circumstances surrounding me. While I was stepping all over those painful events I eventually saw that I might have been focusing way too much on the negative aspects of my life. There's still that speck of positive thing in my life worth living for. Even if there isn't, I'll create one! So please, hang onto your own life too.";
				} else if(randomInt == 6) {
					returnText = "If you're feeling sad or lonely, don't be alrights. Problems are usually problems unless you view them as such. Continue chatting with us and check the following out if you have the time to:";					
				}			
			}
			
			sendTextMessage(sender, returnText, token);	
			if(randomInt == 6) {
				sendGenericMessage(sender);				
			}			
		}
    }
    res.sendStatus(200)
})

function sendTextMessage(sender, text) {
    messageData = {
        text:text
    }
    request({
        url: 'https://graph.facebook.com/v2.6/me/messages',
        qs: {access_token:token},
        method: 'POST',
        json: {
            recipient: {id:sender},
            message: messageData,
        }
    }, function(error, response, body) {
        if (error) {
            console.log('Error sending messages: ', error)
        } else if (response.body.error) {
            console.log('Error: ', response.body.error)
        }
    })
}


function sendGenericMessage(sender) {
    /*
    messageData = {
        "attachment": {
            "type": "template",
            "payload": {
                "template_type": "generic",
                "elements": [{
                    "title": "Ai Chat Bot Communities",
                    "subtitle": "Communities to Follow",
                    "image_url": "http://1u88jj3r4db2x4txp44yqfj1.wpengine.netdna-cdn.com/wp-content/uploads/2016/04/chatbot-930x659.jpg",
                    "buttons": [{
                        "type": "web_url",
                        "url": "https://www.facebook.com/groups/aichatbots/",
                        "title": "FB Chatbot Group"
                    }, {
                        "type": "web_url",
                        "url": "https://www.reddit.com/r/Chat_Bots/",
                        "title": "Chatbots on Reddit"
                    },{
                        "type": "web_url",
                        "url": "https://twitter.com/aichatbots",
                        "title": "Chatbots on Twitter"
                    }],
                }, {
                    "title": "Chatbots FAQ",
                    "subtitle": "Aking the Deep Questions",
                    "image_url": "https://tctechcrunch2011.files.wordpress.com/2016/04/facebook-chatbots.png?w=738",
                    "buttons": [{
                        "type": "postback",
                        "title": "What's the benefit?",
                        "payload": "Chatbots make content interactive instead of static",
                    },{
                        "type": "postback",
                        "title": "What can Chatbots do",
                        "payload": "One day Chatbots will control the Internet of Things! You will be able to control your homes temperature with a text",
                    }, {
                        "type": "postback",
                        "title": "The Future",
                        "payload": "Chatbots are fun! One day your BFF might be a Chatbot",
                    }],
                },  {
                    "title": "Learning More",
                    "subtitle": "Aking the Deep Questions",
                    "image_url": "http://www.brandknewmag.com/wp-content/uploads/2015/12/cortana.jpg",
                    "buttons": [{
                        "type": "postback",
                        "title": "AIML",
                        "payload": "Checkout Artificial Intelligence Mark Up Language. Its easier than you think!",
                    },{
                        "type": "postback",
                        "title": "Machine Learning",
                        "payload": "Use python to teach your maching in 16D space in 15min",
                    }, {
                        "type": "postback",
                        "title": "Communities",
                        "payload": "Online communities & Meetups are the best way to stay ahead of the curve!",
                    }],
                }]  
            } 
        }
    }
	*/
	messageData = {
        "attachment": {
            "type": "template",
            "payload": {
                "template_type": "generic",
                "elements": [{
                    "title": "There is help. Don't cry alone.",
                    "subtitle": "(A great source of information)",
                    "image_url": "https://sos.org.sg/images/sos/SOSLogo_2015.jpg",
                    "buttons": [{
                        "type": "web_url",
                        "url": "https://sos.org.sg/",
                        "title": "Samaritans of Singapore"
                    }],
                }]  
            } 
        }
    }
    request({
        url: 'https://graph.facebook.com/v2.6/me/messages',
        qs: {access_token:token},
        method: 'POST',
        json: {
            recipient: {id:sender},
            message: messageData,
        }
    }, function(error, response, body) {
        if (error) {
            console.log('Error sending messages: ', error)
        } else if (response.body.error) {
            console.log('Error: ', response.body.error)
        }
    })
}

(open git gui to commit)
git push heroku master

(go FB page)
https://developers.facebook.com/apps/

(go to Messenger tab then click Setup Webhook)
(put in the URL of your Heroku server and a token --> "my_voice_is_my_password_verify_me")
(check boxes:
message_deliveries
messages
messaging_options
messaging_postbacks)

(create fb page to generate token)
(save this token and add in the token in the index.js file)
(deploy to heroku again)

paste curl-7.52.1-win32-mingw bin folder path in PATH environment variables
**download pem file at https://curl.haxx.se/ca/cacert.pem and paste in curl directory**
curl -X POST "https://graph.facebook.com/v2.6/me/subscribed_apps?access_token=<PAGE_ACCESS_TOKEN>"
**{"success":true}**

(go fb page to start chatting with chatbot)

CREDITS TO: https://chatbotsmagazine.com/have-15-minutes-create-your-own-facebook-messenger-bot-481a7db54892#.vrmreovg3
