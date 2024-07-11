open api spec for elastic search given below
{
  "openapi": "3.0.1",
  "info": {
    "title": "Custom Search Elastic",
    "description": "Elastic Search API unofficial partial spec from IBM.  We made this by running the API and observing the behavior. This spec is a minimal starting point that can handle some basic use cases.",
    "version": "1.0"
  },
  "servers": [
    {
      "url": "https://{elastic_url}",
      "description": "The Elasticsearch URL as specified in the Overview page within Endpoints -> HTTPS tab on your IBM Cloud Database ICD Elastic Instance",
      "variables": {
        "elastic_url": {
          "default": "abc.databases.appdomain.cloud:31682",
          "description": "The portions of the Elasticsearch URL that follow https://"
        }
      }
    }

  ],
  "paths": {
    "/{index_name}/_search": {
      "post": {
        "parameters": [
          {
            "name": "index_name",
            "in": "path",
            "description": "Name of the index",
            "required": true,
            "schema": {
              "type": "string"
            }
          }
        ],
        "security": [
          {
            "basicAuth": []
          }
        ],
        "description": "Search request to Elasticsearch",
        "requestBody": {
          "content": {
            "application/json": {
              "schema": {
                "type": "object",
                "properties": {
                  "_source": {
                    "type": "array",
                    "items": {
                      "type": "string"
                    }
                  },
                  "size": {
                    "type": "integer"
                  },
                  "query": {
                    "type": "object"
                  },
                  "knn": {
                    "type": "object"
                  }
                }
              },
              "example": {
                "query": {
                  "text_expansion": {
                    "ml.tokens": {
                      "model_id": ".elser_model_1",
                      "model_text": "tell me about a custom extension"
                    }
                  }
                },
                "knn": {
                  "field": "text_embedding.predicted_value",
                  "query_vector_builder": {
                    "text_embedding": {
                      "model_id": "intfloat__multilingual-e5-small",
                      "model_text": "how to set up custom extension?"
                    }
                  },
                  "k": 10,
                  "num_candidates": 100
                }
              }
            }
          }
        },
        "responses": {
          "200": {
            "description": "Search results returned by Elastic",
            "content": {
              "application/json; charset=utf-8": {
                "schema": {
                  "type": "object",
                  "properties": {
                    "took": {
                      "type": "integer"
                    },
                    "timed_out": {
                      "type": "boolean"
                    },
                    "_shards": {
                      "type": "object",
                      "properties": {
                        "total": {
                          "type": "integer"
                        },
                        "successful": {
                          "type": "integer"
                        },
                        "skipped": {
                          "type": "integer"
                        },
                        "failed": {
                          "type": "integer"
                        }
                      }
                    },
                    "hits": {
                      "type": "object",
                      "properties": {
                        "max_score": {
                          "type": "integer"
                        },
                        "hits": {
                          "type": "array",
                          "items": {
                            "type": "object",
                            "properties": {
                              "_id": {
                                "type": "string"
                              },
                              "_score": {
                                "type": "number"
                              },
                              "_index": {
                                "type": "string"
                              },
                              "_source": {
                                "type": "object",
                                "properties": {
                                  "section_title": {
                                    "type": "string"
                                  },
                                  "text": {
                                    "type": "string"
                                  },
                                  "title": {
                                    "type": "string"
                                  }
                                }
                              }
                            }
                          }
                        }
                      }
                    }
                  }
                }
              }
            }
          }
        }
      }
    }
  },
  "components": {
    "securitySchemes": {
      "basicAuth": {
        "type": "http",
        "scheme": "basic"
      }
    }
  }
}

openapi spec for milvus
{
  "openapi": "3.0.3",
  "info": {
    "title": "Milvus search",
    "description": "Unofficial simplified OpenAPI specification for the Milvus search endpoint as documented in https://milvus.io/docs/search.md.",
    "version": "1.0.0"
  },
  "servers": [
    {
      "url": "http://{milvus_url}",
      "description": "IP address and port without encryption/authentication",
      "variables": {
        "milvus_url": {
          "default": "0.0.0.0:9091",
          "description": "The portions of URL that follow http://"
        }
      }
    },
    {
      "url": "https://{milvus_url}",
      "description": "IP address and port with encryption/authentication",
      "variables": {
        "milvus_url": {
          "default": "0.0.0.0:9091",
          "description": "The portions of URL that follow https://"
        }
      }
    }
  ],
  "paths": {
    "/api/v1/search": {
      "post": {
        "summary": "Semantic (vector) search in the given collection",
        "requestBody": {
          "required": true,
          "content": {
            "application/json": {
              "schema": {
                "$ref": "#/components/schemas/SearchRequest"
              }
            }
          }
        },
        "responses": {
          "200": {
            "description": "Successful search",
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/SearchResponse"
                }
              }
            }
          }
        }
      }
    }
  },
  "components": {
    "securitySchemes": {
      "basicAuth": {
        "type": "http",
        "scheme": "basic"
      }
    },
    "schemas": {
      "SearchRequest": {
        "type": "object",
        "description": "A search request made by the client",
        "properties": {
          "collection_name": {
            "type": "string",
            "description": "The name of the collection to search in"
          },
          "output_fields": {
            "type": "array",
            "items": {
              "type": "string"
            },
            "description": "Fields to be included in the response"
          },
          "search_params": {
            "type": "array",
            "items": {
              "$ref": "#/components/schemas/Param"
            },
            "description": "Parameters for the index, as described in https://milvus.io/docs/index.md"
          },
          "dsl_type": {
            "type": "integer",
            "description": "The Data Search Language type for the search; 0=Dsl, 1=BoolExprV1",
            "enum": [
              1,
              2
            ]
          },
          "vectors": {
            "type": "array",
            "items": {
              "type": "array",
              "items": {
                "type": "number"
              }
            },
            "description": "Vector data to search for, typically an embedding of a query using the same embedding model used to make the index"
          }
        }
      },
      "Param": {
        "type": "object",
        "description": "A key-value pair used in search parameters",
        "properties": {
          "key": {
            "type": "string",
            "description": "The name of the parameter; valid values depend in the index type and are listed in https://milvus.io/docs/search.md and/or https://milvus.io/docs/index.md"
          },
          "value": {
            "type": "string",
            "description": "The value of the parameter"
          }
        }
      },
      "SearchResponse": {
        "type": "object",
        "description": "Response received after a successful search",
        "properties": {
          "status": {
            "type": "object",
            "description": "Status of the search operation",
            "properties": {
              "error_code": {
                "type": "integer",
                "description": "If the search failed, a code indicating the failure; this field is absent when there is no failure"
              },
              "reason": {
                "type": "string",
                "description": "If the search failed, a text reason for the failure; this field is absent when there is no failure."
              }
            }
          },
          "results": {
            "type": "object",
            "description": "The results from the search",
            "properties": {
              "num_queries": {
                "type": "integer",
                "description": "The number of queries made"
              },
              "top_k": {
                "type": "integer",
                "description": "The maximum number of search results to return"
              },
              "fields_data": {
                "type": "array",
                "items": {
                  "$ref": "#/components/schemas/FieldData"
                },
                "description": "Data for the output fields"
              },
              "scores": {
                "type": "array",
                "items": {
                  "type": "number"
                },
                "description": "Scores of the search results"
              },
              "ids": {
                "type": "object",
                "description": "IDs of the search results",
                "properties": {
                  "IdField": {
                    "type": "object",
                    "properties": {
                      "IntId": {
                        "type": "object",
                        "properties": {
                          "data": {
                            "type": "array",
                            "items": {
                              "type": "integer"
                            },
                            "description": "Array of integer IDs"
                          }
                        }
                      }
                    }
                  }
                }
              },
              "topks": {
                "type": "array",
                "items": {
                  "type": "integer"
                },
                "description": "The number of search results returned for each query"
              },
              "output_fields": {
                "type": "array",
                "items": {
                  "type": "string"
                },
                "description": "Names of the fields included in the search output"
              }
            }
          },
          "collection_name": {
            "type": "string",
            "description": "The name of the collection searched in"
          }
        }
      },
      "FieldData": {
        "type": "object",
        "description": "Data related to a specific field",
        "properties": {
          "type": {
            "type": "integer",
            "description": "The type of the field"
          },
          "field_name": {
            "type": "string",
            "description": "The name of the field"
          },
          "Field": {
            "type": "object",
            "properties": {
              "StringData": {
                "type": "object",
                "properties": {
                  "data": {
                    "type": "array",
                    "description": "The string values of the field",
                    "items": {
                      "type": "string"
                    }
                  }
                }
              },
              "LongData": {
                "type": "object",
                "properties": {
                  "data": {
                    "type": "array",
                    "description": "The long integer values of the field",
                    "items": {
                      "type": "integer"
                    }
                  }
                }
              }
            }
          },
          "field_id": {
            "type": "integer",
            "description": "The ID of the field"
          }
        }
      }
    }
  }
}

open api spec for huggig face
{
  "openapi": "3.0.3",
  "info": {
    "version": "1.0.0",
    "title": "Hugging Face Hub Embeddings API",
    "description": "Unofficial simplified OpenAPI specification for generating embeddings using Hugging Face Hub."
  },
  "servers": [
    {
      "url": "https://api-inference.huggingface.co"
    }
  ],
  "paths": {
    "/pipeline/feature-extraction/sentence-transformers/{model_id}": {
      "post": {
        "summary": "Generate embeddings using a sentence-transformers model",
        "parameters": [
          {
            "name": "model_id",
            "in": "path",
            "description": "ID of the sentence-transformers model to use for generating embeddings.",
            "example": "all-MiniLM-L6-v2",
            "required": true,
            "schema": {
              "type": "string"
            }
          }
        ],
        "requestBody": {
          "required": true,
          "content": {
            "application/json": {
              "schema": {
                "$ref": "#/components/schemas/EmbeddingInput"
              }
            }
          }
        },
        "responses": {
          "200": {
            "description": "Successful operation",
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/EmbeddingOutput"
                }
              }
            }
          },
          "400": {
            "description": "Invalid input"
          }
        }
      }
    }
  },
  "components": {
    "securitySchemes": {
      "bearerAuth": {
        "type": "http",
        "scheme": "bearer"
      }
    },
    "schemas": {
      "EmbeddingInput": {
        "type": "object",
        "properties": {
          "inputs": {
            "type": "array",
            "items": {
              "type": "string"
            },
            "description": "An array of input text to generate embeddings for."
          },
          "options": {
            "type": "object",
            "properties": {
              "wait_for_model": {
                "type": "boolean",
                "description": "Flag indicating whether to wait for the model to be downloaded and installed on the server (if needed) before generating embeddings."
              }
            },
            "description": "Additional options for generating embeddings."
          }
        },
        "required": ["inputs"],
        "example": {
          "inputs": ["How do I start a purchase order?"],
          "options": {
            "wait_for_model": true
          }
        }
      },
      "EmbeddingOutput": {
        "type": "array",
        "items": {
          "type": "array",
          "items": {
            "type": "number"
          }
        },
        "description": "The generated embeddings."
      }
    }
  }
}

openapi spec for watsonx.ai
{
  "openapi": "3.0.3",
  "info": {
    "description": "Minimal spec for commonly used features in watsonx.ai /generation API endpoint. Missing a few of parameters.",
    "title": "Simplified watsonx.ai generation API",
    "version": "1.1.0"
  },
  "servers": [
    {
      "url": "https://{region}.ml.cloud.ibm.com",
      "description": "watsonx.ai v1",
      "variables": {
        "region": {
          "enum": [
            "us-south",
            "eu-de",
            "eu-gb",
            "jp-tok"
          ],
          "default": "us-south",
          "description": "The region where you want to access watsonx.ai"
        }
      }
    }
  ],
  "components": {
    "securitySchemes": {
      "oauth2": {
        "type": "oauth2",
        "flows": {
          "x-apikey": {
            "tokenUrl": "https://iam.cloud.ibm.com/identity/token",
            "grantType": "urn:ibm:params:oauth:grant-type:apikey",
            "secretKeys": ["apikey"],
            "paramKeys": [],
            "scopes": {}
          }
        }
      }
    }
  },
  "security": [
    {
      "oauth2": []
    }
  ],
  "paths": {
    "/ml/v1/text/generation": {
      "post": {
        "description": "Generation",
        "parameters": [
          {
            "name": "version",
            "in": "query",
            "description": "Release date of the version of the API you want to use. Specify dates in YYYY-MM-DD format. The current version is `2023-05-29`.",
            "required": true,
            "schema": {
              "type": "string"
            }
          }
        ],
        "requestBody": {
          "content": {
            "application/json": {
              "schema": {
                "type": "object",
                "required": ["model_id", "input", "project_id"],
                "properties": {
                  "model_id": {
                    "type": "string",
                    "description": "The ID of the model to be used for this request. Please refer to the list of models at https://dataplatform.cloud.ibm.com/docs/content/wsj/analyze-data/fm-prompt-lab.html?context=wx",
                    "example": "google/flan-ul2"
                  },
                  "input": {
                    "type": "string",
                    "description": "The input is the prompt to generate completions. Note: The method tokenizes the input internally. It is recommended not to leave any trailing spaces."
                  },
                  "project_id": {
                    "type": "string",
                    "description": "id for the associated watsonx project.",
                    "minLength": 1,
                    "maxLength": 255,
                    "pattern": "^[a-zA-Z0-9_-]*$",
                    "example": "3e992422-d337-47f9-869a-0928e49a3ea6"
                  },
                  "parameters": {
                    "type": "object",
                    "properties": {
                      "decoding_method": {
                        "type": "string",
                        "description": "The strategy used for picking the tokens during generation of the output text.",
                        "example": "greedy"
                      },
                      "random_seed": {
                        "type": "integer",
                        "description": "The random number generator seed to use in sampling mode for experimental repeatability.",
                        "example": "1"
                      },
                      "time_limit": {
                        "type": "integer",
                        "description": "The time limit in milliseconds - if not completed within this time, generation will stop. The text generated so far will be returned along with the TIME_LIMIT stop reason.",
                        "example": "600000"
                      },
                      "temperature": {
                        "type": "number",
                        "description": "The value used to module the next token probabilities. The range is 0.00 to 2.00, a value set to 0.00 would make it deterministic.",
                        "example": "0.7"
                      },
                       "top_k": {
                        "type": "integer",
                        "description": "The number of highest probability vocabulary tokens to keep for top-k-filtering. Only applies for sampling mode.  The range is 1 to 100.",
                        "example": "50"
                      },
                      "top_p": {
                        "type": "number",
                        "description": "Similar to top_k except the candidates to generate the next token are the most likely tokens with probabilities that add up to at least top_p. The range is 0.0  to 1.0 . A value of 1.0 is equivalent to disabled.",
                        "example": "0.5"
                      },
                      "max_new_tokens": {
                        "type": "number",
                        "description": "The maximum number of new tokens to be generated.",
                        "example": "150"
                      },
                      "min_new_tokens": {
                        "type": "number",
                        "description": "The minimum number of new tokens to be generated.",
                        "example": "50"
                      },
                      "repetition_penalty": {
                        "type": "number",
                        "description": "The value which represents the penalty for penalizing tokens that have already been generated or belong to the context.",
                        "example": "1.10"
                      },
                      "stop_sequences": {
                        "type": "array",
                        "items": {
                          "type": "string"
                        },
                        "description": "Stop sequences are one or more strings which will cause the text generation to stop if/when they are produced as part of the output. Stop sequences encountered prior to the minimum number of tokens being generated will be ignored.",
                        "example": ["\n\n"]
                      },
                      "include_stop_sequence": {
                        "type": "boolean",
                        "description": "The value to control presence of matched stop sequences from the end of the output text. The default is true, meaning that the output will end with the stop sequence text when matched.",
                        "example": "true"
                      }
                    }
                  }
                }
              }
            }
          }
        },
        "responses": {
          "200": {
            "description": "Default Response",
            "content": {
              "application/json": {
                "schema": {
                  "type": "object",
                  "properties": {
                    "model_id": {
                      "description": "The ID of the model to be used for this request",
                      "type": "string"
                    },
                    "created_at": {
                      "description": "The date and time of the response",
                      "type": "string"
                    },
                    "results": {
                      "type": "array",
                      "items": {
                        "type": "object",
                        "properties": {
                          "generated_text": {
                            "description": "The generated text",
                            "type": "string"
                          },
                          "generated_token_count": {
                            "description": "The number of tokens in the output",
                            "type": "integer"
                          },
                          "input_token_count": {
                            "description": "The number of tokens in the input",
                            "type": "integer"
                          },
                          "stop_reason": {
                            "description": "The reason for stopping the generation.  Can be NOT_FINISHED - Possibly more tokens to be streamed, MAX_TOKENS - Maximum requested tokens reached, EOS_TOKEN - End of sequence token encountered, CANCELLED - Request canceled by the client, TIME_LIMIT - Time limit reached, STOP_SEQUENCE - Stop sequence encountered, TOKEN_LIMIT - Token limit reached, ERROR - Error encountered",
                            "type": "string"
                          }
                        }
                      },
                      "description": "Outputs of the generation"
                    }
                  }
                }
              }
            }
          },
          "default": {
            "description": "Unexpected error"
          }
        }
      }
    }
  }
}

openapi spec for mail sending
{
    "openapi": "3.0.0",
    "info": {
      "title": "Email Service API",
      "version": "1.0.0"
    },
    "servers": [
      {
        "url": "https://{host}:{port}",
        "description": "The address of SMTP extension server",
        "variables": {
          "host": {
            "default": "",
            "description": "Hostname of the API server"
          },
          "port": {
            "default": "443",
            "description": "Port of the API server"
          }
        }
      }
    ],
    "paths": {
      "/send": {
        "post": {
          "summary": "Send an email",
          "requestBody": {
            "required": true,
            "content": {
              "application/json": {
                "schema": {
                  "type": "object",
                  "properties": {
                    "receiver": {
                      "type": "string"
                    },
                    "subject": {
                      "type": "string"
                    },
                    "body": {
                      "type": "string"
                    }
                  },
                  "required": [
                    "receiver",
                    "subject",
                    "body"
                  ]
                }
              }
            }
          },
          "responses": {
            "200": {
              "description": "Email sent successfully",
              "content": {
                "application/json": {
                  "schema": {
                    "type": "object",
                    "properties": {
                      "message": {
                        "type": "string"
                      },
                      "success": {
                        "type": "boolean"
                      }
                    }
                  }
                }
              }
            },
            "401": {
              "description": "Unauthorized - Bearer token is missing or invalid"
            },
            "500": {
              "description": "An error occurred while sending the email"
            }
          },
          "security": [
            {
              "BearerAuth": []
            }
          ]
        }
      }
    },
    "security": [
      {
        "BearerAuth": []
      }
    ],
    "components": {
      "securitySchemes": {
        "BearerAuth": {
          "type": "http",
          "scheme": "bearer"
        }
      }
    }
}

openapi spec for twilio
{
  "openapi": "3.0.0",
  "info": {
    "title": "Twilio API",
    "version": "1.0.0",
    "description": "API for making calls using Twilio",
    "x-ibm-annotations": "true",
    "x-ibm-application-name": "Twilio API",
    "x-ibm-application-id": "twilio-api",
    "x-ibm-skill-type": "imported",
    "x-ibm-application-icon": "<svg xmlns='http://www.w3.org/2000/svg' fill='none' viewBox='0 0 24 24' stroke-width='1.5' stroke='currentColor' class='w-6 h-6'><path stroke-linecap='round' stroke-linejoin='round' d='M16.5 6v.75m0 3v.75m0 3v.75m0 3V18m-9-5.25h5.25M7.5 15h3M3.375 5.25c-.621 0-1.125.504-1.125 1.125v3.026a2.999 2.999 0 0 1 0 5.198v3.026c0 .621.504 1.125 1.125 1.125h17.25c.621 0 1.125-.504 1.125-1.125v-3.026a2.999 2.999 0 0 1 0-5.198V6.375c0-.621-.504-1.125-1.125-1.125H3.375Z' /></svg>"
  },
  "servers": [
    {
      "url": "https://api.twilio.com"
    }
  ],
  "paths": {
    "/2010-04-01/Accounts/{AccountSid}/Calls.json": {
      "post": {
        "tags": ["calls"],
        "summary": "Make a call",
        "description": "Initiate a new outgoing call",
        "operationId": "makeCall",
        "parameters": [
          {
            "name": "AccountSid",
            "in": "path",
            "required": true,
            "schema": {
              "type": "string"
            }
          }
        ],
        "requestBody": {
          "description": "Call details",
          "required": true,
          "content": {
            "application/x-www-form-urlencoded": {
              "schema": {
                "type": "object",
                "properties": {
                  "Url": {
                    "type": "string",
                    "description": "The URL that returns the TwiML instructions for the call.",
                    "example": "http://demo.twilio.com/docs/voice.xml"
                  },
                  "To": {
                    "type": "string",
                    "description": "The phone number to call.",
                    "example": "+14155551212"
                  },
                  "From": {
                    "type": "string",
                    "description": "The phone number to use as the caller ID.",
                    "example": "+15017122661"
                  }
                },
                "required": ["Url", "To", "From"]
              }
            }
          }
        },
        "responses": {
          "200": {
            "description": "Call initiated successfully",
            "content": {
              "application/json": {
                "schema": {
                  "type": "object",
                  "properties": {
                    "sid": {
                      "type": "string"
                    },
                    "status": {
                      "type": "string"
                    }
                  }
                }
              }
            }
          },
          "400": {
            "description": "Invalid request"
          }
        },
        "security": [
          {
            "basic_auth": []
          }
        ]
      }
    }
  },
  "components": {
    "securitySchemes": {
      "basic_auth": {
        "type": "http",
        "scheme": "basic"
      }
    }
  }
}

openapi spec for discovery
{
  "openapi": "3.0.1",
  "info": {
    "title": "IBM Watson Discovery query endpoint",
    "description": "IBM Watson Discovery simplified API spec for querying.  You can also download the full spec for the entire service by going to https://cloud.ibm.com/apidocs/discovery-data and clicking on the menu in the upper right.",
    "version": "1.1"
  },
  "servers": [
    {
      "url": "https://{discovery_url}",
      "description": "The Watson Discovery URL as specfied in the manage/credentials on IBM Cloud or Cloud Pak for Data",
      "variables": {
        "discovery_url": {
          "default": "api.us-south.discovery.watson.cloud.ibm.com/instances/12345-6789",
          "description": "The portions of the Watson Discovery URL that follow https://"
        }
      }
    }
  ],
  "security": [
    {
      "bearerAuth": []
    },
    {
      "basicAuth": []
    }
  ],
  "externalDocs": {
    "description": "For more information about this service, see docs.",
    "url": "https://cloud.ibm.com/docs/discovery-data",
    "x-release-notes": "https://cloud.ibm.com/docs/discovery-data?topic=discovery-data-release-notes"
  },
  "paths": {
    "/v2/projects/{project_id}/query": {
      "post": {
        "operationId": "query",
        "summary": "Query a project",
        "description": "Search your data by submitting queries that are written in natural language or formatted in the Discovery Query Language. For more information, see the [Discovery documentation](https://cloud.ibm.com/docs/discovery-data?topic=discovery-data-query-concepts). The default query parameters differ by project type. For more information about the project default settings, see the [Discovery documentation](https://cloud.ibm.com/docs/discovery-data?topic=discovery-data-query-defaults). See [the Projects API documentation](#create-project) for details about how to set custom default query settings. \n\nThe length of the UTF-8 encoding of the POST body cannot exceed 10,000 bytes, which is roughly equivalent to 10,000 characters in English.",
        "tags": [
          "Queries"
        ],
        "parameters": [
          {
            "name": "version",
            "in": "query",
            "description": "Release date of the version of the API you want to use. Specify dates in YYYY-MM-DD format. The current version is `2020-08-30`.",
            "required": true,
            "schema": {
              "type": "string"
            }
          },
          {
            "name": "project_id",
            "in": "path",
            "description": "The ID of the project. This information can be found from the *Integrate and Deploy* page in Discovery.",
            "required": true,
            "schema": {
              "type": "string",
              "minLength": 1,
              "maxLength": 255,
              "pattern": "^[a-zA-Z0-9_-]*$"
            }
          }
        ],
        "requestBody": {
          "$ref": "#/components/requestBodies/QueryLarge"
        },
        "responses": {
          "200": {
            "description": "Query executed successfully.",
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/QueryResponse"
                }
              }
            }
          },
          "400": {
            "description": "Bad request.\n\n* Project has no collections.\n\n* A list of document ids is required in **similar.document_ids** when **similar.enabled** is `true`.",
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/ErrorResponse"
                }
              }
            }
          },
          "404": {
            "description": "Bad request.",
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/ErrorResponse"
                }
              }
            }
          }
        },
        "x-sdk-operations": {
          "request-examples": {
            "curl": [
              {
                "name": "Example request",
                "example": [
                  {
                    "type": "code",
                    "source": [
                      "curl -X POST {auth} \\\n",
                      "--header \"Content-Type: application/json\" \\\n",
                      "--data \"{ \\\n",
                      "  \\\"collection_ids\\\": [  \\\n",
                      "    \\\"{collection_id_1}\\\", \\\n",
                      "    \\\"{collection_id_2}\\\" \\\n",
                      "  ], \\\n",
                      "  \\\"query\\\": \\\"text:IBM\\\" \\\n",
                      "}\" \\\n",
                      "\"{url}/v2/projects/{project_id}/query?version=2020-08-30\""
                    ]
                  }
                ]
              }
            ],
            "java": [
              {
                "name": "Example request",
                "example": [
                  {
                    "type": "code",
                    "source": [
                      "CloudPakForDataAuthenticator authenticator = new CloudPakForDataAuthenticator(\"https://{cpd_cluster_host}{:port}/icp4d-api/v1/authorize\", \"{username}\", \"{password}\");\n",
                      "Discovery discovery = new Discovery(\"2020-08-30\", authenticator);\n",
                      "discovery.setServiceUrl(\"https://{cpd_cluster_host}{:port}/discovery/{release}/instances/{instance_id}/api\");\n",
                      "\n",
                      "QueryOptions options = new QueryOptions.Builder()\n",
                      "  .projectId(\"{project_id}\")\n",
                      "  .query(\"{field}:{value}\")\n",
                      "  .build();\n",
                      "\n",
                      "QueryResponse response = discovery.query(options).execute().getResult();\n",
                      "\n",
                      "System.out.println(response);"
                    ]
                  }
                ]
              }
            ],
            "node": [
              {
                "name": "Example request",
                "example": [
                  {
                    "type": "code",
                    "source": [
                      "const DiscoveryV2 = require('ibm-watson/discovery/v2');\n",
                      "const { CloudPakForDataAuthenticator } = require('ibm-watson/auth');\n",
                      "\n",
                      "const discovery = new DiscoveryV2({\n",
                      "  authenticator: new CloudPakForDataAuthenticator({\n",
                      "    url: 'https://{cpd_cluster_host}{:port}/icp4d-api/v1/authorize',\n",
                      "    username: '{username}',\n",
                      "    password: '{password}',\n",
                      "  }),\n",
                      "  version: '2020-08-30',\n",
                      "  serviceUrl: 'https://{cpd_cluster_host}{:port}/discovery/{release}/instances/{instance_id}/api',\n",
                      "});\n",
                      "\n",
                      "const params = {\n",
                      "  projectId: '{projectId}',\n",
                      "  query: '{field}:{value}',\n",
                      "};\n",
                      "\n",
                      "discovery.query(params)\n",
                      "  .then(response => {\n",
                      "    console.log(JSON.stringify(response.result, null, 2));\n",
                      "  })\n",
                      "  .catch(err => {\n",
                      "    console.log('error:', err);\n",
                      "  });\n",
                      ""
                    ]
                  }
                ]
              }
            ],
            "python": [
              {
                "name": "Example request",
                "example": [
                  {
                    "type": "code",
                    "source": [
                      "import json\n",
                      "from ibm_watson import DiscoveryV2\n",
                      "from ibm_cloud_sdk_core.authenticators import CloudPakForDataAuthenticator\n",
                      "\n",
                      "authenticator = CloudPakForDataAuthenticator(\n",
                      "                              '{username}',\n",
                      "                              '{password}',\n",
                      "                              'https://{cpd_cluster_host}{:port}/icp4d-api/v1/authorize',\n",
                      "                               disable_ssl_verification=True)\n",
                      "discovery = DiscoveryV2(\n",
                      "  version='2020-08-30',\n",
                      "  authenticator=authenticator\n",
                      ")\n",
                      "discovery.set_service_url('{https://{cpd_cluster_host}{:port}/discovery/{release}/instances/{instance_id}/api}')\n",
                      "\n",
                      "response = discovery.query(\n",
                      "  project_id='{project_id}',\n",
                      "  query='{field:value}'\n",
                      ").get_result()\n",
                      "\n",
                      "print(json.dumps(response, indent=2))\n",
                      ""
                    ]
                  }
                ]
              }
            ]
          }
        }
      }
    }
  },
  "components": {
    "securitySchemes": {
      "basicAuth": {
        "type": "http",
        "scheme": "basic"
      },
      "bearerAuth": {
        "type": "http",
        "scheme": "bearer"
      }
    },
    "parameters": {
      "collectionIdsParam": {
        "name": "collection_ids",
        "in": "query",
        "description": "Comma separated list of the collection IDs. If this parameter is not specified, all collections in the project are used.",
        "schema": {
          "type": "array",
          "items": {
            "type": "string"
          }
        }
      },
      "collectionIdParam": {
        "name": "collection_id",
        "in": "path",
        "description": "The ID of the collection.",
        "required": true,
        "schema": {
          "type": "string",
          "minLength": 1,
          "maxLength": 255,
          "pattern": "^[a-zA-Z0-9_-]*$"
        }
      },
      "documentIdParam": {
        "name": "document_id",
        "in": "path",
        "description": "The ID of the document.",
        "required": true,
        "schema": {
          "type": "string",
          "minLength": 1,
          "maxLength": 255,
          "pattern": "^[a-zA-Z0-9_-]*$"
        }
      },
      "acFieldParam": {
        "name": "field",
        "in": "query",
        "description": "The field in the result documents that autocompletion suggestions are identified from.",
        "schema": {
          "type": "string"
        }
      },
      "acPrefixParam": {
        "name": "prefix",
        "in": "query",
        "description": "The prefix to use for autocompletion. For example, the prefix `Ho` could autocomplete to `hot`, `housing`, or `how`.",
        "required": true,
        "schema": {
          "type": "string"
        }
      },
      "acCountParam": {
        "name": "count",
        "in": "query",
        "description": "The number of autocompletion suggestions to return.",
        "schema": {
          "type": "integer",
          "default": 5
        }
      },
      "filterParam": {
        "name": "filter",
        "in": "query",
        "description": "Searches for documents that match the Discovery Query Language criteria that is specified as input. Filter calls are cached and are faster than query calls because the results are not ordered by relevance. When used with the `aggregation`, `query`, or `natural_language_query` parameters, the `filter` parameter runs first. This parameter is useful for limiting results to those that contain specific metadata values.",
        "schema": {
          "type": "string"
        }
      },
      "queryParam": {
        "name": "query",
        "in": "query",
        "description": "A query search that is written in the Discovery Query Language and returns all matching documents in your data set with full enrichments and full text, and with the most relevant documents listed first.",
        "schema": {
          "type": "string"
        }
      },
      "nlqParam": {
        "name": "natural_language_query",
        "in": "query",
        "description": "A natural language query that returns relevant documents by using training data and natural language understanding.",
        "schema": {
          "type": "string",
          "minLength": 1,
          "maxLength": 2048
        }
      },
      "countParam": {
        "name": "count",
        "in": "query",
        "description": "Number of results to return. The maximum for the **count** and **offset** values together in any one query is **10,000**",
        "schema": {
          "type": "integer",
          "default": 10
        }
      },
      "offsetParam": {
        "name": "offset",
        "in": "query",
        "description": "The number of query results to skip at the beginning. For example, if the total number of results that are returned is 10 and the offset is 8, it returns the last two results. The maximum for the **count** and **offset** values together in any one query is **10000**",
        "schema": {
          "type": "integer"
        }
      },
      "docCountParam": {
        "name": "count",
        "in": "query",
        "description": "The maximum number of documents to return. Up to 1,000 documents are returned by default. The maximum number allowed is 10,000.",
        "schema": {
          "type": "integer",
          "default": 1000
        }
      }
    },
    "requestBodies": {
      "QueryLarge": {
        "content": {
          "application/json": {
            "schema": {
              "$ref": "#/components/schemas/QueryLarge"
            }
          }
        },
        "description": "An object that represents the query to be submitted."
      }
    },
    "schemas": {
      "TableElementLocation": {
        "type": "object",
        "required": [
          "begin",
          "end"
        ],
        "description": "The numeric location of the identified element in the document, represented with two integers labeled `begin` and `end`.",
        "properties": {
          "begin": {
            "description": "The element's `begin` index.",
            "type": "integer",
            "format": "int64"
          },
          "end": {
            "description": "The element's `end` index.",
            "type": "integer",
            "format": "int64"
          }
        }
      },
      "QueryResponse": {
        "type": "object",
        "description": "A response that contains the documents and aggregations for the query.",
        "properties": {
          "matching_results": {
            "description": "The number of matching results for the query. Results that match due to a curation only are not counted in the total.",
            "type": "integer",
            "format": "int32"
          },
          "results": {
            "description": "Array of document results for the query.",
            "type": "array",
            "items": {
              "$ref": "#/components/schemas/QueryResult"
            }
          },
          "retrieval_details": {
            "$ref": "#/components/schemas/RetrievalDetails"
          },
          "suggested_query": {
            "type": "string",
            "description": "Suggested correction to the submitted **natural_language_query** value."
          },
          "suggested_refinements": {
            "description": "Array of suggested refinements. **Note**: The `suggested_refinements` parameter that identified dynamic facets from the data is deprecated.",
            "type": "array",
            "items": {
              "$ref": "#/components/schemas/QuerySuggestedRefinement"
            },
            "deprecated": true
          },
          "table_results": {
            "description": "Array of table results.",
            "type": "array",
            "items": {
              "$ref": "#/components/schemas/QueryTableResult"
            }
          },
          "passages": {
            "type": "array",
            "description": "Passages that best match the query from across all of the collections in the project.",
            "items": {
              "$ref": "#/components/schemas/QueryResponsePassage"
            }
          }
        },
        "example": {
          "matching_results": 24,
          "retrieval_details": {
            "document_retrieval_strategy": "untrained"
          },
          "results": [
            {
              "id": "watson-generated ID"
            }
          ]
        }
      },
      "QueryResult": {
        "type": "object",
        "description": "Result document for the specified query.",
        "required": [
          "document_id",
          "result_metadata"
        ],
        "properties": {
          "document_id": {
            "type": "string",
            "description": "The unique identifier of the document."
          },
          "metadata": {
            "type": "object",
            "description": "Metadata of the document.",
            "additionalProperties": true
          },
          "result_metadata": {
            "$ref": "#/components/schemas/QueryResultMetadata"
          },
          "document_passages": {
            "type": "array",
            "description": "Passages from the document that best matches the query.",
            "items": {
              "$ref": "#/components/schemas/QueryResultPassage"
            }
          }
        },
        "additionalProperties": {
          "type": "object",
          "description": "The remaining key-value pairs"
        }
      },
      "QueryResultMetadata": {
        "type": "object",
        "description": "Metadata of a query result.",
        "required": [
          "collection_id"
        ],
        "properties": {
          "document_retrieval_source": {
            "type": "string",
            "enum": [
              "search",
              "curation"
            ],
            "description": "The document retrieval source that produced this search result."
          },
          "collection_id": {
            "type": "string",
            "description": "The collection id associated with this training data set."
          },
          "confidence": {
            "type": "number",
            "format": "double",
            "description": "The confidence score for the given result. Calculated based on how relevant the result is estimated to be. confidence can range from `0.0` to `1.0`. The higher the number, the more relevant the document. The `confidence` value for a result was calculated using the model specified in the `document_retrieval_strategy` field of the result set. This field is only returned if the **natural_language_query** parameter is specified in the query."
          }
        }
      },
      "QueryResultPassage": {
        "description": "A passage query result.",
        "type": "object",
        "properties": {
          "passage_text": {
            "type": "string",
            "description": "The content of the extracted passage."
          },
          "start_offset": {
            "type": "integer",
            "format": "int32",
            "description": "The position of the first character of the extracted passage in the originating field."
          },
          "end_offset": {
            "type": "integer",
            "format": "int32",
            "description": "The position after the last character of the extracted passage in the originating field."
          },
          "field": {
            "type": "string",
            "description": "The label of the field from which the passage has been extracted."
          },
          "confidence": {
            "type": "number",
            "description": "Estimate of the probability that the passage is relevant.",
            "format": "double",
            "minimum": 0,
            "maximum": 1
          },
          "answers": {
            "type": "array",
            "description": "An arry of extracted answers to the specified query.",
            "items": {
              "$ref": "#/components/schemas/ResultPassageAnswer"
            }
          }
        }
      },
      "QueryResponsePassage": {
        "description": "A passage query response.",
        "type": "object",
        "properties": {
          "passage_text": {
            "type": "string",
            "description": "The content of the extracted passage."
          },
          "passage_score": {
            "type": "number",
            "format": "double",
            "description": "The confidence score of the passage's analysis. A higher score indicates greater confidence. The score is used to rank the passages from all documents and is returned only if **passages.per_document** is `false`."
          },
          "document_id": {
            "type": "string",
            "description": "The unique identifier of the ingested document."
          },
          "collection_id": {
            "type": "string",
            "description": "The unique identifier of the collection."
          },
          "start_offset": {
            "type": "integer",
            "format": "int32",
            "description": "The position of the first character of the extracted passage in the originating field."
          },
          "end_offset": {
            "type": "integer",
            "format": "int32",
            "description": "The position after the last character of the extracted passage in the originating field."
          },
          "field": {
            "type": "string",
            "description": "The label of the field from which the passage has been extracted."
          },
          "confidence": {
            "type": "number",
            "description": "An estimate of the probability that the passage is relevant.",
            "format": "double",
            "minimum": 0,
            "maximum": 1
          },
          "answers": {
            "type": "array",
            "description": "An array of extracted answers to the specified query.",
            "items": {
              "$ref": "#/components/schemas/ResultPassageAnswer"
            }
          }
        }
      },
      "QueryTableResult": {
        "type": "object",
        "description": "A tables whose content or context match a search query.",
        "properties": {
          "table_id": {
            "type": "string",
            "description": "The identifier for the retrieved table."
          },
          "source_document_id": {
            "type": "string",
            "description": "The identifier of the document the table was retrieved from."
          },
          "collection_id": {
            "type": "string",
            "description": "The identifier of the collection the table was retrieved from."
          },
          "table_html": {
            "type": "string",
            "description": "HTML snippet of the table info."
          },
          "table_html_offset": {
            "type": "integer",
            "description": "The offset of the table html snippet in the original document html."
          },
          "table": {
            "$ref": "#/components/schemas/TableResultTable"
          }
        }
      },
      "QuerySuggestedRefinement": {
        "type": "object",
        "description": "A suggested additional query term or terms user to filter results. **Note**: The `suggested_refinements` parameter is deprecated.",
        "properties": {
          "text": {
            "type": "string",
            "description": "The text used to filter."
          }
        },
        "deprecated": true
      },
      "TableHeaders": {
        "type": "object",
        "description": "The contents of the current table's header.",
        "properties": {
          "cell_id": {
            "type": "string",
            "description": "The unique ID of the cell in the current table."
          },
          "location": {
            "type": "object",
            "description": "The location of the table header cell in the current table as defined by its `begin` and `end` offsets, respectfully, in the input document."
          },
          "text": {
            "type": "string",
            "description": "The textual contents of the cell from the input document without associated markup content."
          },
          "row_index_begin": {
            "type": "integer",
            "format": "int64",
            "description": "The `begin` index of this cell's `row` location in the current table."
          },
          "row_index_end": {
            "type": "integer",
            "format": "int64",
            "description": "The `end` index of this cell's `row` location in the current table."
          },
          "column_index_begin": {
            "type": "integer",
            "format": "int64",
            "description": "The `begin` index of this cell's `column` location in the current table."
          },
          "column_index_end": {
            "type": "integer",
            "format": "int64",
            "description": "The `end` index of this cell's `column` location in the current table."
          }
        }
      },
      "TableRowHeaders": {
        "type": "object",
        "description": "Row-level cells, each applicable as a header to other cells in the same row as itself, of the current table.",
        "properties": {
          "cell_id": {
            "type": "string",
            "description": "The unique ID of the cell in the current table."
          },
          "location": {
            "$ref": "#/components/schemas/TableElementLocation"
          },
          "text": {
            "type": "string",
            "description": "The textual contents of this cell from the input document without associated markup content."
          },
          "text_normalized": {
            "type": "string",
            "description": "If you provide customization input, the normalized version of the cell text according to the customization; otherwise, the same value as `text`."
          },
          "row_index_begin": {
            "type": "integer",
            "format": "int64",
            "description": "The `begin` index of this cell's `row` location in the current table."
          },
          "row_index_end": {
            "type": "integer",
            "format": "int64",
            "description": "The `end` index of this cell's `row` location in the current table."
          },
          "column_index_begin": {
            "type": "integer",
            "format": "int64",
            "description": "The `begin` index of this cell's `column` location in the current table."
          },
          "column_index_end": {
            "type": "integer",
            "format": "int64",
            "description": "The `end` index of this cell's `column` location in the current table."
          }
        }
      },
      "TableKeyValuePairs": {
        "type": "object",
        "description": "Key-value pairs detected across cell boundaries.",
        "properties": {
          "key": {
            "$ref": "#/components/schemas/TableCellKey"
          },
          "value": {
            "$ref": "#/components/schemas/TableCellValue"
          }
        }
      },
      "TableCellKey": {
        "type": "object",
        "description": "A key in a key-value pair.",
        "properties": {
          "cell_id": {
            "type": "string",
            "description": "The unique ID of the key in the table."
          },
          "location": {
            "$ref": "#/components/schemas/TableElementLocation"
          },
          "text": {
            "type": "string",
            "description": "The text content of the table cell without HTML markup."
          }
        }
      },
      "TableCellValue": {
        "type": "array",
        "description": "A list of values in a key-value pair.",
        "items": {
          "$ref": "#/components/schemas/TableCellValues"
        }
      },
      "TableCellValues": {
        "type": "object",
        "description": "A value in a key-value pair.",
        "properties": {
          "cell_id": {
            "type": "string",
            "description": "The unique ID of the value in the table."
          },
          "location": {
            "$ref": "#/components/schemas/TableElementLocation"
          },
          "text": {
            "type": "string",
            "description": "The text content of the table cell without HTML markup."
          }
        }
      },
      "TableBodyCells": {
        "type": "object",
        "description": "Cells that are not table header, column header, or row header cells",
        "properties": {
          "cell_id": {
            "type": "string",
            "description": "The unique ID of the cell in the current table."
          },
          "location": {
            "$ref": "#/components/schemas/TableElementLocation"
          },
          "text": {
            "type": "string",
            "description": "The textual contents of this cell from the input document without associated markup content."
          },
          "row_index_begin": {
            "type": "integer",
            "format": "int64",
            "description": "The `begin` index of this cell's `row` location in the current table."
          },
          "row_index_end": {
            "type": "integer",
            "format": "int64",
            "description": "The `end` index of this cell's `row` location in the current table."
          },
          "column_index_begin": {
            "type": "integer",
            "format": "int64",
            "description": "The `begin` index of this cell's `column` location in the current table."
          },
          "column_index_end": {
            "type": "integer",
            "format": "int64",
            "description": "The `end` index of this cell's `column` location in the current table."
          },
          "row_header_ids": {
            "description": "A list of table row header ids",
            "type": "array",
            "items": {
              "$ref": "#/components/schemas/TableRowHeaderIds"
            }
          },
          "row_header_texts": {
            "description": "A list of table row header texts",
            "type": "array",
            "items": {
              "$ref": "#/components/schemas/TableRowHeaderTexts"
            }
          },
          "row_header_texts_normalized": {
            "description": "A list of table row header texts normalized",
            "type": "array",
            "items": {
              "$ref": "#/components/schemas/TableRowHeaderTextsNormalized"
            }
          },
          "column_header_ids": {
            "description": "A list of table column header ids",
            "type": "array",
            "items": {
              "$ref": "#/components/schemas/TableColumnHeaderIds"
            }
          },
          "column_header_texts": {
            "description": "A list of table column header texts",
            "type": "array",
            "items": {
              "$ref": "#/components/schemas/TableColumnHeaderTexts"
            }
          },
          "column_header_texts_normalized": {
            "description": "A list of table column header texts normalized",
            "type": "array",
            "items": {
              "$ref": "#/components/schemas/TableColumnHeaderTextsNormalized"
            }
          },
          "attributes": {
            "description": "A list of document attributes",
            "type": "array",
            "items": {
              "$ref": "#/components/schemas/DocumentAttribute"
            }
          }
        }
      },
      "DocumentAttribute": {
        "type": "object",
        "description": "List of document attributes",
        "properties": {
          "type": {
            "type": "string",
            "description": "The type of attribute."
          },
          "text": {
            "type": "string",
            "description": "The text associated with the attribute."
          },
          "location": {
            "$ref": "#/components/schemas/TableElementLocation"
          }
        }
      },
      "TableRowHeaderIds": {
        "type": "object",
        "description": "An array of values, each being the `id` value of a row header that is applicable to this body cell.",
        "properties": {
          "id": {
            "type": "string",
            "description": "The `id` values of a row header."
          }
        }
      },
      "TableRowHeaderTexts": {
        "type": "object",
        "description": "An array of values, each being the `text` value of a row header that is applicable to this body cell.",
        "properties": {
          "text": {
            "type": "string",
            "description": "The `text` value of a row header."
          }
        }
      },
      "TableRowHeaderTextsNormalized": {
        "type": "object",
        "description": "If you provide customization input, the normalized version of the row header texts according to the customization; otherwise, the same value as `row_header_texts`.",
        "properties": {
          "text_normalized": {
            "type": "string",
            "description": "The normalized version of a row header text."
          }
        }
      },
      "TableColumnHeaderIds": {
        "type": "object",
        "description": "An array of values, each being the `id` value of a column header that is applicable to the current cell.",
        "properties": {
          "id": {
            "type": "string",
            "description": "The `id` value of a column header."
          }
        }
      },
      "TableColumnHeaderTexts": {
        "type": "object",
        "description": "An array of values, each being the `text` value of a column header that is applicable to the current cell.",
        "properties": {
          "text": {
            "type": "string",
            "description": "The `text` value of a column header."
          }
        }
      },
      "TableColumnHeaderTextsNormalized": {
        "type": "object",
        "description": "If you provide customization input, the normalized version of the column header texts according to the customization; otherwise, the same value as `column_header_texts`.",
        "properties": {
          "text_normalized": {
            "type": "string",
            "description": "The normalized version of a column header text."
          }
        }
      },
      "TableColumnHeaders": {
        "type": "object",
        "description": "Column-level cells, each applicable as a header to other cells in the same column as itself, of the current table.",
        "properties": {
          "cell_id": {
            "type": "string",
            "description": "The unique ID of the cell in the current table."
          },
          "location": {
            "type": "object",
            "description": "The location of the column header cell in the current table as defined by its `begin` and `end` offsets, respectfully, in the input document."
          },
          "text": {
            "type": "string",
            "description": "The textual contents of this cell from the input document without associated markup content."
          },
          "text_normalized": {
            "type": "string",
            "description": "If you provide customization input, the normalized version of the cell text according to the customization; otherwise, the same value as `text`."
          },
          "row_index_begin": {
            "type": "integer",
            "format": "int64",
            "description": "The `begin` index of this cell's `row` location in the current table."
          },
          "row_index_end": {
            "type": "integer",
            "format": "int64",
            "description": "The `end` index of this cell's `row` location in the current table."
          },
          "column_index_begin": {
            "type": "integer",
            "format": "int64",
            "description": "The `begin` index of this cell's `column` location in the current table."
          },
          "column_index_end": {
            "type": "integer",
            "format": "int64",
            "description": "The `end` index of this cell's `column` location in the current table."
          }
        }
      },
      "TableResultTable": {
        "type": "object",
        "description": "Full table object retrieved from Table Understanding Enrichment.",
        "properties": {
          "location": {
            "$ref": "#/components/schemas/TableElementLocation"
          },
          "text": {
            "type": "string",
            "description": "The textual contents of the current table from the input document without associated markup content."
          },
          "section_title": {
            "$ref": "#/components/schemas/TableTextLocation"
          },
          "title": {
            "$ref": "#/components/schemas/TableTextLocation"
          },
          "table_headers": {
            "type": "array",
            "description": "An array of table-level cells that apply as headers to all the other cells in the current table.",
            "items": {
              "$ref": "#/components/schemas/TableHeaders"
            }
          },
          "row_headers": {
            "type": "array",
            "description": "An array of row-level cells, each applicable as a header to other cells in the same row as itself, of the current table.",
            "items": {
              "$ref": "#/components/schemas/TableRowHeaders"
            }
          },
          "column_headers": {
            "type": "array",
            "description": "An array of column-level cells, each applicable as a header to other cells in the same column as itself, of the current table.",
            "items": {
              "$ref": "#/components/schemas/TableColumnHeaders"
            }
          },
          "key_value_pairs": {
            "type": "array",
            "description": "An array of key-value pairs identified in the current table.",
            "items": {
              "$ref": "#/components/schemas/TableKeyValuePairs"
            }
          },
          "body_cells": {
            "type": "array",
            "description": "An array of cells that are neither table header nor column header nor row header cells, of the current table with corresponding row and column header associations.",
            "items": {
              "$ref": "#/components/schemas/TableBodyCells"
            }
          },
          "contexts": {
            "type": "array",
            "description": "An array of lists of textual entries across the document related to the current table being parsed.",
            "items": {
              "$ref": "#/components/schemas/TableTextLocation"
            }
          }
        }
      },
      "TableTextLocation": {
        "type": "object",
        "description": "Text and associated location within a table.",
        "properties": {
          "text": {
            "type": "string",
            "description": "The text retrieved."
          },
          "location": {
            "$ref": "#/components/schemas/TableElementLocation"
          }
        }
      },
      "ErrorResponse": {
        "type": "object",
        "description": "Error response information",
        "required": [
          "code",
          "error"
        ],
        "properties": {
          "code": {
            "description": "The HTTP error status code.",
            "type": "integer",
            "format": "int32",
            "minimum": 100,
            "maximum": 600
          },
          "error": {
            "description": "A message describing the error.",
            "type": "string"
          }
        }
      },
      "QueryLarge": {
        "type": "object",
        "description": "Object that describes a long query.",
        "properties": {
          "collection_ids": {
            "type": "array",
            "items": {
              "type": "string"
            },
            "description": "A comma-separated list of collection IDs to be queried against."
          },
          "filter": {
            "type": "string",
            "description": "Searches for documents that match the Discovery Query Language criteria that is specified as input. Filter calls are cached and are faster than query calls because the results are not ordered by relevance. When used with the **aggregation**, **query**, or **natural_language_query** parameters, the **filter** parameter runs first. This parameter is useful for limiting results to those that contain specific metadata values."
          },
          "query": {
            "type": "string",
            "description": "A query search that is written in the Discovery Query Language and returns all matching documents in your data set with full enrichments and full text, and with the most relevant documents listed first. Use a query search when you want to find the most relevant search results."
          },
          "natural_language_query": {
            "type": "string",
            "description": "A natural language query that returns relevant documents by using training data and natural language understanding.",
            "minLength": 1,
            "maxLength": 2048
          },
          "aggregation": {
            "type": "string",
            "description": "An aggregation search that returns an exact answer by combining query search with filters. Useful for applications to build lists, tables, and time series. For more information about the supported types of aggregations, see the [Discovery documentation](https://cloud.ibm.com/docs/discovery-data?topic=discovery-data-query-aggregations)."
          },
          "count": {
            "type": "integer",
            "description": "Number of results to return."
          },
          "return": {
            "type": "array",
            "items": {
              "type": "string"
            },
            "description": "A list of the fields in the document hierarchy to return. You can specify both root-level (`text`) and nested (`extracted_metadata.filename`) fields. If this parameter is an empty list, then all fields are returned.",
            "x-item-name": "return_field"
          },
          "offset": {
            "type": "integer",
            "description": "The number of query results to skip at the beginning. For example, if the total number of results that are returned is 10 and the offset is 8, it returns the last two results."
          },
          "sort": {
            "type": "string",
            "description": "A comma-separated list of fields in the document to sort on. You can optionally specify a sort direction by prefixing the field with `-` for descending or `+` for ascending. Ascending is the default sort direction if no prefix is specified."
          },
          "highlight": {
            "type": "boolean",
            "description": "When `true`, a highlight field is returned for each result that contains fields that match the query. The matching query terms are emphasized with surrounding `<em></em>` tags. This parameter is ignored if **passages.enabled** and **passages.per_document** are `true`, in which case passages are returned for each document instead of highlights."
          },
          "spelling_suggestions": {
            "type": "boolean",
            "description": "When `true` and the **natural_language_query** parameter is used, the **natural_language_query** parameter is spell checked. The most likely correction is returned in the **suggested_query** field of the response (if one exists)."
          },
          "table_results": {
            "type": "object",
            "description": "Configuration for table retrieval",
            "properties": {
              "enabled": {
                "type": "boolean",
                "description": "Whether to enable table retrieval."
              },
              "count": {
                "type": "integer",
                "description": "Maximum number of tables to return."
              }
            }
          },
          "suggested_refinements": {
            "type": "object",
            "description": "Configuration for suggested refinements.\n\n**Note**: The **suggested_refinements** parameter that identified dynamic facets from the data is deprecated.",
            "properties": {
              "enabled": {
                "type": "boolean",
                "description": "Whether to perform suggested refinements."
              },
              "count": {
                "type": "integer",
                "description": "Maximum number of suggested refinements texts to be returned. The maximum is `100`.",
                "maximum": 100,
                "minimum": 1
              }
            },
            "deprecated": true
          },
          "passages": {
            "type": "object",
            "description": "Configuration for passage retrieval.",
            "properties": {
              "enabled": {
                "type": "boolean",
                "description": "A passages query that returns the most relevant passages from the results."
              },
              "per_document": {
                "type": "boolean",
                "description": "If `true`, ranks the documents by document quality, and then returns the highest-ranked passages per document in a `document_passages` field for each document entry in the results list of the response.\n\nIf `false`, ranks the passages from all of the documents by passage quality regardless of the document quality and returns them in a separate `passages` field in the response."
              },
              "max_per_document": {
                "type": "integer",
                "description": "Maximum number of passages to return per document in the result. Ignored if **passages.per_document** is `false`."
              },
              "fields": {
                "type": "array",
                "items": {
                  "type": "string"
                },
                "description": "A list of fields to extract passages from. If this parameter is an empty list, then all root-level fields are included."
              },
              "count": {
                "type": "integer",
                "description": "The maximum number of passages to return. Ignored if **passages.per_document** is `true`.",
                "maximum": 400
              },
              "characters": {
                "type": "integer",
                "description": "The approximate number of characters that any one passage will have.",
                "maximum": 2000,
                "minimum": 50
              },
              "find_answers": {
                "type": "boolean",
                "description": "When true, `answer` objects are returned as part of each passage in the query results. The primary difference between an `answer` and a `passage` is that the length of a passage is defined by the query, where the length of an `answer` is calculated by Discovery based on how much text is needed to answer the question.\n\nThis parameter is ignored if passages are not enabled for the query, or no **natural_language_query** is specified.\n\nIf the **find_answers** parameter is set to `true` and **per_document** parameter is also set to `true`, then the document search results and the passage search results within each document are reordered using the answer confidences. The goal of this reordering is to place the best answer as the first answer of the first passage of the first document. Similarly, if the **find_answers** parameter is set to `true` and **per_document** parameter is set to `false`, then the passage search results are reordered in decreasing order of the highest confidence answer for each document and passage.\n\nThe **find_answers** parameter is available only on managed instances of Discovery.",
                "default": false
              },
              "max_answers_per_passage": {
                "type": "integer",
                "description": "The number of `answer` objects to return per passage if the **find_answers** parmeter is specified as `true`.",
                "default": 1
              }
            }
          },
          "similar": {
            "type": "object",
            "description": "Finds results from documents that are similar to documents of interest. Use this parameter to add a *More like these* function to your search. You can include this parameter with or without a **query**, **filter** or **natural_language_query** parameter.",
            "properties": {
              "enabled": {
                "type": "boolean",
                "description": "When `true`, includes documents in the query results that are similar to documents you specify.",
                "default": false
              },
              "document_ids": {
                "type": "array",
                "description": "The list of documents of interest. Required if **enabled** is `true`.",
                "items": {
                  "type": "string"
                }
              },
              "fields": {
                "type": "array",
                "description": "Looks for similarities in the specified subset of fields in the documents. If not specified, all of the document fields are used.",
                "items": {
                  "type": "string"
                }
              }
            }
          }
        }
      },
      "RetrievalDetails": {
        "type": "object",
        "description": "An object contain retrieval type information.",
        "properties": {
          "document_retrieval_strategy": {
            "type": "string",
            "description": "Identifies the document retrieval strategy used for this query. `relevancy_training` indicates that the results were returned using a relevancy trained model. \n\n**Note**: In the event of trained collections being queried, but the trained model is not used to return results, the **document_retrieval_strategy** is listed as `untrained`.",
            "enum": [
              "untrained",
              "relevancy_training"
            ]
          }
        }
      },
      "ResultPassageAnswer": {
        "type": "object",
        "description": "Object that contains a potential answer to the specified query.",
        "properties": {
          "answer_text": {
            "type": "string",
            "description": "Answer text for the specified query as identified by Discovery."
          },
          "start_offset": {
            "type": "integer",
            "format": "int32",
            "description": "The position of the first character of the extracted answer in the originating field."
          },
          "end_offset": {
            "type": "integer",
            "format": "int32",
            "description": "The position after the last character of the extracted answer in the originating field."
          },
          "confidence": {
            "type": "number",
            "description": "An estimate of the probability that the answer is relevant.",
            "format": "double",
            "minimum": 0,
            "maximum": 1
          }
        }
      }
    }
  }
}

openapi spec for ibm rpa( robotic process automation)
{
  "openapi": "3.0.3",
  "info": {
    "title": "Swagger IBM RPA Asynch Server",
    "description": "This is a Swagger representing IBM RPA Server for Asynch",
    "version": "1.0.11"
  },
  "externalDocs": {
    "description": "Find out more about Swagger",
    "url": "http://swagger.io"
  },
  "servers": [
    {
      "url": "https://{region}.functions.cloud.ibm.com/api/v1/web/{cloud_function_id}/default",
      "variables": {
        "region": {
          "default": "us-south",
          "description": "This value is based on which region your cloud functions are located."
        },
        "cloud_function_id": {
          "default": "cloud_function_id"
        }
      }
    }
  ],
  "paths": {
    "/Start%20RPA%20Asynchronously.json": {
      "post": {
        "parameters": [
          {
            "name": "guid",
            "in": "query",
            "required": true,
            "description": "a unique identifier representing the cloud function namespace",
            "schema": {
              "type": "string"
            }
          }
        ],
        "summary": "Insert Item into IBM RPA",
        "description": "Insert Item into IBM RPA to kick off an instance",
        "operationId": "insertItem",
        "requestBody": {
          "description": "Payload to insert items into IBM RPA by means of Cloud Function",
          "content": {
            "application/json": {
              "schema": {
                "$ref": "#/components/schemas/InsertItem"
              }
            }
          }
        },
        "responses": {
          "200": {
            "description": "Successful operation",
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/InstanceOutput"
                }
              }
            }
          },
          "405": {
            "description": "Invalid input"
          }
        }
      }
    },
    "/Get%20RPA%20Asynchronous%20Status.json": {
      "post": {
        "parameters": [
          {
            "name": "guid",
            "in": "query",
            "required": true,
            "description": "a unique identifier representing the cloud function namespace",
            "schema": {
              "type": "string"
            }
          }
        ],
        "summary": "Get RPA Status",
        "description": "Get Status of an RPA Instance",
        "operationId": "getStatusOfRPAInstance",
        "requestBody": {
          "description": "Payload to get status of an item in IBM RPA by means of Cloud Function",
          "content": {
            "application/json": {
              "schema": {
                "$ref": "#/components/schemas/InstanceStatus"
              }
            }
          }
        },
        "responses": {
          "200": {
            "description": "Successful operation",
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/StatusOutput"
                }
              }
            }
          },
          "405": {
            "description": "Invalid input"
          }
        }
      }
    }
  },
  "components": {
    "schemas": {
      "InsertItem": {
        "type": "object",
        "properties": {
          "rpaInputParam1": {
            "type": "string",
            "description": "RPA First input Value"
          },
          "rpaInputParam2": {
            "type": "string",
            "description": "RPA Second input Value"
          }
        }
      },
      "InstanceStatus": {
        "type": "object",
        "properties": {
          "instanceID": {
            "type": "string",
            "description": "ID of Item"
          }
        }
      },
      "InstanceOutput": {
        "type": "object",
        "properties": {
          "rpaInstanceID": {
            "type": "string",
            "example": "0bO0vKrd00cTYYBZe0Po8ZxPevFiZXa4TrLj0UwZh8kSWAZNoU6bwoo-heffvABDspPvryNzMKzyU9waPrEuctkAhxiEgsRMqPzWVyZa1a-pMsqIXtZnb_F1KPi44JpkXfQyRe0QqFWn8a-IaeH2Ld874kWjg2Av0BrHKZ9SxGc4OgRNuTZDBxMKIg-27eFe79SxP870Pt-6jtNsQRpRuKtISdfRX9vktC0qa2Day8mmso_lHXhc5CowEVisUQ0eXLqLJM2Eb8YATyEIJfYkUNxgzMXue7HJ1u8lObGPlAdVO7DdIcoOX4MbSQjE4XLbIG5VY05BJT4Jys0c-c5yuVlXezLzDlU9e4Z7LVpywz7ZYwxCJKzMtEVZYO--YLGR8543G3AzH8CeZASHh5aTg8a-J3LVpW1tEbjspjCJXLW76pHsUhi3oCDbBqHxKLVIpuhJLcXErFh"
          }
        }
      },
      "StatusOutput": {
        "type": "object",
        "properties": {
          "rpaInstanceStatus": {
            "type": "object",
            "properties": {
              "rpaOutputVariable1": {
                "type": "string"
              },
              "rpaOutputVariable2": {
                "type": "string"
              }
            }
          }
        }
      }
    }
  }
}

how to write an open api spec
 v1.0
 
 OpenAPI Specification for Software Developers
 Preface
Hi there!
I hope you are doing good.
I’ve created this OpenAPI quick guide to help you with your everyday programming in API. You can ​use this as a reference document​ to check the steps involved in creating an OpenAPI specification. You can find the key points you need to consider for each step while defining an OpenAPI Specification.
OpenAPI Specification version used: ​3.0.0
OpenAPI is the most popular way to work with APIs. ​This downloadable resource is part of the OpenAPI(Swagger) Specification for Software Developers course​, which covers the essentials of OpenAPI concepts that any API programmer ​must ​know. ​Click here​ to know more about the companion course.
See you in the course video! .
  2 ​Top
  
 OpenAPI Specification for Software Developers
 Table of Contents
Preface
Table of Contents
Step 1: Add the OpenAPI Version
Step 2: Add Metadata
Step 3: Add Additional References
Step 4: Add Server Details
Step 5: Add Tags to Group API Operations
Step 6: Add Paths (Part 1: Path Details)
Step 6: Add Paths (Part 2: Operations)
Step 6: Add Paths (Part 3: Parameters)
Step 6: Add Paths (Part 4: Responses)
Step 6: Add Paths (Part 5: Request Body)
Step 7: Add Components (Part 1: Components Object) Step 7: Add Components (Part 2: Schema)
Step 7: Add Components (Part 3: Response)
Step 8: Add Security
                 3 ​Top
  
 OpenAPI Specification for Software Developers
 Step 1: Add the OpenAPI Version
As the first step, you need to include​ ​information about the OpenAPI version itself.
The ​version you use defines the structure​ of the API definitions that you will be creating. Depending upon the OpenAPI version you are targeting, the behavior will change.
You use the keyword ​openapi​ ​to enter the version. This is a ​MANDATORY ​parameter. OpenAPI Specification is shortly referred to as OAS.
Fields: None
● As of this writing, the latest major version is 3.0.0.
● Note that from 3.1, the OpenAPI Consortium has decided to change the versioning
format from the current x.x.x to x.x format.
● Minor versions are being released​ then and there such as 3.0.1, 3.0.2, 3.0.3.
However, they generally improve readability and accuracy without any change in the
behavior of the spec.
● The tools that support OAS 3.0 SHOULD be compatible with all OAS 3.0.* versions.
Similarly for the minor version.
● Do not worry, you just need to ​concentrate on the major version. ​For example, 3.*.*.
  4 ​Top
  
 OpenAPI Specification for Software Developers
 Step 2: Add Metadata
In this step, you include ​details about the API ​using the ​info s​ ection. In short, you provide the metadata about the API. The metadata may be used by the clients or editing or documentation generation tools.
You use the ​info k​ eyword to add metadata to an API. This is a ​MANDATORY ​parameter. Fields: title, description, termsOfService, contact, license, version.
● The ​title ​field is used to enter the main title of the API document whereas the description ​field is used to enter a good description for the document. Note that the ​title field is ​MANDATORY.
● Moreover, you include the version of your OpenAPI Specification document using the version p​ arameter. It is a ​MANDATORY ​parameter. Note that you need to enclose the version within single quotes to treat it as a string. Eg. ‘1.0’ and not 1.0
● termsOfService​, ​optionally​, c​ an be used to enter the URL of your terms of service.
● In case you want to include contact details to contact for any discussion regarding this
API, you add it using the ​contact f​ ield. It takes name, URL, email. All are optional. The
contact details can refer to a specific person or an organization.
● Finally, the license information for the API can be added using the ​license ​field. It takes
a​ name (MANDATORY) ​and U​ RL​.
  5 ​Top
  
 OpenAPI Specification for Software Developers
 Step 3: Add Additional References
In Step 3, you add external documentation resources to the API documentation.
Use the keyword ​externalDocs​ ​to add optional external documentation references. It allows referencing an external resource for extended documentation.
Fields: description and URL.
● The ​description p​ arameter can be used to provide an optional description whereas the url ​parameter holds the actual external documentation address.
● url ​is a ​MANDATORY ​parameter if externalDocs is added to the document.
  6 ​Top
  
 OpenAPI Specification for Software Developers
 Step 4: Add Server Details
Next, you add the server details that will be used by the clients to connect to your web services.
Use the keyword ​servers t​ o add optional server details to your API specification. The servers section specifies one or more servers that host the current API. You can define one or several servers, such as production and staging.
Fields: url, description, variables.
● url ​field holds the base URL for your server. All API paths are relative to the server URL. Eg. For /users path, the final URL may be ​http://api.example.com/v1/users​. ​url​ is a MANDATORY​ field.
● The optional ​description p​ arameter can be used to provide a description.
● The optional ​variables ​field​ ​is used to create a map between the name and its value. It
is used to make the URL dynamic so that the user can pass value during execution. Each variable can have ​3 properties - enum, default, description​.
○ enum -​ a list of values. Note that in YAML, an array is represented using hyphens (-).
○ default ​- the default value to use if not passed.
○ description -​ description for the server variable.
   7 ​Top
  
 OpenAPI Specification for Software Developers
 Step 5: Add Tags to Group API Operations
You add tags to group the API operations that are added to the API documentation.
Tags can be ​used for the logical grouping of operations by resources​ or any other qualifier. For eg., you can group all course-related operations such as add course, delete a course, update course, etc to a tag named ​course.​ In such a case, those operations will be shown grouped under that tag.
They are referred to while defining an operation. You use the ​tags k​ eyword to define a tag.
   8 ​Top
  
 OpenAPI Specification for Software Developers
 It is ​NOT​ mandatory to have tags defined. In such a case, the operations will be grouped under default.​
Fields: name, description, externalDocs
● The ​name ​field holds the name of the tag.
● description ​fields hold an optional description.
● externalDocs h​ olds optional external documentation references for this tag.
 9 ​Top
  
 OpenAPI Specification for Software Developers
 Step 6: Add Paths (Part 1: Path Details)
In this part, you define a new path that will hold various operations for a resource.
Paths define the actual path of the resource, its operations, requests, responses, and so on. Paths are defined using the ​paths k​ eyword. It is then followed by the actual path.
Note that the path is a​ relative path ​to the individual endpoints. The path is appended to the URL from the ​servers ​section to construct the full URL.
The paths can contain ​parameters t​ hat are dynamic and passed while calling this API. Parameters are covered in a forthcoming chapter.
Fields: summary, description, get, post, delete, put, options, head, patch, trace, servers, parameters.
● The optional ​summary ​field captures a nice title for the path. I ​RECOMMEND a​ dding.
● Optional ​description f​ ields hold an optional description. I ​RECOMMEND ​adding.
    10 ​Top
  
 OpenAPI Specification for Software Developers
 Step 6: Add Paths (Part 2: Operations)
In this part, you add operations such as GET, POST, DELETE, etc for the ​paths t​ hat you defined in the previous part.
To define an operation, use the keyword:
● get,​ to return some details of a resource from the system.
● post​, to add an item to the resource collection.
● put,​toupdateanexistingitem.
● delete,​todeleteanexistingitem
Fields: tags, summary, description, externalDocs, operationId, parameters, requestBody, responses, deprecated, and so on.
  ● Usetheoptional​tagsf​ieldtogroupthisoperationunderarelatedtopic.I​RECOMMEND adding.
● The optional ​summary ​field captures a nice title for the path. I ​RECOMMEND a​ dding.
● Optional ​description f​ ields hold an optional description. I ​RECOMMEND ​adding.
● Use the ​operationId f​ ield to provide a unique identifier across this API for the current
operation.
 11 ​Top
  
 OpenAPI Specification for Software Developers
 Step 6: Add Paths (Part 3: Parameters) In this part, we add parameters to the path, if needed.
 Use the keyword ​parameter ​to define parameters for an operation. A parameter is uniquely determined by its name and location.
The location information is entered using the ‘​in’ ​field. The possible locations are:
● path​ -​ Parameter value is part of the operation's URL. Eg, in /items/{itemId}, the path parameter is itemId.
● query -​ Parameters that are appended to the URL.
Eg. /colleges?sortOrder=xxx
The parameter sortOrder is located in the query parameter.
● header -​ They are part of the header of the request.
● cookie ​- Part of the cookie of the API.
Fields: name, in, description, required, deprecated, schema/content, example, etc.
● name ​- The name of the parameter. It is a ​MANDATORY ​field.
● in ​- The location of the parameter. Possible values are ​query, header, path, ​and ​cookie.​
It is a ​MANDATORY ​field.
● description ​- Optional description for this parameter.
● required ​- Used to mention whether this parameter is mandatory. If the parameter
location is "path", this property is ​MANDATORY ​and its value ​MUST ​be ​true.​
● deprecated ​- Used to inform the clients no avoid using this parameter.
  12 ​Top
  
 OpenAPI Specification for Software Developers
 ● schema/content​ - Used to define the type of the parameter. It is a ​MANDATORY ​field. Also,
● example​ - Provide an example value for the parameter.
● examples​ - Provide one or more examples for the parameter.
   13 ​Top
  
 OpenAPI Specification for Software Developers
 Step 6: Add Paths (Part 4: Responses) In this part, you add responses to the operation of a path.
Use the keyword ​responses t​ o define responses for the operation. It contains all the responses expected from an operation. You define one response for an HTTP Status Code.
An operation ​MUST ​have at least one response defined. Fields: HTTP Response Code, default
● default​: Used to refer ‘all’ HTTP codes that aren’t captured in the specification. For any HTTP status code that isn’t defined in the specification, the default response body will be considered.
● HTTP Response Code:​ Define the response for one or more HTTP status codes.
○ You can use X to represent a range of response codes. Note that it is a capital X
character. Eg, 4XX represents all response codes between 400 and 499.
○ Each response contains a ​description ​and c​ ontent ​fields.
     14 ​Top
  
 OpenAPI Specification for Software Developers
 Step 6: Add Paths (Part 5: Request Body)
In this part, you add a request body for the operations that require some input values to be passed.
Use the keyword ​requestBody ​to define requests for an operation.
​Fields: description, required, content
● description ​- Optional description for this parameter.
● required ​- Used to mention whether this parameter is mandatory.
● content ​- The content of the request body. It is a ​MANDATORY ​field.
   15 ​Top
  
 OpenAPI Specification for Software Developers
 Step 7: Add Components (Part 1: Components Object)
In the programming world, as a best practice, it is always recommended to define once and use it in many places. This way you need to make the changes only in a single place. You will have better control.
While writing OpenAPI Specification, you can follow the same principle - ​define the types once and reference it in multiple places​. Components are used to achieve this in OpenAPI.
You use the keyword ​components ​to define reusable types within an OpenAPI Specification. It can be used to include schemas, responses, parameters, examples, request bodies, headers, etc.
Fields: schemas, responses, parameters, examples, requestBodies, headers, securitySchemes, links, callbacks
Note that the objects defined within the components object will not affect​ the API unless they are explicitly referenced​ from other places in the API documentation.
  16 ​Top
  
 OpenAPI Specification for Software Developers
 Step 7: Add Components (Part 2: Schema)
In this part, you add schema to the components section so that the types defined can be reused throughout the API.
First, you need to give a name for the type. Next, you define the type using various fields.
Fields: type, format, minimum, maximum, required, enum, default, description, example ● type ​- The type of the schema that you are defining. If it’s a user-defined type, then use
‘​object’.
   17 ​Top
  
 OpenAPI Specification for Software Developers
 ● format​ - The format used for the above type (if applicable).
● description​ - Description of this type.
● properties ​- One or more fields to define the object schema type.
Note that each of the property value itself is another schema type!
● minimum ​- For applicable types, mention the minimum value it can take.
● maximum ​- For applicable types, mention the maximum value to allow.
● required ​- Mark the list of mandatory properties.
● example​ - Provide example values for the type that is being defined.
     18 ​Top
  
 OpenAPI Specification for Software Developers
 Step 7: Add Components (Part 3: Response)
In this part, you add the response body to the components section so that it can be reused throughout the API.
Similar to schemas, you can define responses that can be defined once and used multiple times.
Use the keyword ​responses ​to create reusable types under the ​components ​section.
You can add any number of responses. For each response body, you provide a name followed by a set of field values.
Fields: description, content
● description​ - A description for the response body. For simple responses, you can
include only the description.
● content ​- The content for the response body. It follows the structure for defining a content body.
Note that the response body can refer to any types defined within the component section such as schemas, etc. In the above example, the Course type defined in the ​schemas ​section is referenced in the ​responses ​section.
     19 ​Top
  
 OpenAPI Specification for Software Developers
 Step 8: Add Security
In this step, you add the security mechanism to the API documentation.
Use the keyword ​securitySchemes t​ o define one or more security schemes for your API specification. It should be located under the ​components ​section.
Fields: description, type, scheme
● description ​- Optional description for this parameter.
● type ​- The type of security scheme. OpenAPI Specification supported security schemes:
○ HTTP
○ API Key
○ OAuth 2
○ OpenID
● scheme ​- The individual scheme for the above ​type​.
○ Basic, Bearer, etc. for HTTP type.
The schemes defined can then be referred throughout the API document such as in the security section and operation section.
If required, individual operations can override this global definition by including them under their operation definition.
    20 ​Top
  
 OpenAPI Specification for Software Developers
  In the above example, irrespective of the global security scheme mentioned, the delete operation for the individual Course path will use Bearer authentication.
 21 ​Top
  
 OpenAPI Specification for Software Developers
 Thank you!
I hope this resource was helpful to you.
 22 ​Top
 


