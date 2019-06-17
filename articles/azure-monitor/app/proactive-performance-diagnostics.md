---
title: Deteção inteligente - desempenho anomalias | Documentos da Microsoft
description: Application Insights faz uma análise inteligente da telemetria da aplicação e avisa sobre possíveis problemas. Esta funcionalidade não precisa nenhuma configuração.
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
ms.openlocfilehash: b1a3b04427839736359c88f8ad6a8db5eedf8488
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61294088"
---
# <a name="smart-detection---performance-anomalies"></a>Deteção inteligente – anomalias de desempenho

[O Application Insights](../../azure-monitor/app/app-insights-overview.md) automaticamente analisa o desempenho da sua aplicação web e pode avisá-lo sobre potenciais problemas. Pode estar lendo este artigo porque beneficiou de uma das nossas notificações de deteção inteligente.

Esta funcionalidade não requer nenhuma configuração especial, além de configurar a sua aplicação para o Application Insights (no [ASP.NET](../../azure-monitor/app/asp-net.md), [Java](../../azure-monitor/app/java-get-started.md), ou [node. js](../../azure-monitor/app/nodejs.md)e, em [página da web código](../../azure-monitor/app/javascript.md)). Ele está ativo quando a sua aplicação gerar telemetria suficiente.

## <a name="when-would-i-get-a-smart-detection-notification"></a>Quando é que eu teria uma notificação de deteção inteligente?

O Application Insights detetou que o desempenho da sua aplicação tem degradado em uma das seguintes formas:

* **Degradação do tempo de resposta** -a aplicação foi iniciada a responder a pedidos mais lentamente do que costumava. A alteração pode ter sido rápida, por exemplo porque não havia uma regressão na sua implementação mais recente. Ou talvez ele seja gradual, talvez causado por um vazamento de memória. 
* **Degradação de duração de dependência** -a aplicação faz chamadas para uma API REST, base de dados ou outras dependências. A dependência está a responder mais lentamente do que costumava.
* **Padrão de desempenho lento** -a aplicação é apresentada a ter problemas de desempenho que está a afetar apenas alguns pedidos. Por exemplo, páginas estão a carregar mais lentamente num tipo de navegador que outros; ou pedidos estão sendo atendidos mais lenta de um servidor específico. Atualmente, nossos algoritmos examinar os tempos de carregamento de página, tempos de resposta do pedido e tempos de resposta de dependência.  

Deteção inteligente requer, pelo menos, 8 dias de telemetria num volume viável para estabelecer uma linha de base de desempenho normal. Então, depois da aplicação está em execução nesse período, qualquer problema significativo resultará numa notificação.


## <a name="does-my-app-definitely-have-a-problem"></a>O meu aplicativo tem definitivamente um problema?

Não, uma notificação não significa que a aplicação tem definitivamente um problema. É simplesmente uma sugestão sobre algo que pode querer examinar mais detalhadamente.

## <a name="how-do-i-fix-it"></a>Como posso corrigi-lo?

As notificações incluem informações de diagnóstico. Segue-se um exemplo:


![Eis um exemplo de deteção de degradação de tempo de resposta do servidor](media/proactive-performance-diagnostics/server_response_time_degradation.png)

1. **Triagem**. A notificação mostra o número de utilizadores ou quantas operações são afetadas. Isto pode ajudar a atribuir uma prioridade para o problema.
2. **Âmbito**. O problema é afetar todo o tráfego, ou apenas algumas páginas? Está restringida para navegadores específicos ou localizações? Estas informações podem ser obtidas a partir da notificação.
3. **Diagnosticar**. Muitas vezes, as informações de diagnóstico na notificação sugere a natureza do problema. Por exemplo, se o tempo de resposta mais lento quando a taxa de pedidos é alta, isso indica que ao servidor ou dependências estão sobrecarregadas. 

    Caso contrário, abra o painel de desempenho no Application Insights. Lá, encontrará [Profiler](profiler.md) dados. Se forem geradas exceções, também pode tentar o [depurador de instantâneos](../../azure-monitor/app/snapshot-debugger.md).



## <a name="configure-email-notifications"></a>Configurar notificações por E-Mail

Notificações de deteção inteligentes são ativadas por predefinição e enviadas para os utilizadores que têm [proprietários, contribuidores e leitores de acesso para o recurso do Application Insights](../../azure-monitor/app/resources-roles-access-control.md). Para alterar isso, clique em **configurar** na notificação por e-mail ou abra definições de deteção inteligente no Application Insights. 
  
  ![Definições de deteção inteligente](media/proactive-performance-diagnostics/smart_detection_configuration.png)
  
  * Pode utilizar o **anular a subscrição** ligação no e-mail de deteção inteligente para parar de receber as notificações por e-mail.

E-mails sobre anomalias de desempenho de detecções inteligente estão limitados a um e-mail por dia por recurso do Application Insights. Será enviado o e-mail apenas se existir pelo menos um problema novo que foi detetado nesse dia. Não obtém repete de qualquer mensagem. 

## <a name="faq"></a>FAQ

* *Portanto, à equipe da Microsoft examinar meus dados?*
  * Não. O serviço é totalmente automático. Obter apenas as notificações. Os seus dados estão [privada](../../azure-monitor/app/data-retention-privacy.md).
* *Analisar todos os dados recolhidos pelo Application Insights?*
  * Não neste momento. Atualmente, vamos analisar o pedido de tempo de carregamento da tempo de resposta, de página e de tempo de resposta de dependência. Análise das métricas adicionais está no nosso registo de segurança olhando para o futuro.

* Que tipos de aplicativo funciona para?
  * Estes degradações são detetadas em qualquer aplicativo que gera a telemetria apropriada. Se instalou o Application Insights na sua aplicação web, em seguida, pedidos e as dependências são automaticamente controladas. Mas em serviços de back-end ou outras aplicações, se tiver inserido chamadas para [TrackRequest()](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) ou [TrackDependency](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency), deteção inteligente funcionará da mesma forma.

* *Pode criar regras de deteção de meu próprio anomalias ou personalizar as regras existentes?*

  * Ainda não, mas pode:
    * [Configure alertas](../../azure-monitor/app/alerts.md) que informá-lo quando uma métrica ultrapassa um limiar.
    * [Exportar telemetria](../../azure-monitor/app/export-telemetry.md) para uma [base de dados](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md) ou [para o Power BI](../../azure-monitor/app/export-power-bi.md ), onde pode analisá-lo por conta própria.
* *A frequência com que a análise é executada?*

  * Vamos executar a análise diária da telemetria do dia anterior (dia completo no fuso horário UTC).
* *Assim como o este replace [alertas de métricas](../../azure-monitor/app/alerts.md)?*
  * Não.  Não estamos empenhados detetar cada comportamento que pode considerar anormal.


* *Se não fizer nada em resposta a uma notificação, posso obter um lembrete?*
  * Não, receberá uma mensagem sobre cada problema apenas uma vez. Se o problema persistir será atualizado na deteção inteligente, feed painel.
* *Eu perdi o e-mail. Onde posso encontrar as notificações no portal?*
  * Na descrição de geral da Application Insights da sua aplicação, clique nas **deteção inteligente** mosaico. Aqui, poderá encontrar todas as notificações de segurança para efetuar cópias de 90 dias.

## <a name="how-can-i-improve-performance"></a>Como melhorar o desempenho?
As respostas lentas e falhadas são uma das maiores frustrações para utilizadores do web site, como sabe da sua própria experiência. Por isso, é importante resolver os problemas.

### <a name="triage"></a>Triagem
Em primeiro lugar, é importante? Se uma página é sempre lenta a carregar, mas apenas 1% de utilizadores do seu site nunca mais terá de vê-lo, talvez tenha coisas mais importantes a considerar. Por outro lado, se apenas 1% de utilizadores abri-lo, mas ele emite exceções sempre, que pode ser que vale a pena investigar.

Usar a instrução de impacto (os utilizadores afetados ou % do tráfego) como um guia Geral, mas lembre-se de que não é a história toda. Obter outras evidências para confirmar.

Considere os parâmetros do problema. Se for dependente de geografia, configure [testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) incluindo essa região: simplesmente que haja problemas de rede nessa área.

### <a name="diagnose-slow-page-loads"></a>Diagnosticar carregamentos lentos de página
Onde está o problema? É o servidor lento responder, é a página muito longas ou o browser tem de fazer muito trabalho para apresentá-la?

Abra o painel de métrico de navegadores. A exibição segmentada de onde será o tempo dos programas de tempo de carga para página browser. 

* Se **enviar o pedido de tempo** é alto, o servidor está a responder lentamente ou o pedido é uma mensagem com uma grande quantidade de dados. Examinar os [métricas de desempenho](../../azure-monitor/app/web-monitor-performance.md#metrics) para investigar os tempos de resposta.
* Configurar [rastreamento de dependências](../../azure-monitor/app/asp-net-dependencies.md) se a lentidão é devido a serviços externos ou a sua base de dados.
* Se **receber resposta** é predominante, sua página e suas partes dependentes - JavaScript, CSS, imagens e assim por diante (mas não assincronamente carregados dados) são longos. Configurar uma [teste de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md)e não se esqueça de definir a opção carregar componentes dependentes. Ao obter alguns resultados, abra os detalhes de um resultado e expanda-o para ver os tempos de carregamento de ficheiros diferentes.
* Alta **tempo de processamento de cliente** sugere scripts estão em execução lenta. Se o motivo pelo qual não é óbvio, considere adicionar um código de tempo e enviar as horas em trackMetric chamadas.

### <a name="improve-slow-pages"></a>Melhorar a páginas lentas
Há uma web completa de aviso em melhorar suas respostas do servidor e os tempos de carregamento de página, para que nós não tentaremos de repeti-lo tudo aqui. Aqui estão algumas dicas que provavelmente já sabe sobre, apenas para ajudá-lo a concepção de idéias:

* Carregamento lento devido à grande de ficheiros: Carregar os scripts e outras partes de forma assíncrona. Utilize o agrupamento de script. Quebra a página principal em widgets que carregar os seus dados em separado. Não enviar HTML simples de antigo para tabelas longo: utilizar um script para pedir os dados como JSON ou outro formato compacto, em seguida, preencher a tabela no local. Há excelentes arquiteturas para ajudar em tudo isso. (Eles também envolve scripts grandes, é claro.)
* Dependências de servidor lento: Considere as localizações geográficas de seus componentes. Por exemplo, se estiver a utilizar o Azure, certifique-se de que o servidor web e a base de dados estão na mesma região. Consultas obter mais informações do que eles precisam? Seria a colocação em cache ou criação de batches de ajuda?
* Problemas de capacidade: Observe as métricas de servidor dos tempos de resposta e contagens de pedido. Se os tempos de resposta desproporcional picos com picos de contagens de pedidos, é provável que os servidores são transferidos.


## <a name="server-response-time-degradation"></a>Degradação de tempo de resposta do servidor

A notificação de degradação do tempo de resposta diz a:

* O tempo de resposta em comparação comparado o tempo de resposta normal para esta operação.
* Quantos utilizadores são afetados.
* Tempo médio de resposta e 90th tempo de resposta de percentil para esta operação no dia de deteção e sete dias antes. 
* Contagem de pedidos esta operação o dia da deteção e sete dias antes.
* Correlação entre a degradação nesta operação e degradações nas dependências relacionadas. 
* Ligações para o ajudar a diagnosticar o problema.
  * Rastreios do Profiler para o ajudar a ver onde é gasto o tempo de operação (o link está disponível se exemplos de rastreio do Profiler foram recolhidos para esta operação durante o período de deteção a maiúsculas e minúsculas). 
  * Relatórios de desempenho no Explorador de métricas, onde pode segmentação e decomposição tempo/filtros de intervalo para esta operação.
  * Pesquisa para esta chamada ver as propriedades de chamada específica.
  * Falha de comunica - se a contagem de > 1, isso significa que ocorreram falhas nesta operação que poderá têm contribuído para degradação do desempenho.

## <a name="dependency-duration-degradation"></a>Degradação de duração de dependência

Aplicação moderna mais adotar a abordagem de design de microsserviços, que, em muitos casos, leva a confiabilidade pesada em serviços externos. Por exemplo, se seu aplicativo se baseia em alguma plataforma de dados ou até mesmo se criar o seu bot service, que provavelmente irá reencaminhar em algum provedor de serviços cognitivos para ativar seus bots interagir de forma mais humana e algum serviço de arquivo de dados para o bot puxar as respostas do m.  

Notificação de degradação de dependência de exemplo:

![Eis um exemplo de deteção de degradação de duração de dependência](media/proactive-performance-diagnostics/dependency_duration_degradation.png)

Tenha em atenção que ele diz a:

* A duração em comparação comparada o tempo de resposta normal para esta operação
* Quantos utilizadores são afetados
* Duração média e 90th duração de percentil para esta dependência no dia da deteção e sete dias antes da
* Número de dependência chama o dia da deteção e sete dias antes da
* Ligações para o ajudar a diagnosticar o problema
  * Relatórios de desempenho no Explorador de métricas para esta dependência
  * Procure esta dependência chama-se para ver as propriedades de chamadas
  * Relatórios de falhas - se a contagem > 1 isso significa que houve falha na dependência chama durante o período de deteção que pode ter contribuído para degradação de duração. 
  * Abrir o Analytics com consultas que calcular esta duração de dependência e a contagem  

## <a name="smart-detection-of-slow-performing-patterns"></a>Deteção inteligente de padrões de desempenho lentos 

O Application Insights encontra problemas de desempenho que podem afetar apenas uma parte dos seus utilizadores ou afetam apenas os utilizadores em alguns casos. Por exemplo, a notificação sobre o carregamento de páginas é mais lenta num tipo de browser que em outros tipos de navegadores, ou se os pedidos são servidos mais lenta de um servidor específico. Também pode detetar problemas relacionados com combinações de propriedades, como o carregamento da página lenta numa área geográfica para clientes que utilizam o sistema operativo específico.  

Anomalias de como esses são muito difíceis de detetar apenas ao inspecionar os dados, mas são mais comuns que imagina. Muitas vezes, eles só surgem quando reclamam os seus clientes. Nesse momento, é tarde demais: os utilizadores afetados já estão mudando para seus concorrentes!

Atualmente, nossos algoritmos examinar os tempos de carregamento de página, tempos de resposta do pedido no servidor e tempos de resposta de dependência.  

Não é preciso definir quaisquer limiares ou configurar as regras. Machine learning e algoritmos de mineração de dados são utilizados para detetar padrões anormais.

![De alerta por e-mail, clique na ligação para abrir o relatório de diagnóstico no Azure](./media/proactive-performance-diagnostics/03.png)

* **Quando** mostra o tempo foi detetado o problema.
* **O que** descreve:

  * O problema que foi detetado;
  * As características do conjunto de eventos que foram identificadas apresentado o comportamento do problema.
* A tabela compara o conjunto de desempenho inadequado de mensagens em fila com o comportamento de média de todos os outros eventos.

Clique nas hiperligações para abrir o Explorador de métricas e pesquisa no relatórios relevantes, filtrado com base no tempo e as propriedades do conjunto de desempenho lento.

Modificar o intervalo de tempo e os filtros para explorar a telemetria.

## <a name="next-steps"></a>Passos Seguintes
Estas ferramentas de diagnóstico ajudá-lo inspecionar a telemetria da sua aplicação:

* [Gerador de perfis](profiler.md) 
* [Depurador de instantâneos](../../azure-monitor/app/snapshot-debugger.md)
* [Análise](../../azure-monitor/log-query/get-started-portal.md)
* [Diagnóstico de análise inteligente](../../azure-monitor/app/analytics.md)

Deteções inteligentes são totalmente automáticas. Mas talvez deseje configurar alguns alertas mais?

* [Alertas de métricas configurados manualmente](../../azure-monitor/app/alerts.md)
* [Testes web de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md)
