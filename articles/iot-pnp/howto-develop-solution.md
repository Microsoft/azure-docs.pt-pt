---
title: Interaja com um dispositivo IoT Plug e Play Preview a partir de uma solução Azure IoT ! Microsoft Docs
description: Como desenvolvedor de soluções, aprenda sobre como usar o serviço SDK para interagir com dispositivos IoT Plug e Play.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: how-to
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: e349aadfd629202b1c8cdb5c53a88e0a6c2e06de
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80159222"
---
# <a name="connect-to-and-interact-with-an-iot-plug-and-play-preview-device"></a>Conecte-se e interaja com um dispositivo IoT Plug e Play Preview

Este guia de como usar as amostras no serviço de nó SDK que lhe mostram como a sua Solução IoT pode interagir com dispositivos IoT Plug e Reprodução.

Se ainda não tiver concluído o [dispositivo Connect a Plug and Play IoT para a sua solução](quickstart-connect-pnp-device-solution-node.md) de arranque rápido, deve fazê-lo agora. O quickstart mostra-lhe como descarregar e instalar o SDK e executar algumas das amostras.

Antes de executar as amostras de serviço, abra um novo terminal, vá à pasta raiz do seu repositório clonado, navegue até à pasta **digitaltwins/quickstarts/service** e, em seguida, executar o seguinte comando para instalar as dependências:

```cmd/sh
npm install
```

## <a name="run-the-service-samples"></a>Executar as amostras de serviço

Utilize as seguintes amostras para explorar as capacidades do serviço de Node.js SDK. Certifique-se de que a `IOTHUB_CONNECTION_STRING` variável ambiental está definida na concha que utiliza:

### <a name="retrieve-a-digital-twin-and-list-the-interfaces"></a>Recupere um gémeo digital e enuse as interfaces

**get_digital_twin.js** obtém o gémeo digital associado ao seu dispositivo e imprime o seu componente na linha de comando. Não requer uma amostra de dispositivo de corrida para ter sucesso.

**get_digital_twin_interface_instance.js** obtém uma única instância de interface de gémeos digitais associados ao seu dispositivo e imprime-o na linha de comando. Não requer que a amostra do dispositivo seja executada.

### <a name="get-and-set-properties-using-the-node-service-sdk"></a>Obter e definir propriedades usando o serviço de nó SDK

**update_digital_twin.js** atualiza uma propriedade writable no seu dispositivo digital twin usando um patch completo. Pode atualizar várias propriedades em várias interfaces, se assim o desejar. Para ter sucesso, a amostra do dispositivo tem de estar a funcionar ao mesmo tempo. O sucesso parece que a amostra do dispositivo está a imprimir algo sobre a atualização de uma propriedade a amostra de serviço que imprimiu um gémeo digital atualizado no terminal.

### <a name="send-a-command-and-retrieve-the-response-using-the-node-service-sdk"></a>Envie um comando e recupere a resposta usando o serviço de nó SDK

**invoke_command.js** invoca um comando sincronizado no seu dispositivo digital twin. Para ter sucesso, a amostra do dispositivo tem de estar a funcionar ao mesmo tempo. O sucesso parece que a amostra do dispositivo está a imprimir algo sobre reconhecer um comando, e o cliente de serviço a imprimir o resultado do comando no terminal.

### <a name="connect-to-the-public-repository-and-retrieve-a-model-definition-using-the-node-service-sdk"></a>Ligue-se ao repositório público e recupere uma definição de modelo utilizando o serviço de nó SDK

Utilizando as mesmas instruções que para as amostras de serviço e dispositivo, é necessário definir a seguinte variável ambiental:

* `AZURE_IOT_MODEL_REPOSITORY_CONNECTION_STRING`

Pode encontrar esta cadeia de ligação no [portal Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com) no **separador de cordas De Ligação** para o **seu repositório da Empresa**.

A cadeia de ligação parece o seguinte exemplo:

```text
HostName={repo host name};RepositoryId={repo ID};SharedAccessKeyName={repo key ID};SharedAccessKey={repo key secret}
```

Depois de definir estas quatro variáveis ambientais, faça a amostra da mesma forma que analisou as outras amostras:

```cmd/sh
node model_repo.js
```

Esta amostra descarrega a interface **ModelDiscovery** e imprime este modelo no terminal.

### <a name="run-queries-in-iot-hub-based-on-capability-models-and-interfaces"></a>Executar consultas no IoT Hub com base em modelos e interfaces de capacidade

A linguagem de consulta IoT Hub suporta `HAS_INTERFACE` `HAS_CAPABILITYMODEL` e, como mostra os seguintes exemplos:

```sql
select * from devices where HAS_INTERFACE('id without version', version)
```

```sql
select * from devices where HAS_CAPABILITYMODEL('id without version', version)
```

### <a name="creating-digital-twin-routes"></a>Criação de rotas gémeas digitais

A sua solução pode receber notificações de eventos digitais de mudança de gémeos. Para subscrever estas notificações, utilize a [função de encaminhamento IoT Hub](../iot-hub/iot-hub-devguide-endpoints.md) para enviar as notificações para um ponto final, como o armazenamento de bolhas, os Centros de Eventos ou uma fila de autocarros de serviço.

Para criar uma rota digital twin:

1. No portal Azure, vá ao seu recurso IoT Hub.
1. Selecione **o encaminhamento de mensagens**.
1. No separador **Rotas** selecione **Adicionar**.
1. Introduza um valor no campo **Nome** e escolha um **Ponto final.** Se ainda não configurar um ponto final, selecione **Add endpoint**.
1. Na **origem de dados,** selecione **Digital Twin Change Events**.
1. Selecione **Guardar**.

O seguinte JSON mostra um exemplo de um evento digital twin change:

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

Agora que aprendeu sobre soluções de serviço que interagem com os seus dispositivos IoT Plug e Play, um próximo passo sugerido é aprender sobre a [descoberta do Modelo.](concepts-model-discovery.md)
