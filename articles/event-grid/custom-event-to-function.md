---
title: 'Início rápido: enviar eventos personalizados para a grade de eventos do Azure function'
description: 'Início rápido: Use a grade de eventos do Azure e CLI do Azure ou portal para publicar um tópico e assinar esse evento. Uma função do Azure é usada para o ponto de extremidade.'
services: event-grid
keywords: ''
author: banisadr
ms.author: babanisa
ms.date: 11/15/2019
ms.topic: quickstart
ms.service: event-grid
ms.openlocfilehash: 5c4ba510360475e1365d4901136c94181e8c3da3
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74174371"
---
# <a name="quickstart-route-custom-events-to-an-azure-function-with-event-grid"></a>Início rápido: rotear eventos personalizados para um Azure function com a grade de eventos

O Azure Event Grid é um serviço de eventos para a cloud. Azure Functions é um dos manipuladores de eventos com suporte. Neste artigo, o portal do Azure serve para criar um tópico personalizado, subscrever o tópico personalizado e acionar o evento para ver o resultado. Você envia os eventos para uma função do Azure.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-azure-function"></a>Criar Azure function

Antes de assinar o tópico personalizado, vamos criar uma função para manipular os eventos. No portal do Azure, clique em ' criar um recurso ' e digite ' função ' e escolha ' Aplicativo de funções ' e clique em criar. Selecione ' criar novo ' em grupo de recursos e dê um nome a ele. Você usará isso para o restante do tutorial. Dê ao Aplicativo de funções um nome, deixe a alternância ' publicar ' em ' código ', selecione qualquer tempo de execução e região e, em seguida, clique em criar.

Quando o Aplicativo de funções estiver pronto, navegue até ele e clique em ' + nova função '. Selecione "no portal" para o ambiente de desenvolvimento e clique em continuar. Em criar uma função, escolha ' mais modelos ' para exibir mais modelos e, em seguida, procure ' gatilho de grade de eventos do Azure ' e selecione-o. Se esta for a primeira vez que você usa esse gatilho, talvez seja necessário clicar em ' instalar ' para instalar a extensão.

![Gatilho de grade de eventos de função](./media/custom-event-to-function/grid-trigger.png)

Depois de instalar a extensão, clique em continuar, dê um nome à sua função e clique em criar.

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="create-a-custom-topic"></a>Criar um tópico personalizado

Um tópico do Event Grid fornece um ponto final definido pelo utilizador no qual publica os eventos. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione **todos os serviços** no menu de navegação à esquerda, procure **grade de eventos**e selecione **Tópicos da grade de eventos**. 

    ![Selecionar tópicos da grade de eventos](./media/custom-event-to-function/select-event-grid-topics.png)
3. Na página **Tópicos da grade de eventos** , selecione **+ Adicionar** na barra de ferramentas. 

    ![Botão Adicionar tópico da grade de eventos](./media/custom-event-to-function/add-event-grid-topic-button.png)

4. Na página **Criar tópico** , siga estas etapas:

    1. Forneça um **nome** exclusivo para o tópico personalizado. O nome do tópico deve ser exclusivo, porque este é representado por uma entrada DNS. Não utilize o nome apresentado na imagem. Em vez disso, criar seu próprio nome - tem de ter entre 3 e 50 carateres e conter apenas valores-z, A-Z, 0-9, e "-".
    2. Selecione a sua **subscrição** do Azure.
    3. Selecione o mesmo grupo de recursos das etapas anteriores.
    4. Selecione um **local** para o tópico da grade de eventos.
    5. Mantenha o valor padrão **esquema da grade de eventos** para o campo **esquema de evento** . 

       ![Página Criar tópico](./media/custom-event-to-function/create-custom-topic.png)
    6. Selecione **Criar**. 

5. Depois de o tópico personalizado ter sido criado, verá a notificação de êxito. Selecione **ir para o grupo de recursos**. 

   ![Ver notificação de êxito](./media/custom-event-to-function/success-notification.png)

6. Na página **grupo de recursos** , selecione o tópico grade de eventos. 

   ![Selecionar o recurso de tópico da grade de eventos](./media/custom-event-to-function/select-event-grid-topic.png)

7. Você vê a página de **tópico da grade de eventos** para sua grade de eventos. Mantenha esta página aberta. Você o usará posteriormente no início rápido. 

    ![Tópico da grade de eventos home page](./media/custom-event-to-function/event-grid-topic-home-page.png)

## <a name="subscribe-to-custom-topic"></a>Subscrever um tópico personalizado

Subscreva um tópico do Event Grid para comunicar ao Event Grid os eventos que pretende controlar e para onde enviar os eventos.

1. Agora, na página de **tópico da grade de eventos** do tópico personalizado, selecione **+ assinatura de evento** na barra de ferramentas.

   ![Adicionar subscrição de evento](./media/custom-event-to-function/new-event-subscription.png)

2. Na página **criar assinatura de evento** , siga estas etapas:
    1. Insira um **nome** para a assinatura de evento.
    3. Selecione **Azure function** para o **tipo de ponto de extremidade**. 
    4. Escolha **selecionar um ponto de extremidade**. 

       ![Indicar os valores da subscrição de evento](./media/custom-event-to-function/provide-subscription-values.png)

    5. Para o ponto de extremidade da função, selecione a assinatura do Azure e o grupo de recursos no qual Aplicativo de funções está e, em seguida, selecione o Aplicativo de funções e a função que você criou anteriormente. Selecione **Confirmar a Seleção**.

       ![Indicar o URL de ponto final](./media/custom-event-to-function/provide-endpoint.png)

    6. De volta à página **criar assinatura de evento** , selecione **criar**.

## <a name="send-an-event-to-your-topic"></a>Enviar um evento para o seu tópico

Agora, vamos acionar um evento para ver como o Event Grid distribui a mensagem para o ponto final. Utilize a CLI do Azure ou o PowerShell para enviar um evento de teste para o seu tópico personalizado. Normalmente, uma aplicação ou serviço do Azure enviaria os dados do evento.

O primeiro exemplo utiliza a CLI do Azure. Obtém o URL e a chave para o tópico personalizado e os dados do evento de exemplo. Utilize o nome do tópico personalizado de `<topic name>`. Este cria dados do evento de exemplo. O elemento `data` do JSON é o payload do evento. Qualquer JSON bem formado pode ir para este campo. Também pode utilizar o campo do assunto para encaminhamento e filtragem avançados. CURL é um utilitário que envia os pedidos HTTP.


### <a name="azure-cli"></a>CLI do Azure
1. Na portal do Azure, selecione **Cloud Shell**. Selecione **bash** no canto superior esquerdo da janela de Cloud Shell. 

    ![Cloud Shell-bash](./media/custom-event-quickstart-portal/cloud-shell-bash.png)
1. Execute o seguinte comando para obter o **ponto de extremidade** para o tópico: depois de copiar e colar o comando, atualize o nome do **tópico** e o nome do **grupo de recursos** antes de executar o comando. 

    ```azurecli
    endpoint=$(az eventgrid topic show --name <topic name> -g <resource group name> --query "endpoint" --output tsv)
    ```
2. Execute o seguinte comando para obter a **chave** para o tópico personalizado: depois de copiar e colar o comando, atualize o nome do **tópico** e o nome do **grupo de recursos** antes de executar o comando. 

    ```azurecli
    key=$(az eventgrid topic key list --name <topic name> -g <resource group name> --query "key1" --output tsv)
    ```
3. Copie a instrução a seguir com a definição de evento e pressione **Enter**. 

    ```json
    event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
    ```
4. Execute o seguinte comando de **rotação** para postar o evento:

    ```
    curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
    ```

### <a name="azure-powershell"></a>Azure PowerShell
O segundo exemplo utiliza o PowerShell para efetuar passos semelhantes.

1. Na portal do Azure, selecione **Cloud Shell** (como alternativa, acesse https://shell.azure.com/). Selecione **PowerShell** no canto superior esquerdo da janela de Cloud Shell. Consulte a imagem da janela **Cloud Shell** de exemplo na seção CLI do Azure.
2. Defina as variáveis a seguir. Depois de copiar e colar cada comando, atualize o nome do **tópico** e o **nome do grupo de recursos** antes de executar o comando:

    ```powershell
    $resourceGroupName = <resource group name>
    $topicName = <topic name>
    ```
3. Execute os seguintes comandos para obter o **ponto de extremidade** e as **chaves** para o tópico:

    ```powershell
    $endpoint = (Get-AzEventGridTopic -ResourceGroupName $resourceGroupName -Name $topicName).Endpoint
    $keys = Get-AzEventGridTopicKey -ResourceGroupName $resourceGroupName -Name $topicName
    ```
4. Prepare o evento. Copie e execute as instruções na janela Cloud Shell. 

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
5. Use o cmdlet **Invoke-WebRequest** para enviar o evento. 

    ```powershell
    Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
    ```

### <a name="verify-in-the-event-grid-viewer"></a>Verificar no Visualizador de grade de eventos
Acionou o evento e o Event Grid enviou a mensagem para o ponto final que configurou ao subscrever. Navegue até a função disparada pela grade de eventos e abra os logs. Você deverá ver uma cópia da carga de dados do evento nos logs. Se você não tiver certeza de abrir a janela logs primeiro ou se reconectar e depois tentar enviar um evento de teste novamente.

![Log de gatilho de função bem-sucedido](./media/custom-event-to-function/successful-function.png)

## <a name="clean-up-resources"></a>Limpar recursos
Se quiser continuar a trabalhar com este evento, não limpe os recursos criados neste artigo. Caso contrário, elimine os recursos que criou neste artigo.

1. Selecione **grupos de recursos** no menu à esquerda. Se você não o vir no menu à esquerda, selecione **todos os serviços** no menu à esquerda e selecione **grupos de recursos**. 
2. Selecione o grupo de recursos para iniciar a página do **grupo de recursos** . 
3. Selecione **excluir grupo de recursos** na barra de ferramentas. 
4. Confirme a exclusão digitando o nome do grupo de recursos e selecione **excluir**. 

    ![Grupos de recursos](./media/custom-event-to-function/delete-resource-groups.png)

    O outro grupo de recursos que você vê na imagem foi criado e usado pela janela de Cloud Shell. Exclua-o se você não planeja usar a janela de Cloud Shell mais tarde. 
```

## Next steps

Now that you know how to create topics and event subscriptions, learn more about what Event Grid can help you do:

- [About Event Grid](overview.md)
- [Route Blob storage events to a custom web endpoint](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitor virtual machine changes with Azure Event Grid and Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Stream big data into a data warehouse](event-grid-event-hubs-integration.md)
