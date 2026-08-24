# Managing multiple agents 
wheather this is for inner multi agents of for actual humans. this guide will teach you to defire multiple agents in Whatsapp or Telegram (guide for Discord exists in Learn 02). for other platform the agent will help you. 

Whatsapp is annoying and best practice is to buy 1 device for the OC and use that device to message with consumers, and thats the example, and best practice there is to do 1 actual message from actual device to anyone.

telegram is very bot friendly and best option.

## last part will be about sandboxing



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



# Sandboxing

sandboxing in openclaw is basically simple and for any extra usage or tool bit complex.

the basic is basic, just use `agents.list[1].sandbox` like this

```
"agents": {
    "defaults": { ... },
    "list":[
        "id": "test-ariel",
        "name": "test-ariel",
        "workspace": "/root/.openclaw/workspace-test-ariel",
        "agentDir": "/root/.openclaw/agents/test-ariel/agent",
        "tools": {
            "profile": "coding",
            "deny": [
                "gateway",
                "nodes",
                "terminal",
                "portal",
                "dashboard"
            ]
        },
        "sandbox": {
            "mode": "all",
            "backend": "docker",
            "scope": "agent",
            "workspaceAccess": "rw",
            "docker": {
                "network": "bridge",
                "readOnlyRoot": true,
                "capDrop": [
                    "ALL"
                ]
            }
        }
    ]
}
```

let go over every line there, and lets start with `sandbox`
1. `mode`, is basically th on/off switch, with on being actually `all` (=sandbox all sessions)
2. `backend`, actually not really needed and `docker` is the default, means a docker is created for your agent to go wild, instead of toucing your actual machine
3. `scope`, again default is `agent` which means 1 docker for all agent's sessions. can be `shared` for all agents 1 docker, or the other extreme `session` for having 1 docker per session.
4. `workspaceAccess` means can the container access (mount) the workspace with all the agents files. you can have it `rw` for full mount, or `ro` for read only, meaning you have to edit those files somehow, or `none` which means the files are to be created in the docker and wont sustain long run
5. `docker.network` allows the docker to communicate with the host, like for `git` or `pip` or other custom API's you create for the agents. needs `gateway.bind` to be `loopback`. other options are `none` to isolate or `host` to be as host, but then its kinda open so why even sandbox?
6. `docker.readOnlyRoot` as `true` so agent can change anything in root other than temp folders and mounter workspace. can use `docker.setupCommand` for advanced
7. `"capDrop": [ "ALL" ]` drops the linux admin privliges.

truth be told can be just
```
    "sandbox": {
        "mode": "all",
        "workspaceAccess": "rw",
        "docker": {
            "network": "bridge"
        }
    }
```


so what the agent can do?

basically if you drop the `tools.profile` to `messaging` only, personally i am not sure why you need a sandbox, the agent already cant do anything.

but if you put it as `coding` or above (`full`) that is the point your agent can actually use real stuff and can damage the host, and you want to defend you host from the rushing agent and human.

this is where `tools.deny` comes in, and that is just an example list I personally selects for my clients.

## but about adding tools...

for example i like to add all this
```
  "alsoAllow": [
      "group:web",
      "group:memory",
      "group:agents",
      "group:media",
      "group:plugins",
      "cron",
      "heartbeat_respond",
      "screen",
      "browser",
      "canvas"
  ],
```
so you actually need to allow stuff from multiple gateways in the settings

the `agents.defaults` needs the 2 later definitions so that sandboxes agents can "see" other agents in the `sessions_` tools or `agents_list`
```
"agents": {
    "defaults": {
        "model": { "primary": "ollama/kimi-k2.7-code:cloud" },
        "workspace": "/root/.openclaw/workspace",
        "sandbox": {
            "sessionToolsVisibility": "all"
        },
        "subagents": { "allowAgents": ["*"] }
```

but also all that must be also in the `tools` section AND the allowAlso TWICE 

```
    "tools": {
        "web": { ... },
        "sessions": {
            "visibility": "all"
        },
        "agentToAgent": {
            "enabled": true
        },
        "alsoAllow": [
            "group:agents",
            ...
        ],
        "sandbox": {
            "tools": {
                "alsoAllow": [
                    "group:agents",
                    ...
                ]
            }
        }
    },
```

so for most tools 2 layers of gatekeeping, and for "seeing" other agents 4.



