---
title: Monitoramento de desempenho de aplicativo Web-insights de Aplicativo Azure
description: Como Application Insights se encaixa no ciclo de devOps
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/21/2018
ms.openlocfilehash: 1396bc86971941fdf8c6559df71a4c47f58a899c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928827"
---
# <a name="deep-diagnostics-for-web-apps-and-services-with-application-insights"></a>Diagnósticos avançados de aplicações Web e serviços com o Application Insights
## <a name="why-do-i-need-application-insights"></a>Por que preciso de Application Insights?
Application Insights monitora seu aplicativo Web em execução. Ele informa sobre falhas e problemas de desempenho e ajuda a analisar como os clientes usam seu aplicativo. Ele funciona para aplicativos executados em várias plataformas (ASP.NET, Java EE, Node. js,...) e é hospedado na nuvem ou no local. 

![Aspectos da complexidade de fornecimento de aplicativos Web](./media/devops/010.png)

É essencial monitorar um aplicativo moderno enquanto ele está em execução. O mais importante é que você deseja detectar falhas antes que a maioria dos seus clientes faça. Você também deseja descobrir e corrigir problemas de desempenho que, embora não sejam catastróficos, talvez prejudiquem as coisas ou causem algumas inconveniências para os usuários. E quando o sistema estiver executando sua satisfação, você deseja saber o que os usuários estão fazendo com ele: eles estão usando o recurso mais recente? Eles estão tendo sucesso com ele?

Os aplicativos Web modernos são desenvolvidos em um ciclo de entrega contínua: Libere um novo recurso ou melhoria; Observe como ele funciona para os usuários; Planeje o próximo incremento de desenvolvimento com base nesse conhecimento. Uma parte importante desse ciclo é a fase de observação. Application Insights fornece as ferramentas para monitorar o desempenho e o uso de um aplicativo Web.

O aspecto mais importante desse processo é o diagnóstico e o diagnóstico. Se o aplicativo falhar, a empresa estará sendo perdida. A função principal de uma estrutura de monitoramento é, portanto, detectar falhas de forma confiável, notificá-lo imediatamente e apresentar as informações necessárias para diagnosticar o problema. Isso é exatamente o que Application Insights faz.

### <a name="where-do-bugs-come-from"></a>De onde vêm os bugs?
As falhas em sistemas da Web normalmente surgem de problemas de configuração ou interações incorretas entre seus vários componentes. A primeira tarefa ao lidar com um incidente de site ativo é, portanto, identificar o local do problema: Qual componente ou relação é a causa?

Alguns de nós, aqueles com cabelo cinza, podem se lembrar de uma era mais simples na qual um programa de computador foi executado em um computador. Os desenvolvedores o testaram exaustivamente antes de enviá-lo; e depois de tê-lo enviado, raramente veremos ou pensamos nele. Os usuários teriam que colocar os bugs residuais por muitos anos. 

As coisas são tão diferentes agora. Seu aplicativo tem uma infinidade de dispositivos diferentes para execução e pode ser difícil garantir o mesmo comportamento em cada um. Hospedar aplicativos na nuvem significa que os bugs podem ser corrigidos rapidamente, mas também significa competição contínua e a expectativa de novos recursos em intervalos frequentes. 

Nessas condições, a única maneira de manter um controle firme sobre a contagem de bugs é o teste de unidade automatizado. Seria impossível testar manualmente tudo em cada entrega. O teste de unidade agora é uma parte comum do processo de compilação. Ferramentas como o Xamarin Test Cloud ajudam fornecendo testes automatizados de interface do usuário em várias versões do navegador. Esses testes regimes nos permitem esperar que a taxa de bugs encontrados dentro de um aplicativo possa ser mantida em um mínimo.

Aplicativos Web típicos têm muitos componentes ao vivo. Além do cliente (em um navegador ou aplicativo de dispositivo) e do servidor Web, é provável que haja processamento de back-end substancial. Talvez o back-end seja um pipeline de componentes ou uma coleção mais flexível de peças de colaboração. E muitos deles não estarão em seu controle-eles são serviços externos dos quais você depende.

Em configurações como essas, pode ser difícil e demorado testar, ou prever, todos os modos de falha possíveis, além do próprio sistema ativo. 

### <a name="questions-"></a>Perguntas...
Algumas perguntas que perguntamos quando estamos desenvolvendo um sistema Web:

* Meu aplicativo está falhando? 
* O que aconteceu exatamente? -Se uma solicitação falhou, quero saber como ela foi obtida. Precisamos de um rastreamento de eventos...
* Meu aplicativo é rápido o suficiente? Quanto tempo leva para responder às solicitações típicas?
* O servidor pode lidar com a carga? Quando a taxa de solicitações aumenta, o tempo de resposta é mantido estável?
* O quão responsivo são minhas dependências: as APIs REST, os bancos de dados e outros componentes que meu aplicativo chama. Em particular, se o sistema estiver lento, ele é meu componente ou estou recebendo respostas lentas de outra pessoa?
* Meu aplicativo está ativo ou inativo? Ele pode ser visto em todo o mundo? Avise-me se ele parar...
* Qual é a causa raiz? Houve falha no meu componente ou em uma dependência? É um problema de comunicação?
* Quantos usuários são afetados? Se eu tiver mais de um problema para lidar, o que é mais importante?

## <a name="what-is-application-insights"></a>O que são Informações da Aplicação?
![Fluxo de trabalho básico de Application Insights](./media/devops/020.png)

1. Application Insights instrumentar seu aplicativo e envia a telemetria sobre ele enquanto o aplicativo está em execução. Você pode criar o SDK do Application Insights no aplicativo ou pode aplicar a instrumentação em tempo de execução. O método anterior é mais flexível, pois você pode adicionar sua própria telemetria aos módulos regulares.
2. A telemetria é enviada ao portal de Application Insights, onde é armazenada e processada. (Embora Application Insights esteja hospedado no Microsoft Azure, ele pode monitorar qualquer aplicativo Web, não apenas aplicativos do Azure.)
3. A telemetria é apresentada a você na forma de gráficos e tabelas de eventos.

Há dois tipos principais de telemetria: instâncias agregadas e brutas. 

* Os dados da instância incluem, por exemplo, um relatório de uma solicitação que foi recebida pelo seu aplicativo Web. Você pode encontrar e inspecionar os detalhes de uma solicitação usando a ferramenta de pesquisa no portal de Application Insights. A instância incluiria dados como quanto tempo o aplicativo levou para responder à solicitação, bem como a URL solicitada, o local aproximado do cliente e outros dados.
* Os dados agregados incluem contagens de eventos por tempo unitário, para que você possa comparar a taxa de solicitações com os tempos de resposta. Ele também inclui a média de métricas, como tempos de resposta de solicitação.

As principais categorias de dados são:

* Solicitações para seu aplicativo (geralmente solicitações HTTP), com dados em URL, tempo de resposta e êxito ou falha.
* Dependências – chamadas REST e SQL feitas por seu aplicativo, também com URI, tempos de resposta e êxito
* Exceções, incluindo rastreamentos de pilha.
* Dados de exibição de página, que são provenientes dos navegadores dos usuários.
* Métricas como contadores de desempenho, bem como métricas que você escreve por conta própria. 
* Eventos personalizados que você pode usar para acompanhar eventos de negócios
* Rastreamentos de log usados para depuração.

## <a name="case-study-real-madrid-fc"></a>Estudo de caso: Real Madri F.C.
O serviço da Web do [realista de futebol](https://www.realmadrid.com/) tem cerca de 450 milhões fãs em todo o mundo. Os fãs o acessam por meio de navegadores da Web e dos aplicativos móveis do clube. Os fãs não podem apenas marcar tíquetes, mas também acessar informações e clipes de vídeo nos resultados, jogadores e jogos futuros. Eles podem pesquisar com filtros, como números de metas pontuadas. Também há links para mídia social. A experiência do usuário é altamente personalizada e é projetada como uma comunicação bidirecional para envolver ventiladores.

A solução [é um sistema de serviços e aplicativos em Microsoft Azure](https://www.microsoft.com/inculture/sports/real-madrid/). A escalabilidade é um requisito importante: o tráfego é variável e pode alcançar volumes muito altos durante e em contrapartidas.

Para o verdadeiro Madri, é vital monitorar o desempenho do sistema. O Aplicativo Azure insights fornece uma visão abrangente em todo o sistema, garantindo um alto nível de serviço e confiável. 

O clube também tem uma compreensão aprofundada de seus fãs: onde estão (apenas 3% estão na Espanha), o interesse que eles têm em jogadores, resultados históricos e jogos futuros e como eles respondem a resultados de correspondência.

A maioria desses dados de telemetria é coletada automaticamente sem nenhum código adicionado, o que simplificou a solução e reduziu a complexidade operacional.  Para o verdadeiro Madri, Application Insights lida com pontos de telemetria 3.800.000.000 a cada mês.

O Real Madri usa o módulo Power BI para exibir sua telemetria.

![Power BI exibição de telemetria de Application Insights](./media/devops/080.png)

## <a name="smart-detection"></a>Detecção inteligente
O [diagnóstico proativo](../../azure-monitor/app/proactive-diagnostics.md) é um recurso recente. Sem qualquer configuração especial por você, Application Insights automaticamente detecta e alerta você sobre aumentos incomuns em tarifas de falha em seu aplicativo. É inteligente o suficiente para ignorar um plano de fundo de falhas ocasionais e também aumentos que são simplesmente proporcionais a um aumento nas solicitações. Por exemplo, se houver uma falha em um dos serviços dos quais você depende, ou se a nova compilação que você acabou de implantar não estiver funcionando tão bem, você saberá sobre isso assim que examinar seu email. (E há WebHooks para que você possa disparar outros aplicativos.)

Outro aspecto desse recurso executa uma análise detalhada diária de sua telemetria, procurando padrões incomuns de desempenho que são difíceis de descobrir. Por exemplo, ele pode encontrar um desempenho lento associado a uma área geográfica específica ou com uma versão específica do navegador.

Em ambos os casos, o alerta não apenas informa os sintomas descobertos, mas também fornece dados de que você precisa para ajudar a diagnosticar o problema, como relatórios de exceção relevantes.

![Email de diagnóstico proativo](./media/devops/030.png)

O cliente Samtec disse: "durante uma transferência de recursos recente, encontramos um banco de dados em escala que estava atingindo seus limites de recursos e causando tempos limite. Os alertas de detecção proativas passaram de forma literalmente, pois estamos separando o problema, quase em tempo real, como anunciado. Esse alerta junto com os alertas da plataforma Azure nos ajudou a corrigir o problema quase instantaneamente. Tempo de inatividade total < 10 minutos. "

## <a name="live-metrics-stream"></a>Live Metrics Stream
A implantação da compilação mais recente pode ser uma experiência ansioso. Se houver algum problema, você desejará saber sobre eles imediatamente, para que possa fazer o back-out, se necessário. Live Metrics Stream fornece métricas-chave com uma latência de aproximadamente um segundo.

![Métricas ao vivo](./media/devops/0040.png)

E permite inspecionar imediatamente um exemplo de quaisquer falhas ou exceções.

![Eventos de falha ao vivo](./media/devops/002-live-stream-failures.png)

## <a name="application-map"></a>Mapeamento de Aplicações
O mapa de aplicativos descobre automaticamente a topologia do aplicativo, estabelecendo as informações de desempenho sobre ela, para permitir que você identifique facilmente os gargalos de desempenho e os fluxos problemáticos em seu ambiente distribuído. Ele permite que você descubra dependências de aplicativos nos serviços do Azure. Você pode fazer uma triagem do problema entendendo se ele é relacionado ao código ou à dependência e de um único lugar analisar a experiência de diagnóstico relacionada. Por exemplo, seu aplicativo pode estar falhando devido à degradação do desempenho na camada SQL. Com o mapa do aplicativo, você pode vê-lo imediatamente e detalhar a experiência do SQL Index Advisor ou do insights de consulta.

![Mapeamento de Aplicações](./media/devops/0050.png)

## <a name="application-insights-analytics"></a>Análise de Application Insights
Com o [Analytics](../../azure-monitor/app/analytics.md), você pode escrever consultas arbitrárias em uma linguagem poderosa semelhante ao SQL.  O diagnóstico em toda a pilha de aplicativos se torna fácil, pois várias perspectivas são conectadas e você pode fazer as perguntas certas para correlacionar o desempenho do serviço com as métricas de negócios e a experiência do cliente. 

Você pode consultar toda a instância de telemetria e os dados brutos de métrica armazenados no Portal. O idioma inclui filtro, junção, agregação e outras operações. Você pode calcular os campos e executar a análise estatística. Há visualizações de tabela e gráficas.

![Gráfico de resultados e consulta do Analytics](./media/devops/0025.png)

Por exemplo, é fácil:

* Segmente os dados de desempenho de solicitação do aplicativo por camadas de cliente para entender sua experiência.
* Procure códigos de erro específicos ou nomes de eventos personalizados durante investigações de site ativo.
* Faça uma busca detalhada no uso do aplicativo de clientes específicos para entender como os recursos são adquiridos e adotados.
* Rastreie sessões e tempos de resposta para usuários específicos para permitir que as equipes de suporte e operações forneçam atendimento ao cliente instantâneo.
* Determine os recursos de aplicativo usados com frequência para responder a perguntas de priorização de recursos.

DNN de clientes disse: "Application Insights nos forneceu a parte ausente da equação para poder combinar, classificar, consultar e filtrar dados, conforme necessário. Permitir que nossa equipe use seu próprio ingenuidade e experiência para encontrar dados com uma linguagem de consulta eficiente nos permitiu encontrar informações e resolver problemas que nem sabíamos que tínhamos. Muitas respostas interessantes vêm das perguntas que começam com *"Eu imagino se...".*

## <a name="development-tools-integration"></a>Integração das ferramentas de desenvolvimento
### <a name="configuring-application-insights"></a>Configurando Application Insights
O Visual Studio e o eclipse têm ferramentas para configurar os pacotes SDK corretos para o projeto que você está desenvolvendo. Há um comando de menu para adicionar Application Insights.

Se você estiver usando uma estrutura de log de rastreamento como Log4N, NLog ou System. Diagnostics. Trace, terá a opção de enviar os logs para Application Insights junto com a outra telemetria, para que você possa correlacionar facilmente os rastreamentos com solicitações, dependência chamadas e exceções.

### <a name="search-telemetry-in-visual-studio"></a>Pesquisar telemetria no Visual Studio
Ao desenvolver e depurar um recurso, você pode exibir e pesquisar a telemetria diretamente no Visual Studio, usando os mesmos recursos de pesquisa que no portal da Web.

E quando Application Insights registra uma exceção, você pode exibir o ponto de dados no Visual Studio e ir diretamente para o código relevante.

![Pesquisa do Visual Studio](./media/devops/060.png)

Durante a depuração, você tem a opção de manter a telemetria em seu computador de desenvolvimento, exibindo-a no Visual Studio, mas sem enviá-la ao Portal. Essa opção local evita a combinação de depuração com telemetria de produção.

### <a name="work-items"></a>Itens de trabalho
Quando um alerta é gerado, Application Insights pode criar automaticamente um item de trabalho em seu sistema de controle de trabalho.

## <a name="but-what-about"></a>Mas e quanto a...?
* [Privacidade e armazenamento](../../azure-monitor/app/data-retention-privacy.md) -sua telemetria é mantida em servidores seguros do Azure.
* Desempenho-o impacto é muito baixo. A telemetria está em lote.
* [Preços](../../azure-monitor/app/pricing.md) – você pode começar gratuitamente e isso continuará enquanto você estiver com um volume baixo.


## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Passos seguintes
É fácil começar a usar o Application Insights. As principais opções são:

* [Servidores IIS](../../azure-monitor/app/monitor-performance-live-website-now.md)e também para [Azure app serviço](../../azure-monitor/app/app-insights-overview.md).
* Instrumente seu projeto durante o desenvolvimento. Você pode fazer isso para aplicativos [ASP.net](../../azure-monitor/app/asp-net.md) ou [Java](../../azure-monitor/app/java-get-started.md) , bem como [node. js](../../azure-monitor/app/nodejs.md) e um host de [outros tipos](../../azure-monitor/app/platforms.md). 
* Instrumente [qualquer página da Web](../../azure-monitor/app/javascript.md) adicionando um trecho de código curto.

