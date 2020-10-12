---
title: Ferramenta de diagnóstico e resolução
description: Saiba como pode resolver problemas com a sua aplicação no Azure App Service com os diagnósticos e resolver a ferramenta no portal Azure.
keywords: serviço de aplicações, serviço de aplicativos azure, diagnósticos, suporte, web app, resolução de problemas, autoajuda
author: jen7714
ms.topic: article
ms.date: 10/18/2019
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: d58341979b0bbe0699a5ca293b20394c43cde1d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88962813"
---
# <a name="azure-app-service-diagnostics-overview"></a>Visão geral do Serviço de Aplicações Azure

Quando estiver a executar uma aplicação web, pretende estar preparado para quaisquer problemas que possam surgir, desde 500 erros aos seus utilizadores, dizendo-lhe que o seu site está em baixo. O diagnóstico do Serviço de Aplicações é uma experiência inteligente e interativa para ajudá-lo a resolver problemas na sua aplicação sem necessidade de configuração. Quando se depara com problemas com a sua app, o diagnóstico do Serviço de Aplicações aponta o que é errado guiá-lo para as informações certas para resolver e resolver o problema de forma mais fácil e rápida.

Embora esta experiência seja mais útil quando está com problemas com a sua app nas últimas 24 horas, todos os gráficos de diagnóstico estão sempre disponíveis para analisar.

O Serviço de Aplicações funciona não só para a sua aplicação no Windows, mas também para aplicações em [Linux/containers,](./overview.md#app-service-on-linux) [App Service Environment](./environment/intro.md)e [Azure Functions](../azure-functions/functions-overview.md).

## <a name="open-app-service-diagnostics"></a>Diagnósticos abertos do Serviço de Aplicações

Para aceder aos diagnósticos do Serviço de Aplicações, navegue para a sua aplicação web do Serviço de Aplicações ou Para o Ambiente de Serviço de Aplicações no [portal Azure.](https://portal.azure.com) Na navegação à esquerda, clique no **Diagnóstico e resolva problemas.**

Para funções Azure, navegue para a sua aplicação de função, e na navegação superior, clique nas **funcionalidades da Plataforma**, e selecione **Diagnosticar e resolver problemas** a partir da secção de gestão de **Recursos.**

Na página inicial de diagnóstico do Serviço de Aplicações, pode escolher a categoria que melhor descreve o problema com a sua aplicação utilizando as palavras-chave em cada azulejo inicial. Além disso, esta página é onde você pode encontrar **Ferramentas de Diagnóstico** para aplicações Windows. Consulte [as ferramentas de diagnóstico (apenas para a aplicação Windows)](#diagnostic-tools-only-for-windows-app).

![Home page](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

> [!NOTE]
> Se a sua aplicação estiver em baixo ou se tiver um desempenho lento, pode [recolher um rastreio](https://azure.github.io/AppService/2018/06/06/App-Service-Diagnostics-Profiling-an-ASP.NET-Web-App-on-Azure-App-Service.html) de perfis para identificar a causa principal do problema. A definição de perfis é leve e é projetada para cenários de produção.
>

## <a name="interactive-interface"></a>Interface interativa

Assim que selecionar uma categoria de página inicial que melhor se alinha com o problema da sua aplicação, a interface interativa de diagnóstico do App Service, Genie, pode guiá-lo através do diagnóstico e resolução de problemas com a sua aplicação. Você pode usar os atalhos de azulejos fornecidos pela Genie para ver o relatório de diagnóstico completo da categoria de problemas que você está interessado. Os atalhos de azulejos proporcionam-lhe uma forma direta de aceder às suas métricas de diagnóstico.

![Atalhos de azulejos](./media/app-service-diagnostics/tile-shortcuts-2.png)

Depois de clicar nestes azulejos, pode ver uma lista de tópicos relacionados com a questão descrita no azulejo. Estes tópicos fornecem fragmentos de informações notáveis do relatório completo. Pode clicar em qualquer um destes tópicos para investigar mais aprofundadamente as questões. Além disso, pode clicar no **Ver Relatório Completo** para explorar todos os tópicos numa única página.

![Tópicos](./media/app-service-diagnostics/application-logs-insights-3.png)

![Ver Relatório Completo](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>Relatório de diagnóstico

Depois de optar por investigar o assunto mais adiante clicando num tópico, pode ver mais detalhes sobre o tema muitas vezes complementado com gráficos e desvalorizações. O relatório de diagnóstico pode ser uma ferramenta poderosa para identificar o problema com a sua aplicação.

![Relatório de diagnóstico](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>Check-up de saúde

Se não sabe o que se passa com a sua app ou não sabe por onde começar a resolver problemas, o check-up de saúde é um bom ponto de partida. O check-up de saúde analisa as suas aplicações para lhe dar uma visão geral rápida e interativa que aponta o que é saudável e o que está errado, dizendo-lhe onde procurar para investigar o assunto. A sua interface inteligente e interativa fornece-lhe orientação através do processo de resolução de problemas. O check-up de saúde está integrado com a experiência Genie para aplicações Windows e relatório de diagnóstico de aplicativos Linux.

### <a name="health-checkup-graphs"></a>Gráficos de check-up de saúde

Há quatro gráficos diferentes no check-up de saúde.

- **pedidos e erros:** Um gráfico que mostra o número de pedidos feitos nas últimas 24 horas juntamente com erros do servidor HTTP.
- **desempenho da aplicação:** Um gráfico que mostra o tempo de resposta nas últimas 24 horas para vários grupos percentil.
- **Utilização do CPU:** Um gráfico que mostra o uso global por cento do CPU por exemplo nas últimas 24 horas.  
- **utilização da memória:** Um gráfico que mostra o uso geral por cento da memória física por exemplo nas últimas 24 horas.

![Check-up de saúde](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>Investigue problemas de código de aplicação (apenas para aplicação Windows)

Como muitos problemas de aplicações estão relacionados com problemas no seu código de aplicação, o Serviço de Aplicações integra-se com [o Application Insights](../azure-monitor/app/app-insights-overview.md) para destacar exceções e problemas de dependência para correlacionar com o tempo de inatividade selecionado. Os Insights de Aplicação têm de ser ativados separadamente.

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

Para ver exceções e dependências do Application Insights, selecione a **aplicação web para baixo** ou **aplicações web atalhos lentos** de azulejos.

### <a name="troubleshooting-steps-only-for-windows-app"></a>Etapas de resolução de problemas (apenas para aplicação Windows)

Se for detetado um problema com uma categoria de problemas específicos nas últimas 24 horas, pode ver o relatório de diagnóstico completo, e os diagnósticos do Serviço de Aplicações podem levá-lo a visualizar mais conselhos de resolução de problemas e próximos passos para uma experiência mais guiada.

![Insights de Aplicação e Resolução de Problemas e Próximos Passos](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>Ferramentas de diagnóstico (apenas para aplicação Windows)

As Ferramentas de Diagnóstico incluem ferramentas de diagnóstico mais avançadas que o ajudam a investigar problemas de código de aplicação, lentidão, cadeias de conexão e muito mais. e ferramentas pró-ativas que o ajudam a mitigar problemas com o uso do CPU, pedidos e memória.

### <a name="proactive-cpu-monitoring"></a>Monitorização proactiva do CPU

A monitorização proativa do CPU proporciona-lhe uma forma fácil e proativa de tomar uma ação quando a sua app ou processo infantil para a sua app está a consumir recursos elevados da CPU. Pode definir as suas próprias regras de limiar de CPU para atenuar temporariamente um problema de CPU elevado até que a verdadeira causa para o problema inesperado seja encontrada. Para mais informações, consulte [Mitigate os seus problemas de CPU antes de acontecerem.](https://azure.github.io/AppService/2019/10/07/Mitigate-your-CPU-problems-before-they-even-happen.html)

![Monitorização proactiva do CPU](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="auto-healing-and-proactive-auto-healing"></a>Auto-cura e auto-cura proativa

A cura automática é uma medida de mitigação que pode tomar quando a sua aplicação está a ter um comportamento inesperado. Pode definir as suas próprias regras com base na contagem de pedidos, pedido lento, limite de memória e código de estado HTTP para desencadear ações de mitigação. Utilize a ferramenta para atenuar temporariamente um comportamento inesperado até encontrar a causa principal. Para obter mais informações, consulte [anunciar a nova experiência de cura automática em diagnósticos de serviços de aplicações.](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html)

![Auto-cura](./media/app-service-diagnostics/auto-healing-10.png)

Tal como a monitorização proativa do CPU, a auto-cura proativa é uma solução chave-na-mão para mitigar comportamentos inesperados da sua aplicação. A auto-cura proativa reinicia a sua aplicação quando o Serviço de Aplicações determina que a sua aplicação está num estado irrecuperável. Para obter mais informações, consulte [a introdução da Auto Heal Proactive.](https://azure.github.io/AppService/2017/08/17/Introducing-Proactive-Auto-Heal.html)

## <a name="navigator-and-change-analysis-only-for-windows-app"></a>Navigator e alteração da análise (apenas para aplicação Windows)

Numa grande equipa com integração contínua e onde a sua app tem muitas dependências, pode ser difícil identificar a mudança específica que causa um comportamento pouco saudável. O Navigator ajuda a obter visibilidade na topologia da sua aplicação, tornando automaticamente um mapa de dependência da sua app e todos os recursos na mesma subscrição. A Navigator permite-lhe ver uma lista consolidada de alterações feitas pela sua app e suas dependências e reduzir-se a uma mudança que causa um comportamento pouco saudável. Pode ser acedido através do azulejo inicial **Navigator** e precisa de ser ativado antes de o utilizar pela primeira vez. Para obter mais informações, consulte [Obter visibilidade nas dependências da sua aplicação com o Navigator.](https://azure.github.io/AppService/2019/08/06/Bring-visibility-to-your-app-and-its-dependencies-with-Navigator.html)

![Página padrão do navegador](./media/app-service-diagnostics/navigator-default-page-11.png)

![Vista difusa](./media/app-service-diagnostics/diff-view-12.png)

A análise de alteração de alterações de apps pode ser acedida através de atalhos de azulejos, **Alterações de Aplicações** e Falhas de **Aplicações** em **Disponibilidade e Desempenho** para que possa usá-la simultaneamente com outras métricas. Antes de utilizar a função, deve primeiro ative-la. Para obter mais informações, consulte [anunciar a nova experiência de análise de alterações no App Service Diagnostics.](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html)

Publique as suas perguntas ou feedback no [UserVoice](https://feedback.azure.com/forums/169385-web-apps) adicionando "[Diag]" no título.