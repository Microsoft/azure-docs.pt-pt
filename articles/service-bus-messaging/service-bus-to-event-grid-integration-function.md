---
title: Lidar com eventos de ônibus de serviço através da grade de eventos usando funções Azure
description: Este artigo fornece passos para lidar com eventos de Service Bus através da Grade de Eventos utilizando funções Azure.
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: afc0a5bf9b83363d1f4baab955b55148fe3a8498
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95819428"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions"></a>Tutorial: Responda aos eventos de autocarro da Azure Service recebidos através da Azure Event Grid utilizando funções Azure
Neste tutorial, você aprende a responder aos eventos do Azure Service Bus que são recebidos através da Azure Event Grid utilizando Azure Functions e Azure Logic Apps. 

Neste tutorial, vai aprender a:
> [!div class="checklist"]
> * Criar um espaço de nomes do Service Bus
> * Preparar uma aplicação de amostra para enviar mensagens
> * Envie mensagens para o tema do Service Bus
> * Receber mensagens através do Logic Apps
> * Configurar uma função de teste no Azure
> * Ligar a função e o espaço de nomes através do Event Grid
> * Receber mensagens através das Funções do Azure

[!INCLUDE [service-bus-event-grid-prerequisites](../../includes/service-bus-event-grid-prerequisites.md)]

## <a name="additional-prerequisites"></a>Pré-requisitos adicionais
Instale [o Visual Studio 2019](https://www.visualstudio.com/vs) e inclua a carga de trabalho de desenvolvimento do **Azure.** Esta carga de trabalho inclui ferramentas de **função Azure** que você precisa para criar, construir e implementar projetos de Funções Azure em Estúdio Visual. 

## <a name="deploy-the-function-app"></a>Implementar a aplicação de funções 

>[!NOTE]
> Para saber mais sobre a criação e implementação de uma aplicação Azure Functions, consulte [Desenvolver Funções Azure utilizando o Visual Studio](../azure-functions/functions-develop-vs.md)

1. Abrir **ReceiveMessagesOnEvent.cs** ficheiro do projeto **FunctionApp1** da solução **SBEventGridIntegration.sln.** 
1. `<SERCICE BUS NAMESPACE - CONNECTION STRING>`Substitua-a pelo fio de ligação ao seu espaço de nomes de Service Bus. Deve ser o mesmo que usou no ficheiro **Program.cs** do projeto **MessageSender** na mesma solução. 
1. Clique com o botão direito **FunctionApp1** e **selecione Publicar**. 
1. Na página **Publicar,** selecione **Iniciar**. Estes passos podem ser diferentes do que você vê, mas o processo de publicação deve ser semelhante. 
1. No assistente **de publicação,** na página **Target,** selecione **Azure** for **Target**. 
1. Na **página-alvo específica,** selecione **Azure Function App (Windows)**. 
1. Na página **de instância de Funções,** selecione **Criar uma nova função Azure**. 
1. Na página **'App' (Windows),** siga estes passos:
    1. Introduza um **nome** para a aplicação de função.
    1. Selecione uma **subscrição Azure**.
    1. Selecione um grupo de **recursos** existente ou crie um novo grupo de recursos. Para este tutorial, selecione o grupo de recursos que tem o espaço de nomes do Service Bus. 
    1. Selecione um **tipo de plano** para o Serviço de Aplicações.
    1. Selecione um **local**. Selecione o mesmo local que o espaço de nomes do Service Bus. 
    1. Selecione um **Azure Storage** existente ou selecione **Novo** para criar uma nova conta de Armazenamento a ser usada pela aplicação Funções. 
    1. Selecione **Criar** para criar a aplicação Funções. 
1. Voltando à página de **instância de Funções** do assistente **publicar,** selecione **Terminar**. 
1. Na página **publicar** no Visual Studio, selecione **Publicar** para publicar a aplicação Funções para Azure. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/publish-function-app.png" alt-text="Publicar aplicação Funções":::    
1. Na janela **Saída,** veja as mensagens de construção e publicação e confirme que ambas foram bem sucedidas. 
1. Agora, na página **Publicar,** **selecione Gerir no portal Azure**. 
1. No portal Azure, na página **'App' funções,** selecione **Funções** no menu esquerdo e confirme que vê duas funções: 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/functions.png" alt-text="Duas funções para o gatilho da grelha de eventos e o gatilho HTTP":::

    > [!NOTE]
    > Usa `EventGridTriggerFunction` um gatilho de Grade de [Evento](../azure-functions/functions-bindings-event-grid-trigger.md) e utiliza um `HTTPTriggerFunction` gatilho [HTTP](../azure-functions/functions-bindings-http-webhook-trigger.md).
1. Selecione **EventGridTriggerFunction** da lista. Recomendamos que utilize o gatilho da Grelha de Eventos com Funções Azure, uma vez que tem alguns benefícios em utilizar o gatilho HTTP. Para mais detalhes, consulte [a função Azure como manipulador de eventos para eventos de Grade de Eventos](../event-grid/handler-functions.md).
1. Na página **'Função'** para o **EventGridTriggerFunction**, selecione **Monitor** no menu esquerdo. 
1. Selecione **Configurar** para configurar insights de aplicação para capturar o registo de invocação. Utiliza esta página para monitorizar as execuções de funções ao receber eventos de Service Bus da Grade de Eventos. 
1. Na página **'Insights de Aplicação',** insira um nome para o recurso, selecione uma **localização** para o recurso e, em seguida, selecione **OK**. 
1. Selecione a função **EventGridTriggerFunction** na parte superior (menu pão ramb) para navegar de volta para a página **'Função'.** 
1. Confirme-o na página do **Monitor.** 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/monitor-before.png" alt-text="Página do monitor para a função antes de invocações de função":::
    
    Mantenha esta página aberta num separador do seu navegador web. Irá refrescar esta página para ver as invocações para esta função mais tarde.

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Ligar a função e o espaço de nomes através do Event Grid
Nesta secção, você liga a função e o espaço de nomes do Service Bus utilizando o portal Azure. 

Para criar uma subscrição da Azure Event Grid, siga estes passos:

1. No portal Azure, vá ao seu espaço de nome e, em seguida, no painel esquerdo, selecione **Eventos**. É aberta a janela do espaço de nomes, com duas subscrições do Event Grid no painel direito. 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png" alt-text="Service Bus - página de eventos":::
2. Selecione **+ Subscrição de evento** na barra de ferramentas. 
3. Na página **'Criar Subscrição de** Eventos', faça os seguintes passos:
    1. Insira um **nome** para a subscrição. 
    2. Insira um **nome** para o tópico do **sistema**. Os tópicos do sistema são tópicos criados para recursos Azure, como a conta Azure Storage e a Azure Service Bus. Para saber mais sobre tópicos do sistema, consulte [a visão geral dos tópicos do Sistema.](../event-grid/system-topics.md)
    2. Selecione **Azure Function** for **Endpoint Type** e clique **em Selecionar um ponto final**. 

        ![Service Bus - Subscrição da Grade de Eventos](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. Na página **Select Azure Function,** selecione a subscrição, grupo de recursos, aplicação de função, slot e a função e, em seguida, selecione **Confirmar seleção**. 

        ![Função - selecione o ponto final](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. Na página **'Criar Subscrição de** Eventos', mude para o **separador Filtros** e faça as seguintes tarefas:
        1. **Selecione Habilitar a filtragem do assunto**
        2. Insira o nome da subscrição do tópico Service Bus **(S1**) que criou anteriormente.
        3. Selecione o botão **Criar**. 

            ![Filtro de assinatura de evento](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Mude para o separador Subscrições de **Eventos** da página **Eventos** e confirme que vê a subscrição do evento na lista.

    ![Subscrição de evento na lista](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="monitor-the-functions-app"></a>Monitorize a aplicação Funções
As mensagens que enviou para o tópico Service Bus mais cedo são reencaminhadas para a subscrição (S1). A Grelha de Eventos reencaminha as mensagens na subscrição da função Azure. Neste passo do tutorial, confirma-se que a função foi invocada e vê as mensagens informativas registadas. 

1. Na página para a sua aplicação de função Azure, mude para o **separador Monitor** se ainda não estiver ativo. Deve ver uma entrada para cada mensagem publicada no tópico do Service Bus. Se não os vir, refresque a página depois de esperar alguns minutos. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/function-monitor.png" alt-text="Página do monitor para a função após invocações":::
2. Selecione a invocação da lista para ver os detalhes. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/invocation-details.png" alt-text="Detalhes da invocação da função" lightbox="./media/service-bus-to-event-grid-integration-example/invocation-details.png":::

    Também pode utilizar o **separador Registos** da página **Monitor** para ver as informações de registo à medida que as mensagens são enviadas. Pode atrasar-se, por isso dá-lhe alguns minutos para ver as mensagens registadas. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/function-logs.png" alt-text="Registo de funções" lightbox="./media/service-bus-to-event-grid-integration-example/function-logs.png":::

## <a name="troubleshoot"></a>Resolução de problemas
Se não vir nenhuma função invocações após esperar e refrescar por algum tempo, siga estes passos: 

1. Confirme que as mensagens chegaram ao tópico do Service Bus. Consulte o contador **de mensagens recebidas** na página **'Tópico do Autocarro de Serviço'.** Neste caso, corri a aplicação **MessageSender** duas vezes, por isso vejo 10 mensagens (5 mensagens para cada execução).

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/topic-incoming-messages.png" alt-text="Página de tópico de ônibus de serviço - mensagens recebidas":::    
1. Confirme que não existem **mensagens ativas** na subscrição do Service Bus. 
    Se não vir nenhum evento nesta página, verifique se a página **de Subscrição de Autocarros** de Serviço não mostra qualquer **contagem de mensagens Ativas**. Se o número deste contador for superior a zero, as mensagens na subscrição não são reencaminhadas para a função de manipulador (manipulador de subscrição de eventos) por alguma razão. Verifique se configura a subscrição do evento corretamente. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/subscription-active-message-count.png" alt-text="Contagem de mensagens ativas na subscrição do Service Bus":::    
1. Você também vê **eventos entregues** na página **eventos** do espaço de nomes do Service Bus. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-page.png" alt-text="Página de eventos - eventos entregues" lightbox="./media/service-bus-to-event-grid-integration-example/invocation-details.png":::
1. Também pode ver que os eventos são entregues na página **de Subscrição** de Eventos. Pode chegar a esta página selecionando a subscrição do evento na página **Eventos.** 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-delivered-events.png" alt-text="Página de subscrição do evento - eventos entregues":::
    


## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o [Azure Event Grid](../event-grid/index.yml).
* Saiba mais sobre as [Funções do Azure](../azure-functions/index.yml).
* Saiba mais sobre a [funcionalidade Logic Apps do Serviço de Aplicações do Azure](../logic-apps/index.yml).
* Saiba mais sobre o [Azure Service Bus](/azure/service-bus/).


[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png