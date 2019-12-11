---
title: Interagir com um dispositivo de visualização de IoT Plug and Play de uma solução de IoT do Azure | Microsoft Docs
description: Como desenvolvedor de soluções, saiba como usar o SDK do serviço para interagir com dispositivos de Plug and Play de IoT.
author: Philmea
ms.author: philmea
ms.date: 07/24/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 543f332087aef4147c9274ca980cb56543a68112
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977595"
---
# <a name="connect-to-and-interact-with-an-iot-plug-and-play-preview-device"></a>Conectar-se a um dispositivo de visualização de Plug and Play IoT e interagir com ele

Este guia de instruções mostra como usar os exemplos no SDK do serviço de nó que mostram como sua solução de IoT pode interagir com dispositivos de visualização de IoT Plug and Play.

Se você ainda não tiver concluído o [dispositivo conectar um plug and Play IOT ao seu](quickstart-connect-pnp-device-solution.md) início rápido de solução, faça isso agora. O guia de início rápido mostra como baixar e instalar o SDK e executar alguns dos exemplos.

Antes de executar os exemplos de serviço, abra um novo terminal, vá para a pasta raiz do seu repositório clonado, navegue até a pasta **digitaltwins/QuickStartES/Service** e, em seguida, execute o seguinte comando para instalar as dependências:

```cmd/sh
npm install
```

## <a name="run-the-service-samples"></a>Executar os exemplos de serviço

Use os exemplos a seguir para explorar os recursos do SDK do serviço node. js. Verifique se a variável de ambiente `IOTHUB_CONNECTION_STRING` está definida no Shell que você usa:

### <a name="retrieve-a-digital-twin-and-list-the-interfaces"></a>Recuperar uma teledigital e listar as interfaces

**get_digital_twin. js** Obtém a impressão digital associada ao seu dispositivo e imprime seu componente na linha de comando. Ele não requer um exemplo de dispositivo em execução para ter sucesso.

**get_digital_twin_interface_instance. js** Obtém uma única instância de interface de cópia digital associada ao seu dispositivo e a imprime na linha de comando. Ele não exige que o exemplo de dispositivo seja executado.

### <a name="get-and-set-properties-using-the-node-service-sdk"></a>Obter e definir propriedades usando o SDK do serviço de nó

o **update_digital_twin. js** atualiza uma propriedade gravável no seu dispositivo digital up usando um patch completo. Você pode atualizar várias propriedades em várias interfaces, se desejar. Para que ele tenha sucesso, o exemplo de dispositivo precisa estar em execução ao mesmo tempo. O sucesso parece que o exemplo de dispositivo está imprimindo algo sobre a atualização de uma propriedade o exemplo de serviço que está imprimindo uma atualização digital do terminal.

### <a name="send-a-command-and-retrieve-the-response-using-the-node-service-sdk"></a>Enviar um comando e recuperar a resposta usando o SDK do serviço de nó

**invoke_command. js** invoca um comando síncrono no seu dispositivo digital. Para que ele tenha sucesso, o exemplo de dispositivo precisa estar em execução ao mesmo tempo. O sucesso parece que o exemplo de dispositivo está imprimindo algo sobre a confirmação de um comando e o cliente de serviço imprimindo o resultado do comando no terminal.

### <a name="connect-to-the-public-repository-and-retrieve-a-model-definition-using-the-node-service-sdk"></a>Conectar-se ao repositório público e recuperar uma definição de modelo usando o SDK do serviço de nó

Usando as mesmas instruções para os exemplos de serviço e dispositivo, você precisa definir a seguinte variável de ambiente:

* `AZURE_IOT_MODEL_REPOSITORY_CONNECTION_STRING`

Você pode encontrar essa cadeia de conexão no [portal do Azure Certified para IOT](https://preview.catalog.azureiotsolutions.com) na guia **cadeias de conexão** do **repositório da sua empresa**.

A cadeia de conexão é semelhante ao exemplo a seguir:

```text
HostName={repo host name};RepositoryId={repo ID};SharedAccessKeyName={repo key ID};SharedAccessKey={repo key secret}
```

Depois de definir essas quatro variáveis de ambiente, execute o exemplo da mesma maneira que você executou os outros exemplos:

```cmd/sh
node model_repo.js
```

Este exemplo baixa a interface **ModelDiscovery** e imprime esse modelo no terminal.

### <a name="run-queries-in-iot-hub-based-on-capability-models-and-interfaces"></a>Executar consultas no Hub IoT com base em modelos e interfaces de recursos

A linguagem de consulta do Hub IoT dá suporte a `HAS_INTERFACE` e `HAS_CAPABILITYMODEL`, conforme mostrado nos exemplos a seguir:

```sql
select * from devices where HAS_INTERFACE('id without version', version)
```

```sql
select * from devices where HAS_CAPABILITYMODEL('id without version', version)
```

### <a name="creating-digital-twin-routes"></a>Criando rotas de entrelaçamento digital

Sua solução pode receber notificações de eventos de alteração de troca digital. Para assinar essas notificações, use o [recurso de roteamento do Hub IOT](../iot-hub/iot-hub-devguide-endpoints.md) para enviar as notificações para um ponto de extremidade, como armazenamento de BLOBs, hubs de eventos ou uma fila do barramento de serviço.

Para criar uma rota de entrelaçamento digital:

1. Na portal do Azure, vá para o recurso do Hub IoT.
1. Selecione **Roteamento de mensagens**.
1. Na guia **rotas** , selecione **Adicionar**.
1. Insira um valor no campo **nome** e escolha um **ponto de extremidade**. Se você ainda não configurou um ponto de extremidade, selecione **Adicionar ponto de extremidade**.
1. Na lista suspensa **fonte de dados** , selecione eventos de alteração de **troca digital**.
1. Selecione **Guardar**.

O JSON a seguir mostra um exemplo de um evento de alteração de troca digital:

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

Agora que você aprendeu sobre soluções de serviço que interagem com seus dispositivos IoT Plug and Play, uma próxima etapa sugerida é aprender sobre a [descoberta de modelo](concepts-model-discovery.md).
