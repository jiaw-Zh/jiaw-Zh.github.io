---
layout: post
title:  "为 openclaw 每个频道创建独立工作区和agent调用"
date:   2026-03-11T00:04:00+08:00
categories: ['AI',openclaw]
tags:  ['2026']
toc: false
---

### 配置 openclaw 每个channel使用不同的agent和工作区，实现一个实例多个agent


官方文档：https://docs.openclaw.ai/zh-CN/concepts/multi-agent

### 这里以QQ、飞书、telegram三个渠道为例

首先在命令行执行创建agentid和工作区的命令

```bash
# agentId:qq  工作目录：~/.openclaw/workspaces/qq
openclaw agents add qq --workspace ~/.openclaw/workspaces/qq
# agentId:feishu  工作目录：~/.openclaw/workspaces/feishu
openclaw agents add feishu --workspace ~/.openclaw/workspaces/feishu
```

然后开始修改配置


```json
{
  "models": { //这里基本上不用动
    "mode": "replace",
    "providers": {
      "apiprovider": {
        "baseUrl": "https://api.example.ai/v1",
        "apiKey": "sk-",
        "auth": "api-key",
        "api": "openai-completions",
        "authHeader": true,
        "models": [
          {
            "id": "kimi-k2.5",
            "name": "kimi-k2.5",
            "api": "openai-completions",
            "reasoning": false
          },
          {
            "id": "glm-4.7",
            "name": "glm-4.7",
            "api": "openai-completions",
            "reasoning": false
          },
          {
            "id": "minimax-m2.5",
            "name": "minimax-m2.5",
            "api": "openai-completions",
            "reasoning": false
          }
        ]
      }
    }
  },
  "agents": {
    "list": [ //执行了命令行这里就会自动写好
      { // 只需要这几配置项就够了，其他可以删了
        "id": "feishu",
        "name": "飞书",
        "workspace": "/root/.openclaw/workspaces/feishu",
        "model": {
          "primary": "apiprovider/kimi-k2.5"
        }
      },
      {
        "id": "qqbot",
        "name": "qq机器人",
        "workspace": "/root/.openclaw/workspaces/qq",
        "model": {
          "primary": "apiprovider/glm-4.7"
        }
      },
      {
        "id": "telegram",
        "name": "电报",
        "workspace": "/root/.openclaw/workspace",
        "model": {
          "primary": "apiprovider/minimax-m2.5"
        }
      }
    ]
  },
  "bindings": [ // 这里是关键，一定要看准
    {
      "agentId": "feishu",
      "match": {
        "channel": "feishu"
      }
    },
    {
      "agentId": "qqbot",
      "match": {
        "channel": "qqbot"
      }
    },
    {
      "agentId": "telegram",
      "match": {
        "channel": "telegram"
      }
    }
  ],
  "channels": {
    "telegram": {
      "enabled": true,
      "dmPolicy": "pairing",
      "botToken": "",
      },
      "groupPolicy": "open",
      "streaming": "partial"
    },
    "feishu": {
      "enabled": true,
      "appId": "cli_a93afb7d24f89cb1",
      "appSecret": "",
      "connectionMode": "websocket",
      "domain": "feishu",
      "groupPolicy": "open",
      "streaming": true
    },
    "qqbot": {
      "enabled": true,
      "appId": "",
      "clientSecret": ""
    }
  },
  "plugins": {
    "allow": [
      "feishu-openclaw-plugin",
      "telegram",
      "openclaw-qqbot"
    ],
    "entries": {
      "openclaw-qqbot": {
        "enabled": true
      },
      "feishu-openclaw-plugin": {
        "enabled": true
      },
      "feishu": { // 如果使用了飞书官方的插件，一定要禁用openclaw的，同时将这个放在最后
        "enabled": false
      }
  }
}

```


就到这里，自己能看懂就行~