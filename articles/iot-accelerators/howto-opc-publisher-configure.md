---
title: Configurar o Publicador OPC-Azure | Microsoft Docs
description: Este artigo descreve como configurar o editor OPC para especificar alterações de dados do nó do OPC UA, eventos do OPC UA para publicar e também o formato de telemetria.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 0db00f670dfcc526d3fc34d41ce731df4c6573ec
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824154"
---
# <a name="configure-opc-publisher"></a>Configurar o Publicador OPC

Você pode configurar o editor OPC para especificar:

- Os dados do nó OPC UA são alterados para publicar.
- Os eventos OPC UA a serem publicados.
- O formato de telemetria.

Você pode configurar o Publicador OPC usando arquivos de configuração ou usando chamadas de método.

## <a name="use-configuration-files"></a>Utilizar os ficheiros de configuração

Esta seção descreve as opções para configurar a publicação de nó OPC UA com arquivos de configuração.

### <a name="use-a-configuration-file-to-configure-publishing-data-changes"></a>Usar um arquivo de configuração para configurar a publicação de alterações de dados

A maneira mais fácil de configurar os nós OPC UA para publicar é com um arquivo de configuração. O formato do arquivo de configuração é documentado em [publishednodes. JSON](https://github.com/Azure/iot-edge-opc-publisher/blob/master/opcpublisher/publishednodes.json) no repositório.

A sintaxe do arquivo de configuração foi alterada ao longo do tempo. O Publicador OPC ainda lê os formatos antigos, mas os converte no formato mais recente quando ele persiste a configuração.

O exemplo a seguir mostra o formato do arquivo de configuração:

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

### <a name="use-a-configuration-file-to-configure-publishing-events"></a>Usar um arquivo de configuração para configurar eventos de publicação

Para publicar eventos OPC UA, use o mesmo arquivo de configuração para alterações de dados.

O exemplo a seguir mostra como configurar a publicação para eventos gerados pelo [servidor SimpleEvents](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/SimpleEvents/Server). O servidor SimpleEvents pode ser encontrado no [repositório OPC Foundation](https://github.com/OPCFoundation/UA-.NETStandard) :

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

Esta seção descreve as chamadas de método que você pode usar para configurar o Publicador OPC.

### <a name="configure-using-opc-ua-method-calls"></a>Configurar usando chamadas de método OPC UA

O editor OPC inclui um servidor OPC UA, que pode ser acessado na porta 62222. Se o nome do host for **Publicador**, o URI do ponto de extremidade será: `opc.tcp://publisher:62222/UA/Publisher`.

Esse ponto de extremidade expõe os quatro métodos a seguir:

- PublishNode
- UnpublishNode
- GetPublishedNodes
- HubDirectMethod IoT

### <a name="configure-using-iot-hub-direct-method-calls"></a>Configurar usando chamadas de método direto do Hub IoT

O Publicador OPC implementa as seguintes chamadas de método direto do Hub IoT:

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

O formato da carga JSON da solicitação e das respostas do método é definido em [opcpublisher/HubMethodModel. cs](https://github.com/Azure/iot-edge-opc-publisher/blob/master/opcpublisher/HubMethodModel.cs).

Se você chamar um método desconhecido no módulo, ele responderá com uma cadeia de caracteres que diz que o método não está implementado. Você pode chamar um método desconhecido como uma maneira de executar o ping no módulo.

### <a name="configure-username-and-password-for-authentication"></a>Configurar nome de usuário e senha para autenticação

O modo de autenticação pode ser definido por meio de chamadas de método direto do Hub IoT. A carga deve conter a propriedade **OpcAuthenticationMode** e o nome de usuário e a senha:

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "UsernamePassword",
    "Username": "<Username>",
    "Password": "<Password>"
    ...
}
```

A senha é criptografada pelo cliente de carga de trabalho do Hub IoT e armazenada na configuração do editor. Para alterar a autenticação de volta para anônima, use o método com a seguinte carga:

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "Anonymous"
    ...
}
```

Se a propriedade **OpcAuthenticationMode** não estiver definida na carga, as configurações de autenticação permanecerão inalteradas na configuração.

## <a name="configure-telemetry-publishing"></a>Configurar a publicação de telemetria

Quando o Publicador OPC recebe uma notificação de uma alteração de valor em um nó publicado, ele gera uma mensagem formatada em JSON que é enviada ao Hub IoT.

Você pode configurar o conteúdo dessa mensagem formatada em JSON usando um arquivo de configuração. Se nenhum arquivo de configuração for especificado com a opção `--tc`, será usada uma configuração padrão compatível com o [Solution Accelerator da fábrica conectada](https://github.com/Azure/azure-iot-connected-factory).

Se o editor OPC estiver configurado para mensagens em lote, eles serão enviados como uma matriz JSON válida.

A telemetria é derivada das seguintes fontes:

- A configuração de nó do editor OPC para o nó
- O objeto **MonitoredItem** da pilha OPC UA para a qual o Publicador OPC recebeu uma notificação.
- O argumento passado para essa notificação, que fornece detalhes sobre a alteração do valor de dados.

A telemetria que é colocada na mensagem formatada em JSON é uma seleção de propriedades importantes desses objetos. Se precisar de mais propriedades, você precisará alterar a base de código do Publicador OPC.

A sintaxe do arquivo de configuração é a seguinte:

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

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu a configurar o editor OPC, a próxima etapa sugerida é aprender a [executar o editor OPC](howto-opc-publisher-run.md).
