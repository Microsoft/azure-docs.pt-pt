---
title: Enviar alertas do Azure Application Insights | Documentos da Microsoft
description: Tutorial para enviar alertas em resposta a erros na sua aplicação com o Azure Application Insights.
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 70a11867dded3b7156f6b212ceb4756ee7c287f6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58079167"
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Monitorizar e alertar relativamente ao estado de funcionamento de aplicação com o Azure Application Insights

O Azure Application Insights permite-lhe monitorizar a sua aplicação e enviar-lhe alertas quando está indisponível, a experienciar falhas ou sofrer problemas de desempenho.  Este tutorial guia-o pelo processo de criação de testes para verificar continuamente a disponibilidade da sua aplicação e enviar diferentes tipos de alertas em resposta a detetados problemas.  Saiba como:

> [!div class="checklist"]
> * Criar teste de disponibilidade para verificar continuamente a resposta do aplicativo
> * Envie um e-mail para os administradores quando ocorrer um problema
> * Criar alertas com base em métricas de desempenho 
> * Utilize uma aplicação lógica para enviar telemetria resumida com base numa agenda.


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Instale o [2017 do Visual Studio](https://www.visualstudio.com/downloads/) com as seguintes cargas de trabalho:
    - Desenvolvimento ASP.NET e Web
    - Desenvolvimento do Azure
    - Implemente uma aplicação .NET no Azure e [ative o Application Insights SDK](../../azure-monitor/app/asp-net.md). 


## <a name="log-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-availability-test"></a>Criar teste de disponibilidade
Testes de disponibilidade no Application Insights permitem-lhe testar automaticamente a sua aplicação de vários locais em todo o mundo.   Neste tutorial, é necessário executar um teste simples para garantir que a aplicação está disponível.  Também é possível criar instruções completas para testar a sua operação detalhada. 

1. Selecione **Application Insights** e, em seguida, selecione a sua subscrição.  
1. Selecione **disponibilidade** sob a **investigar** menu e clique em **adicionar teste**.
 
    ![Adicione o teste de disponibilidade](media/tutorial-alert/add-test.png)

2. Escreva um nome para o teste e deixe as outras predefinições.  Isso solicita a home page da aplicação a cada 5 minutos 5 localizações geográficas diferentes. 
3. Selecione **alertas** para abrir o **alertas** painel onde pode definir os detalhes sobre como responder se o teste é reprovado. Tipo de um endereço de e-mail para enviar quando os critérios de alerta são cumpridos.  Pode opcionalmente digitar o endereço de um webhook para chamar e quando os critérios de alerta são cumpridos.

    ![Criar teste](media/tutorial-alert/create-test.png)
 
4. Regresse ao painel de teste e, após alguns minutos deve começar a ver resultados do teste de disponibilidade.  Clique no nome do teste para ver os detalhes de cada localização.  O gráfico de dispersão mostra o êxito e a duração de cada teste.

    ![Detalhes do teste](media/tutorial-alert/test-details.png)

5.  Pode desagregar detalhes de qualquer teste específico ao clicar no respetivo ponto no gráfico de dispersão.  O exemplo abaixo mostra os detalhes de um pedido falhado.

    ![Resultado do teste](media/tutorial-alert/test-result.png)
  
6. Se os critérios de alerta for cumprida, é enviado um e-mail semelhante ao seguinte para o endereço que especificou.

    ![Email de alerta](media/tutorial-alert/alert-mail.png)


## <a name="create-an-alert-from-metrics"></a>Criar um alerta de métricas
Para além de enviar alertas a partir de um teste de disponibilidade, pode criar um alerta a partir de qualquer métricas de desempenho que estão a ser recolhidas para a sua aplicação.

1. Selecione **alertas** partir do **configurar** menu.  Esta ação abre o painel de alertas do Azure.  Podem existir outras regras de alerta configuradas aqui para outros serviços.
1. Clique em **Adicionar alerta de métrica**.  Esta ação abre o painel para criar uma nova regra de alerta.

    ![Adicionar alerta de métrica](media/tutorial-alert/add-metric-alert.png)

1. Escreva um **nome** para o alerta da regra e selecione a aplicação na lista pendente para **recurso**.
1. Selecione um **métrica** ao exemplo.  Um gráfico é exibido para indicar o valor deste pedido ao longo das últimas 24 horas.  Isto ajuda a definir a condição para a métrica.

    ![Adicionar regra de alerta](media/tutorial-alert/add-alert-01.png)

1. Especifique um **condição** e **limiar** para o alerta. Este é o número de vezes que a métrica tem de ser excedida para um alerta a ser criada. 
1. Sob **notificar através de** verificar o **proprietários, contribuidores e leitores do E-Mail** caixa para enviar um e-mail para estes utilizadores quando a condição de alerta é cumprida e adicionar o endereço de e-mail de todos os destinatários adicionais.  Também pode especificar um webhook ou uma aplicação de lógica aqui que é executada quando a condição é cumprida.  Estes podem ser utilizadas para tentar mitigar o problema detetado ou 

    ![Adicionar regra de alerta](media/tutorial-alert/add-alert-02.png)


## <a name="proactively-send-information"></a>Enviar proativamente informações
Os alertas são criados na reação a um conjunto específico de problemas identificados em seu aplicativo e é normalmente reservar alertas condições críticas que exigem atenção imediata.  Proativamente podem receber informações sobre a sua aplicação com uma aplicação lógica que é executado automaticamente com base numa agenda.  Por exemplo, poderia ter um correio enviado para os administradores diariamente com informações de resumo que requer a avaliação.

Para obter detalhes sobre como criar uma aplicação lógica com o Application Insights, consulte [automatizar o Application Insights processa ao utilizar o Logic Apps](../../azure-monitor/app/automate-with-logic-apps.md)

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu alertar relativamente a problemas, avance para o próximo tutorial para saber como analisar a forma como os usuários estão interagindo com a sua aplicação.

> [!div class="nextstepaction"]
> [Compreender os utilizadores](../../azure-monitor/learn/tutorial-users.md)