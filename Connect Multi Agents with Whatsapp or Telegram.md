# WHATSAPP

after connecting the whatsapp channel the default should be all messages to main agent.

BUT sometimes we want our OPENCLAW INSTANCE to be connected to device X with number Y, and let multiple people to chat from their devices with number Y, each talking to another agent.

that is why we need bindings like that. note that in this example agent `wa-listener-general-bot` is a default bot to accept all other messages, but is not nessecary

```
  "bindings": [
    {
      "agentId": "main",
      "match": {
        "channel": "whatsapp",
        "peer": {
          "kind": "direct",
          "id": "+9725545451"
        }
      }
    },
    {
      "agentId": "anotherbot",
      "match": {
        "channel": "whatsapp",
        "peer": {
          "kind": "direct",
          "id": "+97295495491"
        }
      }
    },
    {
      "agentId": "wa-listener-general-bot",
      "match": {
        "channel": "whatsapp",
        "accountId": "*"
      }
    }
  ],
```



# Telegram

**per agent**


Log into Telegram on Phone 1, find @BotFather, run /newbot, and copy Token 1.

will be like "8...01:AAG....s8_vKY"

```
// define bot in openclaw settings
openclaw config set channels.telegram.accounts.my-agent-bot.botToken "TOKEN"
// restart gateway for changes to take affect
openclaw gateway restart
// bind THAT telegram bot to THIS agent 
openclaw agents bind --agent my-agent --bind telegram:my-agent-bot
// say "hi" to bot and you will get the full cli command like this
openclaw pairing approve telegram CODE_FROM_PHONE_1
```

my-agent and my-agent-bot are free strings names.

bindings for multi telegram agents:
```
    "channels": {
        "telegram": {
            "accounts": {
                "my_telegram_agent_1_bot": {
                    "botToken": "1234:abcd"
                },
                "my_telegram_agent_2_bot": {
                    "botToken": "12345:abcde"
                }
            }
        }
    },
    "bindings": [
        {
            "type": "route",
            "agentId": "my-agent-1",
            "match": {
                "channel": "telegram",
                "accountId": "my_telegram_agent_1_bot"
            }
        },
        {
            "type": "route",
            "agentId": "my-agent-1",
            "match": {
                "channel": "telegram",
                "accountId": "my_telegram_agent_2_bot"
            }
        }
    ],
```


