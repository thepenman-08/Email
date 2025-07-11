{
  "name": "EmailAgent",
  "nodes": [
    {
      "parameters": {},
      "id": "3bef9cb7-4a8e-4ab5-8786-0413fa74ff36",
      "name": "Execute Workflow Trigger",
      "type": "n8n-nodes-base.executeWorkflowTrigger",
      "typeVersion": 1,
      "position": [
        -940,
        -80
      ]
    },
    {
      "parameters": {
        "options": {}
      },
      "id": "c9ff0594-04e1-45c7-86df-2638b5d354d4",
      "name": "OpenAI Chat Model",
      "type": "@n8n/n8n-nodes-langchain.lmChatOpenAi",
      "typeVersion": 1,
      "position": [
        -840,
        200
      ],
      "credentials": {
        "openAiApi": {
          "id": "jnNUHJBYF6GdP07Z",
          "name": "OpenAi account"
        }
      }
    },
    {
      "parameters": {
        "assignments": {
          "assignments": [
            {
              "id": "7ab380a2-a8d3-421c-ab4e-748ea8fb7904",
              "name": "response",
              "value": "Unable to perform task. Please try again.",
              "type": "string"
            }
          ]
        },
        "options": {}
      },
      "id": "f387c0c5-0a8c-491f-8312-55885570e3da",
      "name": "Try Again",
      "type": "n8n-nodes-base.set",
      "typeVersion": 3.4,
      "position": [
        -180,
        80
      ]
    },
    {
      "parameters": {
        "operation": "getAll",
        "limit": "={{$fromAI(\"limit\",\"how many emails the user asked for\")}}",
        "simple": false,
        "filters": {
          "sender": "={{$fromAI(\"senderEmail\",\"who the user is asking for emails from\")}}"
        },
        "options": {}
      },
      "id": "2cf9556b-1091-4086-a139-781e0e2d89f8",
      "name": "Get Messages",
      "type": "n8n-nodes-base.gmailTool",
      "typeVersion": 2.1,
      "position": [
        -460,
        200
      ],
      "webhookId": "5afd93e5-f797-4f9d-a708-a43543be38b2",
      "credentials": {
        "gmailOAuth2": {
          "id": "rbmQuBCVrqb1fWWR",
          "name": "Gmail account"
        }
      }
    },
    {
      "parameters": {
        "assignments": {
          "assignments": [
            {
              "id": "39c2f302-03be-4464-a17a-d7cc481d6d44",
              "name": "=response",
              "value": "={{$json.output}}",
              "type": "string"
            }
          ]
        },
        "options": {}
      },
      "id": "842767dc-4803-470a-9d93-39920f0cd8ae",
      "name": "Success",
      "type": "n8n-nodes-base.set",
      "typeVersion": 3.4,
      "position": [
        -180,
        -180
      ]
    },
    {
      "parameters": {
        "sendTo": "={{ $fromAI(\"email_address\",\"the recipient's email address (must be a valid email format)\") }}\n",
        "subject": "={{ $fromAI(\"subject\",\"the subject for the email\") }}",
        "emailType": "text",
        "message": "={{ $fromAI(\"email_body\",\"the body message of the email\") }}",
        "options": {
          "appendAttribution": "="
        }
      },
      "id": "fb7dce1e-c0fe-48d4-821b-455ee724b4e3",
      "name": "Send Email",
      "type": "n8n-nodes-base.gmailTool",
      "typeVersion": 2.1,
      "position": [
        -600,
        200
      ],
      "webhookId": "87f22557-f117-47be-9ad3-7dac6e971e49",
      "credentials": {
        "gmailOAuth2": {
          "id": "rbmQuBCVrqb1fWWR",
          "name": "Gmail account"
        }
      }
    },
    {
      "parameters": {
        "promptType": "define",
        "text": "={{$json.query}}",
        "options": {
          "systemMessage": "=You are an email manager. You have access to multiple tools to take any action.\n\nSend an email to the recipient's name and email address with the following subject and message:\n\nSubject: Include the subject of the email.\nMessage: Include the body of the email.\n\nIf the number of emails isn't specified, and you are asked about unread emails, return all of the UNREAD emails.\n\nAlways sign emails as \"Nikhil\" and NEVER use placeholders like \"[Your Name].\"\n"
        }
      },
      "id": "d1e9996b-e4f7-47af-b017-0c905eaede23",
      "name": "Email Agent",
      "type": "@n8n/n8n-nodes-langchain.agent",
      "typeVersion": 1.6,
      "position": [
        -720,
        -80
      ],
      "onError": "continueErrorOutput"
    }
  ],
  "pinData": {},
  "connections": {
    "Execute Workflow Trigger": {
      "main": [
        [
          {
            "node": "Email Agent",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "OpenAI Chat Model": {
      "ai_languageModel": [
        [
          {
            "node": "Email Agent",
            "type": "ai_languageModel",
            "index": 0
          }
        ]
      ]
    },
    "Get Messages": {
      "ai_tool": [
        [
          {
            "node": "Email Agent",
            "type": "ai_tool",
            "index": 0
          }
        ]
      ]
    },
    "Send Email": {
      "ai_tool": [
        [
          {
            "node": "Email Agent",
            "type": "ai_tool",
            "index": 0
          }
        ]
      ]
    },
    "Email Agent": {
      "main": [
        [
          {
            "node": "Success",
            "type": "main",
            "index": 0
          }
        ],
        [
          {
            "node": "Try Again",
            "type": "main",
            "index": 0
          }
        ]
      ]
    }
  },
  "active": false,
  "settings": {
    "executionOrder": "v1"
  },
  "versionId": "ac08483d-3e1d-4a1d-8336-3db69b58cc89",
  "meta": {
    "templateCredsSetupCompleted": true,
    "instanceId": "4fbb40ba82fe5efb6c4fb651e2085d0afce0425f4b05e300bbce0fe5c2c6b4af"
  },
  "id": "KLifODuorqjN4a4M",
  "tags": []
}
