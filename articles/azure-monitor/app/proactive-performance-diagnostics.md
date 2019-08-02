---
title: Detecção inteligente-anomalias de desempenho | Microsoft Docs
description: Application Insights executa a análise inteligente de sua telemetria de aplicativo e avisa sobre possíveis problemas. Este recurso não precisa de configuração.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 6acd41b9-fbf0-45b8-b83b-117e19062dd2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/04/2017
ms.reviewer: antonfr
ms.author: mbullwin
ms.openlocfilehash: 5ccff22a74b0cb1edcbae40fca087fe3197cb6ca
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67867717"
---
# <a name="smart-detection---performance-anomalies"></a>Detecção inteligente-anomalias de desempenho

[Application insights](../../azure-monitor/app/app-insights-overview.md) analisa automaticamente o desempenho do seu aplicativo Web e pode avisá-lo sobre possíveis problemas. Você pode estar lendo isso porque recebeu uma de nossas notificações de detecção inteligente.

Esse recurso não requer nenhuma configuração especial, além de configurar seu aplicativo para Application Insights (em [ASP.net](../../azure-monitor/app/asp-net.md), [Java](../../azure-monitor/app/java-get-started.md)ou [node. js](../../azure-monitor/app/nodejs.md), e no [código da página da Web](../../azure-monitor/app/javascript.md)). Ele está ativo quando seu aplicativo gera telemetria suficiente.

## <a name="when-would-i-get-a-smart-detection-notification"></a>Quando obteria uma notificação de detecção inteligente?

Application Insights detectou que o desempenho do seu aplicativo foi prejudicado de uma das seguintes maneiras:

* **Degradação do tempo de resposta** -seu aplicativo começou a responder às solicitações mais lentamente do que costumava ser usado. A alteração pode ter sido rápida, por exemplo, porque houve uma regressão em sua implantação mais recente. Ou pode ter sido gradual, talvez causado por um vazamento de memória. 
* **Degradação da duração da dependência** – seu aplicativo faz chamadas para uma API REST, um banco de dados ou outra dependência. A dependência está respondendo mais lentamente do que costumava ser usada.
* **Padrão de desempenho lento** -seu aplicativo parece ter um problema de desempenho que está afetando apenas algumas solicitações. Por exemplo, as páginas estão sendo carregadas mais lentamente em um tipo de navegador do que outras; ou as solicitações estão sendo atendidas mais lentamente a partir de um servidor específico. Atualmente, nossos algoritmos examinam os tempos de carregamento da página, os tempos de resposta da solicitação e os tempos de resposta da dependência.  

A detecção inteligente requer pelo menos 8 dias de telemetria em um volume funcional para estabelecer uma linha de base de desempenho normal. Portanto, depois que o aplicativo estiver em execução para esse período, qualquer problema significativo resultará em uma notificação.


## <a name="does-my-app-definitely-have-a-problem"></a>Meu aplicativo definitivamente tem um problema?

Não, uma notificação não significa que seu aplicativo definitivamente tem um problema. É simplesmente uma sugestão sobre algo que pode querer examinar mais detalhadamente.

## <a name="how-do-i-fix-it"></a>Como posso corrigi-lo?

As notificações incluem informações de diagnóstico. Segue-se um exemplo:


![Aqui está um exemplo de detecção de degradação do tempo de resposta do servidor](media/proactive-performance-diagnostics/server_response_time_degradation.png)

1. **Triagem**. A notificação mostra quantos usuários ou quantas operações foram afetadas. Isso pode ajudá-lo a atribuir uma prioridade ao problema.
2. **Escopo**. O problema está afetando todo o tráfego ou apenas algumas páginas? Ele é restrito a determinados navegadores ou locais? Essas informações podem ser obtidas na notificação.
3. **Diagnostique**. Muitas vezes, as informações de diagnóstico na notificação sugerirão a natureza do problema. Por exemplo, se o tempo de resposta for mais lento quando a taxa de solicitação for alta, isso sugere que o servidor ou as dependências estão sobrecarregadas. 

    Caso contrário, abra a folha desempenho em Application Insights. Lá, você encontrará [](profiler.md) dados do profiler. Se as exceções forem geradas, você também poderá experimentar o [depurador](../../azure-monitor/app/snapshot-debugger.md)de instantâneos.



## <a name="configure-email-notifications"></a>Configurar notificações por email

As notificações de detecção inteligente são habilitadas por padrão e enviadas aos que têm acesso de [monitoramento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) e [monitoramento de colaborador](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) para a assinatura na qual reside o recurso de Application insights. Para alterar isso, clique em **Configurar** na notificação por email ou abra as configurações de detecção inteligente em Application insights. 
  
  ![Configurações de detecção inteligente](media/proactive-performance-diagnostics/smart_detection_configuration.png)
  
  * Você pode usar o link **cancelar assinatura** no email de detecção inteligente para parar de receber as notificações por email.

Emails sobre anomalias de desempenho de detecções inteligentes são limitados a um email por dia por Application Insights recurso. O email será enviado somente se houver pelo menos um novo problema detectado naquele dia. Você não obterá repetições de nenhuma mensagem. 

## <a name="faq"></a>FAQ

* *Então, a equipe da Microsoft examina meus dados?*
  * Não. O serviço é totalmente automático. Somente você obtém as notificações. Seus dados são [privados](../../azure-monitor/app/data-retention-privacy.md).
* *Você analisa todos os dados coletados por Application Insights?*
  * Não no momento. Atualmente, analisamos o tempo de resposta da solicitação, o tempo de resposta de dependência e o tempo de carregamento da página. A análise de métricas adicionais está em nossa pesquisa de pendências.

* Para quais tipos de aplicativo isso funciona?
  * Essas degradações são detectadas em qualquer aplicativo que gere a telemetria apropriada. Se você instalou Application Insights em seu aplicativo Web, as solicitações e dependências serão rastreadas automaticamente. Mas nos serviços de back-end ou outros aplicativos, se você inseriu chamadas para [TrackRequest ()](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) ou [TrackDependency](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency), a detecção inteligente funcionará da mesma maneira.

* *Posso criar minhas próprias regras de detecção de anomalias ou personalizar as regras existentes?*

  * Ainda não, mas você pode:
    * [Configure alertas](../../azure-monitor/app/alerts.md) que informam quando uma métrica ultrapassa um limite.
    * [Exportar telemetria](../../azure-monitor/app/export-telemetry.md) para um [banco de dados](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md) ou para o [PowerBI](../../azure-monitor/app/export-power-bi.md ), onde você mesmo pode analisá-lo.
* *Com que frequência a análise é realizada?*

  * Executamos a análise diária na telemetria do dia anterior (dia inteiro no fuso horário UTC).
* *Isso substitui os [alertas de métrica](../../azure-monitor/app/alerts.md)?*
  * Não.  Não confirmamos a detecção de todos os comportamentos que podem ser considerados anormais.


* *Se eu não fizer nada em resposta a uma notificação, receberei um lembrete?*
  * Não, você recebe uma mensagem sobre cada problema apenas uma vez. Se o problema persistir, ele será atualizado na folha feed de detecção inteligente.
* *Eu perdi o email. Onde posso encontrar as notificações no portal?*
  * Na Application Insights visão geral do seu aplicativo, clique no bloco **detecção inteligente** . Lá, você poderá encontrar todas as notificações de até 90 dias de volta.

## <a name="how-can-i-improve-performance"></a>Como posso melhorar o desempenho?
As respostas lentas e com falha são uma das maiores frustrações para os usuários do site, como você sabe de sua própria experiência. Portanto, é importante resolver os problemas.

### <a name="triage"></a>Triagem
Primeiro, isso é importante? Se uma página estiver sempre lenta para carregar, mas apenas 1% dos usuários do seu site já precisarem vê-lo, talvez você tenha coisas mais importantes a considerar. Por outro lado, se apenas 1% dos usuários abri-lo, mas ele lançar exceções todas as vezes, isso pode vale a pena investigar.

Use a declaração de impacto (usuários afetados ou% de tráfego) como um guia geral, mas lembre-se de que não se trata de toda a história. Reúna outras evidências para confirmar.

Considere os parâmetros do problema. Se for dependente de Geografia, configure os [testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) , incluindo essa região: pode haver apenas problemas de rede nessa área.

### <a name="diagnose-slow-page-loads"></a>Diagnosticar cargas de página lentas
Onde está o problema? O servidor está lento para responder, é a página muito longa ou o navegador precisa fazer muito trabalho para exibi-lo?

Abra a folha métrica de navegadores. A exibição segmentada do tempo de carregamento da página do navegador mostra onde o tempo está indo. 

* Se o **tempo de solicitação de envio** for alto, o servidor está respondendo lentamente ou a solicitação é uma postagem com muitos dados. Examine as métricas de [desempenho](../../azure-monitor/app/web-monitor-performance.md#metrics) para investigar os tempos de resposta.
* Configure o [acompanhamento de dependência](../../azure-monitor/app/asp-net-dependencies.md) para ver se o lentidão é devido a serviços externos ou seu banco de dados.
* Se a **resposta de recebimento** for predominante, sua página e suas partes dependentes – JavaScript, CSS, imagens e assim por diante (mas não dados carregados de forma assíncrona) serão longas. Configure um [teste de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md)e certifique-se de definir a opção para carregar partes dependentes. Ao obter alguns resultados, abra o detalhe de um resultado e expanda-o para ver os tempos de carregamento de arquivos diferentes.
* O **tempo de processamento** alto do cliente sugere que os scripts estão em execução lenta. Se o motivo não for óbvio, considere adicionar algum código de tempo e enviar os horários em chamadas trackMetric.

### <a name="improve-slow-pages"></a>Melhorar páginas lentas
Há uma Web cheia de conselhos sobre como melhorar suas respostas de servidor e tempos de carregamento de página, portanto, não tentaremos repetir tudo aqui. Aqui estão algumas dicas que você provavelmente já sabe, apenas para ajudá-lo a pensar:

* Carregamento lento devido a arquivos grandes: Carregue os scripts e outras partes de forma assíncrona. Use o agrupamento de scripts. Quebre a página principal em widgets que carregam seus dados separadamente. Não enviar HTML antigo simples para tabelas longas: Use um script para solicitar os dados como JSON ou outro formato compacto e preencha a tabela no lugar. Há excelentes estruturas para ajudar com tudo isso. (Eles também envolvem scripts grandes, é claro).
* Dependências de servidor lentas: Considere as localizações geográficas de seus componentes. Por exemplo, se você estiver usando o Azure, verifique se o servidor Web e o banco de dados estão na mesma região. As consultas recuperam mais informações do que as necessárias? Ajuda em cache ou em lote?
* Problemas de capacidade: Examine as métricas de servidor de tempos de resposta e contagens de solicitação. Se os tempos de resposta forem picos desproporcionalmente com picos nas contagens de solicitação, é provável que os servidores sejam ampliados.


## <a name="server-response-time-degradation"></a>Degradação do tempo de resposta do servidor

A notificação de degradação do tempo de resposta informa:

* O tempo de resposta em comparação com o tempo de resposta normal para esta operação.
* Quantos usuários são afetados.
* Tempo médio de resposta e tempo de resposta do 90 º percentil para esta operação no dia da detecção e 7 dias antes. 
* Contagem dessas solicitações de operação no dia da detecção e sete dias antes.
* Correlação entre degradação nesta operação e degradações em dependências relacionadas. 
* Links para ajudá-lo a diagnosticar o problema.
  * Rastreamentos do criador de perfil para ajudá-lo a exibir onde o tempo de operação é gasto (o link estará disponível se exemplos de rastreamento do criador de perfil forem coletados para essa operação durante o período de detecção). 
  * Relatórios de desempenho no Metrics Explorer, nos quais você pode dividir o intervalo de tempo/os filtros para esta operação.
  * Pesquise essa chamada para exibir propriedades de chamada específicas.
  * Relatórios de falha – se a contagem > 1 isso significa que houve falhas nessa operação que podem ter contribuído para degradação do desempenho.

## <a name="dependency-duration-degradation"></a>Degradação da duração da dependência

O aplicativo moderno cada vez mais adota a abordagem de design de micro serviços, que, em muitos casos, leva a uma confiabilidade pesada em serviços externos. Por exemplo, se seu aplicativo depender de alguma plataforma de dados ou mesmo se você criar seu próprio serviço de bot, você provavelmente retransmitirá em algum provedor de serviços cognitivas para permitir que seus bots interajam de maneiras mais humanas e um serviço de armazenamento de dados para bot para efetuar pull das respostas partir d.  

Exemplo de notificação de degradação de dependência:

![Aqui está um exemplo de detecção de degradação da duração da dependência](media/proactive-performance-diagnostics/dependency_duration_degradation.png)

Observe que ele diz:

* A duração em comparação ao tempo de resposta normal para esta operação
* Quantos usuários são afetados
* Duração média e duração 90 º do percentil para essa dependência no dia da detecção e sete dias antes
* Número de chamadas de dependência no dia da detecção e 7 dias antes
* Links para ajudá-lo a diagnosticar o problema
  * Relatórios de desempenho no Gerenciador de métricas para esta dependência
  * Pesquisar por essas chamadas de dependência para exibir propriedades de chamadas
  * Relatórios de falha – se a contagem > 1 isso significa que houve falha nas chamadas de dependência durante o período de detecção que podem ter contribuído para a degradação da duração. 
  * Abrir o Analytics com consultas que calculam a duração e a contagem da dependência  

## <a name="smart-detection-of-slow-performing-patterns"></a>Detecção inteligente de padrões de execução lenta 

Application Insights encontra problemas de desempenho que podem afetar apenas algumas partes de seus usuários ou afetam apenas os usuários em alguns casos. Por exemplo, a notificação sobre o carregamento de páginas é mais lenta em um tipo de navegador do que em outros tipos de navegadores, ou se as solicitações forem atendidas mais lentamente a partir de um servidor específico. Ele também pode descobrir problemas associados a combinações de propriedades, como carregamentos de página lentos em uma área geográfica para clientes que usam um sistema operacional específico.  

Anomalias como essas são muito difíceis de detectar apenas inspecionando os dados, mas são mais comuns do que você pode imaginar. Muitas vezes, eles só surgem quando seus clientes reclamam. Nesse momento, é tarde demais: os usuários afetados já estão mudando para seus concorrentes!

Atualmente, nossos algoritmos examinam os tempos de carregamento da página, tempos de resposta de solicitação no servidor e tempos de resposta de dependência.  

Você não precisa definir limites ou configurar regras. Os algoritmos de aprendizado de máquina e Data Mining são usados para detectar padrões anormais.

![No alerta de email, clique no link para abrir o relatório de diagnóstico no Azure](./media/proactive-performance-diagnostics/03.png)

* **Quando** mostra a hora em que o problema foi detectado.
* **O que** descreve:

  * O problema que foi detectado;
  * As características do conjunto de eventos que encontramos exibimos o comportamento do problema.
* A tabela compara o conjunto de desempenho insatisfatório com o comportamento médio de todos os outros eventos.

Clique nos links para abrir o Gerenciador de métricas e Pesquisar relatórios relevantes, filtrados na hora e nas propriedades do conjunto de desempenho lento.

Modifique o intervalo de tempo e os filtros para explorar a telemetria.

## <a name="next-steps"></a>Passos Seguintes
Essas ferramentas de diagnóstico ajudam você a inspecionar a telemetria do seu aplicativo:

* [Gerador de perfis](profiler.md) 
* [Depurador de instantâneos](../../azure-monitor/app/snapshot-debugger.md)
* [Análise](../../azure-monitor/log-query/get-started-portal.md)
* [Diagnóstico inteligente do Analytics](../../azure-monitor/app/analytics.md)

As detecções inteligentes são completamente automáticas. Mas talvez você queira configurar mais alguns alertas?

* [Alertas de métrica configurados manualmente](../../azure-monitor/app/alerts.md)
* [Testes da Web de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md)
