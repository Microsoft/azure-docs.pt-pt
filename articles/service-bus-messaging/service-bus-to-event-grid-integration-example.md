---
title: Lidar com eventos de ônibus de serviço através de grade de eventos usando apps Azure Logic
description: Este artigo fornece passos para lidar com eventos de Service Bus através da Grade de Eventos usando Azure Logic Apps.
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 10/16/2020
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: 93375f6047fbe4eda2132e024dab0e067e83ccf1
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95999070"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-logic-apps"></a>Tutorial: Responda aos eventos de ônibus da Azure Service recebidos através da Azure Event Grid utilizando apps Azure Logic
Neste tutorial, você aprende a responder aos eventos do Azure Service Bus que são recebidos através da Azure Event Grid utilizando apps Azure Logic. 

[!INCLUDE [service-bus-event-grid-prerequisites](../../includes/service-bus-event-grid-prerequisites.md)]

## <a name="receive-messages-by-using-logic-apps"></a>Receber mensagens através do Logic Apps
Neste passo, você cria uma app lógica Azure que recebe eventos de Service Bus via Azure Event Grid. 

1. Crie uma aplicação lógica no portal Azure.
    1. Selecione **+ Crie um recurso,** selecione **Integração** e, em seguida, selecione **a Aplicação Lógica**. 
    2. Na **Aplicação Lógica - Criar** página, insira um **nome** para a aplicação lógica.
    3. Selecione a sua **subscrição Azure**. 
    4. Selecione **Utilizar o** **grupo de recursos** e selecione o grupo de recursos que utilizou para outros recursos (como a função Azure, Service Bus namespace) que criou anteriormente. 
    5. Selecione a **Localização** para a aplicação lógica. 
    6. Selecione **Review + Criar**. 
    1. Na página **'Rever + Criar',** selecione **Criar** para criar a aplicação lógica. 
1. Na página **de Design de Aplicações Lógicas,** selecione Blank Logic **App** em **modelos**. 
1. No designer, faça os seguintes passos:
    1. Procure por **Grelha de Eventos.** 
    2. Selecione **Quando ocorrer um evento de recurso - Azure Event Grid**. 

        ![Logic Apps Designer - selecione Event Grid trigger](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. Selecione **Iniciar sôms,** insira as suas credenciais Azure e selecione **Permitir o Acesso**. 
5. Na página **Quando ocorrer um evento de recurso,** faça os seguintes passos:
    1. Selecione a sua subscrição do Azure. 
    2. Para **o tipo de recurso**, selecione **Microsoft.ServiceBus.Namespaces**. 
    3. Para **obter nome de recurso,** selecione o seu espaço de nomes de Service Bus. 
    4. **Selecione Adicione novo parâmetro** e selecione Filtro de **Sufixo**. 
    5. Para **o Filtro Sfixix,** insira o nome da sua segunda subscrição de tópico Service Bus. 
        ![Logic Apps Designer - evento de configuração](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. Selecione **+ Novo Passo** no designer e faça os seguintes passos:
    1. Pesquisa rumo **ao Service Bus**.
    2. Selecione **Service Bus** na lista. 
    3. Selecione para **Obter mensagens** na lista **de Ações.** 
    4. **Selecione Obter mensagens de uma subscrição de tópico (peek-lock)**. 

        ![Logic Apps Designer - obtenha ação de mensagens](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. Introduza um **nome para a ligação**. Por exemplo: **Obtenha mensagens da subscrição de tópicos** e selecione o espaço de nomes do Service Bus. 

        ![Logic Apps Designer - selecione o espaço de nomes do Service Bus](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. Selecione **RootManageSharedAccessKey** e, em seguida, **selecione Create**.

        ![Logic Apps Designer - selecione a chave de acesso partilhada](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    8. Selecione o seu **tópico** e **subscrição**. 
    
        ![Screenshot que mostra onde seleciona o seu tópico e subscrição.](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. Selecione **+ Novo passo**, e faça os seguintes passos: 
    1. Selecione **Service Bus**.
    2. **Selecione Preencha a mensagem numa subscrição de tópico** da lista de ações. 
    3. Selecione o seu tópico de ônibus **de serviço**.
    4. Selecione a segunda **subscrição** do tópico.
    5. Para **bloquear o sinal da mensagem,** selecione Lock **Token** a partir do **conteúdo Dynamic**. 

        ![Logic Apps Designer - complete a mensagem](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. **Selecione Guardar** na barra de ferramentas no Logic Apps Designer para guardar a aplicação lógica. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/save-logic-app.png" alt-text="Salvar aplicativo de lógica":::
1. Se ainda não enviou mensagens de teste para o tema, siga as instruções na secção Enviar mensagens para a secção [de assuntos do Service Bus](#send-messages-to-the-service-bus-topic) para enviar mensagens para o tema. 
1. Mude para a página **geral** da sua aplicação lógica. Você vê a aplicação lógica corre na história do **Runs** para as mensagens enviadas. Pode levar alguns minutos até ver a aplicação lógica ser executado. Selecione **Refresh** na barra de ferramentas para refrescar a página. 

    ![Logic Apps Designer - aplicação lógica corre](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)
1. Selecione uma aplicação lógica executada para ver os detalhes. Note que processou 5 mensagens no loop. 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/logic-app-run-details.png" alt-text="Detalhes de execução de aplicativos lógicos":::    

## <a name="troubleshoot"></a>Resolução de problemas
Se não vir nenhuma invocação depois de esperar e refrescar por algum tempo, siga estes passos: 

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