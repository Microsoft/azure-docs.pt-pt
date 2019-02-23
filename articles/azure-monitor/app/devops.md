---
title: Desempenho do aplicativo da Web de monitorização - Azure Application Insights | Documentos da Microsoft
description: Como o Application Insights se encaixa no ciclo de devOps
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 479522a9-ff5c-471e-a405-b8fa221aedb3
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/21/2018
ms.author: mbullwin
ms.openlocfilehash: 24b0bc01b5cb4f1d2696a7c9526d586c9b42d0fc
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56674587"
---
# <a name="deep-diagnostics-for-web-apps-and-services-with-application-insights"></a>Diagnósticos avançados de aplicações Web e serviços com o Application Insights
## <a name="why-do-i-need-application-insights"></a>Por que razão necessito de Application Insights?
Application Insights monitoriza a sua aplicação web em execução. Apresenta informações sobre problemas de desempenho e falhas e ajuda a analisar como os clientes utilizam a sua aplicação. Ele funciona para aplicações em execução em muitas plataformas (ASP.NET, Java EE, node. js,...) e está alojado na Cloud ou no local. 

![Aspectos da complexidade do fornecimento de aplicações web](./media/devops/010.png)

É essencial para monitorizar uma aplicação moderna enquanto está em execução. Mais importante, que pretende detetar falhas antes de fazer a maior parte dos seus clientes. Também pretende detetar e corrigir problemas de desempenho que, embora não catastróficos, talvez retardam as ou causar algum inconveniência para seus usuários. E quando o sistema está a efetuar para sua satisfação, deseja saber o que os usuários estão fazendo com o mesmo: Elas estão usando o recurso mais recente? São eles que os sucedem com ele?

Aplicações web modernas são desenvolvidas num ciclo de entrega contínua: uma nova função ou uma melhoria; da versão Observe como ele funciona para os utilizadores; Planear o próximo incremento de desenvolvimento com base nesse conhecimento. Uma parte fundamental do ciclo desta é a fase de observação. O Application Insights fornece as ferramentas para monitorizar uma aplicação web para o desempenho e utilização.

O aspecto mais importante desse processo é o diagnóstico e de diagnóstico. Se o aplicativo falhar, negócio está sendo perdido. A função principal de uma estrutura de monitoramento é, portanto, para detetar falhas de forma fiável, notificá-lo imediatamente e apresentar-lhe as informações necessárias para diagnosticar o problema. Isso é exatamente o que faz o Application Insights.

### <a name="where-do-bugs-come-from"></a>Em que vêm bugs?
Falhas nos sistemas da web normalmente resultam de problemas de configuração ou ruins interações entre os vários componentes. A primeira tarefa ao lidar com um incidente de site em direto é, portanto identificar o locus do problema: o componente ou uma relação é a causa?

Alguns de nós, aqueles com cabelos cinzento, podem não se esqueça de uma época mais simples em que um programa de computador foi executado num computador. Os desenvolvedores seriam testá-lo cuidadosamente antes do envio-lo. e ter fornecido, seria raramente Consulte ou pensar sobre isso novamente. Os utilizadores teria de autógrafos com os bugs residuais por muitos anos. 

As coisas são agora tão muito diferentes. A aplicação tem uma grande quantidade de diferentes dispositivos para serem executadas em e pode ser difícil garantir um comportamento exatamente igual em cada um. Alojar aplicações na cloud significa bugs podem ser corrigidos rapidamente, mas também significa competição contínua e as expectativas dos novos recursos em intervalos frequentes. 

Nestas condições, a única forma de manter um controle sólido na contagem de bug é testes de unidade automatizados. Seria impossível manualmente testar tudo no cada entrega novamente. Teste de unidade agora é parte do processo de compilação local comum. Ferramentas, como a Xamarin Test Cloud ajudam fornecendo automatizado de interface do usuário do teste em várias versões do navegador. Esses testes regimes permitem que Esperamos que a taxa de erros encontradas dentro de uma aplicação pode ser mantida num mínimo.

Os aplicativos web típicos têm vários componentes em direto. Além do cliente (numa aplicação de browser ou dispositivo) e o servidor web, é provável que seja o processamento back-end substancial. Talvez o back-end é um pipeline de componentes, ou uma coleção menos rígida de partes de colaboração. Muitos deles não estar em seu controle — são serviços externos qual dependa.

Em configurações como essas, pode ser difícil e uneconomical testar ou previr, cada modo de falha possíveis, senão o próprio sistema ao vivo. 

### <a name="questions-"></a>Perguntas...
Algumas perguntas Pedimos quando estamos a desenvolver um sistema web:

* Está a minha aplicação falhar? 
* O que aconteceu exatamente? -No caso de falha um pedido, quero saber como ficou lá. Precisamos de um rastreamento de eventos...
* É rápido o bastante a minha aplicação? Quanto tempo demora a responder a pedidos típicos?
* O servidor pode lidar com a carga? Quando a taxa de pedidos aumenta, o tempo de resposta manter constante?
* Grau de resposta são minhas dependências - as APIs REST, bases de dados e outros componentes que meu aplicativo chama. Em particular, se o sistema estiver lento, ele é meu componente ou eu ganho repostas lentas de outra pessoa?
* É a minha aplicação ou reduzir verticalmente? Pode-poderiam ser visto em todo o mundo? Conte-me se parar...
* O que é a causa? Foi a falha no meu componente ou uma dependência? É um problema de comunicação?
* Quantos utilizadores são afetados? Se tiver mais do que um problema para resolver o problema, o que é o mais importante?

## <a name="what-is-application-insights"></a>O que é o Application Insights?
![Fluxo de trabalho básico do Application Insights](./media/devops/020.png)

1. Application Insights instrui a sua aplicação e envia a telemetria sobre ela, enquanto a aplicação está em execução. Ou pode criar o SDK do Application Insights na aplicação ou pode aplicar instrumentação no tempo de execução. O primeiro método é mais flexível, como pode adicionar sua própria telemetria para os módulos regulares.
2. A telemetria é enviada para o portal do Application Insights, onde são armazenado e processado. (Embora o Application Insights está alojado no Microsoft Azure, pode monitorizar todas as aplicações web - aplicações não apenas do Azure).
3. A telemetria é apresentada em forma de gráficos e tabelas de eventos.

Existem dois tipos principais de telemetria: as instâncias não processadas e agregadas. 

* Dados de instância incluem, por exemplo, um relatório de um pedido que foi recebido pela sua aplicação web. Pode encontrar para e inspecionar os detalhes de um pedido com a ferramenta de pesquisa no portal do Application Insights. A instância inclui dados, tais como a sua aplicação de quanto tempo demorou para responder à solicitação, bem como o URL pedido, fazer uma aproximação local de cliente e outros dados.
* Dados agregados incluem contagens de eventos por hora de unidade, para que pode comparar a taxa de pedidos com os tempos de resposta. Ele também inclui as médias de métricas, como tempos de resposta do pedido.

As principais categorias de dados são:

* Pedidos para a sua aplicação (normalmente, os pedidos HTTP), com os dados no URL, tempo de resposta e êxito ou falha.
* Dependências - chamadas REST e o SQL, graças à sua aplicação, também com o URI, tempos de resposta e o sucesso
* Exceções, incluindo os rastreamentos de pilha.
* Dados vista de página, que vêm de browsers dos utilizadores.
* As métricas, como contadores de desempenho, bem como as métricas escritas por si. 
* Eventos personalizados que pode utilizar para controlar eventos comerciais
* Rastreios de registo utilizados para depuração.

## <a name="case-study-real-madrid-fc"></a>Estudo de caso: O real Madrid F.C.
O serviço web do [clube de futebol do Real Madrid](https://www.realmadrid.com/) serve cerca de 450 milhões de fãs em todo o mundo. Os fãs acessá-lo através de browsers e aplicações móveis do clube. Os fãs não é possível apenas permissões de livro, mas também acessar clipes de informações e vídeo nos resultados, jogadores e jogos futuros. Pode pesquisar com filtros, como números das metas classificada. Também existem links para mídia social. A experiência do usuário é altamente personalizada e foi concebida como uma comunicação bidirecional para interagir com os fãs.

A solução [é um sistema de serviços e aplicações no Microsoft Azure](https://www.microsoft.com/inculture/sports/real-madrid/). Escalabilidade é um requisito-chave: tráfego é a variável e pode chegar a volumes muito elevados durante e em torno de correspondências.

Para o Real Madrid, é vital para monitorizar o desempenho do sistema. O Azure Application Insights oferece uma vista abrangente do sistema, garantindo um fiável e de alto nível de serviço. 

Clube também obtém a compreensão das suas fãs: onde estão (são apenas 3% em Espanha), que interesse têm no jogadores, os resultados históricos e jogos futuros e como eles respondem de acordo com os resultados.

A maioria destes dados de telemetria é recolhida automaticamente sem nenhum código adicionado, o que simplificou a solução e reduziu a complexidade operacional.  Para o Real Madrid, Application Insights lida com pontos de 3.8 bilhões de telemetria de cada mês.

O real Madrid utiliza o módulo de Power BI para ver a sua telemetria.

![Modo de exibição do Power BI de telemetria do Application Insights](./media/devops/080.png)

## <a name="smart-detection"></a>Deteção inteligente
[Diagnósticos proativos](../../azure-monitor/app/proactive-diagnostics.md) é uma funcionalidade recente. Sem nenhuma configuração especial por si, o Application Insights automaticamente Deteta e alerta-o sobre aumentos invulgares na taxas de falhas na sua aplicação. É inteligente o suficiente para ignorar um plano de fundo de falhas ocasionais e também aumenta é simplesmente proporcional para um aumento nos pedidos. Por exemplo, se ocorrer uma falha dos serviços que depende, ou se criar o novo que acabou de implementar não está a funcionar tão bem, em seguida, terá conhecimento assim que examinar o seu e-mail. (E há webhooks para que pode disparar outras aplicações.)

Outro aspecto esse recurso executa uma análise detalhada diária da sua telemetria, procurando invulgares padrões de desempenho que são difíceis de detetar. Por exemplo, encontrará um desempenho lento associado uma área geográfica específica ou com uma versão de browser específico.

Em ambos os casos, o alerta não só indica os sintomas é detetado, mas também fornece dados de que precisa para ajudar a diagnosticar o problema, como relatórios de exceções relevantes.

![Enviar um e-mail de diagnósticos proativos](./media/devops/030.png)

Cliente Samtec disse: "Durante uma funcionalidade recente transferência, encontrámos uma base de dados em dimensionamento, que estava atingindo seus limites de recursos e esgotando. Conjunto de alertas de deteção proativa com a literalmente vamos foram Triar o problema, muito quase em tempo real conforme anunciado. Este alerta juntamente com os alertas de plataforma do Azure ajudou-na quase instantaneamente corrigir o problema. Total de tempo de inatividade < 10 minutos."

## <a name="live-metrics-stream"></a>Live Metrics Stream
Implementar a compilação mais recente pode ser uma experiência ansioso. Se existirem quaisquer problemas, que pretende saber sobre eles de imediato, para que pode de volta se necessário. Stream de métricas em direto fornece as principais métricas com uma latência de cerca de um segundo.

![Métricas em direto](./media/devops/0040.png)

E permite-lhe inspecionar imediatamente uma amostra de falhas ou exceções.

![Eventos de falha em direto](./media/devops/002-live-stream-failures.png)

## <a name="application-map"></a>Mapeamento de Aplicações
Mapa da aplicação Deteta automaticamente a topologia do seu aplicativo, definindo as informações de desempenho com base no mesmo, para que possa identificar facilmente estrangulamentos de desempenho e fluxos problemáticos em todo o ambiente distribuído. Permite-lhe detetar dependências de aplicações nos serviços do Azure. Pode fazer a triagem do problema por Noções básicas sobre se é relacionadas ao código ou dependência relacionadas e a partir de um único local teste para diagnóstico relacionado descobrir. Por exemplo, seu aplicativo pode estar a falhar devido a degradação do desempenho no escalão SQL. Com o mapa da aplicação, pode vê-lo imediatamente e explorar o Assistente de índice de SQL ou a experiência de informações de consulta.

![Mapeamento de Aplicações](./media/devops/0050.png)

## <a name="application-insights-analytics"></a>O Application Insights Analytics
Com o [Analytics](../../azure-monitor/app/analytics.md), pode escrever consultas arbitrárias numa poderosa linguagem de tipo SQL.  Diagnosticar em toda a pilha de todo aplicativo torna-se fácil à medida que conecte-se várias perspectivas e pode fazer as perguntas certas para correlacionar o desempenho do serviço com métricas de negócio e a experiência do cliente. 

Pode consultar a instância de telemetria e métricos dados não processados armazenados no portal. O idioma inclui o filtro, join, agregação e outras operações. Pode calcular campos e efetuar análises estatísticas. Existem visualizações de tabela e gráficas.

![Gráfico de consulta e os resultados da análise](./media/devops/0025.png)

Por exemplo, é fácil:

* Segmente dados de desempenho de pedido de seu aplicativo por camadas de cliente para compreender a sua experiência.
* Procure códigos de erro específicos ou nomes de eventos personalizados durante as investigações de site em direto.
* Desagregue a utilização da aplicação de clientes específicos para compreender como os recursos são obtidos e adotados.
* Controle as sessões e tempos de resposta para utilizadores específicos permitir que as equipes de suporte e operações fornecer suporte ao cliente instantânea.
* Determine as funcionalidades de aplicação utilizada com frequência, responder às perguntas da priorização de funcionalidade.

Cliente DNN disse: "Application Insights nos proporcionou com a parte em falta da equação por ser capaz de combinação, ordenar, consulta e filtrar dados conforme necessário. Permitir que a nossa equipa usar seus próprios ingenuidade e a experiência para encontrar dados com uma linguagem de consulta poderosa permitiu-nos encontrar informações e resolver problemas sequer Sabíamos que tínhamos. Muito interessantes respostas provenientes de perguntas a partir *"eu admirar se...".*"

## <a name="development-tools-integration"></a>Integração de ferramentas de desenvolvimento
### <a name="configuring-application-insights"></a>Configuração do Application Insights
O Visual Studio e o Eclipse possuem ferramentas para configurar os pacotes SDK corretos para o projeto que estiver a desenvolver. Existe um comando de menu para adicionar o Application Insights.

Se usar uma arquitetura de registo de rastreio, como o Log4N, NLog ou Trace, em seguida, tem a opção para enviar os registos para o Application Insights, juntamente com a telemetria, para que pode correlacionar facilmente os rastreios com pedidos, dependências chamadas e as exceções.

### <a name="search-telemetry-in-visual-studio"></a>Pesquisar telemetria no Visual Studio
Desenvolvimento e depuração de um recurso, pode ver e procurar a telemetria diretamente no Visual Studio, utilizando os mesmo recursos de pesquisa como no web portal.

E quando o Application Insights faz uma exceção, pode ver o ponto de dados no Visual Studio e Pular diretamente para o código relevante.

![Pesquisa do Visual Studio](./media/devops/060.png)

Durante a depuração, tem a opção para manter a telemetria na sua máquina de desenvolvimento, visualizá-lo no Visual Studio, mas sem enviar para o portal. Esta opção local evita a mistura de depuração com telemetria de produção.

### <a name="work-items"></a>Itens de trabalho
Quando é gerado um alerta, o Application Insights pode criar automaticamente um item de trabalho no seu sistema de controlo de trabalho.

## <a name="but-what-about"></a>Mas o que dizer sobre...?
* [Privacidade e o armazenamento](../../azure-monitor/app/data-retention-privacy.md) -encontram-se a telemetria na proteger os servidores do Azure.
* O impacto no desempenho - é muito baixo. Telemetria é em lotes.
* [Preços](../../azure-monitor/app/pricing.md) - pode começar a utilizar gratuitamente, e que continua enquanto está na volume baixo.


## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Passos Seguintes
Introdução ao Application Insights é fácil. As principais opções são:

* [Servidores IIS](../../azure-monitor/app/monitor-performance-live-website-now.md)e também para [serviço de aplicações do Azure](../../azure-monitor/app/app-insights-overview.md).
* Instrumente o seu projeto durante o desenvolvimento. Pode fazê-lo [ASP.NET](../../azure-monitor/app/asp-net.md) ou [Java](../../azure-monitor/app/java-get-started.md) aplicações, bem como [node. js](../../azure-monitor/app/nodejs.md) e um host de [outros tipos de](../../azure-monitor/app/platforms.md). 
* Instrumentar [qualquer página da web](../../azure-monitor/app/javascript.md) adicionando um trecho de código curtos.

