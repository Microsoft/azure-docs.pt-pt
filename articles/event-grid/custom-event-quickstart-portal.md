---
title: 'Quickstart: Envie eventos personalizados para web endpoint - Event Grid, portal Azure'
description: 'Quickstart: Use Azure Event Grid e portal Azure para publicar um tópico personalizado, e subscrever eventos para esse tema. Os eventos são processados por um aplicativo web.'
services: event-grid
keywords: ''
author: spelluru
ms.author: spelluru
ms.date: 11/05/2019
ms.topic: quickstart
ms.service: event-grid
ms.custom: seodec18
ms.openlocfilehash: 398e63ec9a8b9e1b16d8ffcee538351fc6572de9
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78387030"
---
# <a name="quickstart-route-custom-events-to-web-endpoint-with-the-azure-portal-and-event-grid"></a>Início rápido: Encaminhar eventos personalizados para o ponto final da web com o portal do Azure e o Event Grid

O Azure Event Grid é um serviço de eventos para a cloud. Neste artigo, o portal do Azure serve para criar um tópico personalizado, subscrever o tópico personalizado e acionar o evento para ver o resultado. Normalmente, envia eventos para um ponto final que processa os dados de eventos e efetua ações. No entanto, para simplificar este artigo, vai enviar eventos para uma aplicação Web que recolhe e apresenta as mensagens.

Quando tiver terminado, verá que os dados do evento foram enviados para a aplicação Web.

![Ver resultados](./media/custom-event-quickstart-portal/view-result.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="create-a-custom-topic"></a>Criar um tópico personalizado

Um tópico do Event Grid fornece um ponto final definido pelo utilizador no qual publica os eventos. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione **todos os serviços** no menu de navegação à esquerda, procure a **Grelha de Eventos,** e selecione Tópicos da Grelha de **Eventos**. 

    ![Selecione tópicos da grelha de eventos](./media/custom-event-quickstart-portal/select-event-grid-topics.png)
3. Na página tópicos da grelha de **eventos,** selecione **+ Adicione** na barra de ferramentas. 

    ![Adicione o botão tópico da grelha de eventos](./media/custom-event-quickstart-portal/add-event-grid-topic-button.png)
4. Na página **Criar Tópico,** siga estes passos:
    1. Forneça um **nome** único para o tópico personalizado. O nome do tópico deve ser exclusivo, porque este é representado por uma entrada DNS. Não utilize o nome apresentado na imagem. Em vez disso, criar seu próprio nome - tem de ter entre 3 e 50 carateres e conter apenas valores-z, A-Z, 0-9, e "-".
    2. Selecione a sua **subscrição** do Azure.
    3. Selecione um grupo de recursos existente ou selecione **Criar novo,** e introduza um **nome** para o **grupo de recursos**.
    4. Selecione um **local** para o tópico da grelha de eventos.
    5. Mantenha o valor padrão da **Grelha de Eventos Schema** para o campo **Event Schema.** 

       ![Criar página tópico](./media/custom-event-quickstart-portal/create-custom-topic.png)
    6. Selecione **Criar**. 
5. Depois de o tópico personalizado ter sido criado, verá a notificação de êxito. Selecione **Ir para o grupo de recursos**. 

   ![Ver notificação de êxito](./media/custom-event-quickstart-portal/success-notification.png)
6. Na página do **Grupo de Recursos,** selecione o tópico da grelha de eventos. 

   ![Selecione o recurso tópico da grelha de eventos](./media/custom-event-quickstart-portal/select-event-grid-topic.png)
7. Você vê a página **de Tópicode Evento** para a sua grelha de eventos. Mantenha esta página aberta. Usa-o mais tarde, no início. 

    ![Página inicial do Tópico da Grelha de Eventos](./media/custom-event-quickstart-portal/event-grid-topic-home-page.png)

## <a name="create-a-message-endpoint"></a>Criar um ponto final de mensagem
Antes de criar uma subscrição para o tópico personalizado, crie um ponto final para a mensagem do evento. Normalmente, o ponto final executa as ações com base nos dados do evento. Para simplificar este início rápido, vai implementar uma [aplicação Web pré-criada](https://github.com/Azure-Samples/azure-event-grid-viewer) para apresentar as mensagens de evento. A solução implementada inclui um plano do Serviço de Aplicações, uma aplicação Web do Serviço de Aplicações e o código de origem do GitHub.

1. Na página do artigo, selecione **Deploy para Azure** para implementar a solução para a sua subscrição. No portal do Azure, indique os valores para os parâmetros.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
1. A implementação pode demorar alguns minutos. Após a implementação ter sido concluída com êxito, verifique a aplicação Web para verificar se está em execução. Num browser, navegue para: `https://<your-site-name>.azurewebsites.net`
1. Vê o site, mas ainda não foram publicados eventos no mesmo.

   ![Ver novo site](./media/custom-event-quickstart-portal/view-site.png)

## <a name="subscribe-to-custom-topic"></a>Subscrever um tópico personalizado

Subscreva um tópico do Event Grid para comunicar ao Event Grid os eventos que pretende controlar e para onde enviar os eventos.

1. Agora, na página tópico da grelha de **eventos** para o seu tópico personalizado, selecione **+ Subscrição de eventos** na barra de ferramentas.

   ![Adicionar subscrição de evento](./media/custom-event-quickstart-portal/new-event-subscription.png)
2. Na página de Subscrição de **Eventos Create,** siga estes passos:
    1. Insira um **nome** para a subscrição do evento.
    3. Selecione **Web Hook** para o **tipo endpoint**. 
    4. Escolha **Selecione um ponto final**. 

       ![Indicar os valores da subscrição de evento](./media/custom-event-quickstart-portal/provide-subscription-values.png)
    5. Para o ponto final do webhook, indique o URL da sua aplicação Web e adicione `api/updates` ao URL da home page. Selecione **Confirmar a Seleção**.

       ![Indicar o URL de ponto final](./media/custom-event-quickstart-portal/provide-endpoint.png)
    6. De volta à página de Subscrição de **Eventos Create,** selecione **Criar**.

3. Verifique a aplicação Web novamente e repare que um evento de validação de subscrição foi enviado para a mesma. Selecione o ícone do olho para expandir os dados do evento. O Event Grid envia o evento de validação para que o ponto final possa verificar que pretende receber dados de eventos. A aplicação Web inclui código para validar a subscrição.

    ![Ver evento da subscrição](./media/custom-event-quickstart-portal/view-subscription-event.png)

## <a name="send-an-event-to-your-topic"></a>Enviar um evento para o seu tópico

Agora, vamos acionar um evento para ver como o Event Grid distribui a mensagem para o ponto final. Utilize a CLI do Azure ou o PowerShell para enviar um evento de teste para o seu tópico personalizado. Normalmente, uma aplicação ou serviço do Azure enviaria os dados do evento.

O primeiro exemplo utiliza a CLI do Azure. Obtém o URL e a chave para o tópico personalizado e os dados do evento de exemplo. Utilize o nome do tópico personalizado de `<topic name>`. Este cria dados do evento de exemplo. O elemento `data` do JSON é o payload do evento. Qualquer JSON bem formado pode ir para este campo. Também pode utilizar o campo do assunto para encaminhamento e filtragem avançados. CURL é um utilitário que envia os pedidos HTTP.


### <a name="azure-cli"></a>CLI do Azure
1. No portal Azure, selecione **Cloud Shell**. Selecione **Bash** no canto superior esquerdo da janela Cloud Shell. 

    ![Cloud Shell - Bash](./media/custom-event-quickstart-portal/cloud-shell-bash.png)
1. Execute o seguinte comando para obter o **ponto final** para o tópico: Depois de copiar e colar o comando, atualize o nome do **tópico** e o nome do grupo de **recursos** antes de executar o comando. 

    ```azurecli
    endpoint=$(az eventgrid topic show --name <topic name> -g <resource group name> --query "endpoint" --output tsv)
    ```
2. Execute o seguinte comando para obter a **chave** para o tópico personalizado: Depois de copiar e colar o comando, atualize o nome do **tópico** e o nome do grupo de **recursos** antes de executar o comando. 

    ```azurecli
    key=$(az eventgrid topic key list --name <topic name> -g <resource group name> --query "key1" --output tsv)
    ```
3. Copie a seguinte declaração com a definição do evento e prima **ENTER**. 

    ```json
    event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
    ```
4. Executar o seguinte comando **Curl** para registar o evento:

    ```
    curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
    ```

### <a name="azure-powershell"></a>Azure PowerShell
O segundo exemplo utiliza o PowerShell para efetuar passos semelhantes.

1. No portal Azure, selecione **Cloud Shell** (em alternativa vá para https://shell.azure.com/). Selecione **PowerShell** no canto superior esquerdo da janela Cloud Shell. Veja a imagem da janela **Cloud Shell** na secção Azure CLI.
2. Desconte as seguintes variáveis. Depois de copiar e colar cada comando, atualize o nome do **tópico** e o nome do **grupo de recursos** antes de executar o comando:

    ```powershell
    $resourceGroupName = <resource group name>
    $topicName = <topic name>
    ```
3. Executar os seguintes comandos para obter o **ponto final** e as **chaves** para o tópico:

    ```powershell
    $endpoint = (Get-AzEventGridTopic -ResourceGroupName $resourceGroupName -Name $topicName).Endpoint
    $keys = Get-AzEventGridTopicKey -ResourceGroupName $resourceGroupName -Name $topicName
    ```
4. Prepare o evento. Copie e execute as declarações na janela Cloud Shell. 

    ```powershell
    $eventID = Get-Random 99999

    #Date format should be SortableDateTimePattern (ISO 8601)
    $eventDate = Get-Date -Format s

    #Construct body using Hashtable
    $htbody = @{
        id= $eventID
        eventType="recordInserted"
        subject="myapp/vehicles/motorcycles"
        eventTime= $eventDate   
        data= @{
            make="Ducati"
            model="Monster"
        }
        dataVersion="1.0"
    }
    
    #Use ConvertTo-Json to convert event body from Hashtable to JSON Object
    #Append square brackets to the converted JSON payload since they are expected in the event's JSON payload syntax
    $body = "["+(ConvertTo-Json $htbody)+"]"
    ```
5. Utilize o cmdlet **Invoke-WebRequest** para enviar o evento. 

    ```powershell
    Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
    ```

### <a name="verify-in-the-event-grid-viewer"></a>Verifique no Espectador da Grelha de Eventos
Acionou o evento e o Event Grid enviou a mensagem para o ponto final que configurou ao subscrever. Verifique a aplicação Web para ver o evento que acabámos de enviar.

```json
{
  "id": "974",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2019-03-28T01:11:59+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/myegridrg/providers/Microsoft.EventGrid/topics/myegridtopic"
}
```

## <a name="clean-up-resources"></a>Limpar recursos
Se quiser continuar a trabalhar com este evento, não limpe os recursos criados neste artigo. Caso contrário, elimine os recursos que criou neste artigo.

1. Selecione **Grupos de Recursos** no menu esquerdo. Se não o vir no menu esquerdo, selecione **Todos os Serviços** no menu esquerdo e selecione **Grupos**de Recursos . 
2. Selecione o grupo de recursos para lançar a página **do Grupo de Recursos.** 
3. Selecione Eliminar o **grupo de recursos** na barra de ferramentas. 
4. Confirme a eliminação inserindo o nome do grupo de recursos e selecione **Eliminar**. 

    ![Grupos de recursos](./media/custom-event-quickstart-portal/delete-resource-groups.png)

    O outro grupo de recursos que se vê na imagem foi criado e usado pela janela Cloud Shell. Apague-o se não pretender usar a janela Cloud Shell mais tarde. 

## <a name="next-steps"></a>Passos seguintes

Agora que sabe como criar tópicos personalizados e subscrições de eventos, saiba mais sobre o que o Event Grid pode ajudá-lo a fazer:

- [Sobre o Event Grid](overview.md)
- [Encaminhar eventos de armazenamento de Blobs para um ponto final Web personalizado](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitorizar alterações de máquina virtual com o Azure Event Grid e Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Transmitir macrodados em fluxo para um armazém de dados](event-grid-event-hubs-integration.md)
