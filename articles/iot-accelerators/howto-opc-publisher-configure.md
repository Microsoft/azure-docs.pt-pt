---
title: Configure Editor OPC - Azure | Microsoft Docs
description: Este artigo descreve como configurar o OPC Publisher para especificar alterações de dados de nó OPC UA, eventos OPC UA para publicar e também o formato de telemetria.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.custom: devx-track-csharp
ms.openlocfilehash: 405fa04a3b6af7f0c87888aa48aba9bdffeaf282
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646447"
---
# <a name="configure-opc-publisher"></a>Configurar o Publicador OPC

> [!IMPORTANT]
> Enquanto atualizamos este artigo, consulte [a Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) para obter o conteúdo mais atualizado.

Pode configurar o Editor OPC para especificar:

- Os dados do nó UA da OPC alteram a publicação.
- Os eventos da UA da OPC para publicar.
- O formato da telemetria.

Pode configurar o Editor OPC usando ficheiros de configuração ou usando chamadas de método.

## <a name="use-configuration-files"></a>Utilizar os ficheiros de configuração

Esta secção descreve opções para configurar a publicação do nó UA OPC com ficheiros de configuração.

### <a name="use-a-configuration-file-to-configure-publishing-data-changes"></a>Utilize um ficheiro de configuração para configurar alterações de dados de publicação

A forma mais fácil de configurar os nós UA da OPC para publicar é com um ficheiro de configuração. O formato de ficheiro de configuração é documentado em [publishednodes.jsno](https://github.com/Azure/iot-edge-opc-publisher/blob/master/opcpublisher/publishednodes.json) repositório.

A sintaxe do ficheiro de configuração mudou ao longo do tempo. O OPC Publisher ainda lê formatos antigos, mas converte-os no formato mais recente quando persiste na configuração.

O exemplo a seguir mostra o formato do ficheiro de configuração:

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

### <a name="use-a-configuration-file-to-configure-publishing-events"></a>Utilize um ficheiro de configuração para configurar eventos de publicação

Para publicar eventos OPC UA, utilize o mesmo ficheiro de configuração que para alterações de dados.

O exemplo a seguir mostra como configurar a publicação para eventos gerados pelo [servidor SimpleEvents](https://github.com/OPCFoundation/UA-.NETStandard-Samples/tree/master/Workshop/SimpleEvents/Server). O servidor SimpleEvents pode ser encontrado no [repositório da Fundação OPC:](https://github.com/OPCFoundation/UA-.NETStandard-Samples)

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

## <a name="use-method-calls"></a>Use chamadas de método

Esta secção descreve as chamadas de método que pode utilizar para configurar o Editor OPC.

### <a name="configure-using-opc-ua-method-calls"></a>Configure usando chamadas de método OPC UA

O OPC Publisher inclui um Servidor UA OPC, que pode ser acedido na porta 62222. Se o nome de anfitrião for **editor,** então o ponto final URI é: `opc.tcp://publisher:62222/UA/Publisher` .

Este ponto final expõe os seguintes quatro métodos:

- PublicarNode
- Não-publicaNode
- Obter Nomes Publicados
- IoT HubDirectMethod

### <a name="configure-using-iot-hub-direct-method-calls"></a>Configure usando chamadas de método direto IoT Hub

A OPC Publisher implementa as seguintes chamadas de método direto IoT Hub:

- PublicarNodes
- Não-publicanteNodes
- UnpublishAllNodes
- Obter Pontos DeEndpoints Configurados
- GetConfiguredNodesOnEndpoint
- GetDiagnosticInfo
- ObterDiagnosticLog
- GetDiagnosticStartupLog
- ExitApplication
- GetInfo

O formato da carga útil JSON do pedido e respostas do método é definido em [opcpublisher/HubMethodModel.cs](https://github.com/Azure/iot-edge-opc-publisher/tree/master/opcpublisher).

Se chamar um método desconhecido no módulo, responde com uma corda que diz que o método não é implementado. Pode chamar um método desconhecido como forma de pingar o módulo.

### <a name="configure-username-and-password-for-authentication"></a>Configurar o nome de utilizador e a palavra-passe para a autenticação

O modo de autenticação pode ser definido através de um método ioT hub chamadas diretas. A carga útil deve conter a propriedade **OpcAuthenticationMode** e o nome de utilizador e senha:

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "UsernamePassword",
    "Username": "<Username>",
    "Password": "<Password>"
    ...
}
```

A palavra-passe é encriptada pelo Cliente de Carga de Trabalho do Hub IoT e armazenada na configuração da editora. Para alterar a autenticação de volta para anónima, utilize o método com a seguinte carga útil:

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "Anonymous"
    ...
}
```

Se a propriedade **OpcAuthenticationMode** não estiver definida na carga útil, as definições de autenticação permanecem inalteradas na configuração.

## <a name="configure-telemetry-publishing"></a>Configure a publicação de telemetria

Quando a OPC Publisher recebe uma notificação de uma alteração de valor num nó publicado, gera uma mensagem formatada JSON que é enviada para o IoT Hub.

Pode configurar o conteúdo desta mensagem formatada JSON utilizando um ficheiro de configuração. Se nenhum ficheiro de configuração for especificado com a `--tc` opção, é utilizada uma configuração predefinida que seja compatível com o acelerador de [solução de fábrica Ligado](https://github.com/Azure/azure-iot-connected-factory).

Se o Editor OPC estiver configurado para enviar mensagens de lote, então são enviadas como uma matriz JSON válida.

A telemetria provém das seguintes fontes:

- A configuração do nó do nó do editor OPC para o nó
- O objeto **MonitoredItem** da pilha OPC UA para a qual a OPC Publisher recebeu uma notificação.
- O argumento passou para esta notificação, que fornece detalhes sobre a alteração do valor dos dados.

A telemetria que é colocada na mensagem formatada JSON é uma seleção de propriedades importantes destes objetos. Se precisar de mais propriedades, tem de alterar a base de códigos OPC Publisher.

A sintaxe do ficheiro de configuração é a seguinte:

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
            // If this key is omitted (which is the default), then no regex matching is done
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

Agora que aprendeu a configurar a Editora OPC, o próximo passo sugerido é aprender a gerir a [Editora OPC.](howto-opc-publisher-run.md)
