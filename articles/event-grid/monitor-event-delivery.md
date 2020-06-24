---
title: Ver métricas da grelha de eventos Azure e definir alertas
description: Este artigo descreve como usar o portal Azure para ver métricas para tópicos e subscrições de Azure Event Grid e criar alertas sobre eles.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: spelluru
ms.openlocfilehash: e74d2d8982cac961aa65d6576c80a92cb53ce387
ms.sourcegitcommit: 51718f41d36192b9722e278237617f01da1b9b4e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85100665"
---
# <a name="monitor-event-grid-message-delivery"></a>Monitorar a entrega de mensagens da grelha de eventos 
Este artigo descreve como usar o portal para ver métricas para tópicos e subscrições de Event Grid, e criar alertas sobre eles. 

## <a name="metrics"></a>Métricas

O portal apresenta métricas para o estado de entrega de mensagens de evento.

Para tópicos, aqui estão algumas das métricas:

* **Publicação Conseguida**: Evento enviado com sucesso para o tema, e processado com uma resposta 2xx.
* **Publicar Falha:** Evento enviado para o tópico mas rejeitado com um código de erro.
* **Incomparável**: Evento publicado com sucesso para o tema, mas não corresponde a uma subscrição de evento. O evento foi encerrado.

Para subscrições, aqui estão algumas das métricas:

* **Entrega Conseguida**: Evento entregue com sucesso no ponto final da subscrição, e recebeu uma resposta de 2xx.
* **Entrega Falhada**: Sempre que o serviço tenta entregar e o manipulador de eventos não devolve um código 2xx de sucesso, o contador **De entrega falhou.** Se tentarmos entregar o mesmo evento várias vezes e falharmos, o contador **Dedesema falha de entrega** é incrementado para cada falha.
* **Eventos Expirados**: O evento não foi entregue e todas as tentativas de retenção foram enviadas. O evento foi encerrado.
* **Eventos Combinados**: Evento no tópico foi acompanhado pela subscrição do evento.

    > [!NOTE]
    > Para obter a lista completa de métricas, consulte [métricas suportadas pela Azure Event Grid](metrics.md).

## <a name="view-custom-topic-metrics"></a>Ver métricas de tópicos personalizados

Se publicou um tópico personalizado, pode ver as métricas para o mesmo. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Na barra de pesquisa do tópico, escreva **Tópicos de Grelha de Eventos**e, em seguida, selecione Tópicos de Grelha de **Eventos** da lista de drop-down. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topics.png" alt-text="Procurar e selecionar tópicos de grelha de eventos":::
3. Selecione o seu tópico personalizado na lista de tópicos. 

    :::image type="content" source="./media/monitor-event-delivery/select-custom-topic.png" alt-text="Selecione o seu tópico personalizado":::
4. Veja as métricas para o tema do evento personalizado na página **tópico da grelha de evento.** Na imagem a seguir, a secção **Essentials** que mostra o grupo de recursos, subscrição etc. é minimizada. 

    :::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics.png" alt-text="Ver métricas do evento":::

Pode criar gráficos com métricas suportadas utilizando o separador **Métricas** da página **Tópico de Grelha de Evento.**

:::image type="content" source="./media/monitor-event-delivery/topics-metrics-page.png" alt-text="Tópico - Página de métricas":::

Para saber mais sobre métricas, consulte [métricas no Azure Monitor](../azure-monitor/platform/data-platform-metrics.md)

Por exemplo, consulte o gráfico de métricas para a **métrica de Eventos Publicados.**

:::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics-example.png" alt-text="Métrica de eventos publicados":::


## <a name="view-subscription-metrics"></a>Ver métricas de subscrição
1. Navegue para a página **tópico da grelha de eventos** seguindo os passos da secção anterior. 
2. Selecione a subscrição a partir do painel inferior, como mostrado no exemplo seguinte. 

    :::image type="content" source="./media/monitor-event-delivery/select-event-subscription.png" alt-text="Selecione subscrição de eventos":::    

    Também pode pesquisar **subscrições de Grade de Eventos** na barra de pesquisa no portal Azure, selecione **Topic Type,** **Subscrição**e **Localização** para ver uma subscrição do evento. 

    :::image type="content" source="./media/monitor-event-delivery/event-subscriptions-page.png" alt-text="Selecione a subscrição do evento a partir da página de subscrições da Grade de Eventos":::        

    Para tópicos personalizados, selecione **Tópicos de Grelha de Eventos** como **Tipo tópico**. Para tópicos do sistema, selecione o tipo de recurso Azure, por exemplo, **Contas de Armazenamento (Blob, GPv2)**. 
3. Consulte as métricas da subscrição na página inicial para a subscrição num gráfico. Pode ver as métricas **General,** **Error,** **Latência**e **Carta Morta** durante as últimas 1 hora, 6 horas, 12 horas, 1 dia, 7 dias ou 30 dias. 

    :::image type="content" source="./media/monitor-event-delivery/subscription-home-page-metrics.png" alt-text="Métricas na página inicial da subscrição":::    

## <a name="view-system-topic-metrics"></a>Ver métricas de tópicos do sistema

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Na barra de pesquisa do tópico, escreva **Tópicos do Sistema de Grelha de Eventos**e, em seguida, selecione Tópicos do Sistema de Grelha de **Eventos** da lista de drop-down. 

    :::image type="content" source="./media/monitor-event-delivery/search-system-topics.png" alt-text="Procurar e selecionar tópicos do sistema de grelha de eventos":::
3. Selecione o tópico do seu sistema na lista de tópicos. 

    :::image type="content" source="./media/monitor-event-delivery/select-system-topic.png" alt-text="Selecione o tópico do seu sistema":::
4. Veja as métricas do tópico do sistema na página **tópico do sistema de grelha de evento.** Na imagem a seguir, a secção **Essentials** que mostra o grupo de recursos, subscrição etc. é minimizada. 

    :::image type="content" source="./media/monitor-event-delivery/system-topic-overview-metrics.png" alt-text="Ver métricas de tópico do sistema na página geral":::

Pode criar gráficos com métricas suportadas utilizando o separador **Métricas** da página **Tópico de Grelha de Evento.**

:::image type="content" source="./media/monitor-event-delivery/system-topic-metrics-page.png" alt-text="Tópico do Sistema - Página de métricas":::

Para saber mais sobre métricas, consulte [métricas no Azure Monitor](../azure-monitor/platform/data-platform-metrics.md)


## <a name="set-alerts"></a>Definir alertas
Pode definir alertas sobre métricas para tópicos e domínios no portal Azure. 

O procedimento a seguir mostra como criar um alerta sobre a métrica **de eventos com letras mortas** para um tópico personalizado. Neste exemplo, um e-mail é enviado ao proprietário do grupo de recursos Azure quando o evento com letras mortas conta para um tópico que vai acima de 10. 

1. Na página **Tópico da Grelha de Evento** para o seu tópico, selecione **Alertas** no menu esquerdo e, em seguida, selecione **+Nova regra de alerta**. 

    :::image type="content" source="./media/monitor-event-delivery/new-alert-button.png" alt-text="Página de alertas - Novo botão de regra de alerta":::
    
    
    Também pode criar alertas utilizando a página **Métricas.** Os degraus são semelhantes. 

    :::image type="content" source="./media/monitor-event-delivery/metric-page-create-alert-button.png" alt-text="Página de métricas - Criar botão de alerta":::   
    
2. Na página **'Criar' regra de alerta,** verifique se o seu tópico está selecionado para o recurso. Em seguida, clique **em Selecionar a condição**. 

    :::image type="content" source="./media/monitor-event-delivery/alert-select-condition.png" alt-text="Página de alertas - selecione condição":::    
3. Na página lógica de **sinal de configuração,** siga estes passos:
    1. Selecione uma métrica ou uma entrada de registo de atividade. Neste exemplo, é selecionado **o Dead Lettered Events.** 

        :::image type="content" source="./media/monitor-event-delivery/select-dead-lettered-events.png" alt-text="Selecione eventos com letras mortas":::        
    2. Selecione dimensões (opcional). 
        
        :::image type="content" source="./media/monitor-event-delivery/configure-signal-logic.png" alt-text="Configurar lógica de sinal":::        
    3. Rola para baixo. Na secção **lógica alerta,** selecione um **Operador,** **tipo de agregação,** e introduza um **valor Threshold**, e selecione **Fazer**. Neste exemplo, um alerta é desencadeado quando o número total de eventos mortos é superior a 10. 
    
        :::image type="content" source="./media/monitor-event-delivery/alert-logic.png" alt-text="Lógica de alerta":::                
4. De volta à página **de regra de alerta Criar,** clique em Selecionar grupo de **ação**.

    :::image type="content" source="./media/monitor-event-delivery/select-action-group-button.png" alt-text="Selecione botão de grupo de ação":::
5. Selecione **Criar grupo de ação** na barra de ferramentas para criar um novo grupo de ação. Também pode selecionar um grupo de ação existente.        
6. Na página do **grupo de ação Add,** siga estes passos:
    1. Insira um **nome para o grupo de ação**.
    1. Insira um **nome curto** para o grupo de ação.
    1. Selecione uma **subscrição Azure** na qual pretende criar o grupo de ação.
    1. Selecione o **grupo de recursos Azure** no qual pretende criar o grupo de ação.
    1. Insira um **nome para a ação.** 
    1. Selecione o **tipo de ação**. Neste exemplo, é selecionada **a Função de Gestor de Recursos do Email Azure,** especificamente a função **de Proprietário.** 
    1. Selecione **OK** para fechar a página. 
    
        :::image type="content" source="./media/monitor-event-delivery/add-action-group-page.png" alt-text="Adicionar página de grupo de ação":::                   
7. Volte à página **de regra de alerta Criar,** insira um nome para a regra de alerta e, em seguida, selecione Criar a regra de **alerta**.

    :::image type="content" source="./media/monitor-event-delivery/alert-rule-name.png" alt-text="Nome da regra de alerta":::  
8. Agora, na página **alertas** do tópico, vê-se um link para gerir as regras de alerta se ainda não houver alertas. Se houver alertas, selecione **as regras de alerta do Gestor** na barra de ferramentas.  

    :::image type="content" source="./media/monitor-event-delivery/manage-alert-rules.png" alt-text="Gerir alertas":::

    > [!NOTE]
    > Este artigo não cobre todos os diferentes passos e combinações que pode usar para criar um alerta. Para obter uma visão geral dos alertas, consulte [a visão geral dos Alertas.](../azure-monitor/platform/alerts-overview.md)

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre entrega de eventos e retrórias, [entrega e redação de mensagens de Event Grid.](delivery-and-retry.md)
* Para obter uma introdução ao Event Grid, veja [Sobre o Azure Event Grid](overview.md).
* Para começar rapidamente a utilizar a Grade de Eventos, consulte [Criar e encaminhar eventos personalizados com a Azure Event Grid](custom-event-quickstart.md).
