---
title: Detecção inteligente-anomalias de falha, em Application Insights | Microsoft Docs
description: Alerta você sobre alterações incomuns na taxa de solicitações com falha para seu aplicativo Web e fornece análise de diagnóstico. Nenhuma configuração é necessária.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/18/2018
ms.reviewer: yossiy
ms.openlocfilehash: f8b8318a16b36593d2fbaf08bcbc19156dc96006
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820583"
---
# <a name="smart-detection---failure-anomalies"></a>Detecção inteligente-anomalias de falha
[Application insights](../../azure-monitor/app/app-insights-overview.md) notificará você automaticamente quase em tempo real se seu aplicativo Web sofrer um aumento anormal na taxa de solicitações com falha. Ele detecta um aumento incomum na taxa de solicitações HTTP ou chamadas de dependência que são relatadas como com falha. Para solicitações, as solicitações com falha geralmente são aquelas com códigos de resposta de 400 ou superior. Para ajudá-lo a fazer a triagem e diagnosticar o problema, uma análise das características das falhas e da telemetria relacionada é fornecida na notificação. Também há links para o portal de Application Insights para diagnóstico mais detalhado. O recurso não precisa de instalação nem configuração, pois usa algoritmos de aprendizado de máquina para prever a taxa normal de falhas.

Esse recurso funciona para qualquer aplicativo Web, hospedado na nuvem ou em seus próprios servidores, que gera telemetria de solicitação ou de dependência, por exemplo, se você tiver uma função de trabalho que chame [TrackRequest ()](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) ou [TrackDependency ()](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

Depois de configurar [Application insights para seu projeto](../../azure-monitor/app/app-insights-overview.md)e desde que seu aplicativo gere uma certa quantidade mínima de telemetria, a detecção inteligente de anomalias de falha leva 24 horas para aprender o comportamento normal do seu aplicativo, antes que ele seja ligado e possa enviar alertas.

Aqui está um alerta de exemplo.

![Alerta de detecção inteligente de exemplo mostrando a análise de cluster em relação à falha](./media/proactive-failure-diagnostics/013.png)

> [!NOTE]
> Por padrão, você obtém um email de formato mais curto do que este exemplo. Mas você pode [alternar para esse formato detalhado](#configure-alerts).
>
>

Observe que ele diz:

* A taxa de falha em comparação com o comportamento normal do aplicativo.
* Quantos usuários são afetados, portanto, você sabe quanto deve se preocupar.
* Um padrão característico associado às falhas. Neste exemplo, há um código de resposta específico, o nome da solicitação (operação) e a versão do aplicativo. Isso informa imediatamente onde começar a procurar em seu código. Outras possibilidades podem ser um sistema operacional específico de navegador ou cliente.
* A exceção, os rastreamentos de log e a falha de dependência (bancos de dados ou outros componentes externos) que parecem estar associados às falhas caracterizadas.
* Links diretamente para pesquisas relevantes na telemetria no Application Insights.

## <a name="failure-anomalies-v2"></a>Anomalias de falha v2
Uma nova versão da regra de alerta de anomalias de falha agora está disponível. Essa nova versão está em execução na nova plataforma de alerta do Azure e apresenta uma variedade de melhorias em relação à versão existente.

### <a name="whats-new-in-this-version"></a>O que há de novo nesta versão?
- Detecção mais rápida de problemas
- Um conjunto mais rico de ações-a regra de alerta é criada com um [grupo de ações](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) associado chamado "detecção inteligente de Application insights" que contém ações de email e webhook e pode ser estendido para disparar ações adicionais quando o alerta é disparado.
- Notificações mais focadas-as notificações por email enviadas por essa regra de alerta agora são enviadas por padrão para os usuários associados às funções de colaborador de monitoramento e monitoração da assinatura. Mais informações sobre isso estão disponíveis [aqui](https://docs.microsoft.com/azure/azure-monitor/app/proactive-email-notification).
- Configuração mais fácil por meio de modelos ARM – Veja o exemplo [aqui](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config).
- Suporte a esquema de alerta comum-as notificações enviadas desta regra de alerta seguem o [esquema de alerta comum](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema).
- Modelo de email unificado – as notificações por email dessa regra de alerta têm uma aparência consistente & se sentem com outros tipos de alertas. Com essa alteração, a opção de obter os alertas de anomalias com informações de diagnóstico detalhadas não está mais disponível.

### <a name="how-do-i-get-the-new-version"></a>Como fazer obter a nova versão?
- Os recursos de Application Insights criados recentemente agora são provisionados com a nova versão da regra de alerta de anomalias de falha.
- Os recursos existentes do Application Insights com a versão clássica da regra de alerta de anomalias receberão a nova versão depois que sua assinatura de hospedagem for migrada para a nova plataforma de alerta como parte do processo de desativação de [alertas clássicos](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement).

> [!NOTE]
> A nova versão da regra de alerta de anomalias de falha permanece gratuita. Além disso, ações de email e webhook disparadas pelo grupo de ações "detecção inteligente de Application Insights" associado também são gratuitas.
> 
> 

## <a name="benefits-of-smart-detection"></a>Benefícios da detecção inteligente
Os [alertas de métrica](../../azure-monitor/app/alerts.md) comuns informam que pode haver um problema. Mas a detecção inteligente inicia o trabalho de diagnóstico para você, executando uma grande parte da análise que, de outra forma, você teria de fazer por conta própria. Você Obtém os resultados empacotados de forma organizada, ajudando-o a obter rapidamente a raiz do problema.

## <a name="how-it-works"></a>Como funciona
A detecção inteligente monitora a telemetria recebida do seu aplicativo e, em particular, as tarifas de falha. Essa regra conta o número de solicitações para as quais a propriedade `Successful request` é falsa e o número de chamadas de dependência para as quais a propriedade `Successful call` é falsa. Para solicitações, por padrão, `Successful request == (resultCode < 400)` (a menos que você tenha escrito código personalizado para [Filtrar](../../azure-monitor/app/api-filtering-sampling.md#filtering) ou gerar suas próprias chamadas [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) ). 

O desempenho do aplicativo tem um padrão típico de comportamento. Algumas solicitações ou chamadas de dependência serão mais propensas a falhas do que outras; e a taxa geral de falha pode aumentar à medida que a carga aumenta. A detecção inteligente usa o aprendizado de máquina para encontrar essas anomalias.

Como a telemetria entra em Application Insights de seu aplicativo Web, a detecção inteligente compara o comportamento atual com os padrões vistos nos últimos dias. Se uma elevação anormal na taxa de falha for observada por comparação com o desempenho anterior, uma análise será disparada.

Quando uma análise é disparada, o serviço executa uma análise de cluster na solicitação com falha, para tentar identificar um padrão de valores que caracterizam as falhas. No exemplo acima, a análise descobriu que a maioria das falhas são sobre um código de resultado específico, o nome da solicitação, o host de URL do servidor e a instância de função. Por outro lado, a análise descobriu que a propriedade do sistema operacional do cliente é distribuída por vários valores e, portanto, não está listada.

Quando seu serviço é instrumentado com essas chamadas de telemetria, o analisador procura uma exceção e uma falha de dependência associadas a solicitações no cluster que ele identificou, junto com um exemplo de todos os logs de rastreamento associados a essas solicitações.

A análise resultante é enviada para você como alerta, a menos que você o tenha configurado para não.

Como os [alertas definidos manualmente](../../azure-monitor/app/alerts.md), você pode inspecionar o estado do alerta e configurá-lo na folha alertas de seu recurso de Application insights. Mas, ao contrário de outros alertas, você não precisa configurar nem configurar a detecção inteligente. Se desejar, você pode desabilitá-lo ou alterar seus endereços de email de destino.

### <a name="alert-logic-details"></a>Detalhes da lógica de alerta

Os alertas são disparados por nosso algoritmo de aprendizado de máquina proprietário para que não seja possível compartilhar os detalhes de implementação exatos. Dito isso, entendemos que às vezes você precisa saber mais sobre como a lógica subjacente funciona. Os principais fatores que são avaliados para determinar se um alerta deve ser disparado são: 

* Análise da porcentagem de falhas de solicitações/dependências em uma janela de tempo de interrupção de 20 minutos.
* Uma comparação do percentual de falha dos últimos 20 minutos até a taxa nos últimos 40 minutos e nos últimos sete dias e procurando desvios significativos que excedem X vezes esse desvio padrão.
* Usando um limite adaptável para a porcentagem mínima de falhas, que varia com base no volume do aplicativo de solicitações/dependências.

## <a name="configure-alerts"></a>Configurar alertas
Você pode desabilitar a detecção inteligente, alterar os destinatários do email, criar um webhook ou aceitar mensagens de alerta mais detalhadas.

Abra a página alertas. As anomalias de falha são incluídas junto com todos os alertas que você definiu manualmente, e você pode ver se ele está no estado do alerta no momento.

![Na página Visão geral, clique no bloco alertas. Ou em qualquer página de métricas, clique no botão alertas.](./media/proactive-failure-diagnostics/021.png)

Clique no alerta para configurá-lo.

![Configuração](./media/proactive-failure-diagnostics/032.png)

Observe que você pode desabilitar a detecção inteligente, mas não pode excluí-la (ou criar outra).

#### <a name="detailed-alerts"></a>Alertas detalhados
Se você selecionar "obter diagnóstico mais detalhado", o email conterá mais informações de diagnóstico. Às vezes, você poderá diagnosticar o problema apenas dos dados no email.

Há um pequeno risco de que o alerta mais detalhado possa conter informações confidenciais, pois inclui mensagens de rastreamento e exceção. No entanto, isso só aconteceria se seu código pudesse permitir informações confidenciais nessas mensagens.

## <a name="triaging-and-diagnosing-an-alert"></a>Triagem e diagnosticando um alerta
Um alerta indica que um aumento anormal na taxa de solicitação com falha foi detectado. É provável que haja algum problema com seu aplicativo ou seu ambiente.

Do percentual de solicitações e do número de usuários afetados, você pode decidir o quão urgente é o problema. No exemplo acima, a taxa de falha de 22,5% é comparada com uma taxa normal de 1%, indica que algo insatisfatório está acontecendo. Por outro lado, apenas 11 usuários foram afetados. Se fosse seu aplicativo, seria possível avaliar o quão sério é.

Em muitos casos, você poderá diagnosticar o problema rapidamente do nome da solicitação, da exceção, da falha de dependência e dos dados de rastreamento fornecidos.

Há algumas outras pistas. Por exemplo, a taxa de falha de dependência neste exemplo é igual à taxa de exceção (89,3%). Isso sugere que a exceção surge diretamente da falha de dependência, dando a você uma ideia clara de onde começar a procurar em seu código.

Para investigar melhor, os links em cada seção levarão você diretamente para uma [página de pesquisa](../../azure-monitor/app/diagnostic-search.md) filtrada para as solicitações, exceções, dependências ou rastreamentos relevantes. Ou você pode abrir o [portal do Azure](https://portal.azure.com), navegar até o recurso de Application insights para seu aplicativo e abrir a folha falhas.

Neste exemplo, clicar no link ' Exibir detalhes de falhas de dependência ' abre a folha de pesquisa Application Insights. Ele mostra a instrução SQL que tem um exemplo da causa raiz: os nulos foram fornecidos em campos obrigatórios e não passaram na validação durante a operação de salvamento.

![Pesquisa de diagnóstico](./media/proactive-failure-diagnostics/051.png)

## <a name="review-recent-alerts"></a>Examinar alertas recentes

Clique em **detecção inteligente** para obter o alerta mais recente:

![Resumo de alertas](./media/proactive-failure-diagnostics/070.png)


## <a name="whats-the-difference-"></a>Qual é a diferença...
A detecção inteligente de anomalias de falha complementa outros recursos semelhantes, mas distintos de Application Insights.

* Os [alertas de métrica](../../azure-monitor/app/alerts.md) são definidos por você e podem monitorar uma ampla gama de métricas, como ocupação de CPU, taxas de solicitação, tempos de carregamento de página e assim por diante. Você pode usá-los para avisá-lo, por exemplo, se precisar adicionar mais recursos. Por outro lado, a detecção inteligente de anomalias de falha cobre uma pequena gama de métricas críticas (atualmente, apenas a taxa de solicitação com falha), projetada para notificá-lo quase em tempo real, uma vez que a taxa de solicitação com falha do seu aplicativo Web aumenta significativamente em comparação com o aplicativo Web comportamento normal.

    A detecção inteligente ajusta automaticamente seu limite em resposta às condições que prevalecem.

    A detecção inteligente inicia o trabalho de diagnóstico para você.
* A [detecção inteligente de anomalias de desempenho](proactive-performance-diagnostics.md) também usa a inteligência de máquina para descobrir padrões incomuns em suas métricas e nenhuma configuração por você é necessária. Mas, ao contrário da detecção inteligente de anomalias de falha, a finalidade da detecção inteligente de anomalias de desempenho é encontrar segmentos de seu uso diversa que podem ser atendidos incorretamente, por exemplo, por páginas específicas em um tipo específico de navegador. A análise é executada diariamente e, se qualquer resultado for encontrado, provavelmente será muito menos urgente do que um alerta. Por outro lado, a análise de anomalias de falha é executada continuamente na telemetria de entrada e você será notificado em minutos se as taxas de falha do servidor forem maiores do que o esperado.

## <a name="if-you-receive-a-smart-detection-alert"></a>Se você receber um alerta de detecção inteligente
*Por que eu recebi esse alerta?*

* Detectamos um aumento anormal na taxa de solicitações com falha em comparação com a linha de base normal do período anterior. Após a análise das falhas e da telemetria associada, achamos que há um problema que você deve examinar.

*A notificação significa que eu definitivamente tenho um problema?*

* Tentamos alertar sobre a interrupção ou degradação do aplicativo, mas apenas você pode entender totalmente a semântica e o impacto sobre o aplicativo ou os usuários.

*Então, vocês examinam os meus dados?*

* Não. O serviço é totalmente automático. Somente você obtém as notificações. Seus dados são [privados](../../azure-monitor/app/data-retention-privacy.md).

*É necessário assinar este alerta?*

* Não. Cada aplicativo que envia telemetria de solicitação tem a regra de alerta de detecção inteligente.

*Posso cancelar a assinatura ou receber as notificações enviadas aos meus colegas?*

* Sim, em regras de alerta, clique na regra de detecção inteligente para configurá-la. Você pode desabilitar o alerta ou alterar os destinatários do alerta.

*Eu perdi o email. Onde posso encontrar as notificações no portal?*

* Nos logs de atividade. No Azure, abra o recurso Application Insights para seu aplicativo e, em seguida, selecione logs de atividade.

*Alguns dos alertas são sobre problemas conhecidos e não quero recebê-los.*

* Temos a supressão de alerta em nossa pendência.

## <a name="next-steps"></a>Passos seguintes
Essas ferramentas de diagnóstico ajudam você a inspecionar a telemetria do seu aplicativo:

* [Gerenciador de métricas](../../azure-monitor/app/metrics-explorer.md)
* [Gerenciador de pesquisa](../../azure-monitor/app/diagnostic-search.md)
* [Análise – linguagem de consulta eficiente](../../azure-monitor/log-query/get-started-portal.md)

As detecções inteligentes são completamente automáticas. Mas talvez você queira configurar mais alguns alertas?

* [Alertas de métrica configurados manualmente](../../azure-monitor/app/alerts.md)
* [Testes da Web de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md)
