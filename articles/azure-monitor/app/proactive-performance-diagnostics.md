---
title: Smart Detection - anomalias de desempenho | Microsoft Docs
description: A Application Insights realiza uma análise inteligente da telemetria da sua aplicação e alerta para potenciais problemas. Esta funcionalidade não necessita de configuração.
ms.topic: conceptual
ms.date: 05/04/2017
ms.reviewer: antonfr
ms.openlocfilehash: a055a323001e0291d3cb5c1716e640b3c8b21dbf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100573736"
---
# <a name="smart-detection---performance-anomalies"></a>Deteção Inteligente - Anomalias de desempenho

[O Application Insights](./app-insights-overview.md) analisa automaticamente o desempenho da sua aplicação web e pode alertá-lo sobre potenciais problemas. Pode estar a ler isto porque recebeu uma das nossas notificações inteligentes de deteção.

Esta funcionalidade não requer nenhuma configuração especial, além de configurar a sua aplicação para Insights de Aplicação para o seu [idioma suportado.](./platforms.md) Está ativo quando a sua aplicação gera telemetria suficiente.

## <a name="when-would-i-get-a-smart-detection-notification"></a>Quando é que eu receberia uma notificação de deteção inteligente?

A Application Insights detetou que o desempenho da sua aplicação se degradou de uma destas formas:

* **Degradação do tempo de resposta** - A sua aplicação começou a responder aos pedidos mais lentamente do que costumava. A mudança pode ter sido rápida, por exemplo, porque houve uma regressão na sua última implantação. Ou pode ter sido gradual, talvez causado por uma fuga de memória. 
* **Degradação da duração da dependência** - A sua aplicação faz chamadas para uma API, base de dados ou outra dependência do REST. A dependência está a responder mais lentamente do que antes.
* **Padrão de desempenho lento** - A sua aplicação parece ter um problema de desempenho que está a afetar apenas alguns pedidos. Por exemplo, as páginas estão a carregar mais lentamente num tipo de navegador do que noutros; ou os pedidos estão a ser servidos mais lentamente a partir de um servidor em particular. Atualmente, os nossos algoritmos analisam os tempos de carga da página, solicitam tempos de resposta e tempos de resposta à dependência.  

A Deteção Inteligente requer pelo menos 8 dias de telemetria a um volume viável, a fim de estabelecer uma linha de base de desempenho normal. Assim, depois de a sua aplicação estar em execução durante esse período, qualquer problema significativo resultará numa notificação.


## <a name="does-my-app-definitely-have-a-problem"></a>A minha aplicação tem algum problema?

Não, uma notificação não significa que a sua aplicação definitivamente tem um problema. É simplesmente uma sugestão sobre algo que pode querer examinar mais detalhadamente.

## <a name="how-do-i-fix-it"></a>Como posso corrigi-lo?

As notificações incluem informações de diagnóstico. Eis um exemplo:


![Aqui está um exemplo de deteção de tempo de degradação do tempo de resposta do servidor](media/proactive-performance-diagnostics/server_response_time_degradation.png)

1. **Triagem.** A notificação mostra quantos utilizadores ou quantas operações são afetadas. Isto pode ajudá-lo a atribuir uma prioridade ao problema.
2. **Âmbito**. O problema está a afetar todo o tráfego, ou apenas algumas páginas? É restrito a navegadores ou locais específicos? Estas informações podem ser obtidas a partir da notificação.
3. **Diagnosticar**. Muitas vezes, a informação de diagnóstico na notificação sugere a natureza do problema. Por exemplo, se o tempo de resposta abrandar quando a taxa de pedido é elevada, isso sugere que o seu servidor ou dependências estão sobrecarregados. 

    Caso contrário, abra a lâmina de desempenho em Insights de Aplicação. Lá, encontrará dados [do Profiler.](profiler.md) Se forem lançadas exceções, também pode experimentar o [depurar instantâneo](./snapshot-debugger.md).



## <a name="configure-email-notifications"></a>Configure notificações de e-mail

As notificações de Deteção Inteligente são ativadas por padrão e enviadas para aqueles que têm o [Monitore de Monitorização](../../role-based-access-control/built-in-roles.md#monitoring-reader) e [o Acompanhamento do Contribuinte](../../role-based-access-control/built-in-roles.md#monitoring-contributor) acesso à subscrição em que reside o recurso Application Insights. Para alterar isto, clique em **Configurar** na notificação de e-mail ou abra definições de Deteção Inteligente em Insights de Aplicação. 
  
  ![Definições de deteção inteligente](media/proactive-performance-diagnostics/smart_detection_configuration.png)
  
  * Pode utilizar o link **de cancelar a subscrição** no e-mail de Deteção Inteligente para parar de receber as notificações de e-mail.

Os e-mails sobre anomalias de desempenho de Smart Detections estão limitados a um e-mail por dia por recurso De Insights de Aplicação. O e-mail só será enviado se houver pelo menos um novo problema que foi detetado nesse dia. Não vai receber repetições de nenhuma mensagem. 

## <a name="faq"></a>FAQ

* *Então, o pessoal da Microsoft olha para os meus dados?*
  * N.º O serviço é totalmente automático. Só recebe as notificações. Os seus dados são [privados.](./data-retention-privacy.md)
* *Analisa todos os dados recolhidos pela Application Insights?*
  * De momento, não. Atualmente, analisamos o tempo de resposta do pedido, o tempo de resposta da dependência e o tempo de carga da página. A análise de métricas adicionais está no nosso atraso olhando para a frente.

* Para que tipo de aplicação funciona?
  * Estas degradações são detetadas em qualquer aplicação que gere a telemetria apropriada. Se instalou o Application Insights na sua aplicação web, então os pedidos e dependências são automaticamente rastreados. Mas em serviços de backend ou outras aplicações, se inseriu chamadas para [TrackRequest()](./api-custom-events-metrics.md#trackrequest) ou [TrackDependency,](./api-custom-events-metrics.md#trackdependency)então a Smart Detection funcionará da mesma forma.

* *Posso criar as minhas próprias regras de deteção de anomalias ou personalizar as regras existentes?*

  * Ainda não, mas pode:
    * [Configurar alertas](../alerts/alerts-log.md) que lhe digam quando uma métrica cruza um limiar.
    * [Exporte telemetria](./export-telemetry.md) para uma [base de dados](./code-sample-export-sql-stream-analytics.md) ou [para Power BI,](./export-power-bi.md)onde você pode analisá-la por si mesmo.
* *Com que frequência é feita a análise?*

  * Fazemos a análise diariamente sobre a telemetria do dia anterior (dia inteiro no fuso horário utc).
* *Isto substitui [os alertas métricos?](../alerts/alerts-log.md)*
  * N.º  Não nos comprometemos a detetar todos os comportamentos que possa supor anormais.


* *Se eu não fizer nada em resposta a uma notificação, vou receber um lembrete?*
  * Não, só recebes uma mensagem sobre cada assunto uma vez. Se o problema persistir, será atualizado na lâmina de alimentação smart Detection.
* *Perdi o e-mail. Onde posso encontrar as notificações no portal?*
  * Na visão geral do Application Insights da sua aplicação, clique no azulejo **de Deteção Inteligente.** Lá poderá encontrar todas as notificações até 90 dias de volta.

## <a name="how-can-i-improve-performance"></a>Como posso melhorar o desempenho?
Respostas lentas e falhadas são uma das maiores frustrações para os utilizadores do site, como sabe pela sua própria experiência. Então, é importante abordar as questões.

### <a name="triage"></a>Triagem
Primeiro, importa? Se uma página é sempre lenta a carregar, mas apenas 1% dos utilizadores do seu site alguma vez terão de olhar para ela, talvez tenha coisas mais importantes para pensar. Por outro lado, se apenas 1% dos utilizadores a abrirem, mas lança sempre exceções, isso pode valer a pena investigar.

Utilize a declaração de impacto (utilizadores afetados ou % do tráfego) como guia geral, mas esteja ciente de que não é toda a história. Reúna outras provas para confirmar.

Considere os parâmetros da questão. Se for dependente da geografia, crie testes de [disponibilidade,](./monitor-web-app-availability.md) incluindo aquela região: pode simplesmente haver problemas de rede nessa área.

### <a name="diagnose-slow-page-loads"></a>Diagnosticar cargas de página lenta
Onde está o problema? O servidor é lento para responder, a página é muito longa, ou o navegador tem que fazer muito trabalho para exibi-lo?

Abra a lâmina métrica dos Browsers. A exibição segmentada do tempo de carregamento da página do navegador mostra para onde vai o tempo. 

* Se **o tempo de pedido de envio** for elevado, ou o servidor está a responder lentamente, ou o pedido é um post com muitos dados. Veja as [métricas](./performance-counters.md) de desempenho para investigar os tempos de resposta.
* Configurar [o rastreio de dependência](./asp-net-dependencies.md) para ver se a lentidão se deve a serviços externos ou à sua base de dados.
* Se **a Resposta de Receção** for predominante, a sua página e as suas partes dependentes - JavaScript, CSS, imagens e assim por diante (mas não dados carregados assíncronamente) são longos. Configurar um [teste de disponibilidade](./monitor-web-app-availability.md), e certifique-se de definir a opção de carregar peças dependentes. Quando obter alguns resultados, abra o detalhe de um resultado e expanda-o para ver os tempos de carga de diferentes ficheiros.
* O **tempo elevado de processamento do cliente** sugere que os scripts estão a funcionar lentamente. Se a razão não for óbvia, considere adicionar algum código de tempo e envie os tempos em rastreios Chamadasmétricas.

### <a name="improve-slow-pages"></a>Melhorar páginas lentas
Há uma web cheia de conselhos sobre melhorar as respostas do servidor e os tempos de carregamento da página, por isso não vamos tentar repetir tudo aqui. Aqui estão algumas dicas que provavelmente já sabe, só para fazê-lo pensar:

* Carregamento lento por causa de grandes ficheiros: Carregue os scripts e outras partes assíncroneamente. Use a agregação de scripts. Parta a página principal em widgets que carregam os seus dados separadamente. Não envie HTML simples e velho para tabelas longas: use um script para solicitar os dados como JSON ou outro formato compacto e, em seguida, encha a tabela no lugar. Há grandes estruturas para ajudar com tudo isto. (Também envolvem grandes guiões, claro.)
* Dependências lentas do servidor: Considere as localizações geográficas dos seus componentes. Por exemplo, se estiver a utilizar o Azure, certifique-se de que o servidor web e a base de dados estão na mesma região. As consultas recuperam mais informação do que precisam? O caching ou o lote ajuda?
* Problemas de capacidade: Veja as métricas do servidor dos tempos de resposta e as contagens de pedido. Se os tempos de resposta atingirem desproporcionalmente os picos nas contagens de pedidos, é provável que os seus servidores sejam esticados.


## <a name="server-response-time-degradation"></a>Degradação do tempo de resposta do servidor

A notificação de degradação do tempo de resposta diz-lhe:

* O tempo de resposta comparado com o tempo normal de resposta para esta operação.
* Quantos utilizadores são afetados.
* Tempo médio de resposta e tempo de resposta percentil 90 para esta operação no dia da deteção e 7 dias antes. 
* A contagem desta operação solicita no dia da deteção e 7 dias antes.
* Correlação entre a degradação desta operação e as degradações nas dependências relacionadas. 
* Links para ajudá-lo a diagnosticar o problema.
  * Traços de perfil para ajudá-lo a ver onde o tempo de funcionamento é gasto (o link está disponível se foram recolhidos exemplos de rastreio de perfis para esta operação durante o período de deteção). 
  * Relatórios de desempenho no Metric Explorer, onde pode cortar e picar intervalos/filtros para esta operação.
  * Procure esta chamada para ver propriedades de chamadas específicas.
  * Relatórios de falhas - Se a contagem > 1 significa que houve falhas nesta operação que poderiam ter contribuído para a degradação do desempenho.

## <a name="dependency-duration-degradation"></a>Degradação da Duração da Dependência

As aplicações modernas adotam cada vez mais uma abordagem de conceção de micro serviços, o que, em muitos casos, conduz a uma forte fiabilidade nos serviços externos. Por exemplo, se a sua aplicação depender de alguma plataforma de dados ou mesmo se construir o seu próprio serviço de bot, provavelmente irá transmitir algum fornecedor de serviços cognitivos para permitir que os seus bots interajam de formas mais humanas e algum serviço de loja de dados para o bot para obter as respostas.  

Notificação de degradação da dependência de exemplo:

![Aqui está um exemplo de deteção de degradação da duração da dependência](media/proactive-performance-diagnostics/dependency_duration_degradation.png)

Note que lhe diz:

* A duração em comparação com o tempo normal de resposta para esta operação
* Quantos utilizadores são afetados
* Duração média e 90ª duração percentil para esta dependência no dia da deteção e 7 dias antes
* Número de chamadas de dependência no dia da deteção e 7 dias antes
* Links para ajudá-lo a diagnosticar o problema
  * Relatórios de desempenho no Metric Explorer para esta dependência
  * Procure por esta dependência chamadas para ver propriedades de chamadas
  * Relatórios de avaria - Se a contagem > 1 significa que houve chamadas de dependência falhadas durante o período de deteção que poderiam ter contribuído para a degradação da duração. 
  * Open Analytics com consultas que calculam esta duração de dependência e contagem  

## <a name="smart-detection-of-slow-performing-patterns"></a>Deteção inteligente de padrões de desempenho lento 

O Application Insights encontra problemas de desempenho que podem afetar apenas uma parte dos seus utilizadores, ou apenas afetar os utilizadores em alguns casos. Por exemplo, a notificação sobre a carga de páginas é mais lenta num tipo de navegador do que em outros tipos de navegadores, ou se os pedidos são servidos mais lentamente a partir de um determinado servidor. Também pode descobrir problemas associados a combinações de propriedades, como cargas de página lenta numa área geográfica para clientes que usam sistema operativo específico.  

Anomalias como estas são muito difíceis de detetar apenas inspecionando os dados, mas são mais comuns do que se possa pensar. Muitas vezes só aparecem quando os seus clientes se queixam. Nessa altura, já é tarde demais: os utilizadores afetados já estão a mudar para os seus concorrentes!

Atualmente, os nossos algoritmos analisam os tempos de carga da página, solicitam tempos de resposta no servidor e tempos de resposta à dependência.  

Não tens de estabelecer limites ou regras de configuração. Machine learning e algoritmos de mineração de dados são usados para detetar padrões anormais.

![A partir do alerta de e-mail, clique no link para abrir o relatório de diagnóstico em Azure](./media/proactive-performance-diagnostics/03.png)

* **Quando** mostra a hora em que o problema foi detetado.
* **O que** descreve:

  * O problema que foi detetado;
  * As características do conjunto de eventos que encontrámos mostraram o comportamento do problema.
* A tabela compara o conjunto de baixo desempenho com o comportamento médio de todos os outros eventos.

Clique nos links para abrir o Metric Explorer e pesquisar relatórios relevantes, filtrados no tempo e propriedades do conjunto de desempenho lento.

Modifique o intervalo de tempo e os filtros para explorar a telemetria.

## <a name="next-steps"></a>Passos seguintes
Estas ferramentas de diagnóstico ajudam-no a inspecionar a telemetria a partir da sua aplicação:

* [Gerador de perfis](profiler.md) 
* [depurar instantâneo](./snapshot-debugger.md)
* [Análise](../logs/log-analytics-tutorial.md)
* [Analytics diagnósticos inteligentes](../logs/log-query-overview.md)

As deteções inteligentes são completamente automáticas. Mas talvez queira fazer mais alguns alertas?

* [Alertas métricos configurados manualmente](../alerts/alerts-log.md)
* [Testes Web de disponibilidade](./monitor-web-app-availability.md)