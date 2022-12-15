# Test task for PHP Backend Developer

## Task

You need to create a system that is allowing to update (edit) any json-document. 
Client can create a draft of the document. Till the document is in draft, it can be updated anytime. Draft can be published, and once published, it can not be edited anymore.

## Requirements

|  tearm           | description                                             |
|------------------|---------------------------------------------------------|
| Estimation       | up to 8 hours                                           |
| Framework        | none                                                    |
| Other packages   | any                                                     |
| PHP version      | >=7.3                                                   |
| Db               | any relational                                          |

1. Publish your result in any git repo
2. Fill down README file how to execute the project
3. Follow code-style within the project
4. Strict typing is mandatory
5. First commit must be setting up and configuring the skeleton (don't finish the whole project just in 1 commit)
6. Report about difficulties, solutions, questions, and etc.

## API

- `POST` localhost/index.php?route=create - credting draft of the document
- `GET   localhost/index.php?route=view&id=id` - getting document by id
- `PATCH localhost/index.php?route=edit&id=id` - edit document
- `POST  localhost/index.php?route=publish&id=id` - publish document
- `GET   localhost/index.php?route=list&page=1&perPage=20` - get last document with pagination, sorting (new added are on the top)


- If document is not found, 404 NOT Found must be returned
- If document is already published, and user tries to update it, return 400.
- Try to publish arelady published document should return 200
- `PATCH` is sending in the body of JSON document, all fields except `payload` are ignored. If `payload` is not sent/defined, then return 400.

### Document object

```js
document = {
  id: "some-uuid-string",
  status: "draft|published",
  payload: Object,
  createAt: "iso-8601 date time with time zone",
  modifyAt: "iso-8601 date time with time zone"
}
```

## Example

### 1. Client is creating document

Request:

```http
POST /api/v1/document HTTP/1.1
accept: application/json
```

Response:

```http
HTTP/1.1 200 OK
content-type: application/json

{
    "document": {
        "id": "718ce61b-a669-45a6-8f31-32ba41f94784",
        "status": "draft",
        "payload": {},
        "createAt": "2018-09-01 20:00:00+07:00",
        "modifyAt": "2018-09-01 20:00:00+07:00"
    }
}
```

### 2. Client is editing document for the first time

Request:

```http
PATCH /api/v1/document/718ce61b-a669-45a6-8f31-32ba41f94784 HTTP/1.1
accept: application/json
content-type: application/json

{
    "document": {
        "payload": {
            "actor": "The fox",
            "meta": {
                "type": "quick",
                "color": "brown"
            },
            "actions": [
                {
                    "action": "jump over",
                    "actor": "lazy dog"
                }
            ]
        }
    }
}
```

Response:

```http
HTTP/1.1 200 OK
content-type: application/json

{
    "document": {
        "id": "718ce61b-a669-45a6-8f31-32ba41f94784",
        "status": "draft",
        "payload": {
            "actor": "The fox",
            "meta": {
                "type": "quick",
                "color": "brown"
            },
            "actions": [
                {
                    "action": "jump over",
                    "actor": "lazy dog"
                }
            ]
        },
        "createAt": "2018-09-01 20:00:00+07:00",
        "modifyAt": "2018-09-01 20:01:00+07:00"
    }
}
```

### 3. Client editing document

Request:

```http
PATCH /api/v1/document/718ce61b-a669-45a6-8f31-32ba41f94784 HTTP/1.1
accept: application/json
content-type: application/json

{
    "document": {
        "payload": {
            "meta": {
                "type": "cunning",
                "color": null
            },
            "actions": [
                {
                    "action": "eat",
                    "actor": "blob"
                },
                {
                    "action": "run away"
                }
            ]
        }
    }
}
```

Response:

```http
HTTP/1.1 200 OK
content-type: application/json

{
    "document": {
        "id": "718ce61b-a669-45a6-8f31-32ba41f94784",
        "status": "draft",
        "payload": {
            "actor": "The fox",
            "meta": {
                "type": "cunning",
            },
            "actions": [
                {
                    "action": "eat",
                    "actor": "blob"
                },
                {
                    "action": "run away"
                }
            ]
        },
        "createAt": "2018-09-01 20:00:00+07:00",
        "modifyAt": "2018-09-01 20:02:00+07:00"
    }
}
```

### 4. Client is publishing document

Request:

```http
POST /api/v1/document/718ce61b-a669-45a6-8f31-32ba41f94784/publish HTTP/1.1
accept: application/json
```

Response:

```http
HTTP/1.1 200 OK
content-type: application/json

{
    "document": {
        "id": "718ce61b-a669-45a6-8f31-32ba41f94784",
        "status": "published",
        "payload": {
            "actor": "The fox",
            "meta": {
                "type": "cunning",
            },
            "actions": [
                {
                    "action": "eat",
                    "actor": "blob"
                },
                {
                    "action": "run away"
                }
            ]
        },
        "createAt": "2018-09-01 20:00:00+07:00",
        "modifyAt": "2018-09-01 20:03:00+07:00"
    }
}
```

### 5. Client is getting records in list

Request:

```http
GET /api/v1/document/?page=1 HTTP/1.1
accept: application/json
```

Response:

```http
HTTP/1.1 200 OK
content-type: application/json

{
    "document": [
        {
            "id": "718ce61b-a669-45a6-8f31-32ba41f94784",
            "status": "published",
            "payload": {
                "actor": "The fox",
                "meta": {
                    "type": "cunning",
                },
                "actions": [
                    {
                        "action": "eat",
                        "actor": "blob"
                    },
                    {
                        "action": "run away"
                    }
                ]
            },
            "createAt": "2018-09-01 20:00:00+07:00",
            "modifyAt": "2018-09-01 20:03:00+07:00"
        }
    ],
    "pagination": {
        "page": 1,
        "perPage": 20,
        "total": 1
    }
}
```
