---
title: Diagnóstico e ferramenta de resolução
description: Saiba como pode resolver problemas com a sua aplicação no Azure App Service com os diagnósticos e resolver a ferramenta no portal Azure.
keywords: serviço de aplicações, serviço de aplicações azure, diagnósticos, suporte, aplicação web, resolução de problemas, autoajuda
author: jen7714
ms.topic: article
ms.date: 10/18/2019
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: df8f68e47776f46ae2d1331f85bd76d6cfe17b80
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869951"
---
# <a name="azure-app-service-diagnostics-overview"></a>Visão geral do serviço de aplicações azure

Quando estiver a executar uma aplicação web, pretende estar preparado para quaisquer problemas que possam surgir, desde 500 erros aos seus utilizadores, dizendo-lhe que o seu site está em baixo. O diagnóstico do Serviço de Aplicações é uma experiência inteligente e interativa para ajudá-lo a resolver problemas com a sua aplicação sem necessidade de configuração. Quando se depara com problemas com a sua aplicação, os diagnósticos do Serviço de Aplicações apontam o que está errado em guiá-lo para a informação certa para mais facilmente e rapidamente resolver o problema.

Embora esta experiência seja mais útil quando se está a ter problemas com a sua app nas últimas 24 horas, todos os gráficos de diagnóstico estão sempre disponíveis para analisar.

Os diagnósticos do Serviço de Aplicações funcionam não só para a sua aplicação no Windows, mas também para aplicações em [Linux/contentores,](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro) [App Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro)e [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview).

## <a name="open-app-service-diagnostics"></a>Diagnósticos de Serviço de Aplicações Abertas

Para aceder aos diagnósticos do Serviço de Aplicações, navegue para a sua aplicação web do App Service ou para o App Service Environment no [portal Azure](https://portal.azure.com). Na navegação à esquerda, clique em **Diagnosticar e resolver problemas**.

Para funções Azure, navegue para a sua app de funções e na navegação de topo, clique nas **funcionalidades da Plataforma**, e selecione **Diagnosticar e resolver problemas** a partir da secção de gestão de **Recursos.**

Na página inicial de diagnósticodo Serviço de Aplicações, pode escolher a categoria que melhor descreve o problema com a sua aplicação utilizando as palavras-chave em cada azulejo da página inicial. Além disso, esta página é onde pode encontrar **Ferramentas de Diagnóstico** para aplicações Windows. Consulte [as ferramentas de diagnóstico (apenas para aplicação Windows)](#diagnostic-tools-only-for-windows-app).

![Home page](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

> [!NOTE]
> Se a sua aplicação estiver em baixo ou a apresentar-se lentamente, pode [recolher um rastreio](https://azure.github.io/AppService/2018/06/06/App-Service-Diagnostics-Profiling-an-ASP.NET-Web-App-on-Azure-App-Service.html) de perfis para identificar a causa principal do problema. O perfil é leve e é projetado para cenários de produção.
>

## <a name="interactive-interface"></a>Interface interativa

Uma vez que selecione uma categoria de página inicial que melhor se alinha com o problema da sua aplicação, a interface interativa dos diagnósticos do Serviço de Aplicações, Genie, pode guiá-lo através do diagnóstico e resolução de problemas com a sua app. Pode utilizar os atalhos de azulejos fornecidos pela Genie para ver o relatório completo de diagnóstico da categoria de problemas que está interessado. Os atalhos de azulejos proporcionam-lhe uma forma direta de aceder às suas métricas de diagnóstico.

![Atalhos de azulejos](./media/app-service-diagnostics/tile-shortcuts-2.png)

Depois de clicar nestes azulejos, pode ver uma lista de tópicos relacionados com o problema descrito no azulejo. Estes tópicos fornecem fragmentos de informações notáveis a partir do relatório completo. Pode clicar em qualquer um destes tópicos para investigar mais aprofundadamente as questões. Além disso, pode clicar no **Ver Relatório Completo** para explorar todos os tópicos numa única página.

![Tópicos](./media/app-service-diagnostics/application-logs-insights-3.png)

![Ver Relatório Completo](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>Relatório de diagnóstico

Depois de optar por investigar mais aprofundadamente o assunto clicando num tópico, pode ver mais detalhes sobre o tema muitas vezes complementado com gráficos e marcações. O relatório de diagnóstico pode ser uma ferramenta poderosa para identificar o problema com a sua aplicação.

![Relatório de diagnóstico](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>Check-up de saúde

Se não sabe o que há de errado com a sua aplicação ou não sabe por onde começar a resolver problemas, o check-up de saúde é um bom lugar para começar. O check-up de saúde analisa as suas aplicações para lhe dar uma visão rápida e interativa que aponta o que é saudável e o que está errado, dizendo-lhe onde procurar investigar o problema. A sua interface inteligente e interativa fornece-lhe orientação através do processo de resolução de problemas. O check-up de saúde está integrado com a experiência Genie para aplicações windows e aplicação web para baixo relatório de diagnóstico para aplicações Linux.

### <a name="health-checkup-graphs"></a>Gráficos de check-up de saúde

Há quatro gráficos diferentes no check-up de saúde.

- **pedidos e erros:** Um gráfico que mostra o número de pedidos feitos nas últimas 24 horas juntamente com erros do servidor HTTP.
- **desempenho da aplicação:** Um gráfico que mostra o tempo de resposta nas últimas 24 horas para vários grupos de percentil.
- **Utilização do CPU:** Um gráfico que mostra o uso global de CPU por exemplo nas últimas 24 horas.  
- **utilização da memória:** Um gráfico que mostra o uso geral da memória física por exemplo nas últimas 24 horas.

![Check-up de saúde](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>Investigar problemas de código de aplicação (apenas para aplicação Windows)

Como muitos problemas de aplicações estão relacionados com problemas no seu código de aplicação, o diagnóstico do App Service integra-se com [os Insights de Aplicação](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) para destacar exceções e problemas de dependência para se relacionar com o tempo de inatividade selecionado. Os Insights de Aplicação têm de ser ativados separadamente.

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

Para ver as exceções e dependências da Application Insights, selecione a **aplicação web para baixo** ou a **aplicação web aparatos lentos** de azulejos.

### <a name="troubleshooting-steps-only-for-windows-app"></a>Passos de resolução de problemas (apenas para aplicação Windows)

Se um problema for detetado com uma categoria de problema específico nas últimas 24 horas, pode ver o relatório completo de diagnóstico, e os diagnósticos do Serviço de Aplicações podem levar-lhe a ver mais conselhos de resolução de problemas e próximos passos para uma experiência mais guiada.

![Insights de aplicação e resolução de problemas e próximos passos](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>Ferramentas de diagnóstico (apenas para aplicação Windows)

DiagnósticoS As ferramentas incluem ferramentas de diagnóstico mais avançadas que o ajudam a investigar problemas de código de aplicação, lentidão, cordas de ligação e muito mais. e ferramentas proativas que o ajudam a mitigar problemas com o uso, pedidos e memória do CPU.

### <a name="proactive-cpu-monitoring"></a>Monitorização proactiva do CPU

A monitorização proactiva do CPU proporciona-lhe uma forma fácil e proativa de tomar uma ação quando a sua app ou processo infantil para a sua aplicação está a consumir elevados recursos cpu. Pode definir as suas próprias regras de limiar de CPU para atenuar temporariamente um problema elevado de CPU até que a verdadeira causa para o problema inesperado seja encontrada. Para mais informações, consulte [atenuar os seus problemas de CPU antes de acontecerem](https://azure.github.io/AppService/2019/10/07/Mitigate-your-CPU-problems-before-they-even-happen.html).

![Monitorização proactiva do CPU](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="auto-healing-and-proactive-auto-healing"></a>Auto-cura e cura automática proactiva

A cicatrização automática é uma ação de mitigação que pode tomar quando a sua aplicação está a ter um comportamento inesperado. Pode definir as suas próprias regras com base na contagem de pedidos, pedido lento, limite de memória e código de estado HTTP para desencadear ações de mitigação. Utilize a ferramenta para atenuar temporariamente um comportamento inesperado até encontrar a causa da raiz. Para mais informações, consulte Anunciando a nova experiência de cura automática em diagnósticos de [serviçode aplicações.](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html)

![Auto-cura](./media/app-service-diagnostics/auto-healing-10.png)

Tal como a monitorização proactiva do CPU, a auto-cura proativa é uma solução chave-na-mão para atenuar comportamentos inesperados da sua aplicação. A cura automática proactiva reinicia a sua aplicação quando o Serviço de Aplicações determina que a sua aplicação se encontra num estado irrecuperável. Para mais informações, consulte A introdução proactiva de [auto-cura](https://azure.github.io/AppService/2017/08/17/Introducing-Proactive-Auto-Heal.html).

## <a name="navigator-and-change-analysis-only-for-windows-app"></a>Navegador e alteração de análise (apenas para aplicação Windows)

Numa grande equipa com integração contínua e onde a sua app tem muitas dependências, pode ser difícil identificar a mudança específica que causa um comportamento pouco saudável. A Navigator ajuda a obter visibilidade na topologia da sua aplicação, tornando automaticamente um mapa de dependência da sua app e todos os recursos na mesma subscrição. A Navigator permite-lhe visualizar uma lista consolidada de alterações feitas pela sua app e as suas dependências e reduzir-se a uma mudança que causa comportamentos pouco saudáveis. Pode ser acedido através do **navegador** de azulejos inicial e precisa de ser ativado antes de usá-lo pela primeira vez. Para mais informações, consulte [Obter visibilidade nas dependências da sua aplicação com](https://azure.github.io/AppService/2019/08/06/Bring-visibility-to-your-app-and-its-dependencies-with-Navigator.html)o Navigator .

![Página padrão do navegador](./media/app-service-diagnostics/navigator-default-page-11.png)

![Vista difusa](./media/app-service-diagnostics/diff-view-12.png)

A análise de alterações de alterações de alterações de aplicações pode ser acedida através de atalhos de azulejos, **alterações de aplicações** e falhas de **aplicação** na **Disponibilidade e Desempenho** para que possa utilizá-la simultaneamente com outras métricas. Antes de utilizar a funcionalidade, tem de a ativar primeiro. Para mais informações, consulte Anunciando a nova experiência de análise de [alterações em Diagnósticos](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html)de Serviços de Aplicações.

Publique as suas perguntas ou feedback no [UserVoice](https://feedback.azure.com/forums/169385-web-apps) adicionando "[Diag]" no título.
