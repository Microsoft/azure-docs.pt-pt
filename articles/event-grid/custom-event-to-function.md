---
title: 'Quickstart: Envie eventos personalizados para a Função Azure - Grelha de Eventos'
description: 'Quickstart: Use Azure Event Grid e Azure CLI ou portal para publicar um tópico, e subscrever esse evento. Uma função Azure é utilizada para o ponto final.'
services: event-grid
keywords: ''
author: banisadr
ms.author: babanisa
ms.date: 11/15/2019
ms.topic: quickstart
ms.service: event-grid
ms.openlocfilehash: 5e38571cf84537fd722093b96cd277743e8ce80c
ms.sourcegitcommit: fab450a18a600d72b583ecfbe6c5e53afd43408c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80292156"
---
# <a name="quickstart-route-custom-events-to-an-azure-function-with-event-grid"></a>Quickstart: Rota eventos personalizados para uma função Azure com grelha de eventos

O Azure Event Grid é um serviço de eventos para a cloud. A Azure Functions é um dos manipuladores de eventos suportados. Neste artigo, o portal do Azure serve para criar um tópico personalizado, subscrever o tópico personalizado e acionar o evento para ver o resultado. Envia os eventos para uma Função Azure.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-azure-function"></a>Criar a Função do Azure

Antes de subscrever o tópico personalizado, vamos criar uma função para lidar com os eventos. No portal Azure, clique em 'Criar um recurso' e digitar 'função' e, em seguida, escolha 'App de função' e clique em criar. Selecione 'Create new' under resource group e dê-lhe um nome. Vais usar isto para o resto do tutorial. Dê um nome à App função, deixe o 'Publicar' a alternar em 'Código', selecione qualquer tempo de funcionamento e região e, em seguida, bata na criação.

Assim que a sua App de Funções estiver pronta, navegue para ela e clique em '+ Nova Função'. Selecione 'In-portal' para ambiente de desenvolvimento e o hit continue. Em criar uma função, escolha 'Mais modelos' para ver mais modelos e, em seguida, procure por 'Azure Event Grid Trigger' e selecione-o. Se for a primeira vez que utiliza este gatilho, poderá ter de clicar em 'Instalar' para instalar a extensão.

![Gatilho da grelha do evento de função](./media/custom-event-to-function/grid-trigger.png)

Depois de instalar a extensão, clique em continuar, dê um nome à sua função e, em seguida, clique em criar.

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="create-a-custom-topic"></a>Criar um tópico personalizado

Um tópico do Event Grid fornece um ponto final definido pelo utilizador no qual publica os eventos. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione **todos os serviços** no menu de navegação à esquerda, procure a **Grelha de Eventos,** e selecione Tópicos da Grelha de **Eventos**. 

    ![Selecione tópicos da grelha de eventos](./media/custom-event-to-function/select-event-grid-topics.png)
3. Na página tópicos da grelha de **eventos,** selecione **+ Adicione** na barra de ferramentas. 

    ![Adicione o botão tópico da grelha de eventos](./media/custom-event-to-function/add-event-grid-topic-button.png)

4. Na página **Criar Tópico,** siga estes passos:

    1. Forneça um **nome** único para o tópico personalizado. O nome do tópico deve ser exclusivo, porque este é representado por uma entrada DNS. Não utilize o nome apresentado na imagem. Em vez disso, crie o seu próprio nome - deve ser entre 3-50 caracteres e conter apenas valores a-z, A-Z, 0-9 e "-".
    2. Selecione a sua **subscrição** do Azure.
    3. Selecione o mesmo grupo de recursos dos passos anteriores.
    4. Selecione um **local** para o tópico da grelha de eventos.
    5. Mantenha o valor padrão da **Grelha de Eventos Schema** para o campo **Event Schema.** 

       ![Criar página tópico](./media/custom-event-to-function/create-custom-topic.png)
    6. Selecione **Criar**. 

5. Depois de o tópico personalizado ter sido criado, verá a notificação de êxito. Selecione **Ir para o grupo de recursos**. 

   ![Ver notificação de êxito](./media/custom-event-to-function/success-notification.png)

6. Na página do **Grupo de Recursos,** selecione o tópico da grelha de eventos. 

   ![Selecione o recurso tópico da grelha de eventos](./media/custom-event-to-function/select-event-grid-topic.png)

7. Você vê a página **de Tópicode Evento** para a sua grelha de eventos. Mantenha esta página aberta. Usa-o mais tarde, no início. 

    ![Página inicial do Tópico da Grelha de Eventos](./media/custom-event-to-function/event-grid-topic-home-page.png)

## <a name="subscribe-to-custom-topic"></a>Subscrever um tópico personalizado

Subscreva um tópico do Event Grid para comunicar ao Event Grid os eventos que pretende controlar e para onde enviar os eventos.

1. Agora, na página tópico da grelha de **eventos** para o seu tópico personalizado, selecione **+ Subscrição de eventos** na barra de ferramentas.

   ![Adicionar subscrição de evento](./media/custom-event-to-function/new-event-subscription.png)

2. Na página de Subscrição de **Eventos Create,** siga estes passos:
    1. Insira um **nome** para a subscrição do evento.
    3. Selecione **função Azure** para o **tipo endpoint**. 
    4. Escolha **Selecione um ponto final**. 

       ![Indicar os valores da subscrição de evento](./media/custom-event-to-function/provide-subscription-values.png)

    5. Para o ponto final da função, selecione o Grupo de Subscrição e Recursos Azure em que a sua App de Funções está e, em seguida, selecione a App de Funções e função que criou anteriormente. Selecione **Confirmar a Seleção**.

       ![Indicar o URL de ponto final](./media/custom-event-to-function/provide-endpoint.png)

    6. De volta à página de Subscrição de **Eventos Create,** selecione **Criar**.

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

1. No portal Azure, selecione **Cloud** `https://shell.azure.com/`Shell (em alternativa ir a ). Selecione **PowerShell** no canto superior esquerdo da janela Cloud Shell. Veja a imagem da janela **Cloud Shell** na secção Azure CLI.
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
Acionou o evento e o Event Grid enviou a mensagem para o ponto final que configurou ao subscrever. Navegue para a sua função ativada pela Grelha de Eventos e abra os registos. Deve ver uma cópia da carga útil dos dados do evento nos registos. Se não se certificar de que abre a janela de registos primeiro, ou volta a ligar-se e, em seguida, tente enviar um evento de teste novamente.

![Log de gatilho de função bem-sucedido](./media/custom-event-to-function/successful-function.png)

## <a name="clean-up-resources"></a>Limpar recursos
Se quiser continuar a trabalhar com este evento, não limpe os recursos criados neste artigo. Caso contrário, elimine os recursos que criou neste artigo.

1. Selecione **Grupos de Recursos** no menu esquerdo. Se não o vir no menu esquerdo, selecione **Todos os Serviços** no menu esquerdo e selecione **Grupos**de Recursos . 
2. Selecione o grupo de recursos para lançar a página **do Grupo de Recursos.** 
3. Selecione Eliminar o **grupo de recursos** na barra de ferramentas. 
4. Confirme a eliminação inserindo o nome do grupo de recursos e selecione **Eliminar**. 

    ![Grupos de recursos](./media/custom-event-to-function/delete-resource-groups.png)

    O outro grupo de recursos que se vê na imagem foi criado e usado pela janela Cloud Shell. Apague-o se não pretender usar a janela Cloud Shell mais tarde. 

## <a name="next-steps"></a>Passos seguintes

Agora que sabe como criar tópicos e subscrições de eventos, saiba mais sobre o que o Event Grid pode ajudá-lo a fazer:

- [Sobre o Event Grid](overview.md)
- [Encaminhar eventos de armazenamento de Blobs para um ponto final Web personalizado](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitorizar alterações de máquina virtual com o Azure Event Grid e Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Transmitir macrodados em fluxo para um armazém de dados](event-grid-event-hubs-integration.md)
