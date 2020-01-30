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
ms.openlocfilehash: e403d690470f3c4f1d0c8e565e90641d9c114a80
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844559"
---
# <a name="tutorial-publish-subscribe-to-events-locally"></a>Tutorial: Publicar, subscrever eventos localmente

Este artigo percorre-o todos os passos necessários para publicar e subscrever eventos usando a Grelha de Eventos no IoT Edge.

> [!NOTE]
> Para conhecer tópicos e subscrições da Azure Event Grid, consulte os Conceitos de Grelha de [Eventos.](concepts.md)

## <a name="prerequisites"></a>Pré-requisitos 
Para concluir este tutorial, você precisará de:

* **Assinatura do Azure** – crie uma [conta gratuita](https://azure.microsoft.com/free) se você ainda não tiver uma. 
* **Hub IOT do Azure e IOT Edge dispositivo** -siga as etapas no guia de início rápido para dispositivos [Linux](../../iot-edge/quickstart-linux.md) ou [Windows](../../iot-edge/quickstart.md) se você ainda não tiver um.

## <a name="deploy-event-grid-iot-edge-module"></a>Implantar o módulo de IoT Edge da grade de eventos

Há várias maneiras de implantar módulos em um dispositivo IoT Edge e todos eles funcionam para a grade de eventos do Azure no IoT Edge. Este artigo descreve as etapas para implantar a grade de eventos em IoT Edge do portal do Azure.

>[!NOTE]
> Neste tutorial, você implantará o módulo de grade de eventos sem persistência. Isso significa que todos os tópicos e assinaturas criados neste tutorial serão excluídos quando você reimplantar o módulo. Para obter mais informações sobre como configurar a persistência, consulte os seguintes artigos: [persistir estado no Linux](persist-state-linux.md) ou [persistir o estado no Windows](persist-state-windows.md). Para cargas de trabalho de produção, recomendamos que você instale o módulo de grade de eventos com persistência.


### <a name="select-your-iot-edge-device"></a>Selecione seu dispositivo IoT Edge

1. Inicie sessão no [portal do Azure](https://portal.azure.com)
1. Navegue até o Hub IoT.
1. Selecione **IOT Edge** no menu da seção **Gerenciamento de dispositivo automático** . 
1. Clique na ID do dispositivo de destino na lista de dispositivos
1. Selecione **Definir Módulos**. Mantenha a página aberta. Você continuará com as etapas na próxima seção.

### <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais os módulos para implementar, como os dados fluem entre os módulos e propriedades pretendidas do duplos de módulo. O portal do Azure tem um assistente que o orienta na criação de um manifesto de implantação, em vez de criar o documento JSON manualmente.  Ele tem três passos: **adicionar módulos**, **especificar rotas**, e **rever implementação**.

### <a name="add-modules"></a>Adicionar módulos

1. Na seção **módulos de implantação** , selecione **Adicionar**
1. Nos tipos de módulos na lista suspensa, selecione **IOT Edge módulo**
1. Forneça as opções nome, imagem e contêiner criar do contêiner:

   * **Nome**: eventgridmodule
   * **URI da imagem**: `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Opções de criação de contêiner**:

   [!INCLUDE [event-grid-edge-module-version-update](../../../includes/event-grid-edge-module-version-update.md)]

    ```json
        {
          "Env": [
            "inbound__clientAuth__clientCert__enabled=false",
            "outbound__webhook__httpsOnly=false"
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
 1. Continue na secção seguinte para adicionar o módulo Funções Azure antes de os colocar juntos.

    >[!IMPORTANT]
    > Neste tutorial, irá implementar o módulo Event Grid com a autenticação do cliente desativada e permitirá aos subscritores http. Para cargas de trabalho de produção, recomendamos que ative a autenticação do cliente e permita apenas subscritores HTTPs. Para obter mais informações sobre como configurar o módulo de grade de eventos com segurança, consulte [segurança e autenticação](security-authentication.md).
    > 
    > Se estiver a utilizar um VM Azure como dispositivo de borda, adicione uma regra de entrada para permitir o tráfego de entrada na porta 4438. Para obter instruções sobre como adicionar a regra, consulte [como abrir portas para uma VM](../../virtual-machines/windows/nsg-quickstart-portal.md).
    

## <a name="deploy-azure-function-iot-edge-module"></a>Implantar o módulo IoT Edge do Azure function

Esta seção mostra como implantar o módulo Azure Functions IoT, que atuaria como um assinante de grade de eventos no qual os eventos podem ser entregues.

>[!IMPORTANT]
>Nesta seção, você implantará um exemplo de módulo de assinatura baseado em função do Azure. Naturalmente, pode ser qualquer módulo IoT personalizado que possa escutar solicitações HTTP POST.


### <a name="add-modules"></a>Adicionar módulos

1. Na seção **módulos de implantação** , selecione **Adicionar** novamente. 
1. Nos tipos de módulos na lista suspensa, selecione **IOT Edge módulo**
1. Forneça as opções nome, imagem e contêiner criar do contêiner:

   * **Nome**: assinante
   * **URI da imagem**: `mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber-azfunc:latest`
   * **Opções de criação de contêiner**:

       ```json
            {
              "HostConfig": {
                "PortBindings": {
                  "80/tcp": [
                    {
                      "HostPort": "8080"
                    }
                  ]
                }
              }
            }
       ```

1. Clicar em **Guardar**
1. Clique em **Avançar** para continuar na seção rotas

 ### <a name="setup-routes"></a>Rotas de instalação

Mantenha as rotas padrão e selecione **Avançar** para continuar na seção revisão

### <a name="submit-the-deployment-request"></a>Submeter o pedido de implantação

1. A seção revisão mostra o manifesto de implantação JSON criado com base em suas seleções na seção anterior. Confirme que vê ambos os módulos: **eventgridmodule** e **assinante** listado no JSON. 
1. Reveja as suas informações de implantação, em seguida, selecione **submeter**. Depois de submeter a implementação, volte à página do **dispositivo.**
1. Na **secção Módulos,** verifique se tanto os módulos **de eventgrid** como **de subscritores** estão listados. E, verifique se os **especificados na implementação** e **reportados pelas** colunas do dispositivo estão definidos para **Sim**.

    Pode levar alguns minutos para que o módulo seja iniciado no dispositivo e reportado de volta ao Hub IoT. Atualize a página para ver um status atualizado.

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

1. Executar o seguinte comando para criar um tópico de grelha de eventos. Confirme que você vê o código de status HTTP `200 OK`.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

1. Executar o seguinte comando para verificar o tópico foi criado com sucesso. O código de status HTTP de 200 OK deve ser retornado.

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

Os assinantes podem se registrar para eventos publicados em um tópico. Para receber qualquer evento, terá de criar uma subscrição da Event Grid para um tema de interesse.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Crie subscrição.json com o seguinte conteúdo. Para obter detalhes sobre a carga, consulte nossa [documentação de API](api.md)

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "http://subscriber:80/api/subscriber"
              }
            }
          }
        }
    ```

    >[!NOTE]
    > A propriedade **EndpointType** especifica que o assinante é um **webhook**.  O **endpointUrl** especifica a URL na qual o assinante está escutando eventos. Essa URL corresponde ao exemplo de Azure function que você implantou anteriormente.
2. Execute o comando a seguir para criar uma assinatura para o tópico. Confirme que você vê o código de status HTTP `200 OK`.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```
3. Execute o comando a seguir para verificar se a assinatura foi criada com êxito. O código de status HTTP de 200 OK deve ser retornado.

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
                "endpointUrl": "http://subscriber:80/api/subscriber"
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

## <a name="verify-event-delivery"></a>Verificar a entrega de eventos

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
        Received event data [
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
          ]
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

