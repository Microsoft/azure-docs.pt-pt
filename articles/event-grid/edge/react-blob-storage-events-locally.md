---
title: Reagir aos eventos do módulo de armazenamento Blob - Azure Event Grid IoT Edge Microsoft Docs
description: Reagir aos eventos do módulo de armazenamento Blob
author: arduppal
manager: brymat
ms.author: arduppal
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 230e158a970f8c815b1575403c013e30749124c5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005066"
---
# <a name="tutorial-react-to-blob-storage-events-on-iot-edge-preview"></a>Tutorial: Reagir aos eventos de armazenamento blob em IoT Edge (Preview)
Este artigo mostra-lhe como implementar o Azure Blob Storage no módulo IoT, que funcionaria como um editor de Grade de Eventos para enviar eventos sobre a criação blob e a eliminação de Blob para a Grade de Eventos.  

Para uma visão geral do Azure Blob Storage on IoT Edge, consulte [o Azure Blob Storage on IoT Edge](../../iot-edge/how-to-store-data-blob.md) e as suas funcionalidades.

> [!WARNING]
> Azure Blob Storage na integração IoT Edge com a Grade de Eventos está em Pré-visualização

Para completar este tutorial, você precisará:

* **Azure subscrição** - Crie uma [conta gratuita](https://azure.microsoft.com/free) se ainda não tiver uma. 
* **Azure IoT Hub e IoT Edge dispositivo** - Siga os passos no arranque rápido para [dispositivos](../../iot-edge/quickstart.md) [Linux](../../iot-edge/quickstart-linux.md) ou Windows se ainda não tiver um.

## <a name="deploy-event-grid-iot-edge-module"></a>Implementar módulo de borda IoT da grelha de eventos

Existem várias formas de implantar módulos para um dispositivo IoT Edge e todos eles trabalham para a Azure Event Grid em IoT Edge. Este artigo descreve os passos para implementar a Grade de Eventos no IoT Edge a partir do portal Azure.

>[!NOTE]
> Neste tutorial, irá implantar o módulo De Grelha de Eventos sem persistência. Significa que quaisquer tópicos e subscrições que criar neste tutorial serão eliminados quando recolocar o módulo. Para obter mais informações sobre como configurar a persistência, consulte os seguintes artigos: [Persist state in Linux](persist-state-linux.md) ou [Persist state in Windows](persist-state-windows.md). Para cargas de trabalho de produção, recomendamos que instale o módulo De Grelha de Eventos com persistência.


### <a name="select-your-iot-edge-device"></a>Selecione o seu dispositivo IoT Edge

1. Inicie sessão no [portal do Azure](https://portal.azure.com)
1. Navegue até ao seu Hub IoT.
1. Selecione **IoT Edge** do menu na secção De Gestão automática de **Dispositivos.** 
1. Clique no ID do dispositivo alvo a partir da lista de dispositivos
1. Selecione **módulos de conjunto**. Mantenha a página aberta. Continuará com os passos na próxima secção.

### <a name="configure-a-deployment-manifest"></a>Configure um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais os módulos a implementar, como os dados fluem entre os módulos e as propriedades desejadas dos gémeos módulos. O portal Azure tem um assistente que o acompanha através da criação de um manifesto de implantação, em vez de construir o documento JSON manualmente.  Tem três etapas: **Adicionar módulos,** **especificar rotas** e **revisão.**

### <a name="add-modules"></a>Adicionar módulos

1. Na secção **de Módulos de Implementação,** selecione **Adicionar**
1. A partir dos tipos de módulos na lista de drop-down, selecione **IoT Edge Module**
1. Fornecer o nome, imagem, recipiente criar opções do recipiente:

   * **Nome**: eventgridmodule
   * **Imagem URI:**`mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Opções de criação de contentores:**

    ```json
        {
          "Env": [
           "inbound__serverAuth__tlsPolicy=enabled",
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

 1. Clique em **Guardar**
 1. Continue na secção seguinte para adicionar o módulo Azure Event Grid Subscriber antes de os colocar em conjunto.

    >[!IMPORTANT]
    > Neste tutorial, aprenderá a implantar o módulo 'Grade de Eventos' para permitir tanto pedidos HTTP/HTTPs como desativação do cliente. Para cargas de trabalho de produção, recomendamos que permita apenas pedidos de HTTPs e assinantes com autenticação do cliente ativado. Para obter mais informações sobre como configurar o módulo de Grelha de Eventos de forma segura, consulte [Segurança e autenticação.](security-authentication.md)
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>Implementar módulo IoT Edge assinante da grelha de eventos

Esta secção mostra-lhe como implantar outro módulo IoT que funcionaria como um manipulador de eventos para o qual os eventos podem ser entregues.

### <a name="add-modules"></a>Adicionar módulos

1. Na secção **Módulos de Implantação,** selecione **Adicionar** novamente. 
1. A partir dos tipos de módulos na lista de drop-down, selecione **IoT Edge Module**
1. Fornecer o nome, imagem e recipiente criar opções do recipiente:

   * **Nome**: assinante
   * **Imagem URI:**`mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **Opções de criação de contentores:** Nenhuma
1. Clique em **Guardar**
1. Continue até a secção seguinte para adicionar o módulo de armazenamento Azure Blob

## <a name="deploy-azure-blob-storage-module"></a>Implementar módulo de armazenamento Azure Blob

Esta secção mostra-lhe como implantar o módulo de armazenamento Azure Blob, que funcionaria como uma editora de Event Grid publicando eventos criados e apagados.

### <a name="add-modules"></a>Adicionar módulos

1. Na secção **de Módulos de Implementação,** selecione **Adicionar**
2. A partir dos tipos de módulos na lista de drop-down, selecione **IoT Edge Module**
3. Fornecer o nome, imagem e recipiente criar opções do recipiente:

   * **Nome**: azureblobstorageoniotedge
   * **Imagem URI**: mcr.microsoft.com/azure-blob-storage:latest
   * **Opções de criação de contentores:**

   ```json
       {
         "Env":[
           "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
           "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>",
           "EVENTGRID_ENDPOINT=http://<event grid module name>:5888"
         ],
         "HostConfig":{
           "Binds":[
               "<storage mount>"
           ],
           "PortBindings":{
             "11002/tcp":[{"HostPort":"11002"}]
           }
         }
       }
   ```

   > [!IMPORTANT]
   > - O módulo blob Storage pode publicar eventos usando HTTPS e HTTP. 
   > - Se ativou a autenticação baseada no cliente para o EventGrid, certifique-se de atualizar o valor de EVENTGRID_ENDPOINT para permitir https, como este: `EVENTGRID_ENDPOINT=https://<event grid module name>:4438` .
   > - Adicione também outra variável ambiental `AllowUnknownCertificateAuthority=true` ao Json acima. Ao falar com o EventGrid sobre HTTPS, **a AllowUnknownCertificateAuthority** permite que o módulo de armazenamento confie em certificados de servidor EventGrid auto-assinados.

4. Atualize o JSON que copiou com as seguintes informações:

   - Substitua `<your storage account name>` por um nome de que se lembre. Os nomes das contas devem ter entre 3 a 24 caracteres, com letras e números minúsculos. Sem espaços.

   - `<your storage account key>`Substitua-a por uma tecla base64 de 64 bytes. Pode gerar uma chave com ferramentas como [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Você usará estas credenciais para aceder ao armazenamento de bolhas de outros módulos.

   - `<event grid module name>`Substitua-o pelo nome do módulo 'Grelha de Eventos'.
   - Substitua `<storage mount>` de acordo com o seu sistema de funcionamento do contentor.
     - Para recipientes Linux, **o meu volume:/blobroot**
     - Para recipientes Windows,**o meu volume:C:/BlobRoot**

5. Clique em **Guardar**
6. Clique **ao lado** para continuar na secção de rotas

    > [!NOTE]
    > Se estiver a utilizar um Azure VM como dispositivo de borda, adicione uma regra de entrada de porta para permitir o tráfego de entrada nas portas hospedeiras utilizadas neste tutorial: 4438, 5888, 8080 e 11002. Para obter instruções sobre a adição da regra, consulte [como abrir portas a um VM](../../virtual-machines/windows/nsg-quickstart-portal.md).

### <a name="setup-routes"></a>Rotas de configuração

Mantenha as rotas predefinidos e selecione **Seguinte** para continuar na secção de revisão

### <a name="review-deployment"></a>Implementação de revisão

1. A secção de revisão mostra-lhe o manifesto de implantação JSON que foi criado com base nas suas seleções na secção anterior. Confirme que vê os seguintes quatro módulos: **$edgeAgent**, **$edgeHub,** **eventgridmodule,** **assinante** e **azureblobstorageoniotedge** que estão todos a ser implantados.
2. Reveja as suas informações de implementação e, em seguida, **selecione Enviar por isso**.

## <a name="verify-your-deployment"></a>Verifique a sua implantação

1. Depois de submeter a implementação, volte à página IoT Edge do seu hub IoT.
2. Selecione o **dispositivo IoT Edge** que direcionou com a implementação para abrir os seus detalhes.
3. Nos dados do dispositivo, verifique se os módulos eventgridmodule, assinante e azureblobstorageoniotedge estão listados como **especificados na implementação** e **reportados pelo dispositivo**.

   Pode levar alguns momentos para que o módulo seja iniciado no dispositivo e depois reportado de volta ao IoT Hub. Refresque a página para ver um estado atualizado.

## <a name="publish-blobcreated-and-blobdeleted-events"></a>Publicar eventos BlobCreated e BlobDeleted

1. Este módulo cria automaticamente o tópico **MicrosoftStorage**. Verifique se existe
    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview
    ```

    Resultado do exemplo:

    ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage",
            "name": "MicrosoftStorage",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/MicrosoftStorage/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
    ```

    > [!IMPORTANT]
    > - Para o fluxo HTTPS, se a autenticação do cliente estiver ativada através da tecla SAS, a tecla SAS especificada anteriormente deve ser adicionada como cabeçalho. Daí que o pedido de caracóis seja: `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`
    > - Para o fluxo HTTPS, se a autenticação do cliente estiver ativada através de certificado, o pedido de caracóis será: `curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`

2. Os assinantes podem inscrever-se para eventos publicados num tema. Para receber qualquer evento, terá de criar uma subscrição de Event Grid para o tópico **MicrosoftStorage.**
    1. Crie blobsubscription.jscom o seguinte conteúdo. Para mais detalhes sobre a carga útil, consulte a nossa [documentação da API](api.md)

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
       > A propriedade **endpointType** especifica que o assinante é um **Webhook**.  O **endpointUrl** especifica o URL no qual o assinante está a ouvir eventos. Este URL corresponde à amostra da Função Azure que implementou anteriormente.

    2. Executar o seguinte comando para criar uma subscrição para o tópico. Confirme que vê o código de estado `200 OK` HTTP.

       ```sh
       curl -k -H "Content-Type: application/json" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       > [!IMPORTANT]
       > - Para o fluxo HTTPS, se a autenticação do cliente estiver ativada através da tecla SAS, a tecla SAS especificada anteriormente deve ser adicionada como cabeçalho. Daí que o pedido de caracóis seja: `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview` 
       > - Para o fluxo HTTPS, se a autenticação do cliente estiver ativada através de certificado, o pedido de caracóis será:`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

    3. Executar o seguinte comando para verificar se a subscrição foi criada com sucesso. HTTP Código de Estado de 200 OK deve ser devolvido.

       ```sh
       curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       Resultado do exemplo:

       ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription5",
          "properties": {
            "Topic": "MicrosoftStorage",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
       ```

       > [!IMPORTANT]
       > - Para o fluxo HTTPS, se a autenticação do cliente estiver ativada através da tecla SAS, a tecla SAS especificada anteriormente deve ser adicionada como cabeçalho. Daí que o pedido de caracóis seja: `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`
       > - Para o fluxo HTTPS, se a autenticação do cliente estiver ativada através de certificado, o pedido de caracóis será: `curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

3. Baixe [o Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) e [conecte-o ao seu armazenamento local](../../iot-edge/how-to-store-data-blob.md#connect-to-your-local-storage-with-azure-storage-explorer)

## <a name="verify-event-delivery"></a>Verificar a entrega do evento

### <a name="verify-blobcreated-event-delivery"></a>Verificar entrega de eventos BlobCreated

1. Faça upload de ficheiros como blobs de bloco para o armazenamento local do Azure Storage Explorer, e o módulo publicará automaticamente criar eventos. 
2. Confira os registos de assinantes para criar evento. Siga os passos para [verificar a entrega do evento](pub-sub-events-webhook-local.md#verify-event-delivery)

    Saída da amostra:

    ```json
            Received Event:
            {
              "id": "d278f2aa-2558-41aa-816b-e6d8cc8fa140",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobCreated",
              "eventTime": "2019-10-01T21:35:17.7219554Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "PutBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "ef1c387b-4c3c-4ac0-8e04-ff73c859bfdc",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

### <a name="verify-blobdeleted-event-delivery"></a>Verifique a entrega do evento BlobDeleted

1. Elimine as bolhas do armazenamento local utilizando o Azure Storage Explorer, e o módulo publicará automaticamente eventos de eliminação. 
2. Confira os registos de subscrição para eliminar o evento. Siga os passos para [verificar a entrega do evento](pub-sub-events-webhook-local.md#verify-event-delivery)

    Saída da amostra:
    
    ```json
            Received Event:
            {
              "id": "ac669b6f-8b0a-41f3-a6be-812a3ce6ac6d",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobDeleted",
              "eventTime": "2019-10-01T21:36:09.2562941Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "DeleteBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "2996bbfb-c819-4d02-92b1-c468cc67d8c6",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

Parabéns! Completou o tutorial. As seguintes secções fornecem detalhes sobre as propriedades do evento.

### <a name="event-properties"></a>Propriedades do evento

Aqui está a lista de propriedades de eventos suportados e seus tipos e descrições. 

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| tópico | string | Caminho completo de recursos para a fonte do evento. Este campo não é escrito. O Event Grid fornece este valor. |
| subject | string | Caminho definido pelo publicador para o assunto do evento. |
| eventType | string | Um dos tipos de eventos registados para esta origem de evento. |
| eventTime | string | O tempo que o evento é gerado com base no tempo UTC do fornecedor. |
| ID | string | Identificador único para o evento. |
| dados | objeto | Dados do evento de armazenamento de bolhas. |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. O Event Grid define o esquema das propriedades de nível superior. O Event Grid fornece este valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| api | string | A operação que desencadeou o evento. Pode ser um dos seguintes valores: <ul><li>BlobCreated - valores permitidos são: `PutBlob` e `PutBlockList`</li><li>BlobDeleted - valores permitidos são `DeleteBlob` , `DeleteAfterUpload` e `AutoDelete` . <p>O `DeleteAfterUpload` evento é gerado quando a bolha é automaticamente eliminada porque deleteAfterUpload propriedade desejada é definida como verdadeira. </p><p>`AutoDelete` o evento é gerado quando o blob é automaticamente eliminado porque eliminaMinutes o valor de propriedade desejado expirou.</p></li></ul>|
| clienteRequestId | string | um ID de pedido fornecido pelo cliente para a operação de API de armazenamento. Este ID pode ser usado para correlacionar com os registos de diagnóstico do Azure Storage utilizando o campo "cliente-pedido-id" nos registos, e pode ser fornecido em pedidos do cliente usando o cabeçalho "x-ms-cliente-pedido-id". Para mais informações, consulte [o Formato de Registo](/rest/api/storageservices/storage-analytics-log-format). |
| requestId | string | ID de pedido gerado pelo serviço para a operação de API de armazenamento. Pode ser usado para correlacionar com os registos de diagnóstico de armazenamento Azure usando o campo "solicit-id-header" nos registos e é devolvido do início da chamada API no cabeçalho 'x-ms-request-id'. Ver [Formato de Registo](/rest/api/storageservices/storage-analytics-log-format). |
| eTag | string | O valor que pode usar para realizar operações condicionalmente. |
| conteúdoType | string | O tipo de conteúdo especificado para a bolha. |
| contentAver | número inteiro | O tamanho da bolha em bytes. |
| BlobType | string | O tipo de bolha. Os valores válidos são "BlockBlob" ou "PageBlob". |
| url | string | O caminho para a bolha. <br>Se o cliente utilizar uma API Blob REST, então o url tem esta estrutura: *\<storage-account-name\> .blob.core.windows.net/ \<container-name\> / \<file-name\>*. <br>Se o cliente utilizar uma API de Armazenamento de Data Lake, então o url tem esta estrutura: *\<storage-account-name\> .dfs.core.windows.net/ \<file-system-name\> / \<file-name\>*. |


## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos da documentação blob Storage:

- [Eventos de armazenamento de blob filtrante](../../storage/blobs/storage-blob-event-overview.md#filtering-events)
- [Práticas recomendadas para consumir eventos de armazenamento de bolhas](../../storage/blobs/storage-blob-event-overview.md#practices-for-consuming-events)

Neste tutorial, publicou eventos criando ou eliminando bolhas num Azure Blob Storage. Veja os outros tutoriais para aprender a encaminhar eventos para cloud (Azure Event Hub ou Azure IoT Hub): 

- [Reencaminhar eventos para o Azure Event Grid](forward-events-event-grid-cloud.md)
- [Reencaminhar eventos para o Hub IoT do Azure](forward-events-iothub.md)
