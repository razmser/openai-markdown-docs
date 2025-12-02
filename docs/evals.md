# Evals

Create, manage, and run evals in the OpenAI platform. Related guide: [Evals](https://platform.openai.com/docs/guides/evals)

## Create eval

Create the structure of an evaluation that can be used to test a model's performance. An evaluation is a set of testing criteria and the config for a data source, which dictates the schema of the data used in the evaluation. After creating an evaluation, you can run it on different models and model parameters. We support several types of graders and datasources. For more information, see the [Evals guide](https://platform.openai.com/docs/guides/evals).

### Example request

```bash
curl https://api.openai.com/v1/evals \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "name": "Sentiment",
        "data_source_config": {
          "type": "stored_completions",
          "metadata": {
              "usecase": "chatbot"
          }
        },
        "testing_criteria": [
          {
            "type": "label_model",
            "model": "o3-mini",
            "input": [
              {
                "role": "developer",
                "content": "Classify the sentiment of the following statement as one of 'positive', 'neutral', or 'negative'"
              },
              {
                "role": "user",
                "content": "Statement: {{item.input}}"
              }
            ],
            "passing_labels": [
              "positive"
            ],
            "labels": [
              "positive",
              "neutral",
              "negative"
            ],
            "name": "Example label grader"
          }
        ]
      }'
```

### Response

```json
{
  "object": "eval",
  "id": "eval_67b7fa9a81a88190ab4aa417e397ea21",
  "data_source_config": {
    "type": "stored_completions",
    "metadata": {
      "usecase": "chatbot"
    },
    "schema": {
      "type": "object",
      "properties": {
        "item": {
          "type": "object"
        },
        "sample": {
          "type": "object"
        }
      },
      "required": [
        "item",
        "sample"
      ]
  },
  "testing_criteria": [
    {
      "name": "Example label grader",
      "type": "label_model",
      "model": "o3-mini",
      "input": [
        {
          "type": "message",
          "role": "developer",
          "content": {
            "type": "input_text",
            "text": "Classify the sentiment of the following statement as one of positive, neutral, or negative"
          }
        },
        {
          "type": "message",
          "role": "user",
          "content": {
            "type": "input_text",
            "text": "Statement: {{item.input}}"
          }
        }
      ],
      "passing_labels": [
        "positive"
      ],
      "labels": [
        "positive",
        "neutral",
        "negative"
      ]
    }
  ],
  "name": "Sentiment",
  "created_at": 1740110490,
  "metadata": {
    "description": "An eval for sentiment analysis"
  }
}
```

## Get an eval

Get an evaluation by ID.

### Example request

```bash
curl https://api.openai.com/v1/evals/eval_67abd54d9b0081909a86353f6fb9317a \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json"
```

### Response

```json
{
  "object": "eval",
  "id": "eval_67abd54d9b0081909a86353f6fb9317a",
  "data_source_config": {
    "type": "custom",
    "schema": {
      "type": "object",
      "properties": {
        "item": {
          "type": "object",
          "properties": {
            "input": {
              "type": "string"
            },
            "ground_truth": {
              "type": "string"
            }
          },
          "required": [
            "input",
            "ground_truth"
          ]
        }
      },
      "required": [
        "item"
      ]
    }
  },
  "testing_criteria": [
    {
      "name": "String check",
      "id": "String check-2eaf2d8d-d649-4335-8148-9535a7ca73c2",
      "type": "string_check",
      "input": "{{item.input}}",
      "reference": "{{item.ground_truth}}",
      "operation": "eq"
    }
  ],
  "name": "External Data Eval",
  "created_at": 1739314509,
  "metadata": {},
}
```

## Update an eval

Update certain properties of an evaluation.

### Example request

```bash
curl https://api.openai.com/v1/evals/eval_67abd54d9b0081909a86353f6fb9317a \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"name": "Updated Eval", "metadata": {"description": "Updated description"}}'
```

### Response

```json
{
  "object": "eval",
  "id": "eval_67abd54d9b0081909a86353f6fb9317a",
  "data_source_config": {
    "type": "custom",
    "schema": {
      "type": "object",
      "properties": {
        "item": {
          "type": "object",
          "properties": {
            "input": {
              "type": "string"
            },
            "ground_truth": {
              "type": "string"
            }
          },
          "required": [
            "input",
            "ground_truth"
          ]
        }
      },
      "required": [
        "item"
      ]
    }
  },
  "testing_criteria": [
    {
      "name": "String check",
      "id": "String check-2eaf2d8d-d649-4335-8148-9535a7ca73c2",
      "type": "string_check",
      "input": "{{item.input}}",
      "reference": "{{item.ground_truth}}",
      "operation": "eq"
    }
  ],
  "name": "Updated Eval",
  "created_at": 1739314509,
  "metadata": {"description": "Updated description"},
}
```

## Delete an eval

Delete an evaluation.

### Example request

```bash
curl https://api.openai.com/v1/evals/eval_abc123 \
  -X DELETE \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

### Response

```json
{
  "object": "eval.deleted",
  "deleted": true,
  "eval_id": "eval_abc123"
}
```

## List evals

List evaluations for a project.

### Example request

```bash
curl https://api.openai.com/v1/evals?limit=1 \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json"
```

### Response

```json
{
  "object": "list",
  "data": [
    {
      "id": "eval_67abd54d9b0081909a86353f6fb9317a",
      "object": "eval",
      "data_source_config": {
        "type": "stored_completions",
        "metadata": {
          "usecase": "push_notifications_summarizer"
        },
        "schema": {
          "type": "object",
          "properties": {
            "item": {
              "type": "object"
            },
            "sample": {
              "type": "object"
            }
          },
          "required": [
            "item",
            "sample"
          ]
        }
      },
      "testing_criteria": [
        {
          "name": "Push Notification Summary Grader",
          "id": "Push Notification Summary Grader-9b876f24-4762-4be9-aff4-db7a9b31c673",
          "type": "label_model",
          "model": "o3-mini",
          "input": [
            {
              "type": "message",
              "role": "developer",
              "content": {
                "type": "input_text",
                "text": "\nLabel the following push notification summary as either correct or incorrect.\nThe push notification and the summary will be provided below.\nA good push notificiation summary is concise and snappy.\nIf it is good, then label it as correct, if not, then incorrect.\n"
              }
            },
            {
              "type": "message",
              "role": "user",
              "content": {
                "type": "input_text",
                "text": "\nPush notifications: {{item.input}}\nSummary: {{sample.output_text}}\n"
              }
            }
          ],
          "passing_labels": [
            "correct"
          ],
          "labels": [
            "correct",
            "incorrect"
          ],
          "sampling_params": null
        }
      ],
      "name": "Push Notification Summary Grader",
      "created_at": 1739314509,
      "metadata": {
        "description": "A stored completions eval for push notification summaries"
      }
    }
  ],
  "first_id": "eval_67abd54d9b0081909a86353f6fb9317a",
  "last_id": "eval_67aa884cf6688190b58f657d4441c8b7",
  "has_more": true
}
```

## Get eval runs

Get a list of runs for an evaluation.

### Example request

```bash
curl https://api.openai.com/v1/evals/egroup_67abd54d9b0081909a86353f6fb9317a/runs \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json"
```

### Response

```json
{
  "object": "list",
  "data": [
    {
      "object": "eval.run",
      "id": "evalrun_67e0c7d31560819090d60c0780591042",
      "eval_id": "eval_67e0c726d560819083f19a957c4c640b",
      "report_url": "https://platform.openai.com/evaluations/eval_67e0c726d560819083f19a957c4c640b",
      "status": "completed",
      "model": "o3-mini",
      "name": "bulk_with_negative_examples_o3-mini",
      "created_at": 1742784467,
      "result_counts": {
        "total": 1,
        "errored": 0,
        "failed": 0,
        "passed": 1
      },
      "per_model_usage": [
        {
          "model_name": "o3-mini",
          "invocation_count": 1,
          "prompt_tokens": 563,
          "completion_tokens": 874,
          "total_tokens": 1437,
          "cached_tokens": 0
        }
      ],
      "per_testing_criteria_results": [
        {
          "testing_criteria": "Push Notification Summary Grader-1808cd0b-eeec-4e0b-a519-337e79f4f5d1",
          "passed": 1,
          "failed": 0
        }
      ],
      "data_source": {
        "type": "completions",
        "source": {
          "type": "file_content",
          "content": [
            {
              "item": {
                "notifications": "\n- New message from Sarah: \"Can you call me later?\"\n- Your package has been delivered!\n- Flash sale: 20% off electronics for the next 2 hours!\n"
              }
            }
          ]
        },
        "input_messages": {
          "type": "template",
          "template": [
            {
              "type": "message",
              "role": "developer",
              "content": {
                "type": "input_text",
                "text": "\n\n\n\nYou are a helpful assistant that takes in an array of push notifications and returns a collapsed summary of them.\nThe push notification will be provided as follows:\n<push_notifications>\n...notificationlist...\n</push_notifications>\n\nYou should return just the summary and nothing else.\n\n\nYou should return a summary that is concise and snappy.\n\n\nHere is an example of a good summary:\n<push_notifications>\n- Traffic alert: Accident reported on Main Street.- Package out for delivery: Expected by 5 PM.- New friend suggestion: Connect with Emma.\n</push_notifications>\n<summary>\nTraffic alert, package expected by 5pm, suggestion for new friend (Emily).\n</summary>\n\n\nHere is an example of a bad summary:\n<push_notifications>\n- Traffic alert: Accident reported on Main Street.- Package out for delivery: Expected by 5 PM.- New friend suggestion: Connect with Emma.\n</push_notifications>\n<summary>\nTraffic alert reported on main street. You have a package that will arrive by 5pm, Emily is a new friend suggested for you.\n</summary>\n"
              }
            },
            {
              "type": "message",
              "role": "user",
              "content": {
                "type": "input_text",
                "text": "<push_notifications>{{item.notifications}}</push_notifications>"
              }
            }
          ]
        },
        "model": "o3-mini",
        "sampling_params": null
      },
      "error": null,
      "metadata": {}
    }
  ],
  "first_id": "evalrun_67e0c7d31560819090d60c0780591042",
  "last_id": "evalrun_67e0c7d31560819090d60c0780591042",
  "has_more": true
}
```

## Get an eval run

Get an evaluation run by ID.

### Example request

```bash
curl https://api.openai.com/v1/evals/eval_67abd54d9b0081909a86353f6fb9317a/runs/evalrun_67abd54d60ec8190832b46859da808f7 \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json"
```

### Response

```json
{
  "object": "eval.run",
  "id": "evalrun_67abd54d60ec8190832b46859da808f7",
  "eval_id": "eval_67abd54d9b0081909a86353f6fb9317a",
  "report_url": "https://platform.openai.com/evaluations/eval_67abd54d9b0081909a86353f6fb9317a?run_id=evalrun_67abd54d60ec8190832b46859da808f7",
  "status": "queued",
  "model": "gpt-4o-mini",
  "name": "gpt-4o-mini",
  "created_at": 1743092069,
  "result_counts": {
    "total": 0,
    "errored": 0,
    "failed": 0,
    "passed": 0
  },
  "per_model_usage": null,
  "per_testing_criteria_results": null,
  "data_source": {
    "type": "completions",
    "source": {
      "type": "file_content",
      "content": [
        {
          "item": {
            "input": "Tech Company Launches Advanced Artificial Intelligence Platform",
            "ground_truth": "Technology"
          }
        },
        {
          "item": {
            "input": "Central Bank Increases Interest Rates Amid Inflation Concerns",
            "ground_truth": "Markets"
          }
        },
        {
          "item": {
            "input": "International Summit Addresses Climate Change Strategies",
            "ground_truth": "World"
          }
        },
        {
          "item": {
            "input": "Major Retailer Reports Record-Breaking Holiday Sales",
            "ground_truth": "Business"
          }
        },
        {
          "item": {
            "input": "National Team Qualifies for World Championship Finals",
            "ground_truth": "Sports"
          }
        },
        {
          "item": {
            "input": "Stock Markets Rally After Positive Economic Data Released",
            "ground_truth": "Markets"
          }
        },
        {
          "item": {
            "input": "Global Manufacturer Announces Merger with Competitor",
            "ground_truth": "Business"
          }
        },
        {
          "item": {
            "input": "Breakthrough in Renewable Energy Technology Unveiled",
            "ground_truth": "Technology"
          }
        },
        {
          "item": {
            "input": "World Leaders Sign Historic Climate Agreement",
            "ground_truth": "World"
          }
        },
        {
          "item": {
            "input": "Professional Athlete Sets New Record in Championship Event",
            "ground_truth": "Sports"
          }
        },
        {
          "item": {
            "input": "Financial Institutions Adapt to New Regulatory Requirements",
            "ground_truth": "Business"
          }
        },
        {
          "item": {
            "input": "Tech Conference Showcases Advances in Artificial Intelligence",
            "ground_truth": "Technology"
          }
        },
        {
          "item": {
            "input": "Global Markets Respond to Oil Price Fluctuations",
            "ground_truth": "Markets"
          }
        },
        {
          "item": {
            "input": "International Cooperation Strengthened Through New Treaty",
            "ground_truth": "World"
          }
        },
        {
          "item": {
            "input": "Sports League Announces Revised Schedule for Upcoming Season",
            "ground_truth": "Sports"
          }
        }
      ]
    },
    "input_messages": {
      "type": "template",
      "template": [
        {
          "type": "message",
          "role": "developer",
          "content": {
            "type": "input_text",
            "text": "Categorize a given news headline into one of the following topics: Technology, Markets, World, Business, or Sports.\n\n# Steps\n\n1. Analyze the content of the news headline to understand its primary focus.\n2. Extract the subject matter, identifying any key indicators or keywords.\n3. Use the identified indicators to determine the most suitable category out of the five options: Technology, Markets, World, Business, or Sports.\n4. Ensure only one category is selected per headline.\n\n# Output Format\n\nRespond with the chosen category as a single word. For instance: \"Technology\", \"Markets\", \"World\", \"Business\", or \"Sports\".\n\n# Examples\n\n**Input**: \"Apple Unveils New iPhone Model, Featuring Advanced AI Features\"  \n**Output**: \"Technology\"\n\n**Input**: \"Global Stocks Mixed as Investors Await Central Bank Decisions\"  \n**Output**: \"Markets\"\n\n**Input**: \"War in Ukraine: Latest Updates on Negotiation Status\"  \n**Output**: \"World\"\n\n**Input**: \"Microsoft in Talks to Acquire Gaming Company for $2 Billion\"  \n**Output**: \"Business\"\n\n**Input**: \"Manchester United Secures Win in Premier League Football Match\"  \n**Output**: \"Sports\" \n\n# Notes\n\n- If the headline appears to fit into more than one category, choose the most dominant theme.\n- Keywords or phrases such as \"stocks\", \"company acquisition\", \"match\", or technological brands can be good indicators for classification.\n"
          }
        },
        {
          "type": "message",
          "role": "user",
          "content": {
            "type": "input_text",
            "text": "{{item.input}}"
          }
        }
      ]
    },
    "model": "gpt-4o-mini",
    "sampling_params": {
      "seed": 42,
      "temperature": 1.0,
      "top_p": 1.0,
      "max_completions_tokens": 2048
    }
  },
  "error": null,
  "metadata": {}
}
```

## Create eval run

Kicks off a new run for a given evaluation, specifying the data source, and what model configuration to use to test. The datasource will be validated against the schema specified in the config of the evaluation.

### Example request

```bash
curl https://api.openai.com/v1/evals/eval_67e579652b548190aaa83ada4b125f47/runs \
  -X POST \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"name":"gpt-4o-mini","data_source":{"type":"completions","input_messages":{"type":"template","template":[{"role":"developer","content":"Categorize a given news headline into one of the following topics: Technology, Markets, World, Business, or Sports.\n\n# Steps\n\n1. Analyze the content of the news headline to understand its primary focus.\n2. Extract the subject matter, identifying any key indicators or keywords.\n3. Use the identified indicators to determine the most suitable category out of the five options: Technology, Markets, World, Business, or Sports.\n4. Ensure only one category is selected per headline.\n\n# Output Format\n\nRespond with the chosen category as a single word. For instance: \"Technology\", \"Markets\", \"World\", \"Business\", or \"Sports\".\n\n# Examples\n\n**Input**: \"Apple Unveils New iPhone Model, Featuring Advanced AI Features\"  \n**Output**: \"Technology\"\n\n**Input**: \"Global Stocks Mixed as Investors Await Central Bank Decisions\"  \n**Output**: \"Markets\"\n\n**Input**: \"War in Ukraine: Latest Updates on Negotiation Status\"  \n**Output**: \"World\"\n\n**Input**: \"Microsoft in Talks to Acquire Gaming Company for $2 Billion\"  \n**Output**: \"Business\"\n\n**Input**: \"Manchester United Secures Win in Premier League Football Match\"  \n**Output**: \"Sports\" \n\n# Notes\n\n- If the headline appears to fit into more than one category, choose the most dominant theme.\n- Keywords or phrases such as \"stocks\", \"company acquisition\", \"match\", or technological brands can be good indicators for classification.\n"} , {"role":"user","content":"{{item.input}}"}]} ,"sampling_params":{"temperature":1,"max_completions_tokens":2048,"top_p":1,"seed":42},"model":"gpt-4o-mini","source":{"type":"file_content","content":[{"item":{"input":"Tech Company Launches Advanced Artificial Intelligence Platform","ground_truth":"Technology"}}]}}'
```

### Response

```json
{
  "object": "eval.run",
  "id": "evalrun_67e57965b480819094274e3a32235e4c",
  "eval_id": "eval_67e579652b548190aaa83ada4b125f47",
  "report_url": "https://platform.openai.com/evaluations/eval_67e579652b548190aaa83ada4b125f47&run_id=evalrun_67e57965b480819094274e3a32235e4c",
  "status": "queued",
  "model": "gpt-4o-mini",
  "name": "gpt-4o-mini",
  "created_at": 1743092069,
  "result_counts": {
    "total": 0,
    "errored": 0,
    "failed": 0,
    "passed": 0
  },
  "per_model_usage": null,
  "per_testing_criteria_results": null,
  "data_source": {
    "type": "completions",
    "source": {
      "type": "file_content",
      "content": [
        {
          "item": {
            "input": "Tech Company Launches Advanced Artificial Intelligence Platform",
            "ground_truth": "Technology"
          }
        }
      ]
    },
    "input_messages": {
      "type": "template",
      "template": [
        {
          "type": "message",
          "role": "developer",
          "content": {
            "type": "input_text",
            "text": "Categorize a given news headline into one of the following topics: Technology, Markets, World, Business, or Sports.\n\n# Steps\n\n1. Analyze the content of the news headline to understand its primary focus.\n2. Extract the subject matter, identifying any key indicators or keywords.\n3. Use the identified indicators to determine the most suitable category out of the five options: Technology, Markets, World, Business, or Sports.\n4. Ensure only one category is selected per headline.\n\n# Output Format\n\nRespond with the chosen category as a single word. For instance: \"Technology\", \"Markets\", \"World\", \"Business\", or \"Sports\".\n\n# Examples\n\n**Input**: \"Apple Unveils New iPhone Model, Featuring Advanced AI Features\"  \n**Output**: \"Technology\"\n\n**Input**: \"Global Stocks Mixed as Investors Await Central Bank Decisions\"  \n**Output**: \"Markets\"\n\n**Input**: \"War in Ukraine: Latest Updates on Negotiation Status\"  \n**Output**: \"World\"\n\n**Input**: \"Microsoft in Talks to Acquire Gaming Company for $2 Billion\"  \n**Output**: \"Business\"\n\n**Input**: \"Manchester United Secures Win in Premier League Football Match\"  \n**Output**: \"Sports\" \n\n# Notes\n\n- If the headline appears to fit into more than one category, choose the most dominant theme.\n- Keywords or phrases such as \"stocks\", \"company acquisition\", \"match\", or technological brands can be good indicators for classification.\n"
          }
        },
        {
          "type": "message",
          "role": "user",
          "content": {
            "type": "input_text",
            "text": "{{item.input}}"
          }
        }
      ]
    },
    "model": "gpt-4o-mini",
    "sampling_params": {
      "seed": 42,
      "temperature": 1.0,
      "top_p": 1.0,
      "max_completions_tokens": 2048
    }
  },
  "error": null,
  "metadata": {}
}
```

## Cancel eval run

Cancel an ongoing evaluation run.

### Example request

```bash
curl https://api.openai.com/v1/evals/eval_67abd54d9b0081909a86353f6fb9317a/runs/evalrun_67abd54d60ec8190832b46859da808f7/cancel \
  -X POST \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json"
```

### Response

```json
{
  "object": "eval.run",
  "id": "evalrun_67abd54d60ec8190832b46859da808f7",
  "eval_id": "eval_67abd54d9b0081909a86353f6fb9317a",
  "report_url": "https://platform.openai.com/evaluations/eval_67abd54d9b0081909a86353f6fb9317a?run_id=evalrun_67abd54d60ec8190832b46859da808f7",
  "status": "canceled",
  "model": "gpt-4o-mini",
  "name": "gpt-4o-mini",
  "created_at": 1743092069,
  "result_counts": {
    "total": 0,
    "errored": 0,
    "failed": 0,
    "passed": 0
  },
  "per_model_usage": null,
  "per_testing_criteria_results": null,
  "data_source": {
    "type": "completions",
    "source": {
      "type": "file_content",
      "content": [
        {
          "item": {
            "input": "Tech Company Launches Advanced Artificial Intelligence Platform",
            "ground_truth": "Technology"
          }
        },
        {
          "item": {
            "input": "Central Bank Increases Interest Rates Amid Inflation Concerns",
            "ground_truth": "Markets"
          }
        },
        {
          "item": {
            "input": "International Summit Addresses Climate Change Strategies",
            "ground_truth": "World"
          }
        },
        {
          "item": {
            "input": "Major Retailer Reports Record-Breaking Holiday Sales",
            "ground_truth": "Business"
          }
        },
        {
          "item": {
            "input": "National Team Qualifies for World Championship Finals",
            "ground_truth": "Sports"
          }
        },
        {
          "item": {
            "input": "Stock Markets Rally After Positive Economic Data Released",
            "ground_truth": "Markets"
          }
        },
        {
          "item": {
            "input": "Global Manufacturer Announces Merger with Competitor",
            "ground_truth": "Business"
          }
        },
        {
          "item": {
            "input": "Breakthrough in Renewable Energy Technology Unveiled",
            "ground_truth": "Technology"
          }
        },
        {
          "item": {
            "input": "World Leaders Sign Historic Climate Agreement",
            "ground_truth": "World"
          }
        },
        {
          "item": {
            "input": "Professional Athlete Sets New Record in Championship Event",
            "ground_truth": "Sports"
          }
        },
        {
          "item": {
            "input": "Financial Institutions Adapt to New Regulatory Requirements",
            "ground_truth": "Business"
          }
        },
        {
          "item": {
            "input": "Tech Conference Showcases Advances in Artificial Intelligence",
            "ground_truth": "Technology"
          }
        },
        {
          "item": {
            "input": "Global Markets Respond to Oil Price Fluctuations",
            "ground_truth": "Markets"
          }
        },
        {
          "item": {
            "input": "International Cooperation Strengthened Through New Treaty",
            "ground_truth": "World"
          }
        },
        {
          "item": {
            "input": "Sports League Announces Revised Schedule for Upcoming Season",
            "ground_truth": "Sports"
          }
        }
      ]
    },
    "input_messages": {
      "type": "template",
      "template": [
        {
          "type": "message",
          "role": "developer",
          "content": {
            "type": "input_text",
            "text": "Categorize a given news headline into one of the following topics: Technology, Markets, World, Business, or Sports.\n\n# Steps\n\n1. Analyze the content of the news headline to understand its primary focus.\n2. Extract the subject matter, identifying any key indicators or keywords.\n3. Use the identified indicators to determine the most suitable category out of the five options: Technology, Markets, World, Business, or Sports.\n4. Ensure only one category is selected per headline.\n\n# Output Format\n\nRespond with the chosen category as a single word. For instance: \"Technology\", \"Markets\", \"World\", \"Business\", or \"Sports\".\n\n# Examples\n\n**Input**: \"Apple Unveils New iPhone Model, Featuring Advanced AI Features\"  \n**Output**: \"Technology\"\n\n**Input**: \"Global Stocks Mixed as Investors Await Central Bank Decisions\"  \n**Output**: \"Markets\"\n\n**Input**: \"War in Ukraine: Latest Updates on Negotiation Status\"  \n**Output**: \"World\"\n\n**Input**: \"Microsoft in Talks to Acquire Gaming Company for $2 Billion\"  \n**Output**: \"Business\"\n\n**Input**: \"Manchester United Secures Win in Premier League Football Match\"  \n**Output**: \"Sports\" \n\n# Notes\n\n- If the headline appears to fit into more than one category, choose the most dominant theme.\n- Keywords or phrases such as \"stocks\", \"company acquisition\", \"match\", or technological brands can be good indicators for classification.\n"
          }
        },
        {
          "type": "message",
          "role": "user",
          "content": {
            "type": "input_text",
            "text": "{{item.input}}"
          }
        }
      ]
    },
    "model": "gpt-4o-mini",
    "sampling_params": {
      "seed": 42,
      "temperature": 1.0,
      "top_p": 1.0,
      "max_completions_tokens": 2048
    }
  },
  "error": null,
  "metadata": {}
}
```

## Delete eval run

Delete an eval run.

### Example request

```bash
curl https://api.openai.com/v1/evals/eval_123abc/runs/evalrun_abc456 \
  -X DELETE \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json"
```

### Response

```json
{
  "object": "eval.run.deleted",
  "deleted": true,
  "run_id": "evalrun_abc456"
}
```

## Get an output item of an eval run

Get an evaluation run output item by ID.

### Example request

```bash
curl https://api.openai.com/v1/evals/eval_67abd54d9b0081909a86353f6fb9317a/runs/evalrun_67abd54d60ec8190832b46859da808f7/output_items/outputitem_67abd55eb6548190bb580745d5644a33 \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json"
```

### Response

```json
{
  "object": "eval.run.output_item",
  "id": "outputitem_67e5796c28e081909917bf79f6e6214d",
  "created_at": 1743092076,
  "run_id": "evalrun_67abd54d60ec8190832b46859da808f7",
  "eval_id": "eval_67abd54d9b0081909a86353f6fb9317a",
  "status": "pass",
  "datasource_item_id": 5,
  "datasource_item": {
    "input": "Stock Markets Rally After Positive Economic Data Released",
    "ground_truth": "Markets"
  },
  "results": [
    {
      "name": "String check-a2486074-d803-4445-b431-ad2262e85d47",
      "sample": null,
      "passed": true,
      "score": 1.0
    }
  ],
  "sample": {
    "input": [
      {
        "role": "developer",
        "content": "Categorize a given news headline into one of the following topics: Technology, Markets, World, Business, or Sports.\n\n# Steps\n\n1. Analyze the content of the news headline to understand its primary focus.\n2. Extract the subject matter, identifying any key indicators or keywords.\n3. Use the identified indicators to determine the most suitable category out of the five options: Technology, Markets, World, Business, or Sports.\n4. Ensure only one category is selected per headline.\n\n# Output Format\n\nRespond with the chosen category as a single word. For instance: \"Technology\", \"Markets\", \"World\", \"Business\", or \"Sports\".\n\n# Examples\n\n**Input**: \"Apple Unveils New iPhone Model, Featuring Advanced AI Features\"  \n**Output**: \"Technology\"\n\n**Input**: \"Global Stocks Mixed as Investors Await Central Bank Decisions\"  \n**Output**: \"Markets\"\n\n**Input**: \"War in Ukraine: Latest Updates on Negotiation Status\"  \n**Output**: \"World\"\n\n**Input**: \"Microsoft in Talks to Acquire Gaming Company for $2 Billion\"  \n**Output**: \"Business\"\n\n**Input**: \"Manchester United Secures Win in Premier League Football Match\"  \n**Output**: \"Sports\" \n\n# Notes\n\n- If the headline appears to fit into more than one category, choose the most dominant theme.\n- Keywords or phrases such as \"stocks\", \"company acquisition\", \"match\", or technological brands can be good indicators for classification.\n",
        "tool_call_id": null,
        "tool_calls": null,
        "function_call": null
      },
      {
        "role": "user",
        "content": "Stock Markets Rally After Positive Economic Data Released",
        "tool_call_id": null,
        "tool_calls": null,
        "function_call": null
      }
    ],
    "output": [
      {
        "role": "assistant",
        "content": "Markets",
        "tool_call_id": null,
        "tool_calls": null,
        "function_call": null
      }
    ],
    "finish_reason": "stop",
    "model": "gpt-4o-mini-2024-07-18",
    "usage": {
      "total_tokens": 325,
      "completion_tokens": 2,
      "prompt_tokens": 323,
      "cached_tokens": 0
    },
    "error": null,
    "temperature": 1.0,
    "max_completion_tokens": 2048,
    "top_p": 1.0,
    "seed": 42
  }
}
```

## Get eval run output items

Get a list of output items for an evaluation run.

### Example request

```bash
curl https://api.openai.com/v1/evals/egroup_67abd54d9b0081909a86353f6fb9317a/runs/erun_67abd54d60ec8190832b46859da808f7/output_items \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json"
```

### Response

```json
{
  "object": "list",
  "data": [
    {
      "object": "eval.run.output_item",
      "id": "outputitem_67e5796c28e081909917bf79f6e6214d",
      "created_at": 1743092076,
      "run_id": "evalrun_67abd54d60ec8190832b46859da808f7",
      "eval_id": "eval_67abd54d9b0081909a86353f6fb9317a",
      "status": "pass",
      "datasource_item_id": 5,
      "datasource_item": {
        "input": "Stock Markets Rally After Positive Economic Data Released",
        "ground_truth": "Markets"
      },
      "results": [
        {
          "name": "String check-a2486074-d803-4445-b431-ad2262e85d47",
          "sample": null,
          "passed": true,
          "score": 1.0
        }
      ],
      "sample": {
        "input": [
          {
            "role": "developer",
            "content": "Categorize a given news headline into one of the following topics: Technology, Markets, World, Business, or Sports.\n\n# Steps\n\n1. Analyze the content of the news headline to understand its primary focus.\n2. Extract the subject matter, identifying any key indicators or keywords.\n3. Use the identified indicators to determine the most suitable category out of the five options: Technology, Markets, World, Business, or Sports.\n4. Ensure only one category is selected per headline.\n\n# Output Format\n\nRespond with the chosen category as a single word. For instance: \"Technology\", \"Markets\", \"World\", \"Business\", or \"Sports\".\n\n# Examples\n\n**Input**: \"Apple Unveils New iPhone Model, Featuring Advanced AI Features\"  \n**Output**: \"Technology\"\n\n**Input**: \"Global Stocks Mixed as Investors Await Central Bank Decisions\"  \n**Output**: \"Markets\"\n\n**Input**: \"War in Ukraine: Latest Updates on Negotiation Status\"  \n**Output**: \"World\"\n\n**Input**: \"Microsoft in Talks to Acquire Gaming Company for $2 Billion\"  \n**Output**: \"Business\"\n\n**Input**: \"Manchester United Secures Win in Premier League Football Match\"  \n**Output**: \"Sports\" \n\n# Notes\n\n- If the headline appears to fit into more than one category, choose the most dominant theme.\n- Keywords or phrases such as \"stocks\", \"company acquisition\", \"match\", or technological brands can be good indicators for classification.\n",
            "tool_call_id": null,
            "tool_calls": null,
            "function_call": null
          },
          {
            "role": "user",
            "content": "Stock Markets Rally After Positive Economic Data Released",
            "tool_call_id": null,
            "tool_calls": null,
            "function_call": null
          }
        ],
        "output": [
          {
            "role": "assistant",
            "content": "Markets",
            "tool_call_id": null,
            "tool_calls": null,
            "function_call": null
          }
        ],
        "finish_reason": "stop",
        "model": "gpt-4o-mini-2024-07-18",
        "usage": {
          "total_tokens": 325,
          "completion_tokens": 2,
          "prompt_tokens": 323,
          "cached_tokens": 0
        },
        "error": null,
        "temperature": 1.0,
        "max_completion_tokens": 2048,
        "top_p": 1.0,
        "seed": 42
      }
    }
  ],
  "first_id": "outputitem_67e5796c28e081909917bf79f6e6214d",
  "last_id": "outputitem_67e5796c28e081909917bf79f6e6214d",
  "has_more": true
}
```

## The eval object

An Eval object with a data source config and testing criteria. An Eval represents a task to be done for your LLM integration. Like:

  * Improve the quality of my chatbot
  * See how well my chatbot handles customer support
  * Check if o4-mini is better at my usecase than gpt-4o

### Parameters

#### created_at - integer
The Unix timestamp (in seconds) for when the eval was created.

#### data_source_config - object
Configuration of data sources used in runs of the evaluation.

- **CustomDataSourceConfig - object**
  A CustomDataSourceConfig which specifies the schema of your `item` and optionally `sample` namespaces. The response schema defines the shape of the data that will be:

    * Used to define your testing criteria and
    * What data is required when creating a run

  - **schema - object**
    The json schema for the run data source items. Learn how to build JSON schemas [here](https://json-schema.org/).

  - **type - string**
    The type of data source. Always `custom`.

- **LogsDataSourceConfig - object**
  A LogsDataSourceConfig which specifies the metadata property of your logs query. This is usually metadata like `usecase=chatbot` or `prompt-version=v2`, etc. The schema returned by this data source config is used to defined what variables are available in your evals. `item` and `sample` are both defined when using this data source config.

  - **schema - object**
    The json schema for the run data source items. Learn how to build JSON schemas [here](https://json-schema.org/).

  - **type - string**
    The type of data source. Always `logs`.

  - **metadata - map**
    Set of 16 key-value pairs that can be attached to an object. This can be useful for storing additional information about the object in a structured format, and querying for objects via API or the dashboard.

    Keys are strings with a maximum length of 64 characters. Values are strings with a maximum length of 512 characters.

- **StoredCompletionsDataSourceConfig - object - Deprecated**
  Deprecated in favor of LogsDataSourceConfig.

  - **schema - object**
    The json schema for the run data source items. Learn how to build JSON schemas [here](https://json-schema.org/).

  - **type - string**
    The type of data source. Always `stored_completions`.

  - **metadata - map**
    Set of 16 key-value pairs that can be attached to an object. This can be useful for storing additional information about the object in a structured format, and querying for objects via API or the dashboard.

    Keys are strings with a maximum length of 64 characters. Values are strings with a maximum length of 512 characters.

#### id - string
Unique identifier for the evaluation.

#### metadata - map
Set of 16 key-value pairs that can be attached to an object. This can be useful for storing additional information about the object in a structured format, and querying for objects via API or the dashboard.

Keys are strings with a maximum length of 64 characters. Values are strings with a maximum length of 512 characters.

#### name - string
The name of the evaluation.

#### object - string
The object type.

#### testing_criteria - array
A list of testing criteria.

- **LabelModelGrader - object**
  A LabelModelGrader object which uses a model to assign labels to each item in the evaluation.

  - **input - array**
    Inputs to the model - can contain template strings.

    - **content - string / object / array**
      Inputs to the model - can contain template strings.

      - **Text input - string**
        A text input to the model.

      - **Input text - object**
        A text input to the model.

        - **text - string**
          The text input to the model.

        - **type - string**
          The type of the input item. Always `input_text`.

      - **Output text - object**
        A text output from the model.

        - **text - string**
          The text output from the model.

        - **type - string**
          The type of the output text. Always `output_text`.

      - **Input image - object**
        An image input to the model.

        - **image_url - string**
          The URL of the image input.

        - **type - string**
          The type of the image input. Always `input_image`.

        - **detail - string**
          The detail level of the image to be sent to the model. One of `high`, `low`, or `auto`. Defaults to `auto`.

      - **Input audio - object**
        An audio input to the model.

        - **input_audio - object**
          Base64-encoded audio data.

          - **data - string**
            Base64-encoded audio data.

          - **format - string**
            The format of the audio data. Currently supported formats are `mp3` and `wav`.

        - **type - string**
          The type of the input item. Always `input_audio`.

      - **An array of Input text, Input image, and Input audio - array**
        A list of inputs, each of which may be either an input text, input image, or input audio object.

    - **role - string**
      The role of the message input. One of `user`, `assistant`, `system`, or `developer`.

    - **type - string**
      The type of the message input. Always `message`.

  - **labels - array**
    The labels to assign to each item in the evaluation.

  - **model - string**
    The model to use for the evaluation. Must support structured outputs.

  - **name - string**
    The name of the grader.

  - **passing_labels - array**
    The labels that indicate a passing result. Must be a subset of labels.

  - **type - string**
    The object type, which is always `label_model`.

- **StringCheckGrader - object**
  A StringCheckGrader object that performs a string comparison between input and reference using a specified operation.

  - **input - string**
    The input text. This may include template strings.

  - **name - string**
    The name of the grader.

  - **operation - string**
    The string check operation to perform. One of `eq`, `ne`, `like`, or `ilike`.

  - **reference - string**
    The reference text. This may include template strings.

  - **type - string**
    The object type, which is always `string_check`.

- **TextSimilarityGrader - object**
  A TextSimilarityGrader object which grades text based on similarity metrics.

  - **evaluation_metric - string**
    The evaluation metric to use. One of `cosine`, `fuzzy_match`, `bleu`, `gleu`, `meteor`, `rouge_1`, `rouge_2`, `rouge_3`, `rouge_4`, `rouge_5`, or `rouge_l`.

  - **input - string**
    The text being graded.

  - **name - string**
    The name of the grader.

  - **pass_threshold - number**
    The threshold for the score.

  - **reference - string**
    The text being graded against.

  - **type - string**
    The type of grader.

- **PythonGrader - object**
  A PythonGrader object that runs a python script on the input.

  - **name - string**
    The name of the grader.

  - **source - string**
    The source code of the python script.

  - **type - string**
    The object type, which is always `python`.

  - **image_tag - string**
    The image tag to use for the python script.

  - **pass_threshold - number**
    The threshold for the score.

- **ScoreModelGrader - object**
  A ScoreModelGrader object that uses a model to assign a score to the input.

  - **input - array**
    The input text. This may include template strings.

    - **content - string / object / array**
      Inputs to the model - can contain template strings.

      - **Text input - string**
        A text input to the model.

      - **Input text - object**
        A text input to the model.

        - **text - string**
          The text input to the model.

        - **type - string**
          The type of the input item. Always `input_text`.

      - **Output text - object**
        A text output from the model.

        - **text - string**
          The text output from the model.

        - **type - string**
          The type of the output text. Always `output_text`.

      - **Input image - object**
        An image input to the model.

        - **image_url - string**
          The URL of the image input.

        - **type - string**
          The type of the image input. Always `input_image`.

        - **detail - string**
          The detail level of the image to be sent to the model. One of `high`, `low`, or `auto`. Defaults to `auto`.

      - **Input audio - object**
        An audio input to the model.

        - **input_audio - object**
          Base64-encoded audio data.

          - **data - string**
            Base64-encoded audio data.

          - **format - string**
            The format of the audio data. Currently supported formats are `mp3` and `wav`.

        - **type - string**
          The type of the input item. Always `input_audio`.

      - **An array of Input text, Input image, and Input audio - array**
        A list of inputs, each of which may be either an input text, input image, or input audio object.

    - **role - string**
      The role of the message input. One of `user`, `assistant`, `system`, or `developer`.

    - **type - string**
      The type of the message input. Always `message`.

  - **model - string**
    The model to use for the evaluation.

  - **name - string**
    The name of the grader.

  - **type - string**
    The object type, which is always `score_model`.

  - **pass_threshold - number**
    The threshold for the score.

  - **range - array**
    The range of the score. Defaults to `[0, 1]`.

  - **sampling_params - object**
    The sampling parameters for the model.

    - **max_completions_tokens - integer**
      The maximum number of tokens the grader model may generate in its response.

    - **reasoning_effort - string**
      Constrains effort on reasoning for [reasoning models](https://platform.openai.com/docs/guides/reasoning). Currently supported values are `none`, `minimal`, `low`, `medium`, and `high`. Reducing reasoning effort can result in faster responses and fewer tokens used on reasoning in a response.

        * `gpt-5.1` defaults to `none`, which does not perform reasoning. The supported reasoning values for `gpt-5.1` are `none`, `low`, `medium`, and `high`. Tool calls are supported for all reasoning values in gpt-5.1.
        * All models before `gpt-5.1` default to `medium` reasoning effort, and do not support `none`.
        * The `gpt-5-pro` model defaults to (and only supports) `high` reasoning effort.

    - **seed - integer**
      A seed value to initialize the randomness, during sampling.

    - **temperature - number**
      A higher temperature increases randomness in the outputs.

    - **top_p - number**
      An alternative to temperature for nucleus sampling; 1.0 includes all tokens.

### OBJECT The eval object

```json
{
  "object": "eval",
  "id": "eval_67abd54d9b0081909a86353f6fb9317a",
  "data_source_config": {
    "type": "custom",
    "item_schema": {
      "type": "object",
      "properties": {
        "label": {"type": "string"},
      },
      "required": ["label"]
    },
    "include_sample_schema": true
  },
  "testing_criteria": [
    {
      "name": "My string check grader",
      "type": "string_check",
      "input": "{{sample.output_text}}",
      "reference": "{{item.label}}",
      "operation": "eq",
    }
  ],
  "name": "External Data Eval",
  "created_at": 1739314509,
  "metadata": {
    "test": "synthetics",
  }
}
```

## The eval run object

A schema representing an evaluation run.

### Parameters

#### created_at - integer
Unix timestamp (in seconds) when the evaluation run was created.

#### data_source - object
Information about the run's data source.

- **JsonlRunDataSource - object**
  A JsonlRunDataSource object with that specifies a JSONL file that matches the eval

  - **source - object**
    Determines what populates the `item` namespace in the data source.

    - **EvalJsonlFileContentSource - object**
      The content of the jsonl file.

      - **content - array**
        The content of the jsonl file.

        - **item - object**

        - **sample - object**

      - **type - string**
        The type of jsonl source. Always `file_content`.

    - **EvalJsonlFileIdSource - object**
      The identifier of the file.

      - **id - string**
        The identifier of the file.

      - **type - string**
        The type of jsonl source. Always `file_id`.

  - **type - string**
    The type of data source. Always `jsonl`.

- **CompletionsRunDataSource - object**
  A CompletionsRunDataSource object describing a model sampling configuration.

  - **source - object**
    Determines what populates the `item` namespace in this run's data source.

    - **EvalJsonlFileContentSource - object**
      The content of the jsonl file.

      - **content - array**
        The content of the jsonl file.

        - **item - object**

        - **sample - object**

      - **type - string**
        The type of jsonl source. Always `file_content`.

    - **EvalJsonlFileIdSource - object**
      The identifier of the file.

      - **id - string**
        The identifier of the file.

      - **type - string**
        The type of jsonl source. Always `file_id`.

    - **StoredCompletionsRunDataSource - object**
      A StoredCompletionsRunDataSource configuration describing a set of filters

      - **type - string**
        The type of source. Always `stored_completions`.

      - **created_after - integer**
        An optional Unix timestamp to filter items created after this time.

      - **created_before - integer**
        An optional Unix timestamp to filter items created before this time.

      - **limit - integer**
        An optional maximum number of items to return.

      - **metadata - map**
        Set of 16 key-value pairs that can be attached to an object. This can be useful for storing additional information about the object in a structured format, and querying for objects via API or the dashboard.

        Keys are strings with a maximum length of 64 characters. Values are strings with a maximum length of 512 characters.

      - **model - string**
        An optional model to filter by (e.g., 'gpt-4o').

  - **type - string**
    The type of run data source. Always `completions`.

  - **input_messages - object**
    Used when sampling from a model. Dictates the structure of the messages passed into the model. Can either be a reference to a prebuilt trajectory (ie, `item.input_trajectory`), or a template with variable references to the `item` namespace.

    - **TemplateInputMessages - object**
      A list of chat messages forming the prompt or context. May include variable references to the `item` namespace, ie \{\{item.name\}\}.

      - **template - array**
        A list of chat messages forming the prompt or context. May include variable references to the `item` namespace, ie \{\{item.name\}\}.

        - **Input message - object**
          A message input to the model with a role indicating instruction following hierarchy. Instructions given with the `developer` or `system` role take precedence over instructions given with the `user` role. Messages with the `assistant` role are presumed to have been generated by the model in previous interactions.

          - **content - string or array**
            Text, image, or audio input to the model, used to generate a response. Can also contain previous assistant responses.

            - **Text input - string**
              A text input to the model.

            - **Input item content list - array**
              A list of one or many input items to the model, containing different content types.

              - **Input text - object**
                A text input to the model.

                - **text - string**
                  The text input to the model.

                - **type - string**
                  The type of the input item. Always `input_text`.

              - **Input image - object**
                An image input to the model. Learn about [image inputs](https://platform.openai.com/docs/guides/vision).

                - **detail - string**
                  The detail level of the image to be sent to the model. One of `high`, `low`, or `auto`. Defaults to `auto`.

                - **type - string**
                  The type of the input item. Always `input_image`.

                - **file_id - string**
                  The ID of the file to be sent to the model.

                - **image_url - string**
                  The URL of the image to be sent to the model. A fully qualified URL or base64 encoded image in a data URL.

              - **Input file - object**
                A file input to the model.

                - **type - string**
                  The type of the input item. Always `input_file`.

                - **file_data - string**
                  The content of the file to be sent to the model.

                - **file_id - string**
                  The ID of the file to be sent to the model.

                - **file_url - string**
                  The URL of the file to be sent to the model.

                - **filename - string**
                  The name of the file to be sent to the model.

          - **role - string**
            The role of the message input. One of `user`, `assistant`, `system`, or `developer`.

          - **type - string**
            The type of the message input. Always `message`.

        - **Eval message object - object**
          A message input to the model with a role indicating instruction following hierarchy. Instructions given with the `developer` or `system` role take precedence over instructions given with the `user` role. Messages with the `assistant` role are presumed to have been generated by the model in previous interactions.

          - **content - string / object / array**
            Inputs to the model - can contain template strings.

            - **Text input - string**
              A text input to the model.

            - **Input text - object**
              A text input to the model.

              - **text - string**
                The text input to the model.

              - **type - string**
                The type of the input item. Always `input_text`.

            - **Output text - object**
              A text output from the model.

              - **text - string**
                The text output from the model.

              - **type - string**
                The type of the output text. Always `output_text`.

            - **Input image - object**
              An image input to the model.

              - **image_url - string**
                The URL of the image input.

              - **type - string**
                The type of the image input. Always `input_image`.

              - **detail - string**
                The detail level of the image to be sent to the model. One of `high`, `low`, or `auto`. Defaults to `auto`.

            - **Input audio - object**
              An audio input to the model.

              - **input_audio - object**
                Base64-encoded audio data.

                - **data - string**
                  Base64-encoded audio data.

                - **format - string**
                  The format of the audio data. Currently supported formats are `mp3` and `wav`.

              - **type - string**
                The type of the input item. Always `input_audio`.

            - **An array of Input text, Input image, and Input audio - array**
              A list of inputs, each of which may be either an input text, input image, or input audio object.

          - **role - string**
            The role of the message input. One of `user`, `assistant`, `system`, or `developer`.

          - **type - string**
            The type of the message input. Always `message`.

      - **type - string**
        The type of input messages. Always `template`.

    - **ItemReferenceInputMessages - object**
      A reference to a variable in the `item` namespace. Ie, "item.input_trajectory"

      - **item_reference - string**
        A reference to a variable in the `item` namespace. Ie, "item.input_trajectory"

      - **type - string**
        The type of input messages. Always `item_reference`.

  - **model - string**
    The name of the model to use for generating completions (e.g. "o3-mini").

  - **sampling_params - object**
    The maximum number of tokens in the generated output.

    - **max_completion_tokens - integer**
      The maximum number of tokens in the generated output.

    - **reasoning_effort - string**
      Constrains effort on reasoning for [reasoning models](https://platform.openai.com/docs/guides/reasoning). Currently supported values are `none`, `minimal`, `low`, `medium`, and `high`. Reducing reasoning effort can result in faster responses and fewer tokens used on reasoning in a response.

        * `gpt-5.1` defaults to `none`, which does not perform reasoning. The supported reasoning values for `gpt-5.1` are `none`, `low`, `medium`, and `high`. Tool calls are supported for all reasoning values in gpt-5.1.
        * All models before `gpt-5.1` default to `medium` reasoning effort, and do not support `none`.
        * The `gpt-5-pro` model defaults to (and only supports) `high` reasoning effort.

    - **response_format - object**
      An object specifying the format that the model must output.

      Setting to `{ "type": "json_schema", "json_schema": {...} }` enables Structured Outputs which ensures the model will match your supplied JSON schema. Learn more in the [Structured Outputs guide](https://platform.openai.com/docs/guides/structured-outputs).

      Setting to `{ "type": "json_object" }` enables the older JSON mode, which ensures the message the model generates is valid JSON. Using `json_schema` is preferred for models that support it.

      - **Text - object**
        Default response format. Used to generate text responses.

        - **type - string**
          The type of response format being defined. Always `text`.

      - **JSON schema - object**
        JSON Schema response format. Used to generate structured JSON responses. Learn more about [Structured Outputs](https://platform.openai.com/docs/guides/structured-outputs).

        - **json_schema - object**
          Structured Outputs configuration options, including a JSON Schema.

          - **name - string**
            The name of the response format. Must be a-z, A-Z, 0-9, or contain underscores and dashes, with a maximum length of 64.

          - **description - string**
            A description of what the response format is for, used by the model to determine how to respond in the format.

          - **schema - object**
            The schema for the response format, described as a JSON Schema object. Learn how to build JSON schemas [here](https://json-schema.org/).

          - **strict - boolean**
            Whether to enable strict schema adherence when generating the output. If set to true, the model will always follow the exact schema defined in the `schema` field. Only a subset of JSON Schema is supported when `strict` is `true`. To learn more, read the [Structured Outputs guide](https://platform.openai.com/docs/guides/structured-outputs).

        - **type - string**
          The type of response format being defined. Always `json_schema`.

      - **JSON object - object**
        JSON object response format. An older method of generating JSON responses. Using `json_schema` is recommended for models that support it. Note that the model will not generate JSON without a system or user message instructing it to do so.

        - **type - string**
          The type of response format being defined. Always `json_object`.

    - **seed - integer**
      A seed value to initialize the randomness, during sampling.

    - **temperature - number**
      A higher temperature increases randomness in the outputs.

    - **tools - array**
      A list of tools the model may call. Currently, only functions are supported as a tool. Use this to provide a list of functions the model may generate JSON inputs for. A max of 128 functions are supported.

      - **function - object**
        The name of the function to be called. Must be a-z, A-Z, 0-9, or contain underscores and dashes, with a maximum length of 64.

        - **name - string**
          The name of the function to be called. Must be a-z, A-Z, 0-9, or contain underscores and dashes, with a maximum length of 64.

        - **description - string**
          A description of what the function does, used by the model to choose when and how to call the function.

        - **parameters - object**
          The parameters the functions accepts, described as a JSON Schema object. See the [guide](https://platform.openai.com/docs/guides/function-calling) for examples, and the [JSON Schema reference](https://json-schema.org/understanding-json-schema/) for documentation about the format.

          Omitting `parameters` defines a function with an empty parameter list.

        - **strict - boolean**
          Whether to enable strict schema adherence when generating the function call. If set to true, the model will follow the exact schema defined in the `parameters` field. Only a subset of JSON Schema is supported when `strict` is `true`. Learn more about Structured Outputs in the [function calling guide](https://platform.openai.com/docs/guides/function-calling).

      - **type - string**
        The type of the tool. Currently, only `function` is supported.

    - **top_p - number**
      An alternative to temperature for nucleus sampling; 1.0 includes all tokens.

- **ResponsesRunDataSource - object**
  A ResponsesRunDataSource object describing a model sampling configuration.

  - **source - object**
    Determines what populates the `item` namespace in this run's data source.

    - **EvalJsonlFileContentSource - object**
      The content of the jsonl file.

      - **content - array**
        The content of the jsonl file.

        - **item - object**

        - **sample - object**

      - **type - string**
        The type of jsonl source. Always `file_content`.

    - **EvalJsonlFileIdSource - object**
      The identifier of the file.

      - **id - string**
        The identifier of the file.

      - **type - string**
        The type of jsonl source. Always `file_id`.

    - **EvalResponsesSource - object**
      A EvalResponsesSource object describing a run data source configuration.

      - **type - string**
        The type of run data source. Always `responses`.

      - **created_after - integer**
        Only include items created after this timestamp (inclusive). This is a query parameter used to select responses.

      - **created_before - integer**
        Only include items created before this timestamp (inclusive). This is a query parameter used to select responses.

      - **instructions_search - string**
        Optional string to search the 'instructions' field. This is a query parameter used to select responses.

      - **metadata - object**
        Metadata filter for the responses. This is a query parameter used to select responses.

      - **model - string**
        The name of the model to find responses for. This is a query parameter used to select responses.

      - **reasoning_effort - string or null**
        Optional reasoning effort parameter. This is a query parameter used to select responses.

      - **temperature - number**
        Sampling temperature. This is a query parameter used to select responses.

      - **tools - array**
        List of tool names. This is a query parameter used to select responses.

      - **top_p - number**
        Nucleus sampling parameter. This is a query parameter used to select responses.

      - **users - array**
        List of user identifiers. This is a query parameter used to select responses.

  - **type - string**
    The type of run data source. Always `responses`.

  - **input_messages - object**
    Used when sampling from a model. Dictates the structure of the messages passed into the model. Can either be a reference to a prebuilt trajectory (ie, `item.input_trajectory`), or a template with variable references to the `item` namespace.

    - **InputMessagesTemplate - object**
      A list of chat messages forming the prompt or context. May include variable references to the `item` namespace, ie \{\{item.name\}\}.

      - **template - array**
        A list of chat messages forming the prompt or context. May include variable references to the `item` namespace, ie \{\{item.name\}\}.

        - **ChatMessage - object**
          The content of the message.

          - **content - string**
            The content of the message.

          - **role - string**
            The role of the message (e.g. "system", "assistant", "user").

        - **Eval message object - object**
          A message input to the model with a role indicating instruction following hierarchy. Instructions given with the `developer` or `system` role take precedence over instructions given with the `user` role. Messages with the `assistant` role are presumed to have been generated by the model in previous interactions.

          - **content - string / object / array**
            Inputs to the model - can contain template strings.

            - **Text input - string**
              A text input to the model.

            - **Input text - object**
              A text input to the model.

              - **text - string**
                The text input to the model.

              - **type - string**
                The type of the input item. Always `input_text`.

            - **Output text - object**
              A text output from the model.

              - **text - string**
                The text output from the model.

              - **type - string**
                The type of the output text. Always `output_text`.

            - **Input image - object**
              An image input to the model.

              - **image_url - string**
                The URL of the image input.

              - **type - string**
                The type of the image input. Always `input_image`.

              - **detail - string**
                The detail level of the image to be sent to the model. One of `high`, `low`, or `auto`. Defaults to `auto`.

            - **Input audio - object**
              An audio input to the model.

              - **input_audio - object**
                Base64-encoded audio data.

                - **data - string**
                  Base64-encoded audio data.

                - **format - string**
                  The format of the audio data. Currently supported formats are `mp3` and `wav`.

              - **type - string**
                The type of the input item. Always `input_audio`.

            - **An array of Input text, Input image, and Input audio - array**
              A list of inputs, each of which may be either an input text, input image, or input audio object.

          - **role - string**
            The role of the message input. One of `user`, `assistant`, `system`, or `developer`.

          - **type - string**
            The type of the message input. Always `message`.

      - **type - string**
        The type of input messages. Always `template`.

    - **InputMessagesItemReference - object**
      A reference to a variable in the `item` namespace. Ie, "item.name"

      - **item_reference - string**
        A reference to a variable in the `item` namespace. Ie, "item.name"

      - **type - string**
        The type of input messages. Always `item_reference`.

  - **model - string**
    The name of the model to use for generating completions (e.g. "o3-mini").

  - **sampling_params - object**
    The maximum number of tokens in the generated output.

    - **max_completion_tokens - integer**
      The maximum number of tokens in the generated output.

    - **reasoning_effort - string**
      Constrains effort on reasoning for [reasoning models](https://platform.openai.com/docs/guides/reasoning). Currently supported values are `none`, `minimal`, `low`, `medium`, and `high`. Reducing reasoning effort can result in faster responses and fewer tokens used on reasoning in a response.

        * `gpt-5.1` defaults to `none`, which does not perform reasoning. The supported reasoning values for `gpt-5.1` are `none`, `low`, `medium`, and `high`. Tool calls are supported for all reasoning values in gpt-5.1.
        * All models before `gpt-5.1` default to `medium` reasoning effort, and do not support `none`.
        * The `gpt-5-pro` model defaults to (and only supports) `high` reasoning effort.

    - **seed - integer**
      A seed value to initialize the randomness, during sampling.

    - **temperature - number**
      A higher temperature increases randomness in the outputs.

    - **text - object**
      Configuration options for a text response from the model. Can be plain text or structured JSON data. Learn more:

        * [Text inputs and outputs](https://platform.openai.com/docs/guides/text)
        * [Structured Outputs](https://platform.openai.com/docs/guides/structured-outputs)

      - **format - object**
        An object specifying the format that the model must output.

        Configuring `{ "type": "json_schema" }` enables Structured Outputs, which ensures the model will match your supplied JSON schema. Learn more in the [Structured Outputs guide](https://platform.openai.com/docs/guides/structured-outputs).

        The default format is `{ "type": "text" }` with no additional options.

        **Not recommended for gpt-4o and newer models:**

        Setting to `{ "type": "json_object" }` enables the older JSON mode, which ensures the message the model generates is valid JSON. Using `json_schema` is preferred for models that support it.

        - **Text - object**
          Default response format. Used to generate text responses.

          - **type - string**
            The type of response format being defined. Always `text`.

        - **JSON schema - object**
          JSON Schema response format. Used to generate structured JSON responses. Learn more about [Structured Outputs](https://platform.openai.com/docs/guides/structured-outputs).

          - **name - string**
            The name of the response format. Must be a-z, A-Z, 0-9, or contain underscores and dashes, with a maximum length of 64.

          - **schema - object**
            The schema for the response format, described as a JSON Schema object. Learn how to build JSON schemas [here](https://json-schema.org/).

          - **type - string**
            The type of response format being defined. Always `json_schema`.

          - **description - string**
            A description of what the response format is for, used by the model to determine how to respond in the format.

          - **strict - boolean**
            Whether to enable strict schema adherence when generating the output. If set to true, the model will always follow the exact schema defined in the `schema` field. Only a subset of JSON Schema is supported when `strict` is `true`. To learn more, read the [Structured Outputs guide](https://platform.openai.com/docs/guides/structured-outputs).

        - **JSON object - object**
          JSON object response format. An older method of generating JSON responses. Using `json_schema` is recommended for models that support it. Note that the model will not generate JSON without a system or user message instructing it to do so.

          - **type - string**
            The type of response format being defined. Always `json_object`.

    - **tools - array**
      An array of tools the model may call while generating a response. You can specify which tool to use by setting the `tool_choice` parameter.

      The two categories of tools you can provide the model are:

        * **Built-in tools** : Tools that are provided by OpenAI that extend the model's capabilities, like [web search](https://platform.openai.com/docs/guides/tools-web-search) or [file search](https://platform.openai.com/docs/guides/tools-file-search). Learn more about [built-in tools](https://platform.openai.com/docs/guides/tools).
        * **Function calls (custom tools)** : Functions that are defined by you, enabling the model to call your own code. Learn more about [function calling](https://platform.openai.com/docs/guides/function-calling).

      - **Function - object**
        Defines a function in your own code the model can choose to call. Learn more about [function calling](https://platform.openai.com/docs/guides/function-calling).

        - **name - string**
          The name of the function to call.

        - **parameters - map**
          A JSON schema object describing the parameters of the function.

        - **strict - boolean**
          Whether to enforce strict parameter validation. Default `true`.

        - **type - string**
          The type of the function tool. Always `function`.

        - **description - string**
          A description of the function. Used by the model to determine whether or not to call the function.

      - **File search - object**
        A tool that searches for relevant content from uploaded files. Learn more about the [file search tool](https://platform.openai.com/docs/guides/tools-file-search).

        - **type - string**
          The type of the file search tool. Always `file_search`.

        - **vector_store_ids - array**
          The IDs of the vector stores to search.

        - **filters - object**
          A filter to apply.

          - **Comparison Filter - object**
            A filter used to compare a specified attribute key to a given value using a defined comparison operation.

            - **key - string**
              The key to compare against the value.

            - **type - string**
              Specifies the comparison operator: `eq`, `ne`, `gt`, `gte`, `lt`, `lte`, `in`, `nin`.

                * `eq`: equals
                * `ne`: not equal
                * `gt`: greater than
                * `gte`: greater than or equal
                * `lt`: less than
                * `lte`: less than or equal
                * `in`: in
                * `nin`: not in

            - **value - string / number / boolean / array**
              The value to compare against the attribute key; supports string, number, or boolean types.

          - **Compound Filter - object**
            Combine multiple filters using `and` or `or`.

            - **filters - array**
              Array of filters to combine. Items can be `ComparisonFilter` or `CompoundFilter`.

              - **Comparison Filter - object**
                A filter used to compare a specified attribute key to a given value using a defined comparison operation.

                - **key - string**
                  The key to compare against the value.

                - **type - string**
                  Specifies the comparison operator: `eq`, `ne`, `gt`, `gte`, `lt`, `lte`, `in`, `nin`.

                    * `eq`: equals
                    * `ne`: not equal
                    * `gt`: greater than
                    * `gte`: greater than or equal
                    * `lt`: less than
                    * `lte`: less than or equal
                    * `in`: in
                    * `nin`: not in

                - **value - string / number / boolean / array**
                  The value to compare against the attribute key; supports string, number, or boolean types.

            - **type - string**
              Type of operation: `and` or `or`.

        - **max_num_results - integer**
          The maximum number of results to return. This number should be between 1 and 50 inclusive.

        - **ranking_options - object**
          Ranking options for search.

          - **hybrid_search - object**
            Weights that control how reciprocal rank fusion balances semantic embedding matches versus sparse keyword matches when hybrid search is enabled.

            - **embedding_weight - number**
              The weight of the embedding in the reciprocal ranking fusion.

            - **text_weight - number**
              The weight of the text in the reciprocal ranking fusion.

          - **ranker - string**
            The ranker to use for the file search.

          - **score_threshold - number**
            The score threshold for the file search, a number between 0 and 1. Numbers closer to 1 will attempt to return only the most relevant results, but may return fewer results.

      - **Computer use preview - object**
        A tool that controls a virtual computer. Learn more about the [computer tool](https://platform.openai.com/docs/guides/tools-computer-use).

        - **display_height - integer**
          The height of the computer display.

        - **display_width - integer**
          The width of the computer display.

        - **environment - string**
          The type of computer environment to control.

        - **type - string**
          The type of the computer use tool. Always `computer_use_preview`.

      - **Web search - object**
        Search the Internet for sources related to the prompt. Learn more about the [web search tool](https://platform.openai.com/docs/guides/tools-web-search).

        - **type - string**
          The type of the web search tool. One of `web_search` or `web_search_2025_08_26`.

        - **filters - object**
          Filters for the search.

          - **allowed_domains - array**
            Allowed domains for the search. If not provided, all domains are allowed. Subdomains of the provided domains are allowed as well.

            Example: `["pubmed.ncbi.nlm.nih.gov"]`

        - **search_context_size - string**
          High level guidance for the amount of context window space to use for the search. One of `low`, `medium`, or `high`. `medium` is the default.

        - **user_location - object**
          The approximate location of the user.

          - **city - string**
            Free text input for the city of the user, e.g. `San Francisco`.

          - **country - string**
            The two-letter [ISO country code](https://en.wikipedia.org/wiki/ISO_3166-1) of the user, e.g. `US`.

          - **region - string**
            Free text input for the region of the user, e.g. `California`.

          - **timezone - string**
            The [IANA timezone](https://timeapi.io/documentation/iana-timezones) of the user, e.g. `America/Los_Angeles`.

          - **type - string**
            The type of location approximation. Always `approximate`.

      - **MCP tool - object**
        Give the model access to additional tools via remote Model Context Protocol (MCP) servers. [Learn more about MCP](https://platform.openai.com/docs/guides/tools-remote-mcp).

        - **server_label - string**
          A label for this MCP server, used to identify it in tool calls.

        - **type - string**
          The type of the MCP tool. Always `mcp`.

        - **allowed_tools - array or object**
          List of allowed tool names or a filter object.

          - **MCP allowed tools - array**
            A string array of allowed tool names

          - **MCP tool filter - object**
            A filter object to specify which tools are allowed.

            - **read_only - boolean**
              Indicates whether or not a tool modifies data or is read-only. If an MCP server is [annotated with `readOnlyHint`](https://modelcontextprotocol.io/specification/2025-06-18/schema#toolannotations-readonlyhint), it will match this filter.

            - **tool_names - array**
              List of allowed tool names.

        - **authorization - string**
          An OAuth access token that can be used with a remote MCP server, either with a custom MCP server URL or a service connector. Your application must handle the OAuth authorization flow and provide the token here.

        - **connector_id - string**
          Identifier for service connectors, like those available in ChatGPT. One of `server_url` or `connector_id` must be provided. Learn more about service connectors [here](https://platform.openai.com/docs/guides/tools-remote-mcp#connectors).

          Currently supported `connector_id` values are:

            * Dropbox: `connector_dropbox`
            * Gmail: `connector_gmail`
            * Google Calendar: `connector_googlecalendar`
            * Google Drive: `connector_googledrive`
            * Microsoft Teams: `connector_microsoftteams`
            * Outlook Calendar: `connector_outlookcalendar`
            * Outlook Email: `connector_outlookemail`
            * SharePoint: `connector_sharepoint`

        - **headers - object**
          Optional HTTP headers to send to the MCP server. Use for authentication or other purposes.

        - **require_approval - object or string**
          Specify which of the MCP server's tools require approval.

          - **MCP tool approval filter - object**
            Specify which of the MCP server's tools require approval. Can be `always`, `never`, or a filter object associated with tools that require approval.

            - **always - object**
              A filter object to specify which tools are allowed.

              - **read_only - boolean**
                Indicates whether or not a tool modifies data or is read-only. If an MCP server is [annotated with `readOnlyHint`](https://modelcontextprotocol.io/specification/2025-06-18/schema#toolannotations-readonlyhint), it will match this filter.

              - **tool_names - array**
                List of allowed tool names.

            - **never - object**
              A filter object to specify which tools are allowed.

              - **read_only - boolean**
                Indicates whether or not a tool modifies data or is read-only. If an MCP server is [annotated with `readOnlyHint`](https://modelcontextprotocol.io/specification/2025-06-18/schema#toolannotations-readonlyhint), it will match this filter.

              - **tool_names - array**
                List of allowed tool names.

          - **MCP tool approval setting - string**
            Specify a single approval policy for all tools. One of `always` or `never`. When set to `always`, all tools will require approval. When set to `never`, all tools will not require approval.

        - **server_description - string**
          Optional description of the MCP server, used to provide more context.

        - **server_url - string**
          The URL for the MCP server. One of `server_url` or `connector_id` must be provided.

      - **Code interpreter - object**
        A tool that runs Python code to help generate a response to a prompt.

        - **container - string or object**
          The code interpreter container. Can be a container ID or an object that specifies uploaded file IDs to make available to your code, along with an optional `memory_limit` setting.

          - **CodeInterpreterToolAuto - object**
            Configuration for a code interpreter container. Optionally specify the IDs of the files to run the code on.

            - **type - string**
              Always `auto`.

            - **file_ids - array**
              An optional list of uploaded files to make available to your code.

            - **memory_limit - string**

        - **type - string**
          The type of the code interpreter tool. Always `code_interpreter`.

      - **Image generation tool - object**
        A tool that generates images using a model like `gpt-image-1`.

        - **type - string**
          The type of the image generation tool. Always `image_generation`.

        - **background - string**
          Background type for the generated image. One of `transparent`, `opaque`, or `auto`. Default: `auto`.

        - **input_fidelity - string**
          Control how much effort the model will exert to match the style and features, especially facial features, of input images. This parameter is only supported for `gpt-image-1`. Unsupported for `gpt-image-1-mini`. Supports `high` and `low`. Defaults to `low`.

        - **input_image_mask - object**
          Optional mask for inpainting. Contains `image_url` (string, optional) and `file_id` (string, optional).

          - **file_id - string**
            File ID for the mask image.

          - **image_url - string**
            Base64-encoded mask image.

        - **model - string**
          The image generation model to use. Default: `gpt-image-1`.

        - **moderation - string**
          Moderation level for the generated image. Default: `auto`.

        - **output_compression - integer**
          Compression level for the output image. Default: 100.

        - **output_format - string**
          The output format of the generated image. One of `png`, `webp`, or `jpeg`. Default: `png`.

        - **partial_images - integer**
          Number of partial images to generate in streaming mode, from 0 (default value) to 3.

        - **quality - string**
          The quality of the generated image. One of `low`, `medium`, `high`, or `auto`. Default: `auto`.

        - **size - string**
          The size of the generated image. One of `1024x1024`, `1024x1536`, `1536x1024`, or `auto`. Default: `auto`.

      - **Local shell tool - object**
        A tool that allows the model to execute shell commands in a local environment.

        - **type - string**
          The type of the local shell tool. Always `local_shell`.

      - **Shell tool - object**
        A tool that allows the model to execute shell commands.

        - **type - string**
          The type of the shell tool. Always `shell`.

      - **Custom tool - object**
        A custom tool that processes input using a specified format. Learn more about [custom tools](https://platform.openai.com/docs/guides/function-calling#custom-tools)

        - **name - string**
          The name of the custom tool, used to identify it in tool calls.

        - **type - string**
          The type of the custom tool. Always `custom`.

        - **description - string**
          Optional description of the custom tool, used to provide more context.

        - **format - object**
          The input format for the custom tool. Default is unconstrained text.

          - **Text format - object**
            Unconstrained free-form text.

            - **type - string**
              Unconstrained text format. Always `text`.

          - **Grammar format - object**
            A grammar defined by the user.

            - **definition - string**
              The grammar definition.

            - **syntax - string**
              The syntax of the grammar definition. One of `lark` or `regex`.

            - **type - string**
              Grammar format. Always `grammar`.

      - **Web search preview - object**
        This tool searches the web for relevant results to use in a response. Learn more about the [web search tool](https://platform.openai.com/docs/guides/tools-web-search).

        - **type - string**
          The type of the web search tool. One of `web_search_preview` or `web_search_preview_2025_03_11`.

        - **search_context_size - string**
          High level guidance for the amount of context window space to use for the search. One of `low`, `medium`, or `high`. `medium` is the default.

        - **user_location - object**
          The user's location.

          - **type - string**
            The type of location approximation. Always `approximate`.

          - **city - string**
            Free text input for the city of the user, e.g. `San Francisco`.

          - **country - string**
            The two-letter [ISO country code](https://en.wikipedia.org/wiki/ISO_3166-1) of the user, e.g. `US`.

          - **region - string**
            Free text input for the region of the user, e.g. `California`.

          - **timezone - string**
            The [IANA timezone](https://timeapi.io/documentation/iana-timezones) of the user, e.g. `America/Los_Angeles`.

      - **Apply patch tool - object**
        Allows the assistant to create, delete, or update files using unified diffs.

        - **type - string**
          The type of the tool. Always `apply_patch`.

    - **top_p - number**
      An alternative to temperature for nucleus sampling; 1.0 includes all tokens.

#### error - object
An object representing an error response from the Eval API.

- **code - string**
  The error code.

- **message - string**
  The error message.

#### eval_id - string
The identifier of the associated evaluation.

#### id - string
Unique identifier for the evaluation run.

#### metadata - map
Set of 16 key-value pairs that can be attached to an object. This can be useful for storing additional information about the object in a structured format, and querying for objects via API or the dashboard.

Keys are strings with a maximum length of 64 characters. Values are strings with a maximum length of 512 characters.

#### model - string
The model that is evaluated, if applicable.

#### name - string
The name of the evaluation run.

#### object - string
The type of the object. Always "eval.run".

#### per_model_usage - array
Usage statistics for each model during the evaluation run.

- **cached_tokens - integer**
  The number of tokens retrieved from cache.

- **completion_tokens - integer**
  The number of completion tokens generated.

- **invocation_count - integer**
  The number of invocations.

- **model_name - string**
  The name of the model.

- **prompt_tokens - integer**
  The number of prompt tokens used.

- **total_tokens - integer**
  The total number of tokens used.

#### per_testing_criteria_results - array
Results per testing criteria applied during the evaluation run.

- **failed - integer**
  Number of tests failed for this criteria.

- **passed - integer**
  Number of tests passed for this criteria.

- **testing_criteria - string**
  A description of the testing criteria.

#### report_url - string
The URL to the rendered evaluation run report on the UI dashboard.

#### result_counts - object
Counters summarizing the outcomes of the evaluation run.

- **errored - integer**
  Number of output items that resulted in an error.

- **failed - integer**
  Number of output items that failed to pass the evaluation.

- **passed - integer**
  Number of output items that passed the evaluation.

- **total - integer**
  Total number of executed output items.

#### status - string
The status of the evaluation run.

### OBJECT The eval run object

```json
{
  "object": "eval.run",
  "id": "evalrun_67e57965b480819094274e3a32235e4c",
  "eval_id": "eval_67e579652b548190aaa83ada4b125f47",
  "report_url": "https://platform.openai.com/evaluations/eval_67e579652b548190aaa83ada4b125f47?run_id=evalrun_67e57965b480819094274e3a32235e4c",
  "status": "queued",
  "model": "gpt-4o-mini",
  "name": "gpt-4o-mini",
  "created_at": 1743092069,
  "result_counts": {
    "total": 0,
    "errored": 0,
    "failed": 0,
    "passed": 0
  },
  "per_model_usage": null,
  "per_testing_criteria_results": null,
  "data_source": {
    "type": "completions",
    "source": {
      "type": "file_content",
      "content": [
        {
          "item": {
            "input": "Tech Company Launches Advanced Artificial Intelligence Platform",
            "ground_truth": "Technology"
          }
        },
        {
          "item": {
            "input": "Central Bank Increases Interest Rates Amid Inflation Concerns",
            "ground_truth": "Markets"
          }
        },
        {
          "item": {
            "input": "International Summit Addresses Climate Change Strategies",
            "ground_truth": "World"
          }
        },
        {
          "item": {
            "input": "Major Retailer Reports Record-Breaking Holiday Sales",
            "ground_truth": "Business"
          }
        },
        {
          "item": {
            "input": "National Team Qualifies for World Championship Finals",
            "ground_truth": "Sports"
          }
        },
        {
          "item": {
            "input": "Stock Markets Rally After Positive Economic Data Released",
            "ground_truth": "Markets"
          }
        },
        {
          "item": {
            "input": "Global Manufacturer Announces Merger with Competitor",
            "ground_truth": "Business"
          }
        },
        {
          "item": {
            "input": "Breakthrough in Renewable Energy Technology Unveiled",
            "ground_truth": "Technology"
          }
        },
        {
          "item": {
            "input": "World Leaders Sign Historic Climate Agreement",
            "ground_truth": "World"
          }
        },
        {
          "item": {
            "input": "Professional Athlete Sets New Record in Championship Event",
            "ground_truth": "Sports"
          }
        },
        {
          "item": {
            "input": "Financial Institutions Adapt to New Regulatory Requirements",
            "ground_truth": "Business"
          }
        },
        {
          "item": {
            "input": "Tech Conference Showcases Advances in Artificial Intelligence",
            "ground_truth": "Technology"
          }
        },
        {
          "item": {
            "input": "Global Markets Respond to Oil Price Fluctuations",
            "ground_truth": "Markets"
          }
        },
        {
          "item": {
            "input": "International Cooperation Strengthened Through New Treaty",
            "ground_truth": "World"
          }
        },
        {
          "item": {
            "input": "Sports League Announces Revised Schedule for Upcoming Season",
            "ground_truth": "Sports"
          }
        }
      ]
    },
    "input_messages": {
      "type": "template",
      "template": [
        {
          "type": "message",
          "role": "developer",
          "content": {
            "type": "input_text",
            "text": "Categorize a given news headline into one of the following topics: Technology, Markets, World, Business, or Sports.\n\n# Steps\n\n1. Analyze the content of the news headline to understand its primary focus.\n2. Extract the subject matter, identifying any key indicators or keywords.\n3. Use the identified indicators to determine the most suitable category out of the five options: Technology, Markets, World, Business, or Sports.\n4. Ensure only one category is selected per headline.\n\n# Output Format\n\nRespond with the chosen category as a single word. For instance: \"Technology\", \"Markets\", \"World\", \"Business\", or \"Sports\".\n\n# Examples\n\n**Input**: \"Apple Unveils New iPhone Model, Featuring Advanced AI Features\"  \n**Output**: \"Technology\"\n\n**Input**: \"Global Stocks Mixed as Investors Await Central Bank Decisions\"  \n**Output**: \"Markets\"\n\n**Input**: \"War in Ukraine: Latest Updates on Negotiation Status\"  \n**Output**: \"World\"\n\n**Input**: \"Microsoft in Talks to Acquire Gaming Company for $2 Billion\"  \n**Output**: \"Business\"\n\n**Input**: \"Manchester United Secures Win in Premier League Football Match\"  \n**Output**: \"Sports\" \n\n# Notes\n\n- If the headline appears to fit into more than one category, choose the most dominant theme.\n- Keywords or phrases such as \"stocks\", \"company acquisition\", \"match\", or technological brands can be good indicators for classification.\n"
          }
        },
        {
          "type": "message",
          "role": "user",
          "content": {
            "type": "input_text",
            "text": "{{item.input}}"
          }
        }
      ]
    },
    "model": "gpt-4o-mini",
    "sampling_params": {
      "seed": 42,
      "temperature": 1.0,
      "top_p": 1.0,
      "max_completions_tokens": 2048
    }
  },
  "error": null,
  "metadata": {}
}
```

## The eval run output item object

A schema representing an evaluation run output item.

### Parameters

#### created_at - integer
Unix timestamp (in seconds) when the evaluation run was created.

#### datasource_item - object
Details of the input data source item.

#### datasource_item_id - integer
The identifier for the data source item.

#### eval_id - string
The identifier of the evaluation group.

#### id - string
Unique identifier for the evaluation run output item.

#### object - string
The type of the object. Always "eval.run.output_item".

#### results - array
A list of grader results for this output item.

- **name - string**
  The name of the grader.

- **passed - boolean**
  Whether the grader considered the output a pass.

- **score - number**
  The numeric score produced by the grader.

- **sample - object**

- **type - string**
  The grader type (for example, "string-check-grader").

#### run_id - string
The identifier of the evaluation run associated with this output item.

#### sample - object
A sample containing the input and output of the evaluation run.

- **error - object**
  An object representing an error response from the Eval API.

  - **code - string**
    The error code.

  - **message - string**
    The error message.

- **finish_reason - string**
  The reason why the sample generation was finished.

- **input - array**
  An array of input messages.

  - **content - string**
    The content of the message.

  - **role - string**
    The role of the message sender (e.g., system, user, developer).

- **max_completion_tokens - integer**
  The maximum number of tokens allowed for completion.

- **model - string**
  The model used for generating the sample.

- **output - array**
  An array of output messages.

  - **content - string**
    The content of the message.

  - **role - string**
    The role of the message (e.g. "system", "assistant", "user").

- **seed - integer**
  The seed used for generating the sample.

- **temperature - number**
  The sampling temperature used.

- **top_p - number**
  The top_p value used for sampling.

- **usage - object**
  Token usage details for the sample.

  - **cached_tokens - integer**
    The number of tokens retrieved from cache.

  - **completion_tokens - integer**
    The number of completion tokens generated.

  - **prompt_tokens - integer**
    The number of prompt tokens used.

  - **total_tokens - integer**
    The total number of tokens used.

#### status - string
The status of the evaluation run.

### OBJECT The eval run output item object

```json
{
  "object": "eval.run.output_item",
  "id": "outputitem_67abd55eb6548190bb580745d5644a33",
  "run_id": "evalrun_67abd54d60ec8190832b46859da808f7",
  "eval_id": "eval_67abd54d9b0081909a86353f6fb9317a",
  "created_at": 1739314509,
  "status": "pass",
  "datasource_item_id": 137,
  "datasource_item": {
      "teacher": "To grade essays, I only check for style, content, and grammar.",
      "student": "I am a student who is trying to write the best essay."
  },
  "results": [
    {
      "name": "String Check Grader",
      "type": "string-check-grader",
      "score": 1.0,
      "passed": true,
    }
  ],
  "sample": {
    "input": [
      {
        "role": "system",
        "content": "You are an evaluator bot..."
      },
      {
        "role": "user",
        "content": "You are assessing..."
      }
    ],
    "output": [
      {
        "role": "assistant",
        "content": "The rubric is not clear nor concise."
      }
    ],
    "finish_reason": "stop",
    "model": "gpt-4o-2024-08-06",
    "usage": {
      "total_tokens": 521,
      "completion_tokens": 2,
      "prompt_tokens": 519,
      "cached_tokens": 0
    },
    "error": null,
    "temperature": 1.0,
    "max_completion_tokens": 2048,
    "top_p": 1.0,
    "seed": 42
  }
}
```