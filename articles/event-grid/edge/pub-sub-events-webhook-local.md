---
title: Publique, subscreva eventos localmente - Azure Event Grid IoT Edge  Microsoft Docs
description: Publique, subscreva eventos localmente usando Webhook com Grelha de Eventos em IoT Edge
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ba82b1bea4753cd51e275a78b248247032d79a01
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281005"
---
# <a name="tutorial-publish-subscribe-to-events-locally"></a>Tutorial: Publicar, subscrever eventos localmente

Este artigo percorre-o todos os passos necessários para publicar e subscrever eventos usando a Grelha de Eventos no IoT Edge.

> [!NOTE]
> Para conhecer tópicos e subscrições da Azure Event Grid, consulte os Conceitos de Grelha de [Eventos.](concepts.md)

## <a name="prerequisites"></a>Pré-requisitos 
Para completar este tutorial, necessitará:

* **Assinatura Azure** - Crie uma [conta gratuita](https://azure.microsoft.com/free) se ainda não tiver uma. 
* **Dispositivo Azure IoT Hub e IoT Edge** - Siga os passos no arranque rápido para [dispositivos](../../iot-edge/quickstart.md) [Linux](../../iot-edge/quickstart-linux.md) ou Windows se ainda não tiver um.

## <a name="deploy-event-grid-iot-edge-module"></a>Implementar módulo De Borda da Grelha de Eventos IoT

Existem várias formas de implementar módulos para um dispositivo IoT Edge e todos eles trabalham para a Grelha de Eventos Azure em IoT Edge. Este artigo descreve os passos para implantar a Grelha de Eventos na Borda IoT do portal Azure.

>[!NOTE]
> Neste tutorial, irá implantar o módulo Da Grelha de Eventos sem persistência. Significa que quaisquer tópicos e subscrições que criar neste tutorial serão eliminados quando reimplantar o módulo. Para obter mais informações sobre como configurar a persistência, consulte os seguintes artigos: [Persistir no estado de Linux](persist-state-linux.md) ou Persistir no [Windows](persist-state-windows.md). Para cargas de trabalho de produção, recomendamos que instale o módulo Da Grelha de Eventos com persistência.


### <a name="select-your-iot-edge-device"></a>Selecione o seu dispositivo IoT Edge

1. Inicie sessão no [portal do Azure](https://portal.azure.com)
1. Navegue para o seu Hub IoT.
1. Selecione **IoT Edge** do menu na secção De Gestão automática de **Dispositivos.** 
1. Clique na identificação do dispositivo alvo a partir da lista de dispositivos
1. Selecione **Definir Módulos**. Mantenha a página aberta. Continuará com os passos na próxima secção.

### <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais os módulos para implementar, como os dados fluem entre os módulos e propriedades pretendidas do duplos de módulo. O portal Azure tem um assistente que o acompanha através da criação de um manifesto de implantação, em vez de construir manualmente o documento JSON.  Tem três **passos: Adicionar módulos,** **especificar rotas,** e **rever a implementação**.

### <a name="add-modules"></a>Adicionar módulos

1. Na secção Módulos de **Implementação,** selecione **Adicionar**
1. A partir dos tipos de módulos na lista de drop-down, selecione **IoT Edge Module**
1. Forneça o nome, imagem, recipiente criar opções do recipiente:

   * **Nome**: eventgridmodule
   * **Imagem URI**: `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **O recipiente cria opções:**

   [!INCLUDE [event-grid-edge-module-version-update](../../../includes/event-grid-edge-module-version-update.md)]

    ```json
        {
          "Env": [
            "inbound__clientAuth__clientCert__enabled=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```    
 1. Clicar em **Guardar**
 1. Continue na secção seguinte para adicionar o módulo de Assinante da Grelha de Eventos Azure antes de os colocar juntos.

    >[!IMPORTANT]
    > Neste tutorial, irá implementar o módulo Event Grid com a autenticação do cliente desativada. Para cargas de trabalho de produção, recomendamos que ative a autenticação do cliente. Para obter mais informações sobre como configurar o módulo de Rede de Eventos de forma segura, consulte [segurança e autenticação.](security-authentication.md)
    > 
    > Se estiver a utilizar um VM Azure como dispositivo de borda, adicione uma regra de entrada para permitir o tráfego de entrada na porta 4438. Para obter instruções sobre a adição da regra, consulte [Como abrir portas a um VM](../../virtual-machines/windows/nsg-quickstart-portal.md).
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>Implementar módulo de subscritor de rede de eventos IoT Edge

Esta secção mostra-lhe como implementar outro módulo IoT que funcionaria como um manipulador de eventos para o qual os eventos podem ser entregues.

### <a name="add-modules"></a>Adicionar módulos

1. Na secção Módulos de **Implantação,** selecione **Adicionar** novamente. 
1. A partir dos tipos de módulos na lista de drop-down, selecione **IoT Edge Module**
1. Fornecer o nome, imagem e recipiente criar opções do recipiente:

   * **Nome**: assinante
   * **Imagem URI**: `mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **Recipiente Criar opções**: Nenhum
1. Clicar em **Guardar**
1. Clique **em próximo** para continuar na secção rotas

 ### <a name="setup-routes"></a>Rotas de configuração

Mantenha as rotas predefinidas e selecione **Next** para continuar na secção de revisão

### <a name="submit-the-deployment-request"></a>Submeter o pedido de implantação

1. A secção de revisão mostra-lhe o manifesto de implantação jSON que foi criado com base nas suas seleções na secção anterior. Confirme que vê ambos os módulos: **eventgridmodule** e **assinante** listado no JSON. 
1. Reveja as informações de implementação e, em seguida, selecione **Enviar**. Depois de submeter a implementação, volte à página do **dispositivo.**
1. Na **secção Módulos,** verifique se tanto os módulos **de eventgrid** como **de subscritores** estão listados. E, verifique se os **especificados na implementação** e **reportados pelas** colunas do dispositivo estão definidos para **Sim**.

    Pode levar alguns momentos para o módulo ser iniciado no dispositivo e depois reportado de volta ao IoT Hub. Refresque a página para ver um estado atualizado.

## <a name="create-a-topic"></a>Criar um tópico

Como editor de um evento, você precisa criar um tópico de grelha de eventos. Em Azure Event Grid, um tópico refere-se a um ponto final para onde as editoras podem enviar eventos.

1. Crie tópico.json com o seguinte conteúdo. Para mais detalhes sobre a carga útil, consulte a nossa [documentação da API.](api.md)

    ```json
        {
          "name": "sampleTopic1",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```

1. Executar o seguinte comando para criar um tópico de grelha de eventos. Confirme se vê que o código de estado HTTP está `200 OK`.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

1. Executar o seguinte comando para verificar o tópico foi criado com sucesso. Http Status Code of 200 OK deve ser devolvido.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

   Resultado do exemplo:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1",
            "name": "sampleTopic1",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>Criar uma subscrição de evento

Os assinantes podem inscrever-se para eventos publicados num tópico. Para receber qualquer evento, terá de criar uma subscrição da Event Grid para um tema de interesse.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Crie subscrição.json com o seguinte conteúdo. Para mais detalhes sobre a carga útil, consulte a nossa [documentação da API](api.md)

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

    >[!NOTE]
    > A propriedade **endpointType** especifica que o assinante é um **Webhook**.  O **endpointUrl** especifica o URL no qual o assinante está a ouvir eventos. Este URL corresponde à amostra do Assinante Azure que implementou anteriormente.
2. Executar o seguinte comando para criar uma subscrição para o tópico. Confirme se vê que o código de estado HTTP está `200 OK`.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```
3. Executar o seguinte comando para verificar se a subscrição foi criada com sucesso. Http Status Code of 200 OK deve ser devolvido.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```

    Resultado do exemplo:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1/eventSubscriptions/sampleSubscription1",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription1",
          "properties": {
            "Topic": "sampleTopic1",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>Publicar um evento

1. Crie evento.json com o seguinte conteúdo. Para mais detalhes sobre a carga útil, consulte a nossa [documentação da API.](api.md)

    ```json
        [
          {
            "id": "eventId-func-0",
            "eventType": "recordInserted",
            "subject": "myapp/vehicles/motorcycles",
            "eventTime": "2019-07-28T21:03:07+00:00",
            "dataVersion": "1.0",
            "data": {
              "make": "Ducati",
              "model": "Monster"
            }
          }
        ]
    ```
1. Executar o seguinte comando para publicar um evento.

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Verificar a entrega do evento

1. SSH ou RDP no seu VM IoT Edge.
1. Verifique os registos dos assinantes:

    No Windows, execute o seguinte comando:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine container logs subscriber
    ```

   Em Linux, corra o seguinte comando:

    ```sh
    sudo docker logs subscriber
    ```

    Resultado do exemplo:

    ```sh
        Received Event:
            {
              "id": "eventId-func-0",
              "topic": "sampleTopic1",
              "subject": "myapp/vehicles/motorcycles",
              "eventType": "recordInserted",
              "eventTime": "2019-07-28T21:03:07+00:00",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "make": "Ducati",
                "model": "Monster"
              }
            }
    ```

## <a name="cleanup-resources"></a>Recursos de limpeza

* Execute o seguinte comando para apagar o tópico e todas as suas subscrições.

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```
* Elimine o módulo de subscrição do seu dispositivo IoT Edge.


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, criou um tópico de grelha de eventos, subscrição e eventos publicados. Agora que conhece os passos básicos, consulte os seguintes artigos: 

- Para resolver problemas com a utilização da Grelha de Eventos Azure na IoT Edge, consulte [o guia de resolução de problemas](troubleshoot.md).
- Criar/atualizar subscrição com [filtros](advanced-filtering.md).
- Ativar a persistência do módulo De Rede de Eventos no [Linux](persist-state-linux.md) [ou](persist-state-windows.md) windows
- Siga [a documentação](configure-client-auth.md) para configurar a autenticação do cliente
- Eventos de reencaminhamento para Funções Azure na nuvem seguindo este [tutorial](pub-sub-events-webhook-cloud.md)
- [Reaja aos eventos de armazenamento de Blob na Borda IoT](react-blob-storage-events-locally.md)
- [Monitorize tópicos e subscrições no limite](monitor-topics-subscriptions.md)

