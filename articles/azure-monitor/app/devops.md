---
title: Monitorização do desempenho da aplicação web - Azure Application Insights
description: Como os Insights de Aplicação se enquadram no ciclo de devOps
ms.topic: conceptual
ms.date: 12/21/2018
ms.openlocfilehash: bc897cc0259894964e0b6164219b1e5459e824b6
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579564"
---
# <a name="deep-diagnostics-for-web-apps-and-services-with-application-insights"></a>Diagnósticos avançados de aplicações Web e serviços com o Application Insights
## <a name="why-do-i-need-application-insights"></a>Por que preciso de Insights de Aplicação?
O Application Insights monitoriza a sua aplicação web em execução. Fala sobre falhas e problemas de desempenho e ajuda-o a analisar a forma como os clientes usam a sua aplicação. Funciona para apps que funcionam em muitas plataformas (ASP.NET, Java EE, Node.js, ...) e está hospedado na Cloud ou no local. 

![Aspetos da complexidade da entrega de aplicações web](./media/devops/010.png)

É essencial monitorizar uma aplicação moderna enquanto está em funcionamento. O mais importante é que pretende detetar falhas antes que a maioria dos seus clientes o façam. Também pretende descobrir e corrigir problemas de desempenho que, embora não sejam catastróficos, talvez abrandem as coisas ou causem algum inconveniente aos seus utilizadores. E quando o sistema está a cumprir a sua satisfação, quer saber o que os utilizadores estão a fazer com ele: Estão a usar a mais recente funcionalidade? Estão a conseguir?

As aplicações web modernas são desenvolvidas num ciclo de entrega contínua: lançar uma nova funcionalidade ou melhoria; observar o quão bem funciona para os utilizadores; planear o próximo incremento do desenvolvimento com base nesse conhecimento. Uma parte fundamental deste ciclo é a fase de observação. O Application Insights fornece as ferramentas para monitorizar uma aplicação web para desempenho e utilização.

O aspeto mais importante deste processo é o diagnóstico e o diagnóstico. Se a aplicação falhar, então o negócio está a perder-se. O papel principal de um quadro de monitorização é, portanto, detetar falhas de forma fiável, notificá-lo imediatamente e apresentar-lhe as informações necessárias para diagnosticar o problema. Isto é exatamente o que a Application Insights faz.

### <a name="where-do-bugs-come-from"></a>De onde vêm os insetos?
Falhas nos sistemas web normalmente surgem de problemas de configuração ou más interações entre os seus muitos componentes. A primeira tarefa ao abordar um incidente no local ao vivo é, portanto, identificar o locus do problema: qual componente ou relação é a causa?

Alguns de nós, aqueles de cabelo grisalho, lembram-se de uma era mais simples em que um programa de computador funcionasse num computador. Os desenvolvedores testá-lo-iam cuidadosamente antes de enviá-lo; e tendo enviado, raramente veria ou pensaria sobre isso novamente. Os utilizadores teriam de aturar os bugs residuais durante muitos anos. 

As coisas estão tão diferentes agora. A sua aplicação tem uma infinidade de dispositivos diferentes para executar, e pode ser difícil garantir exatamente o mesmo comportamento em cada um. Hospedar aplicações na nuvem significa que os bugs podem ser corrigidos rapidamente, mas também significa concorrência contínua e a expectativa de novas funcionalidades em intervalos frequentes. 

Nestas condições, a única forma de manter um controlo firme na contagem de erros é testar a unidade automatizada. Seria impossível voltar a testar manualmente tudo em cada entrega. O teste de unidade é agora uma parte comum do processo de construção. Ferramentas como a Xamarin Test Cloud ajudam fornecendo testes de UI automatizados em várias versões de navegador. Estes regimes de teste permitem-nos esperar que a taxa de bugs encontrada dentro de uma app possa ser mantida ao mínimo.

As aplicações típicas da web têm muitos componentes ao vivo. Além do cliente (num browser ou app de dispositivo) e do servidor web, é provável que haja um processamento substancial de backend. Talvez o backend seja um oleoduto de componentes, ou uma coleção mais solta de peças colaborativas. E muitos deles não estarão ao seu controlo- são serviços externos dos quais depende.

Em configurações como esta, pode ser difícil e pouco económico testar, ou prever, todos os modos de falha possíveis, para além do próprio sistema ao vivo. 

### <a name="questions-"></a>Perguntas...
Algumas perguntas que fazemos quando estamos a desenvolver um sistema web:

* A minha aplicação está a falhar? 
* O que aconteceu exatamente? - Se falhou num pedido, quero saber como chegou lá. Precisamos de um traço de eventos...
* A minha aplicação é suficientemente rápida? Quanto tempo demora a responder a pedidos típicos?
* O servidor pode lidar com a carga? Quando a taxa de pedidos sobe, o tempo de resposta mantém-se estável?
* Quão responsivas são as minhas dependências - as APIs REST, bases de dados e outros componentes que a minha aplicação chama. Em particular, se o sistema é lento, é o meu componente, ou estou a receber respostas lentas de outra pessoa?
* A minha aplicação é para cima ou para baixo? Pode ser visto de todo o mundo? Avisa-me se parar...
* Qual é a causa principal? A falha no meu componente ou uma dependência? É uma questão de comunicação?
* Quantos utilizadores são impactados? Se tenho mais do que uma questão a abordar, qual é o mais importante?

## <a name="what-is-application-insights"></a>O que é o Application Insights?
![Fluxo básico de trabalho de Insights de Aplicações](./media/devops/020.png)

1. A Application Insights instrumentos a sua aplicação e envia telemetria sobre a questão enquanto a aplicação está em execução. Ou pode construir o App Insights SDK na aplicação, ou pode aplicar instrumentação em tempo de execução. O método anterior é mais flexível, pois pode adicionar a sua própria telemetria aos módulos regulares.
2. A telemetria é enviada para o portal Application Insights, onde é armazenada e processada. (Embora o Application Insights esteja hospedado no Microsoft Azure, pode monitorizar quaisquer aplicações web - e não apenas aplicações Azure.)
3. A telemetria é-lhe apresentada sob a forma de gráficos e tabelas de eventos.

Existem dois tipos principais de telemetria: casos agregados e crus. 

* Os dados de exemplo incluem, por exemplo, um relatório de um pedido que foi recebido pela sua aplicação web. Pode encontrar e inspecionar os detalhes de um pedido utilizando a ferramenta 'Pesquisar' no portal 'Insights' de aplicação. O caso incluiria dados como o tempo que a sua aplicação demorou a responder ao pedido, bem como o URL solicitado, a localização aproximada do cliente e outros dados.
* Os dados agregados incluem contagem de eventos por tempo unitário, para que possa comparar a taxa de pedidos com os tempos de resposta. Também inclui médias de métricas, como os tempos de resposta do pedido.

As principais categorias de dados são:

* Pedidos para a sua aplicação (geralmente pedidos HTTP), com dados sobre URL, tempo de resposta e sucesso ou falha.
* Dependências - CHAMADAS REST e SQL feitas pela sua app, também com URI, tempos de resposta e sucesso
* Exceções, incluindo vestígios de pilhas.
* Os dados de visualização da página, que vêm dos navegadores dos utilizadores.
* Métricas como contadores de desempenho, bem como métricas que escreve si mesmo. 
* Eventos personalizados que você pode usar para acompanhar eventos de negócios
* Registar vestígios usados para depurar.

## <a name="case-study-real-madrid-fc"></a>Estudo de Caso: Real Madrid F.C.
O serviço web do [Real Madrid Football Club](https://www.realmadrid.com/) serve cerca de 450 milhões de adeptos em todo o mundo. Os fãs acedem-no tanto através dos navegadores web como das aplicações móveis do Clube. Os fãs não podem apenas reservar bilhetes, mas também aceder a informações e vídeos sobre resultados, jogadores e próximos jogos. Podem pesquisar com filtros como números de golos marcados. Há também ligações às redes sociais. A experiência do utilizador é altamente personalizada, e é projetada como uma comunicação bidirecional para envolver os fãs.

A solução [é um sistema de serviços e aplicações no Microsoft Azure.](https://www.microsoft.com/inculture/sports/real-madrid/) A escalabilidade é um requisito fundamental: o tráfego é variável e pode atingir volumes muito elevados durante e em torno dos jogos.

Para o Real Madrid, é vital monitorizar o desempenho do sistema. O Azure Application Insights oferece uma visão abrangente em todo o sistema, garantindo um serviço fiável e de alto nível. 

O Clube também tem uma compreensão aprofundada dos seus adeptos: onde estão (apenas 3% estão em Espanha), que interesse têm nos jogadores, nos resultados históricos e nos próximos jogos, e na forma como respondem aos resultados dos jogos.

A maioria destes dados de telemetria é recolhida automaticamente sem código adicional, o que simplificou a solução e reduziu a complexidade operacional.  Para o Real Madrid, a Application Insights lida com 3,8 mil milhões de pontos de telemetria por mês.

O Real Madrid usa o módulo Power BI para ver a sua telemetria.

![Vista de Power BI da telemetria Application Insights](./media/devops/080.png)

## <a name="smart-detection"></a>Deteção inteligente
[O diagnóstico proactivo](./proactive-diagnostics.md) é uma característica recente. Sem qualquer configuração especial por si, o Application Insights deteta e alerta automaticamente sobre aumentos incomuns nas taxas de falha na sua aplicação. É inteligente o suficiente para ignorar um fundo de falhas ocasionais, e também aumentos que são simplesmente proporcionais a um aumento de pedidos. Assim, por exemplo, se houver uma falha num dos serviços de que depende, ou se a nova construção que acabou de implantar não estiver a funcionar tão bem, então saberá disso assim que olhar para o seu e-mail. (E existem webhooks para que possa desencadear outras aplicações.)

Outro aspeto desta funcionalidade realiza uma análise diária aprofundada da sua telemetria, procurando padrões de desempenho incomuns que são difíceis de descobrir. Por exemplo, pode encontrar um desempenho lento associado a uma determinada área geográfica, ou a uma determinada versão do navegador.

Em ambos os casos, o alerta não só lhe diz os sintomas que foi descoberto, como também lhe dá dados necessários para ajudar a diagnosticar o problema, como relatórios de exceção relevantes.

![E-mail de diagnósticos proativos](./media/devops/030.png)

O cliente Samtec disse: "Durante um recente corte de recursos, encontramos uma base de dados de baixo escala que estava a atingir os seus limites de recursos e a causar intervalos de tempo. Alertas de deteção pró-ativos vieram literalmente à medida que estávamos triagem do problema, muito perto do tempo real, como anunciado. Este alerta associado aos alertas da plataforma Azure ajudaram-nos quase instantaneamente a corrigir o problema. Tempo total de inatividade < 10 minutos."

## <a name="live-metrics-stream"></a>Live Metrics Stream
Implantar a mais recente construção pode ser uma experiência ansiosa. Se houver algum problema, quer saber imediatamente, para que possa recuar se necessário. Live Metrics Stream dá-lhe métricas-chave com uma latência de cerca de um segundo.

![Métricas ao vivo](./media/devops/0040.png)

E permite-lhe inspecionar imediatamente uma amostra de quaisquer falhas ou exceções.

![Eventos de insucesso ao vivo](./media/devops/002-live-stream-failures.png)

## <a name="application-map"></a>Mapeamento de Aplicações
O Application Map descobre automaticamente a topologia da sua aplicação, colocando as informações de desempenho em cima dela, para que possa identificar facilmente estrangulamentos de desempenho e fluxos problemáticos em todo o seu ambiente distribuído. Permite-lhe descobrir dependências de aplicações nos Serviços Azure. Pode triagem do problema entendendo se está relacionado com código ou dependência e de um único local perfurar em experiência de diagnóstico relacionada. Por exemplo, a sua aplicação pode estar a falhar devido à degradação do desempenho no nível SQL. Com o mapa de aplicações, pode vê-lo imediatamente e perfurar a experiência SQL Index Advisor ou Query Insights.

![Mapeamento de Aplicações](./media/devops/0050.png)

## <a name="application-insights-analytics"></a>Aplicações Insights Analytics
Com [o Analytics,](../logs/log-query-overview.md)pode escrever consultas arbitrárias numa poderosa linguagem sql.  O diagnóstico em toda a pilha de aplicações torna-se fácil à medida que várias perspetivas se conectam e você pode fazer as perguntas certas para correlacionar o Desempenho do Serviço com as Métricas de Negócio e a Experiência do Cliente. 

Pode consultar todas as suas instâncias de telemetria e dados métricos em bruto armazenados no portal. O idioma inclui filtro, junção, agregação e outras operações. Pode calcular campos e realizar análises estatísticas. Existem visualizações tabulares e gráficas.

![Consulta de análise e gráfico de resultados](./media/devops/0025.png)

Por exemplo, é fácil:

* Segmentar os dados de desempenho do pedido da sua aplicação pelos níveis de cliente para entender a sua experiência.
* Procure códigos de erro específicos ou nomes de eventos personalizados durante as investigações do site ao vivo.
* Aprofundar o uso da app de clientes específicos para entender como as funcionalidades são adquiridas e adotadas.
* Acompanhar as sessões e os tempos de resposta para utilizadores específicos para permitir que as equipas de suporte e operações forneçam suporte instantâneo ao cliente.
* Determine funcionalidades de aplicações frequentemente utilizadas para responder a questões de priorização de funcionalidades.

O Customer DNN disse: "A Application Insights forneceu-nos a parte que faltava da equação por sermos capazes de combinar, classificar, consultar e filtrar dados conforme necessário. Permitir que a nossa equipa use o seu próprio engenho e experiência para encontrar dados com uma linguagem de consulta poderosa permitiu-nos encontrar insights e resolver problemas que nem sabíamos que tínhamos. Muitas respostas interessantes vêm das perguntas que começam com *"Pergunto-me se..."*

## <a name="development-tools-integration"></a>Integração de ferramentas de desenvolvimento
### <a name="configuring-application-insights"></a>Configurar insights de aplicações
Visual Studio e Eclipse têm ferramentas para configurar os pacotes SDK corretos para o projeto que está a desenvolver. Há um comando de menu para adicionar Insights de Aplicação.

Se por acaso estiver a utilizar uma estrutura de registo de vestígios como Log4N, NLog ou System.Diagnostics.Trace, então terá a opção de enviar os registos para Application Insights juntamente com a outra telemetria, para que possa facilmente correlacionar os vestígios com pedidos, chamadas de dependência e exceções.

### <a name="search-telemetry-in-visual-studio"></a>Pesquisa de telemetria em Estúdio Visual
Ao desenvolver e depurar uma funcionalidade, pode visualizar e pesquisar a telemetria diretamente no Visual Studio, utilizando as mesmas instalações de pesquisa que no portal web.

E quando o Application Insights regista uma exceção, pode ver o ponto de dados no Estúdio Visual e saltar diretamente para o código relevante.

![Pesquisa de Estúdio Visual](./media/devops/060.png)

Durante a depuragem, tem a opção de manter a telemetria na sua máquina de desenvolvimento, visualizando-a no Visual Studio, mas sem enviá-la para o portal. Esta opção local evita misturar depurações com telemetria de produção.

### <a name="work-items"></a>Artigos de trabalho
Quando um alerta é levantado, o Application Insights pode criar automaticamente um item de trabalho no seu sistema de rastreio de trabalho.

## <a name="but-what-about"></a>Mas e...?
* [Privacidade e armazenamento](./data-retention-privacy.md) - A sua telemetria é mantida nos servidores seguros Azure.
* Desempenho - o impacto é muito baixo. A telemetria está em lotada.
* [Preços](./pricing.md) - Pode começar de graça, e isso continua enquanto está em baixo volume.


## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Passos seguintes
Começar com a Application Insights é fácil. As principais opções são:

* [Servidores IIS](./monitor-performance-live-website-now.md), e também para [o Azure App Service](./app-insights-overview.md).
* Instrumento o seu projeto durante o desenvolvimento. Pode [fazê-lo](./asp-net.md) para aplicações ASP.NET ou [Java,](./java-get-started.md) bem como [Node.js](./nodejs.md) e uma série de [outros tipos.](./platforms.md) 
* Instrumento [qualquer página web](./javascript.md) adicionando um pequeno corte de código.

