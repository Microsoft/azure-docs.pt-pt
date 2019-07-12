---
title: Configurar o publicador OPC - Azure | Documentos da Microsoft
description: Como configurar o publicador OPC
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: bccab4dde5e17ec30a0b8c5e36dd78bdd1bdff93
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605712"
---
# <a name="configure-opc-publisher"></a>Configurar o Publicador OPC

Pode configurar para especificar o publicador OPC:

- Publicar o alterações de dados de nó OPC UA.
- Os eventos de OPC UA para publicar.
- O formato de telemetria.

Pode configurar o publicador OPC usando arquivos de configuração ou através de chamadas de método.

## <a name="use-configuration-files"></a>Utilizar os ficheiros de configuração

Esta secção descreve a opções para configurar a publicação do nó OPC UA com ficheiros de configuração.

### <a name="use-a-configuration-file-to-configure-publishing-data-changes"></a>Utilize um ficheiro de configuração para configurar a publicar alterações de dados

É a forma mais fácil de configurar os nós de OPC UA para publicar com um ficheiro de configuração. O formato de ficheiro de configuração está documentado no [publishednodes.json](https://github.com/Azure/iot-edge-opc-publisher/blob/master/opcpublisher/publishednodes.json) no repositório.

Sintaxe do arquivo de configuração foi alterada ao longo do tempo. O publicador OPC lê formatos antigos ainda, mas converte-os para o formato mais recente, quando ele persiste a configuração.

O exemplo seguinte mostra o formato do ficheiro de configuração:

```json
[
  {
    "EndpointUrl": "opc.tcp://testserver:62541/Quickstarts/ReferenceServer",
    "UseSecurity": true,
    "OpcNodes": [
      {
        "Id": "i=2258",
        "OpcSamplingInterval": 2000,
        "OpcPublishingInterval": 5000,
        "DisplayName": "Current time"
      }
    ]
  }
]
```

### <a name="use-a-configuration-file-to-configure-publishing-events"></a>Utilize um ficheiro de configuração para configurar a publicação de eventos

Para publicar eventos de OPC UA, utilize o mesmo arquivo de configuração como para alterações de dados.

O exemplo seguinte mostra como configurar a publicação para eventos gerados pelos [SimpleEvents servidor](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/SimpleEvents/Server). O servidor de SimpleEvents pode ser encontrado na [repositório OPC Foundation](https://github.com/OPCFoundation/UA-.NETStandard) é:

```json
[
  {
    "EndpointUrl": "opc.tcp://testserver:62563/Quickstarts/SimpleEventsServer",
    "OpcEvents": [
      {
        "Id": "i=2253",
        "DisplayName": "SimpleEventServerEvents",
        "SelectClauses": [
          {
            "TypeId": "i=2041",
            "BrowsePaths": [
              "EventId"
            ]
          },
          {
            "TypeId": "i=2041",
            "BrowsePaths": [
              "Message"
            ]
          },
          {
            "TypeId": "nsu=http://opcfoundation.org/Quickstarts/SimpleEvents;i=235",
            "BrowsePaths": [
              "/2:CycleId"
            ]
          },
          {
            "TypeId": "nsu=http://opcfoundation.org/Quickstarts/SimpleEvents;i=235",
            "BrowsePaths": [
              "/2:CurrentStep"
            ]
          }
        ],
        "WhereClause": [
          {
            "Operator": "OfType",
            "Operands": [
              {
                "Literal": "ns=2;i=235"
              }
            ]
          }
        ]
      }
    ]
  }
]
```

## <a name="use-method-calls"></a>Usar chamadas de método

Esta secção descreve as chamadas de método que pode utilizar para configurar o publicador OPC.

### <a name="configure-using-opc-ua-method-calls"></a>Configurar com chamadas de métodos de OPC UA

O publicador OPC inclui um servidor OPC UA, que pode ser acedido na porta 62222. Se o nome de anfitrião for **publicador**, em seguida, o ponto final do URI é: `opc.tcp://publisher:62222/UA/Publisher`.

Este ponto final expõe os quatro métodos seguintes:

- PublishNode
- UnpublishNode
- GetPublishedNodes
- HubDirectMethod de IoT

### <a name="configure-using-iot-hub-direct-method-calls"></a>Configurar através de chamadas de método direto do IoT Hub

O publicador OPC implementa as seguintes chamadas de método direto do IoT Hub:

- PublishNodes
- UnpublishNodes
- UnpublishAllNodes
- GetConfiguredEndpoints
- GetConfiguredNodesOnEndpoint
- GetDiagnosticInfo
- GetDiagnosticLog
- GetDiagnosticStartupLog
- ExitApplication
- GetInfo

O formato do payload JSON da solicitação de método e as respostas são definidas no [opcpublisher/HubMethodModel.cs](https://github.com/Azure/iot-edge-opc-publisher/blob/master/opcpublisher/HubMethodModel.cs).

Se chamar um método desconhecido no módulo, ele responde com uma cadeia de caracteres que informa que o método não está implementado. Pode chamar um método desconhecido como uma forma de enviar pings para o módulo.

### <a name="configure-username-and-password-for-authentication"></a>Configurar o nome de utilizador e palavra-passe para autenticação

O modo de autenticação pode ser definido por meio de um IoT Hub direcionar chamadas de método. O payload tem de conter a propriedade **OpcAuthenticationMode** e o nome de utilizador e palavra-passe:

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "UsernamePassword",
    "Username": "<Username>",
    "Password": "<Password>"
    ...
}
```

A palavra-passe é encriptada pelo cliente de carga de trabalho do Hub IoT e armazenada na configuração do publicador. Para alterar autenticação para anónimo, use o método com o payload do seguinte:

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "Anonymous"
    ...
}
```

Se o **OpcAuthenticationMode** propriedade não está definida no payload, as definições de autenticação permanecem inalteradas na configuração.

## <a name="configure-telemetry-publishing"></a>Configurar a publicação de telemetria

Quando o publicador OPC recebe uma notificação de uma alteração do valor num nó de publicado, ele gera uma mensagem formatada do JSON que é enviada para o IoT Hub.

Pode configurar o conteúdo desta mensagem formatada do JSON através de um ficheiro de configuração. Se não for especificado nenhum ficheiro de configuração com o `--tc` , uma configuração predefinida é utilizada a opção que seja compatível com o [acelerador de solução de fábrica ligada](https://github.com/Azure/azure-iot-connected-factory).

Se o publicador OPC estiver configurado para agrupar mensagens, que são enviados como uma matriz JSON válida.

A telemetria é derivada das seguintes origens:

- A configuração do nó de publicador OPC para o nó
- O **MonitoredItem** objeto da pilha OPC UA para os quais o publicador OPC recebeu uma notificação.
- O argumento transmitido para esta notificação, que fornece detalhes sobre a alteração do valor de dados.

A telemetria que é colocada na mensagem formatada do JSON é uma seleção de propriedades importantes desses objetos. Se precisar de mais propriedades, terá de alterar a base de código de publicador OPC.

A sintaxe do ficheiro de configuração é o seguinte:

```json
// The configuration settings file consists of two objects:
// 1) The 'Defaults' object, which defines defaults for the telemetry configuration
// 2) An array 'EndpointSpecific' of endpoint specific configuration
// Both objects are optional and if they are not specified, then publisher uses
// its internal default configuration, which generates telemetry messages compatible
// with the Microsoft Connected factory Preconfigured Solution (https://github.com/Azure/azure-iot-connected-factory).

// A JSON telemetry message for Connected factory looks like:
//  {
//      "NodeId": "i=2058",
//      "ApplicationUri": "urn:myopcserver",
//      "DisplayName": "CurrentTime",
//      "Value": {
//          "Value": "10.11.2017 14:03:17",
//          "SourceTimestamp": "2017-11-10T14:03:17Z"
//      }
//  }

// The 'Defaults' object in the sample below, are similar to what publisher is
// using as its internal default telemetry configuration.
{
    "Defaults": {
        // The first two properties ('EndpointUrl' and 'NodeId' are configuring data
        // taken from the OpcPublisher node configuration.
        "EndpointUrl": {

            // The following three properties can be used to configure the 'EndpointUrl'
            // property in the JSON message send by publisher to IoT Hub.

            // Publish controls if the property should be part of the JSON message at all.
            "Publish": false,

            // Pattern is a regular expression, which is applied to the actual value of the
            // property (here 'EndpointUrl').
            // If this key is ommited (which is the default), then no regex matching is done
            // at all, which improves performance.
            // If the key is used you need to define groups in the regular expression.
            // Publisher applies the regular expression and then concatenates all groups
            // found and use the resulting string as the value in the JSON message to
            //sent to IoT Hub.
            // This example mimics the default behaviour and defines a group,
            // which matches the conplete value:
            "Pattern": "(.*)",
            // Here some more exaples for 'Pattern' values and the generated result:
            // "Pattern": "i=(.*)"
            // defined for Defaults.NodeId.Pattern, will generate for the above sample
            // a 'NodeId' value of '2058'to be sent by publisher
            // "Pattern": "(i)=(.*)"
            // defined for Defaults.NodeId.Pattern, will generate for the above sample
            // a 'NodeId' value of 'i2058' to be sent by publisher

            // Name allows you to use a shorter string as property name in the JSON message
            // sent by publisher. By default the property name is unchanged and will be
            // here 'EndpointUrl'.
            // The 'Name' property can only be set in the 'Defaults' object to ensure
            // all messages from publisher sent to IoT Hub have a similar layout.
            "Name": "EndpointUrl"

        },
        "NodeId": {
            "Publish": true,

            // If you set Defaults.NodeId.Name to "ni", then the "NodeId" key/value pair
            // (from the above example) will change to:
            //      "ni": "i=2058",
            "Name": "NodeId"
        },

        // The MonitoredItem object is configuring the data taken from the MonitoredItem
        // OPC UA object for published nodes.
        "MonitoredItem": {

            // If you set the Defaults.MonitoredItem.Flat to 'false', then a
            // 'MonitoredItem' object will appear, which contains 'ApplicationUri'
            // and 'DisplayNode' proerties:
            //      "NodeId": "i=2058",
            //      "MonitoredItem": {
            //          "ApplicationUri": "urn:myopcserver",
            //          "DisplayName": "CurrentTime",
            //      }
            // The 'Flat' property can only be used in the 'MonitoredItem' and
            // 'Value' objects of the 'Defaults' object and will be used
            // for all JSON messages sent by publisher.
            "Flat": true,

            "ApplicationUri": {
                "Publish": true,
                "Name": "ApplicationUri"
            },
            "DisplayName": {
                "Publish": true,
                "Name": "DisplayName"
            }
        },
        // The Value object is configuring the properties taken from the event object
        // the OPC UA stack provided in the value change notification event.
        "Value": {
            // If you set the Defaults.Value.Flat to 'true', then the 'Value'
            // object will disappear completely and the 'Value' and 'SourceTimestamp'
            // members won't be nested:
            //      "DisplayName": "CurrentTime",
            //      "Value": "10.11.2017 14:03:17",
            //      "SourceTimestamp": "2017-11-10T14:03:17Z"
            // The 'Flat' property can only be used for the 'MonitoredItem' and 'Value'
            // objects of the 'Defaults' object and will be used for all
            // messages sent by publisher.
            "Flat": false,

            "Value": {
                "Publish": true,
                "Name": "Value"
            },
            "SourceTimestamp": {
                "Publish": true,
                "Name": "SourceTimestamp"
            },
            // 'StatusCode' is the 32 bit OPC UA status code
            "StatusCode": {
                "Publish": false,
                "Name": "StatusCode"
                // 'Pattern' is ignored for the 'StatusCode' value
            },
            // 'Status' is the symbolic name of 'StatusCode'
            "Status": {
                "Publish": false,
                "Name": "Status"
            }
        }
    },

    // The next object allows to configure 'Publish' and 'Pattern' for specific
    // endpoint URLs. Those will overwrite the ones specified in the 'Defaults' object
    // or the defaults used by publisher.
    // It is not allowed to specify 'Name' and 'Flat' properties in this object.
    "EndpointSpecific": [
        // The following shows how a endpoint specific configuration can look like:
        {
            // 'ForEndpointUrl' allows to configure for which OPC UA server this
            // object applies and is a required property for all objects in the
            // 'EndpointSpecific' array.
            // The value of 'ForEndpointUrl' must be an 'EndpointUrl' configured in
            // the publishednodes.json confguration file.
            "ForEndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
            "EndpointUrl": {
                // We overwrite the default behaviour and publish the
                // endpoint URL in this case.
                "Publish": true,
                // We are only interested in the URL part following the 'opc.tcp://' prefix
                // and define a group matching this.
                "Pattern": "opc.tcp://(.*)"
            },
            "NodeId": {
                // We are not interested in the configured 'NodeId' value, 
                // so we do not publish it.
                "Publish": false
                // No 'Pattern' key is specified here, so the 'NodeId' value will be
                // taken as specified in the publishednodes configuration file.
            },
            "MonitoredItem": {
                "ApplicationUri": {
                    // We already publish the endpoint URL, so we do not want
                    // the ApplicationUri of the MonitoredItem to be published.
                    "Publish": false
                },
                "DisplayName": {
                    "Publish": true
                }
            },
            "Value": {
                "Value": {
                    // The value of the node is important for us, everything else we
                    // are not interested in to keep the data ingest as small as possible.
                    "Publish": true
                },
                "SourceTimestamp": {
                    "Publish": false
                },
                "StatusCode": {
                    "Publish": false
                },
                "Status": {
                    "Publish": false
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como configurar o publicador OPC, o passo seguinte sugerido é saber como [executar o publicador OPC](howto-opc-publisher-run.md).
