---
title: 'Quickstart: Enviar eventos personalizados para o ponto final da web - Event Grid, portal Azure'
description: 'Quickstart: Use a Azure Event Grid e o portal Azure para publicar um tópico personalizado e subscreva eventos para esse tópico. Os eventos são tratados por uma aplicação web.'
ms.date: 07/07/2020
ms.topic: quickstart
ms.openlocfilehash: 592e2d6b7393da8cb55a457b022d6c2358048cfe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96013671"
---
# <a name="quickstart-route-custom-events-to-web-endpoint-with-the-azure-portal-and-event-grid"></a>Quickstart: Encaminhe eventos personalizados para o ponto final da Web com o portal Azure e a Grade de Eventos

O Azure Event Grid é um serviço de eventos para a cloud. Neste artigo, o portal do Azure serve para criar um tópico personalizado, subscrever o tópico personalizado e acionar o evento para ver o resultado. Normalmente, envia eventos para um ponto final que processa os dados de eventos e efetua ações. No entanto, para simplificar este artigo, vai enviar eventos para uma aplicação Web que recolhe e apresenta as mensagens.

## <a name="prerequisites"></a>Pré-requisitos
[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="create-a-custom-topic"></a>Criar um tópico personalizado

Um tópico do Event Grid fornece um ponto final definido pelo utilizador no qual publica os eventos. 

1. Inscreva-se no [portal Azure](https://portal.azure.com/).
2. Na barra de pesquisa do tópico, escreva **Tópicos de Grelha de Eventos** e, em seguida, selecione Tópicos de Grelha de **Eventos** da lista de drop down. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topics.png" alt-text="Procurar e selecionar tópicos de grelha de eventos":::
3. Na página **Tópicos da Grelha de Eventos,** selecione **+ Adicione** na barra de ferramentas. 

    :::image type="content" source="./media/custom-event-quickstart-portal/add-event-grid-topic-button.png" alt-text="Adicionar botão tópico de grelha de evento":::
4. Na página **'Criar Tópico',** siga estes passos:
    1. Selecione a sua **subscrição Azure**.
    2. Selecione um grupo de recursos existente ou **selecione Criar novo**, e insira um **nome** para o grupo **de recursos**.
    3. Forneça um **nome** único para o tema personalizado. O nome do tópico deve ser exclusivo, porque este é representado por uma entrada DNS. Não utilize o nome apresentado na imagem. Em vez disso, crie o seu próprio nome - deve estar entre 3-50 caracteres e conter apenas valores a-z, A-Z, 0-9 e "-".
    4. Selecione um **local** para o tópico da grelha do evento.
    5. Selecione **'Rever + criar'** na parte inferior da página. 

        :::image type="content" source="./media/custom-event-quickstart-portal/create-custom-topic.png" alt-text="Criar página de tópicos":::
    6. No **separador 'Rever +' criar** o separador **'Criar' da** página de tópicos, selecione **Criar**. 
    
        :::image type="content" source="./media/custom-event-quickstart-portal/review-create-page.png" alt-text="Rever definições e criar":::
5. Após a implementação ter sucesso, escreva **tópicos de grelha de eventos** novamente na barra de pesquisa e selecione **Tópicos** de Grelha de Evento da lista de drop-down como fez anteriormente. 
6. Selecione o tópico que criou na lista. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topic.png" alt-text="Selecione o seu tópico na lista":::

7. Você vê a página **tópico da grelha de evento** para o seu tópico. Mantenha esta página aberta. Usa-se mais tarde no arranque rápido. 

    :::image type="content" source="./media/custom-event-quickstart-portal/event-grid-topic-home-page.png" alt-text="Página inicial do tópico da grelha de evento":::

## <a name="create-a-message-endpoint"></a>Criar um ponto final de mensagem
Antes de criar uma subscrição para o tópico personalizado, crie um ponto final para a mensagem do evento. Normalmente, o ponto final executa as ações com base nos dados do evento. Para simplificar este início rápido, vai implementar uma [aplicação Web pré-criada](https://github.com/Azure-Samples/azure-event-grid-viewer) para apresentar as mensagens de evento. A solução implementada inclui um plano do Serviço de Aplicações, uma aplicação Web do Serviço de Aplicações e o código de origem do GitHub.

1. Na página do artigo, **selecione Implementar para Azure** para implementar a solução para a sua subscrição. No portal do Azure, indique os valores para os parâmetros.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"  alt="Button to Deploy to Aquent." /></a>
1. A implementação pode demorar alguns minutos. Após a implementação ter sido concluída com êxito, verifique a aplicação Web para verificar se está em execução. Num browser, navegue para: `https://<your-site-name>.azurewebsites.net`

    Se a implementação falhar, verifique a mensagem de erro. Pode ser porque o nome do site já está tomado. Volte a implementar o modelo e escolha um nome diferente para o site. 
1. Vê o site, mas ainda não foram publicados eventos no mesmo.

   ![Ver novo site](./media/custom-event-quickstart-portal/view-site.png)

## <a name="subscribe-to-custom-topic"></a>Subscrever um tópico personalizado

Subscreva um tópico do Event Grid para comunicar ao Event Grid os eventos que pretende controlar e para onde enviar os eventos.

1. Agora, na página **Tópico da Grelha de Eventos** para o seu tópico personalizado, selecione + **Subscrição de evento** na barra de ferramentas.

    :::image type="content" source="./media/custom-event-quickstart-portal/new-event-subscription.png" alt-text="Adicionar botão de subscrição de evento":::
2. Na página **De Subscrição de Eventos** Criar, siga estes passos:
    1. Insira um **nome** para a subscrição do evento.
    3. Selecione **Web Hook** para o **tipo Endpoint**. 
    4. Escolha **Selecionar um ponto final.** 

        :::image type="content" source="./media/custom-event-quickstart-portal/provide-subscription-values.png" alt-text="Indicar os valores da subscrição de evento":::
    5. Para o ponto final do webhook, indique o URL da sua aplicação Web e adicione `api/updates` ao URL da home page. Selecione **Confirmar a Seleção**.

        :::image type="content" source="./media/custom-event-quickstart-portal/provide-endpoint.png" alt-text="Indicar o URL de ponto final":::
    6. De volta à página **de Subscrição de Eventos,** selecione **Criar**.

3. Verifique a aplicação Web novamente e repare que um evento de validação de subscrição foi enviado para a mesma. Selecione o ícone do olho para expandir os dados do evento. O Event Grid envia o evento de validação para que o ponto final possa verificar que pretende receber dados de eventos. A aplicação Web inclui código para validar a subscrição.

    ![Ver evento da subscrição](./media/custom-event-quickstart-portal/view-subscription-event.png)

## <a name="send-an-event-to-your-topic"></a>Enviar um evento para o seu tópico

Agora, vamos acionar um evento para ver como o Event Grid distribui a mensagem para o ponto final. Utilize a CLI do Azure ou o PowerShell para enviar um evento de teste para o seu tópico personalizado. Normalmente, uma aplicação ou serviço do Azure enviaria os dados do evento.

O primeiro exemplo utiliza a CLI do Azure. Obtém o URL e a chave para o tópico personalizado e os dados do evento de exemplo. Utilize o nome do tópico personalizado de `<topic name>`. Este cria dados do evento de exemplo. O elemento `data` do JSON é o payload do evento. Qualquer JSON bem formado pode ir para este campo. Também pode utilizar o campo do assunto para encaminhamento e filtragem avançados. CURL é um utilitário que envia os pedidos HTTP.


### <a name="azure-cli"></a>CLI do Azure
1. No portal Azure, selecione **Cloud Shell**. O Cloud Shell abre no painel inferior do navegador web. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-cloud-shell.png" alt-text="Selecione ícone cloud shell":::
1. Selecione **Bash** no canto superior esquerdo da janela Cloud Shell. 

    ![Cloud Shell - Bash](./media/custom-event-quickstart-portal/cloud-shell-bash.png)
1. Executar o seguinte comando para obter o **ponto final** para o tópico: Depois de copiar e colar o comando, atualize o nome do **tópico** e o nome do grupo **de recursos** antes de executar o comando. Publicará eventos de amostra para este ponto final tópico. 

    ```azurecli
    endpoint=$(az eventgrid topic show --name <topic name> -g <resource group name> --query "endpoint" --output tsv)
    ```
2. Executar o seguinte comando para obter a **chave** para o tópico personalizado: Depois de copiar e colar o comando, atualize o **nome** do tópico e o nome do grupo **de recursos** antes de executar o comando. Esta é a chave principal do tema 'Grade de Eventos'. Para obter esta chave a partir do portal Azure, mude para o separador **Teclas de Acesso** da página **Tópico da Grelha de Evento.** Para poder publicar um evento num tópico personalizado, precisa da chave de acesso. 

    ```azurecli
    key=$(az eventgrid topic key list --name <topic name> -g <resource group name> --query "key1" --output tsv)
    ```
3. Copie a seguinte declaração com a definição do evento e prima **ENTER**. 

    ```json
    event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
    ```
4. Executar o seguinte comando **Curl** para registar o evento: No comando, `aeg-sas-key` o cabeçalho está definido para a chave de acesso que obteve anteriormente. 

    ```
    curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
    ```

### <a name="azure-powershell"></a>Azure PowerShell
O segundo exemplo utiliza o PowerShell para efetuar passos semelhantes.

1. No portal Azure, selecione **Cloud Shell** (em alternativa vá `https://shell.azure.com/` a). O Cloud Shell abre no painel inferior do navegador web. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-cloud-shell.png" alt-text="Selecione ícone cloud shell":::
1. No **Cloud Shell**, selecione **PowerShell** no canto superior esquerdo da janela Cloud Shell. Consulte a imagem da janela **Cloud Shell** na secção Azure CLI.
2. Desa estarda as seguintes variáveis. Depois de copiar e colar cada comando, atualize o nome do **tópico** e **o nome do grupo de recursos** antes de executar o comando:

    **Grupo de recursos**:
    ```powershell
    $resourceGroupName = "<resource group name>"
    ```

    **Nome do tópico da Grelha de Eventos**:    
    ```powershell
    $topicName = "<topic name>"
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
5. Utilize o **cmdlet Invoke-WebRequest** para enviar o evento. 

    ```powershell
    Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
    ```

### <a name="verify-in-the-event-grid-viewer"></a>Verifique no Espectador de Grelha de Eventos
Acionou o evento e o Event Grid enviou a mensagem para o ponto final que configurou ao subscrever. Verifique a aplicação Web para ver o evento que acabámos de enviar.

:::image type="content" source="./media/custom-event-quickstart-portal/event-grid-viewer-end.png" alt-text="Espectador de grelha de evento":::

## <a name="clean-up-resources"></a>Limpar os recursos
Se quiser continuar a trabalhar com este evento, não limpe os recursos criados neste artigo. Caso contrário, elimine os recursos que criou neste artigo.

1. Selecione **Grupos de Recursos** no menu esquerdo. Se não o vir no menu esquerdo, selecione **Todos os Serviços** no menu esquerdo e selecione **Grupos de Recursos**. 

    ![Grupos de recursos](./media/custom-event-quickstart-portal/delete-resource-groups.png)
1. Selecione o grupo de recursos para lançar a página **do Grupo de Recursos.** 
1. Selecione Eliminar o **grupo de recursos** na barra de ferramentas. 
1. Confirme a eliminação introduzindo o nome do grupo de recursos e selecione **Delete**. 

    O outro grupo de recursos que vê na imagem foi criado e usado pela janela Cloud Shell. Elimine-o se não pretender utilizar a janela Cloud Shell mais tarde. 

## <a name="next-steps"></a>Passos seguintes

Agora que sabe como criar tópicos personalizados e subscrições de eventos, saiba mais sobre o que o Event Grid pode ajudá-lo a fazer:

- [Sobre o Event Grid](overview.md)
- [Encaminhar eventos de armazenamento de Blobs para um ponto final Web personalizado](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitorizar alterações de máquina virtual com o Azure Event Grid e Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Transmitir macrodados em fluxo para um armazém de dados](event-grid-event-hubs-integration.md)
