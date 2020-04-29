---
title: Monitorização do desempenho da aplicação web - Azure Application Insights
description: Como os Insights de Aplicação se enquadram no ciclo devOps
ms.topic: conceptual
ms.date: 12/21/2018
ms.openlocfilehash: 24095aade80022d1e1ebb38357971512bfc873c0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77669697"
---
# <a name="deep-diagnostics-for-web-apps-and-services-with-application-insights"></a>Diagnósticos avançados de aplicações Web e serviços com o Application Insights
## <a name="why-do-i-need-application-insights"></a>Por que preciso de informações sobre aplicação?
Application Insights monitoriza a sua aplicação web de execução. Fala-lhe sobre falhas e problemas de desempenho, e ajuda-o a analisar a forma como os clientes usam a sua aplicação. Funciona para apps que funcionam em muitas plataformas (ASP.NET, Java EE, Node.js, ...) e está alojada na Cloud ou no local. 

![Aspetos da complexidade da entrega de aplicações web](./media/devops/010.png)

É essencial monitorizar uma aplicação moderna enquanto está em execução. Mais importante ainda, pretende detetar falhas antes que a maioria dos seus clientes o façam. Também quer descobrir e corrigir problemas de desempenho que, embora não sejam catastróficos, talvez atrasem as coisas ou causem algum inconveniente aos seus utilizadores. E quando o sistema está a funcionar a seu contento, quer saber o que os utilizadores estão a fazer com ele: Estão a usar a mais recente funcionalidade? Estão a conseguir?

As aplicações web modernas são desenvolvidas num ciclo de entrega contínua: lançar uma nova funcionalidade ou melhoria; observar o seu funcionamento para os utilizadores; planear o próximo incremento de desenvolvimento com base nesse conhecimento. Uma parte fundamental deste ciclo é a fase de observação. Application Insights fornece as ferramentas para monitorizar uma aplicação web para desempenho e utilização.

O aspeto mais importante deste processo é o diagnóstico e o diagnóstico. Se a aplicação falhar, então o negócio está a perder-se. O papel principal de um quadro de monitorização é, portanto, detetar falhas de forma fiável, notificá-lo imediatamente e apresentar-lhe as informações necessárias para diagnosticar o problema. Isto é exatamente o que a Application Insights faz.

### <a name="where-do-bugs-come-from"></a>De onde vêm os insetos?
Falhas nos sistemas web geralmente surgem de problemas de configuração ou más interações entre os seus muitos componentes. A primeira tarefa para enfrentar um incidente no local ao vivo é, portanto, identificar o locus do problema: qual é o componente ou relação a causa?

Alguns de nós, aqueles de cabelo grisalho, lembram-se de uma era mais simples em que um programa de computador funcionava num computador. Os desenvolvedores iriam testá-lo completamente antes de enviá-lo; e tendo enviado, raramente veria ou pensaria sobre isso novamente. Os utilizadores teriam de aturar os bugs residuais durante muitos anos. 

As coisas estão muito diferentes agora. A sua aplicação tem uma pletora de dispositivos diferentes para executar, e pode ser difícil garantir exatamente o mesmo comportamento em cada um deles. Hospedar aplicações na nuvem significa que os bugs podem ser corrigidos rapidamente, mas também significa concorrência contínua e a expectativa de novas funcionalidades em intervalos frequentes. 

Nestas condições, a única forma de manter um controlo firme na contagem de erros é o teste automático da unidade. Seria impossível retestar manualmente tudo em cada entrega. O teste de unidade é agora uma parte comum do processo de construção. Ferramentas como a Xamarin Test Cloud ajudam fornecendo testes de UI automatizados em várias versões de navegador. Estes regimes de teste permitem-nos esperar que a taxa de bugs encontrados dentro de uma aplicação possa ser mantida ao mínimo.

Aplicações web típicas têm muitos componentes ao vivo. Além do cliente (numa aplicação de navegador ou dispositivo) e do servidor web, é provável que haja um processamento de backend substancial. Talvez o backend seja um oleoduto de componentes, ou uma coleção mais solta de peças colaborativas. E muitos deles não estarão ao seu controlo - são serviços externos de que você depende.

Em configurações como estas, pode ser difícil e pouco económico testar, ou prever, todos os modos de falha possíveis, para além do próprio sistema ao vivo. 

### <a name="questions-"></a>Perguntas...
Algumas perguntas que fazemos quando estamos a desenvolver um sistema web:

* A minha aplicação está a falhar? 
* O que aconteceu exatamente? - Se falhou num pedido, quero saber como chegou lá. Precisamos de um traço de eventos...
* A minha aplicação é suficientemente rápida? Quanto tempo demora a responder a pedidos típicos?
* O servidor consegue lidar com a carga? Quando a taxa de pedidos aumenta, o tempo de resposta mantém-se estável?
* Quão responsivas são as minhas dependências - as APIs rest, bases de dados e outros componentes que a minha aplicação chama. Em particular, se o sistema é lento, é o meu componente, ou estou a receber respostas lentas de outra pessoa?
* A minha aplicação é para cima ou para baixo? Pode ser visto de todo o mundo? Avisa-me se parar...
* Qual é a causa principal? A falha no meu componente ou dependência? É um problema de comunicação?
* Quantos utilizadores são afetados? Se tenho mais do que um assunto a resolver, qual é o mais importante?

## <a name="what-is-application-insights"></a>O que é o Application Insights?
![Fluxo básico de trabalho de Insights de Aplicação](./media/devops/020.png)

1. Application Insights instrumentos da sua aplicação e envia telemetria sobre ela enquanto a aplicação está em execução. Ou pode construir o SDK de Insights de Aplicação na aplicação, ou pode aplicar instrumentação no prazo de execução. O método anterior é mais flexível, pois pode adicionar a sua própria telemetria aos módulos regulares.
2. A telemetria é enviada para o portal Application Insights, onde é armazenada e processada. (Embora o Application Insights esteja hospedado no Microsoft Azure, pode monitorizar quaisquer aplicações web - e não apenas aplicações Azure.)
3. A telemetria é-lhe apresentada sob a forma de gráficos e tabelas de eventos.

Existem dois tipos principais de telemetria: instâncias agregadas e cruas. 

* Os dados por exemplo incluem, por exemplo, um relatório de um pedido que foi recebido pela sua aplicação web. Pode encontrar e inspecionar os detalhes de um pedido utilizando a ferramenta Search no portal Informações de Aplicação. A instância incluiria dados como o tempo que a sua app demorou a responder ao pedido, bem como o URL solicitado, a localização aproximada do cliente, e outros dados.
* Os dados agregados incluem contagens de eventos por hora de unidade, para que possa comparar a taxa de pedidos com os tempos de resposta. Também inclui médias de métricas como tempos de resposta ao pedido.

As principais categorias de dados são:

* Pedidos para a sua aplicação (geralmente pedidos HTTP), com dados sobre URL, tempo de resposta e sucesso ou falha.
* Dependências - REST e Chamadas SQL feitas pela sua app, também com URI, tempos de resposta e sucesso
* Exceções, incluindo vestígios de pilhas.
* Dados de visualização de página, que vêm dos navegadores dos utilizadores.
* Métricas como contadores de desempenho, bem como métricas que escreve a si mesmo. 
* Eventos personalizados que você pode usar para rastrear eventos de negócios
* Vestígios de registo usados para depuração.

## <a name="case-study-real-madrid-fc"></a>Estudo de Caso: Real Madrid F.C.
O serviço web do [Real Madrid Football Club](https://www.realmadrid.com/) serve cerca de 450 milhões de fãs em todo o mundo. Os fãs acedem a isso tanto através de navegadores web como de aplicações móveis do Clube. Os fãs não podem apenas reservar bilhetes, mas também aceder a informações e videoclips sobre resultados, jogadores e próximos jogos. Podem pesquisar com filtros como o número de golos marcados. Há também ligações às redes sociais. A experiência do utilizador é altamente personalizada e é projetada como uma comunicação bidirecional para envolver os fãs.

A solução [é um sistema de serviços e aplicações no Microsoft Azure.](https://www.microsoft.com/inculture/sports/real-madrid/) A escalabilidade é um requisito fundamental: o tráfego é variável e pode atingir volumes muito elevados durante e em torno dos jogos.

Para o Real Madrid, é vital monitorizar o desempenho do sistema. A Azure Application Insights proporciona uma visão abrangente em todo o sistema, garantindo um nível de serviço fiável e elevado. 

O Clube também tem uma compreensão aprofundada dos seus adeptos: onde estão (apenas 3% estão em Espanha), o interesse que têm nos jogadores, resultados históricos e jogos futuros, e como respondem aos resultados dos jogos.

A maioria destes dados de telemetria é recolhida automaticamente sem um código adicional, o que simplifica a solução e a redução da complexidade operacional.  Para o Real Madrid, a Application Insights lida com 3,8 mil milhões de pontos de telemetria todos os meses.

O Real Madrid usa o módulo Power BI para ver a sua telemetria.

![Visão de POWER BI da telemetria de Insights de Aplicação](./media/devops/080.png)

## <a name="smart-detection"></a>Deteção inteligente
[Diagnósticos proativos](../../azure-monitor/app/proactive-diagnostics.md) é uma característica recente. Sem qualquer configuração especial por si, o Application Insights deteta e alerta automaticamente sobre aumentos incomuns nas taxas de falha na sua aplicação. É inteligente o suficiente para ignorar um passado de falhas ocasionais, e também aumentos que são simplesmente proporcionais a um aumento de pedidos. Por exemplo, se houver uma falha num dos serviços de que depende, ou se a nova construção que lançou não estiver a funcionar tão bem, então saberá disso assim que olhar para o seu e-mail. (E existem webhooks para que possa desencadear outras aplicações.)

Outro aspeto desta funcionalidade realiza uma análise diária e aprofundada da sua telemetria, procurando padrões incomuns de desempenho que são difíceis de descobrir. Por exemplo, pode encontrar um desempenho lento associado a uma determinada área geográfica, ou com uma determinada versão do navegador.

Em ambos os casos, o alerta não só lhe diz os sintomas que foi descoberto, como também lhe dá dados de que precisa para ajudar a diagnosticar o problema, como relatórios de exceção relevantes.

![E-mail de diagnósticos proativos](./media/devops/030.png)

O cliente Samtec disse: "Durante um recente corte de funcionalidades, encontramos uma base de dados subdimensionada que estava a atingir os seus limites de recursos e a causar intervalos. Os alertas de deteção proactiva surgiu literalmente enquanto estávamos a triagem do problema, muito perto do tempo real, como anunciado. Este alerta associado aos alertas da plataforma Azure ajudou-nos quase instantaneamente a resolver o problema. O tempo total de inatividade < 10 minutos."

## <a name="live-metrics-stream"></a>Live Metrics Stream (Fluxo de Métricas em Direto)
Implementar a última construção pode ser uma experiência ansiosa. Se houver algum problema, quer saber imediatamente sobre eles, para que possa recuar se necessário. Live Metrics Stream dá-lhe métricas chave com uma latência de cerca de um segundo.

![Métricas ao vivo](./media/devops/0040.png)

E permite-lhe inspecionar imediatamente uma amostra de quaisquer falhas ou exceções.

![Eventos de fracasso ao vivo](./media/devops/002-live-stream-failures.png)

## <a name="application-map"></a>Mapeamento de Aplicações
O Mapa de Aplicações descobre automaticamente a sua topologia de aplicação, colocando as informações de desempenho em cima dela, para permitir identificar facilmente estrangulamentos de desempenho e fluxos problemáticos em todo o seu ambiente distribuído. Permite-lhe descobrir dependências de aplicações em Serviços Azure. Pode triagem do problema compreendendo se está relacionado com código ou dependência e a partir de um único local para a experiência de diagnóstico relacionada. Por exemplo, a sua aplicação pode estar a falhar devido à degradação do desempenho no nível SQL. Com o mapa de aplicações, pode vê-lo imediatamente e perfurar a experiência SQL Index Advisor ou Query Insights.

![Mapeamento de Aplicações](./media/devops/0050.png)

## <a name="application-insights-analytics"></a>Insights de Aplicação Analytics
Com [o Analytics,](../../azure-monitor/app/analytics.md)pode escrever consultas arbitrárias numa poderosa linguagem semelhante à SQL.  O diagnóstico em toda a pilha de aplicações torna-se fácil à medida que várias perspetivas se conectam e pode fazer as perguntas certas para correlacionar o Desempenho do Serviço com métricas de negócio e experiência do cliente. 

Pode consultar todos os seus casos de telemetria e dados brutos métricos armazenados no portal. A língua inclui filtro, adesão, agregação e outras operações. Pode calcular os campos e realizar análises estatísticas. Há visualizações tabular e gráficas.

![Gráfico de consulta de análise e resultados](./media/devops/0025.png)

Por exemplo, é fácil:

* Segmente os dados de desempenho do pedido da sua aplicação por níveis de cliente para compreender a sua experiência.
* Procure códigos de erro específicos ou nomes de eventos personalizados durante as investigações do site ao vivo.
* Aprofundar o uso da aplicação de clientes específicos para entender como as funcionalidades são adquiridas e adotadas.
* Acompanhe as sessões e os tempos de resposta para utilizadores específicos para permitir que as equipas de suporte e operações forneçam suporte instantâneo ao cliente.
* Determine funcionalidades de aplicação frequentemente utilizadas para responder a questões de priorização de funcionalidades.

O Cliente DNN disse: "A Application Insights forneceu-nos a parte em falta da equação para poder combinar, classificar, consultar e filtrar dados conforme necessário. Permitir que a nossa equipa usasse a sua própria engenhosidade e experiência para encontrar dados com uma linguagem de consulta poderosa permitiu-nos encontrar insights e resolver problemas que nem sabíamos que tínhamos. Muitas respostas interessantes vêm das perguntas começando com *"Pergunto-me se...".*

## <a name="development-tools-integration"></a>Integração de ferramentas de desenvolvimento
### <a name="configuring-application-insights"></a>Configurar insights de aplicação
O Visual Studio e o Eclipse dispõem de ferramentas para configurar os pacotes SDK corretos para o projeto que está a desenvolver. Há um comando de menu para adicionar Insights de Aplicação.

Se por acaso estiver a utilizar uma estrutura de registo de vestígios como Log4N, NLog ou System.Diagnostics.Trace, então obtém a opção de enviar os registos para A aplicação Insights juntamente com a outra telemetria, para que possa facilmente correlacionar os vestígios com pedidos, chamadas de dependência e exceções.

### <a name="search-telemetry-in-visual-studio"></a>Telemetria de pesquisa em Estúdio Visual
Ao desenvolver e depurar uma funcionalidade, pode ver e pesquisar a telemetria diretamente no Estúdio Visual, utilizando as mesmas instalações de pesquisa que no portal web.

E quando o Application Insights regista uma exceção, pode ver o ponto de dados no Estúdio Visual e saltar diretamente para o código relevante.

![Pesquisa de Estúdio Visual](./media/devops/060.png)

Durante a depuração, tem a opção de manter a telemetria na sua máquina de desenvolvimento, visualizando-a no Estúdio Visual mas sem enviá-la para o portal. Esta opção local evita misturar depuração com telemetria de produção.

### <a name="work-items"></a>Artigos de trabalho
Quando um alerta é levantado, os Insights de Aplicação podem criar automaticamente um item de trabalho no seu sistema de rastreio de trabalho.

## <a name="but-what-about"></a>Mas e quanto a...?
* [Privacidade e armazenamento](../../azure-monitor/app/data-retention-privacy.md) - A sua telemetria é mantida em servidores seguros Azure.
* Desempenho - o impacto é muito baixo. A telemetria está lotada.
* [Preços](../../azure-monitor/app/pricing.md) - Você pode começar de graça, e isso continua enquanto você está em baixo volume.


## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Passos seguintes
Começar com a Application Insights é fácil. As principais opções são:

* [Servidores IIS](../../azure-monitor/app/monitor-performance-live-website-now.md), e também para o Serviço de [Aplicações Azure.](../../azure-monitor/app/app-insights-overview.md)
* Instrumente o seu projeto durante o desenvolvimento. Você pode fazê-lo para [ASP.NET](../../azure-monitor/app/asp-net.md) [ou](../../azure-monitor/app/java-get-started.md) java apps, bem como [Node.js](../../azure-monitor/app/nodejs.md) e uma série de [outros tipos](../../azure-monitor/app/platforms.md). 
* Instrumente [qualquer página web](../../azure-monitor/app/javascript.md) adicionando um curto corte de código.

