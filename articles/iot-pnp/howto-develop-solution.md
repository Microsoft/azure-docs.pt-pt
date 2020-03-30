---
title: Interaja com um dispositivo ioT Plug e Play Preview a partir de uma solução Azure IoT [ Microsoft Docs
description: Como um desenvolvedor de soluções, aprenda a usar o serviço SDK para interagir com dispositivos IoT Plug e Play.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: how-to
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: e349aadfd629202b1c8cdb5c53a88e0a6c2e06de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159222"
---
# <a name="connect-to-and-interact-with-an-iot-plug-and-play-preview-device"></a>Ligue-se e interaja com um dispositivo de pré-visualização IoT Plug e Play

Este guia de como fazer mostra como utilizar as amostras no serviço Denó SDK que lhe mostram como a sua Solução IoT pode interagir com dispositivos IoT Plug e Play Preview.

Se ainda não tiver concluído o [dispositivo Connect a IoT Plug and Play à sua solução,](quickstart-connect-pnp-device-solution-node.md) deverá fazê-lo agora. O quickstart mostra-lhe como descarregar e instalar o SDK e executar algumas das amostras.

Antes de executar as amostras de serviço, abra um novo terminal, vá para a pasta raiz do seu repositório clonado, navegue para as **gémeas digitais/quickstarts/pasta** de serviço, e, em seguida, executar o seguinte comando para instalar as dependências:

```cmd/sh
npm install
```

## <a name="run-the-service-samples"></a>Executar as amostras de serviço

Utilize as seguintes amostras para explorar as capacidades do serviço Node.js SDK. Certifique-se `IOTHUB_CONNECTION_STRING` de que a variável ambiental está definida na concha que utiliza:

### <a name="retrieve-a-digital-twin-and-list-the-interfaces"></a>Recupere um gémeo digital e enumere as interfaces

**get_digital_twin.js** obtém o gémeo digital associado ao seu dispositivo e imprime o seu componente na linha de comando. Não requer uma amostra de dispositivo de corrida para ter sucesso.

**get_digital_twin_interface_instance.js** obtém uma única interface de gémeo digital associado ao seu dispositivo e imprime-o na linha de comando. Não requer que a amostra do dispositivo seja executada.

### <a name="get-and-set-properties-using-the-node-service-sdk"></a>Obtenha e detetete propriedades usando o serviço de nó SDK

**update_digital_twin.js** atualiza uma propriedade utilitável no seu dispositivo digital twin usando um patch completo. Pode atualizar várias propriedades em várias interfaces, se quiser. Para ter sucesso, a amostra do dispositivo tem de ser funcionada ao mesmo tempo. O sucesso parece que a amostra do dispositivo está a imprimir algo sobre a atualização de uma propriedade a amostra de serviço que está a imprimir um gémeo digital atualizado no terminal.

### <a name="send-a-command-and-retrieve-the-response-using-the-node-service-sdk"></a>Envie um comando e recupere a resposta utilizando o serviço de nó SDK

**invoke_command.js** invoca um comando sincronizado no seu dispositivo de gémeo digital. Para ter sucesso, a amostra do dispositivo tem de ser funcionada ao mesmo tempo. O sucesso parece que a amostra do dispositivo está a imprimir algo sobre reconhecer um comando, e o cliente de serviço imprimir o resultado do comando no terminal.

### <a name="connect-to-the-public-repository-and-retrieve-a-model-definition-using-the-node-service-sdk"></a>Ligue-se ao repositório público e recupere uma definição de modelo utilizando o serviço Denó SDK

Utilizando as mesmas instruções que para as amostras de serviço e dispositivo, é necessário definir a seguinte variável ambiental:

* `AZURE_IOT_MODEL_REPOSITORY_CONNECTION_STRING`

Pode encontrar esta cadeia de ligação no [portal Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com) no separador de **cordas De ligação** para o seu **repositório da Empresa**.

A corda de ligação parece ser o seguinte exemplo:

```text
HostName={repo host name};RepositoryId={repo ID};SharedAccessKeyName={repo key ID};SharedAccessKey={repo key secret}
```

Depois de definir estas quatro variáveis ambientais, faça a amostra da mesma forma que executou as outras amostras:

```cmd/sh
node model_repo.js
```

Esta amostra descarrega a interface **ModelDiscovery** e imprime este modelo no terminal.

### <a name="run-queries-in-iot-hub-based-on-capability-models-and-interfaces"></a>Executar consultas no IoT Hub com base em modelos e interfaces de capacidade

A linguagem de consulta IoT Hub apoia `HAS_INTERFACE` e, `HAS_CAPABILITYMODEL` como mostram os seguintes exemplos:

```sql
select * from devices where HAS_INTERFACE('id without version', version)
```

```sql
select * from devices where HAS_CAPABILITYMODEL('id without version', version)
```

### <a name="creating-digital-twin-routes"></a>Criação de rotas gémeas digitais

A sua solução pode receber notificações de eventos de mudança de gémeos digitais. Para subscrever estas notificações, utilize a função de [encaminhamento IoT Hub](../iot-hub/iot-hub-devguide-endpoints.md) para enviar as notificações para um ponto final, como armazenamento de blob, Hubs de Eventos ou uma fila de ônibus de serviço.

Para criar uma rota dupla digital:

1. No portal Azure, vá ao seu recurso IoT Hub.
1. Selecione **o encaminhamento de mensagens**.
1. No separador **Rotas** selecione **Adicionar**.
1. Introduza um valor no campo **Nome** e escolha um **Ponto Final**. Se ainda não configurar um ponto final, **selecione Adicionar ponto final**.
1. Na **queda** da fonte de dados, selecione **Digital Twin Change Events**.
1. Selecione **Guardar**.

O seguinte JSON mostra um exemplo de um evento de mudança de gémeos digitais:

```json
{
  "interfaces": {
    "urn_azureiot_ModelDiscovery_DigitalTwin": {
      "name": "urn_azureiot_ModelDiscovery_DigitalTwin",
      "properties": {
        "modelInformation": {
          "reported": {
            "value": {
              "modelId": "urn:domain:capabilitymodel:TestCapability:1",
              "interfaces": {
                "MyInterfaceFoo": "urn:domain:interfaces:FooInterface:1",
                "urn_azureiot_ModelDiscovery_DigitalTwin": "urn:azureiot:ModelDiscovery:DigitalTwin:1"
              }
            }
          }
        }
      }
    },
    "MyInterfaceFoo": {
      "name": "MyInterfaceFoo",
      "properties": {
        "property_1": { "desired": { "value": "value_1" } },
        "property_2": {
          "desired": { "value": 20 },
          "reported": {
            "value": 10,
            "desiredState": {
              "code": 200,
              "version": 22,
              "subCode": 400,
              "description": ""
            }
          }
        },
        "property_3": { "reported": { "value": "value_3" } }
      }
    }
  },
  "version": 4
}
```

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu sobre soluções de serviço que interagem com os seus dispositivos IoT Plug e Play, um próximo passo sugerido é aprender sobre a descoberta do [Modelo.](concepts-model-discovery.md)
