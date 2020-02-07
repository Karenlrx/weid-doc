
.. _weidentity-rest-api:

WeIdentity RestService API 说明文档
=====================================

WeIdentity核心API
-------------------

1. 总体介绍
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

具体地，每个API的入参都是满足以下格式的json字符串：

.. code-block:: java

    {
        "functionArg": 随调用SDK方法而变的入参json字符串 {
        },
        "transactionArg": 交易参数json字符串 {
            "invokerWeId": 用于索引私钥的WeIdentity DID，服务器端会凭此找到所托管的私钥
        }
        "functionName": 调用SDK方法名
        "v": API版本号
    }

参数说明：

* functionArg是随不同的SDK调用方法而变的，具体的参数可以查看SDK接口文档；后文会为每个所提及的接口给出对应的链接
* transactionArg仅包括一个变量invokerWeId，由传入方决定使用在服务器端托管的具体哪个WeIdentity DID所对应的私钥
    * 非必需，只有在那些需要使用不同身份发交易签名的方法（如CreateAuthorityIssuer等）才会需要；后文中详细说明
* functionName是调用的SDK方法名，用于决定具体调用WeIdentity Java SDK的什么功能
* v是调用的API方法版本

每个API的接口返回都是满足以下格式的json字符串：

.. code-block:: java

    {
        "respBody": 随调用SDK方法而变的输出值json字符串 {
        }
        "ErrorCode": 错误码
        "ErrorMessage": 错误信息，成功时为"success"
    }


其中具体的输出值result亦是随不同的SDK调用方法而变的。

在后文中，我们将会逐一说明目前所提供的功能及其使用方式。

2. 创建WeIdentity DID
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

调用接口：

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - 标题
     - 描述
   * - 接口名
     - weid/api/invoke
   * - Method
     - POST
   * - Content-Type
     - application/json

接口入参：

.. list-table::
   :header-rows: 1
   :widths: 30 60 20

   * - Key
     - Value
     - Required
   * - functionName
     - createWeId
     - Y
   * - functionArg
     - 
     - Y
   * - transactionArg
     - 
     - Y
   * - v
     - 版本号
     - Y


接口入参示例：

.. code-block:: java

    {
        "functionArg": {
        },
        "transactionArg": {
        },
        "functionName": "createWeId",
        "v": "1.0.0"
    }


接口返回: application/json


.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - Key
     - Value
   * - ErrorCode
     - 错误码，0表示成功
   * - ErrorMessage
     - 错误信息
   * - respBody
     - WeIdentity DID

返回示例：

.. code-block:: java

    {
        "ErrorCode": 0,
        "ErrorMessage": "success",
        "respBody": "did:weid:0x12025448644151248e5c1115b23a3fe55f4158e4153"
    }

3. 获取WeIdentity DID Document
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

调用接口：

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - 标题
     - 描述
   * - 接口名
     - weid/api/invoke
   * - Method
     - POST
   * - Content-Type
     - application/json

接口入参：

.. list-table::
   :header-rows: 1
   :widths: 30 60 20

   * - Key
     - Value
     - Required
   * - functionName
     - getWeIdDocument
     - Y
   * - functionArg
     - 
     - Y
   * - functionArg.weId
     - WeIdentity DID，与 `SDK直接调用的方式入参 <https://weidentity.readthedocs.io/projects/javasdk/zh_CN/latest/docs/weidentity-java-sdk-doc.html#getweiddocment>`_ 一致，下同
     - Y
   * - transactionArg
     - 
     - N，传空
   * - v
     - 版本号
     - Y

接口入参示例：

.. code-block:: java

    {
        "functionArg": {
            "weId": "did:weid:0x12025448644151248e5c1115b23a3fe55f4158e4153"
        },
        "transactionArg": {
        },
        "functionName": "getWeIdDocument",
        "v": "1.0.0"
    }


接口返回: application/json

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - Key
     - Value
   * - ErrorCode
     - 错误码，0表示成功
   * - ErrorMessage
     - 错误信息
   * - respBody
     - WeIdentity DID Document

返回示例：

.. code-block:: java

    {
        "respBody": {
            "@context" : "https://w3id.org/did/v1",
            "id" : "did:weid:0x12025448644151248e5c1115b23a3fe55f4158e4153",
            "created" : 1553224394993,
            "updated" : 1553224394993,
            "publicKey" : [ ],
            "authentication" : [ ],
            "service" : [ ]
        },
        "ErrorCode": 0,
        "ErrorMessage": "success"
    }

4. 创建AuthorityIssuer
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

调用接口：

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - 标题
     - 描述
   * - 接口名
     - weid/api/invoke
   * - Method
     - POST
   * - Content-Type
     - application/json


接口入参：


.. list-table::
   :header-rows: 1
   :widths: 30 60 20

   * - Key
     - Value
     - Required
   * - functionName
     - registerAuthorityIssuer
     - Y
   * - functionArg
     - 
     - Y
   * - functionArg.weId
     - WeIdentity DID，与 `SDK直接调用的方式入参 <https://weidentity.readthedocs.io/projects/javasdk/zh_CN/latest/docs/weidentity-java-sdk-doc.html#registercpt>`_ 一致，下同
     - Y
   * - functionArg.name
     - 机构名
     - Y
   * - transactionArg
     - 
     - Y
   * - transactionArg.invokerWeId
     - 用于索引私钥的WeIdentity DID，服务器端会凭此找到所托管的私钥。注意：如果在这里填入了预先定义在application.properties里的暗语，则可确保有足够的权限。
     - Y
   * - v
     - 版本号
     - Y

接口调用示例：

.. code-block:: java

    {
        "functionArg": {
            "weid": "did:weid:0x1Ae5b88d37327830307ab8da0ec5D8E8692A35D3",
            "name": "Sample College"
        },
        "transactionArg": {
            "invokerWeId": "did:weid:0x12025448644151248e5c1115b23a3fe55f4158e4153"
        },
        "functionName": "registerAuthorityIssuer",
        "v": "1.0.0"
    }


接口返回: application/json


.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - Key
     - Value
   * - ErrorCode
     - 错误码，0表示成功
   * - ErrorMessage
     - 错误信息
   * - respBody
     - True/False

返回示例：

.. code-block:: java

    {
        "ErrorCode": 0,
        "ErrorMessage": "success",
        "respBody": True
    }


5. 查询AuthorityIssuer
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

调用接口：

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - 标题
     - 描述
   * - 接口名
     - weid/api/invoke
   * - Method
     - POST
   * - Content-Type
     - application/json

接口入参：

.. list-table::
   :header-rows: 1
   :widths: 30 60 20

   * - Key
     - Value
     - Required
   * - functionName
     - queryAuthorityIssuer
     - Y
   * - functionArg
     - 
     - Y
   * - functionArg.weId
     - WeIdentity DID，与 `SDK直接调用的方式入参 <https://weidentity.readthedocs.io/projects/javasdk/zh_CN/latest/docs/weidentity-java-sdk-doc.html#queryauthorityissuer>`_ 一致，下同
     - Y
   * - transactionArg
     - 
     - N，传空
   * - v
     - 版本号
     - Y

接口入参示例：

.. code-block:: java

    {
        "functionArg": {
            "weId": "did:weid:0x1ae5b88d37327830307ab8da0ec5d8e8692a35d3"
        },
        "transactionArg": {
        },
        "functionName": "queryAuthorityIssuer",
        "v": "1.0.0"
    }

接口返回: application/json

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - Key
     - Value
   * - ErrorCode
     - 错误码，0表示成功
   * - ErrorMessage
     - 错误信息
   * - respBody
     - 完整的Authority Issuer信息


.. code-block:: java

    {
        "respBody": {
            "accValue": ,
            "created": 16845611984115,
            "name": "Sample College",
            "weid": "did:weid:0x1ae5b88d37327830307ab8da0ec5d8e8692a35d3"
        }
        "ErrorCode": 0
        "ErrorMessage": "success"
    }

6. 创建CPT
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


调用接口：

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - 标题
     - 描述
   * - 接口名
     - weid/api/invoke
   * - Method
     - POST
   * - Content-Type
     - application/json


接口入参: 

.. list-table::
   :header-rows: 1
   :widths: 30 60 20

   * - Key
     - Value
     - Required
   * - functionName
     - registerCpt
     - Y
   * - functionArg
     - 
     - Y
   * - functionArg.cptJsonSchema
     - CPT Json Schema，与 `SDK直接调用的方式入参 <https://weidentity.readthedocs.io/projects/javasdk/zh_CN/latest/docs/weidentity-java-sdk-doc.html#registercpt>`_ 一致，下同
     - Y
   * - functionArg.weId
     - CPT创建者
     - Y
   * - transactionArg
     - 
     - Y
   * - transactionArg.invokerWeId
     - 用于索引私钥的WeIdentity DID，服务器端会凭此找到所托管的私钥
     - Y
   * - v
     - 版本号
     - Y

.. code-block:: text

    CPT Json Schema是什么？应该满足什么格式？

    答：Json Schema是一种用来定义Json字符串格式的Json字符串，它定义了CPT应包括的字段、属性及规则。
    WeIdentity可以接受 http://json-schema.org/draft-04/schema# 所定义第四版及之前版本作为入参。


接口入参示例：

.. code-block:: java

      {
        "functionArg": {
            "weId": "did:weid:0x1ae5b88d37327830307ab8da0ec5d8e8692a35d3",
            "cptJsonSchema":{
                "title": "cpt",
                "description": "this is cpt",
                "properties": {
                    "name": {
                        "type": "string",
                        "description": "the name of certificate owner"
                    },
                    "gender": {
                        "enum": [
                            "F",
                            "M"
                        ],
                        "type": "string",
                        "description": "the gender of certificate owner"
                    },
                    "age": {
                        "type": "number",
                        "description": "the age of certificate owner"
                    }
                },
                "required": [
                    "name",
                    "age"
                ]
            }
        },
        "transactionArg": {
            "invokerWeId": "did:weid:0x1ae5b88d37327830307ab8da0ec5d8e8692a35d3"
        }，
        "functionName": "registerCpt"，
        "v": "1.0.0"
      }


接口返回: application/json


.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - Key
     - Value
   * - ErrorCode
     - 错误码，0表示成功
   * - ErrorMessage
     - 错误信息
   * - respBody
     - cptBaseInfo

返回示例：

.. code-block:: java

    {
        "respBody": {
            "cptId": 10,
            "cptVersion": 1
        },
        "ErrorCode": 0,
        "ErrorMessage": "success"
    }

7. 查询CPT
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

调用接口：

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - 标题
     - 描述
   * - 接口名
     - weid/api/invoke
   * - Method
     - POST
   * - Content-Type
     - application/json

接口入参：

.. list-table::
   :header-rows: 1
   :widths: 30 60 20

   * - Key
     - Value
     - Required
   * - functionName
     - queryCpt
     - Y
   * - functionArg
     - 
     - Y
   * - functionArg.cptId
     - CPT ID，与 `SDK直接调用的方式入参 <https://weidentity.readthedocs.io/projects/javasdk/zh_CN/latest/docs/weidentity-java-sdk-doc.html#querycpt>`_ 一致。
     - Y
   * - transactionArg
     - 
     - N，传空
   * - v
     - 版本号
     - Y

接口入参示例：

.. code-block:: java

    {
        "functionArg": {
            "cptId": 10,
        },
        "transactionArg": {
        },
        "functionName": "queryCpt",
        "v": "1.0.0"
    }

接口返回: application/json

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - Key
     - Value
   * - ErrorCode
     - 错误码，0表示成功
   * - ErrorMessage
     - 错误信息
   * - respBody
     - 完整的CPT信息

接口返回示例：

.. code-block:: java

    {
        "respBody": {
            "cptBaseInfo" : {
                "cptId" : 10,
                "cptVersion" : 1
            },
            "cptId" : 10,
            "cptJsonSchema" : {
                "$schema" : "http://json-schema.org/draft-04/schema#",
                "title" : "a CPT schema",
                "type" : "object"
            },
            "cptPublisher" : "did:weid:0x104a58c272e8ebde0c29083552ebe78581322908",
            "cptSignature" : "HJPbDmoi39xgZBGi/aj1zB6VQL5QLyt4qTV6GOvQwzfgUJEZTazKZXe1dRg5aCt8Q44GwNF2k+l1rfhpY1hc/ls=",
            "cptVersion" : 1,
            "created" : 1553503354555,
            "metaData" : {
                "cptPublisher" : "did:weid:0x104a58c272e8ebde0c29083552ebe78581322908",
                "cptSignature" : "HJPbDmoi39xgZBGi/aj1zB6VQL5QLyt4qTV6GOvQwzfgUJEZTazKZXe1dRg5aCt8Q44GwNF2k+l1rfhpY1hc/ls=",
                "created" : 1553503354555,
                "updated" : 0
            },
            "updated" : 0
        },
        "ErrorCode": 0,
        "ErrorMessage": "success"
    }

8. 创建Credential
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

调用接口：

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - 标题
     - 描述
   * - 接口名
     - weid/api/invoke
   * - Method
     - POST
   * - Content-Type
     - application/json

接口入参：

.. list-table::
   :header-rows: 1
   :widths: 30 60 20

   * - Key
     - Value
     - Required
   * - functionName
     - createCredential
     - Y
   * - functionArg
     - 
     - Y
   * - functionArg.claim
     - claim Json结构体，与 `SDK直接调用的方式入参 <https://weidentity.readthedocs.io/projects/javasdk/zh_CN/latest/docs/weidentity-java-sdk-doc.html#createcredential>`_ 一致，下同     
     - Y
   * - functionArg.cptId
     - CPT ID
     - Y
   * - functionArg.issuer
     - issuer WeIdentity DID
     - Y
   * - functionArg.expirationDate
     - 过期时间（使用UTC格式）
     - Y
   * - transactionArg
     - 
     - Y
   * - transactionArg.invokerWeId
     - 用于索引私钥的WeIdentity DID，服务器端会凭此找到所托管的私钥
     - Y
   * - v
     - 版本号
     - Y

接口入参：Json，以signature代替私钥

.. code-block:: java

    {
        "functionArg": {
            "cptId": 10,
            "issuer": "did:weid:0x12025448644151248e5c1115b23a3fe55f4158e4153",
            "expirationDate": "2019-04-18T21:12:33Z",
            "claim": {
                "name": "zhang san",
                "gender": "F",
                "age": 18
            },
        },
        "transactionArg": {
            "invokerWeId": "did:weid:0x12025448644151248e5c1115b23a3fe55f4158e4153"
        },
        "functionName": "createCredential",
        "v": "1.0.0"
    }

接口返回: application/json

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - Key
     - Value
   * - ErrorCode
     - 错误码，0表示成功
   * - ErrorMessage
     - 错误信息
   * - respBody
     - 完整的Credential信息


接口返回示例:

.. code-block:: java

    {
      "respBody": {
          "@context": "https://github.com/WeBankFinTech/WeIdentity/blob/master/context/v1",
          "claim": {
              "content": "b1016358-cf72-42be-9f4b-a18fca610fca",
              "receiver": "did:weid:101:0x7ed16eca3b0737227bc986dd0f2851f644cf4754",
              "weid": "did:weid:101:0xfd28ad212a2de77fee518b4914b8579a40c601fa"
          },
          "cptId": 2000156,
          "expirationDate": "2100-04-18T21:12:33Z",
          "id": "da6fbdbb-b5fa-4fbe-8b0c-8659da2d181b",
          "issuanceDate": "2020-02-06T22:24:00Z",
          "issuer": "did:weid:101:0xfd28ad212a2de77fee518b4914b8579a40c601fa",
          "proof": {
              "created": "1580999040000",
              "creator": "did:weid:101:0xfd28ad212a2de77fee518b4914b8579a40c601fa",
              "signature": "G0XzzLY+MqUAo3xXkS3lxVsgFLnTtvdXM24p+G5hSNNMSIa5vAXYXXKl+Y79CO2ho5DIGPPvSs2hvAixmfIJGbw=",
              "type": "Secp256k1"
          }
      },
      "errorCode": 0,
      "errorMessage": "success"
    }


9. 验证Credential
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

调用接口：

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - 标题
     - 描述
   * - 接口名
     - weid/api/invoke
   * - Method
     - POST
   * - Content-Type
     - application/json


接口入参：

.. list-table::
   :header-rows: 1
   :widths: 30 60 20

   * - Key
     - Value
     - Required
   * - functionName
     - verifyCredential
     - Y
   * - functionArg
     - 
     - Y
   * - functionArg.claim
     - claim Json 结构体，与 `SDK直接调用的方式入参 <https://weidentity.readthedocs.io/projects/javasdk/zh_CN/latest/docs/weidentity-java-sdk-doc.html#verify>`_ 一致，下同
     - Y
   * - functionArg.cptId
     - CPT ID
     - Y
   * - functionArg.context
     - context值
     - Y
   * - functionArg.uuid
     - Credential的UUID
     - Y
   * - functionArg.issuer
     - issuer WeIdentity DID
     - Y
   * - functionArg.issuranceDate
     - 颁发时间
     - Y
   * - functionArg.expirationDate
     - 过期时间
     - Y
   * - functionArg.proof
     - Credential签名结构体
     - Y
   * - transactionArg
     - 
     - N，传空
   * - v
     - 版本号
     - Y

接口入参：

.. code-block:: java

    {
        "functionArg": {
          "@context": "https://github.com/WeBankFinTech/WeIdentity/blob/master/context/v1",
          "claim": {
              "content": "b1016358-cf72-42be-9f4b-a18fca610fca",
              "receiver": "did:weid:101:0x7ed16eca3b0737227bc986dd0f2851f644cf4754",
              "weid": "did:weid:101:0xfd28ad212a2de77fee518b4914b8579a40c601fa"
          },
          "cptId": 2000156,
          "expirationDate": "2100-04-18T21:12:33Z",
          "id": "da6fbdbb-b5fa-4fbe-8b0c-8659da2d181b",
          "issuanceDate": "2020-02-06T22:24:00Z",
          "issuer": "did:weid:101:0xfd28ad212a2de77fee518b4914b8579a40c601fa",
          "proof": {
              "created": "1580999040000",
              "creator": "did:weid:101:0xfd28ad212a2de77fee518b4914b8579a40c601fa",
              "signature": "G0XzzLY+MqUAo3xXkS3lxVsgFLnTtvdXM24p+G5hSNNMSIa5vAXYXXKl+Y79CO2ho5DIGPPvSs2hvAixmfIJGbw=",
              "type": "Secp256k1"
          }
        },
        "transactionArg": {
        },
        "functionName": "verifyCredential"
        "v": "1.0.0"
    }


接口返回: application/json

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - Key
     - Value
   * - ErrorCode
     - 错误码，0表示成功
   * - ErrorMessage
     - 错误信息
   * - respBody
     - True/False


接口返回：

.. code-block:: java

    {
        "respBody": true,
        "ErrorCode": 0,
        "ErrorMessage": "success"
    }



10. 创建CredentialPojo
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

调用接口：

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - 标题
     - 描述
   * - 接口名
     - weid/api/invoke
   * - Method
     - POST
   * - Content-Type
     - application/json

接口入参：

.. list-table::
   :header-rows: 1
   :widths: 30 60 20

   * - Key
     - Value
     - Required
   * - functionName
     - createCredentialPojo
     - Y
   * - functionArg
     - 
     - Y
   * - functionArg.claim
     - claim Json结构体，与 `SDK直接调用的方式入参 <https://weidentity.readthedocs.io/projects/javasdk/zh_CN/latest/docs/weidentity-java-sdk-doc.html#createcredential>`_ 一致，下同     
     - Y
   * - functionArg.cptId
     - CPT ID
     - Y
   * - functionArg.issuer
     - issuer WeIdentity DID
     - Y
   * - functionArg.expirationDate
     - 过期时间（使用UTC格式）
     - Y
   * - transactionArg
     - 
     - Y
   * - transactionArg.invokerWeId
     - 用于索引私钥的WeIdentity DID，服务器端会凭此找到所托管的私钥
     - Y
   * - v
     - 版本号
     - Y

接口入参：Json，以signature代替私钥

.. code-block:: java

    {
        "functionArg": {
            "cptId": 10,
            "issuer": "did:weid:0x12025448644151248e5c1115b23a3fe55f4158e4153",
            "expirationDate": "2019-04-18T21:12:33Z",
            "claim": {
                "name": "zhang san",
                "gender": "F",
                "age": 18
            },
        },
        "transactionArg": {
            "invokerWeId": "did:weid:0x12025448644151248e5c1115b23a3fe55f4158e4153"
        },
        "functionName": "createCredentialPojo",
        "v": "1.0.0"
    }

接口返回: application/json

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - Key
     - Value
   * - ErrorCode
     - 错误码，0表示成功
   * - ErrorMessage
     - 错误信息
   * - respBody
     - 完整的CredentialPojo信息


接口返回示例:

.. code-block:: java

    {
      "respBody": {
          "cptId": 2000156,
          "issuanceDate": 1580996777,
          "context": "https://github.com/WeBankFinTech/WeIdentity/blob/master/context/v1",
          "claim": {
              "content": "b1016358-cf72-42be-9f4b-a18fca610fca",
              "receiver": "did:weid:101:0x7ed16eca3b0737227bc986dd0f2851f644cf4754",
              "weid": "did:weid:101:0xfd28ad212a2de77fee518b4914b8579a40c601fa"
          },
          "id": "21d10ab1-75fe-4733-9f1d-f0bad71b5922",
          "proof": {
              "created": 1580996777,
              "creator": "did:weid:101:0xfd28ad212a2de77fee518b4914b8579a40c601fa#keys-0",
              "salt": {
                  "content": "ncZ5F",
                  "receiver": "L0c40",
                  "weid": "I4aop"
              },
              "signatureValue": "HEugP13uDVBg2G0kmmwbTkQXobsrWNqtGQJW6BoHU2Q2VQpwVhK382dArRMFN6BDq7ogozYBRC15QR8ueX5G3t8=",
              "type": "Secp256k1"
          },
          "type": [
              "VerifiableCredential",
              "hashTree"
          ],
          "issuer": "did:weid:101:0xfd28ad212a2de77fee518b4914b8579a40c601fa",
          "expirationDate": 4111737153
      },
      "errorCode": 0,
      "errorMessage": "success"
    }


11. 验证CredentialPojo
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

调用接口：

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - 标题
     - 描述
   * - 接口名
     - weid/api/invoke
   * - Method
     - POST
   * - Content-Type
     - application/json


接口入参：

.. list-table::
   :header-rows: 1
   :widths: 30 60 20

   * - Key
     - Value
     - Required
   * - functionName
     - verifyCredentialPojo
     - Y
   * - functionArg
     - 
     - Y
   * - functionArg.claim
     - claim Json 结构体，与 `SDK直接调用的方式入参 <https://weidentity.readthedocs.io/projects/javasdk/zh_CN/latest/docs/weidentity-java-sdk-doc.html#verify>`_ 一致，下同
     - Y
   * - functionArg.cptId
     - CPT ID
     - Y
   * - functionArg.context
     - context值
     - Y
   * - functionArg.uuid
     - CredentialPojo的UUID
     - Y
   * - functionArg.issuer
     - issuer WeIdentity DID
     - Y
   * - functionArg.issuranceDate
     - 颁发时间
     - Y
   * - functionArg.expirationDate
     - 过期时间
     - Y
   * - functionArg.proof
     - Credential签名值
     - Y
   * - transactionArg
     - 
     - N，传空
   * - v
     - 版本号
     - Y

接口入参：

.. code-block:: java

    {
        "functionArg": {
          "cptId": 2000156,
          "issuanceDate": 1580996777,
          "context": "https://github.com/WeBankFinTech/WeIdentity/blob/master/context/v1",
          "claim": {
              "content": "b1016358-cf72-42be-9f4b-a18fca610fca",
              "receiver": "did:weid:101:0x7ed16eca3b0737227bc986dd0f2851f644cf4754",
              "weid": "did:weid:101:0xfd28ad212a2de77fee518b4914b8579a40c601fa"
          },
          "id": "21d10ab1-75fe-4733-9f1d-f0bad71b5922",
          "proof": {
              "created": 1580996777,
              "creator": "did:weid:101:0xfd28ad212a2de77fee518b4914b8579a40c601fa#keys-0",
              "salt": {
                  "content": "ncZ5F",
                  "receiver": "L0c40",
                  "weid": "I4aop"
              },
              "signatureValue": "HEugP13uDVBg2G0kmmwbTkQXobsrWNqtGQJW6BoHU2Q2VQpwVhK382dArRMFN6BDq7ogozYBRC15QR8ueX5G3t8=",
              "type": "Secp256k1"
          },
          "type": [
              "VerifiableCredential",
              "hashTree"
          ],
          "issuer": "did:weid:101:0xfd28ad212a2de77fee518b4914b8579a40c601fa",
          "expirationDate": 4111737153
        },
        "transactionArg": {
        },
        "functionName": "verifyCredentialPojo"
        "v": "1.0.0"
    }


接口返回: application/json

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - Key
     - Value
   * - ErrorCode
     - 错误码，0表示成功
   * - ErrorMessage
     - 错误信息
   * - respBody
     - True/False


接口返回：

.. code-block:: java

    {
        "respBody": true,
        "ErrorCode": 0,
        "ErrorMessage": "success"
    }


WeIdentity Endpoint Service API
------------------------------------

1. 获取所有已注册的Endpoint信息
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

调用接口：

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - 标题
     - 描述
   * - 接口名
     - weid/api/endpoint
   * - Method
     - GET
   * - Content-Type
     - application/json

接口入参：无

接口返回：

.. code-block:: java

    {
        "ErrorCode": 0,
        "ErrorMessage": "success",
        "respBody": [
            {
                "requestName": "create-passphrase",
                "inAddr": [
                    "127.0.0.1:6010",
                    "127.0.0.1:6011"
                ],
                "description": "Create a valid random passphrase"
            },
            {
                "requestName": "verify-passphrase",
                "inAddr": [
                    "127.0.0.1:6012",
                    "127.0.0.1:6013"
                ],
                "description": "Verify a passphrase"
            }
        ]
    }


2. 进行Endpoint调用
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

调用接口：

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - 标题
     - 描述
   * - 接口名
     - weid/api/endpoint/{endpoint}
   * - Method
     - POST
   * - Content-Type
     - application/json


接口入参：

.. list-table::
   :header-rows: 1
   :widths: 30 60 20

   * - Key
     - Value
     - Required
   * - /{endpoint}
     - 在API路径中标明的API名，String
     - Y
   * - body
     - 以```分隔的多个传入服务端用于执行API的参数
     - Y

接口入参：

.. code-block:: java

    {
        "body": "did:weid:0x12025448644151248e5c1115b23a3fe55f4158e4153```25"
    }


接口返回: application/json

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - Key
     - Value
   * - ErrorCode
     - 错误码，0表示成功
   * - ErrorMessage
     - 错误信息
   * - respBody
     - SDK侧的返回值，String

接口返回：

.. code-block:: java

    {
        "ErrorCode": 0,
        "ErrorMessage": "success",
        "respBody": "did:weid:0x1Ae5b88d37327830307ab8da0ec5D8E8692A35D3",
    }
