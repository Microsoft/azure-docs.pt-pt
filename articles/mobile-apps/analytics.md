---
title: Entender o uso do aplicativo móvel e o comportamento do usuário com Visual Studio App Center e serviços do Azure
description: Saiba mais sobre os serviços como App Center que ajudam a tomar decisões de negócios inteligentes ao entender como os usuários usam seu aplicativo móvel.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: ff25df55e8489819ca9400205cdea1b8ceb8bf80
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454539"
---
# <a name="analyze-and-understand-mobile-application-use"></a>Analisar e entender o uso do aplicativo móvel
Quão bem você entende como os usuários usam seus aplicativos? Quantos usuários ativos seu aplicativo tem e como o uso é alterado ao longo do tempo? Quais recursos eles estão usando e quais são mais usados? Onde esses usuários são baseados? Quantos usuários estão usando a versão mais recente do aplicativo? Todas essas perguntas são importantes para se entender para transformar seu aplicativo em um negócio bem-sucedido. Para responder a esses tipos de perguntas de análise de uso, você precisa coletar dados de uso de seus aplicativos.

Quanto mais você examinar os dados, mais você poderá encontrar maneiras de melhorar seu aplicativo e manter seus usuários satisfeitos. É importante usar dados para encontrar informações acionáveis e manter os usuários satisfeitos.

## <a name="importance-of-analytics"></a>Importância da análise
- Entenda seus usuários, como eles interagem com seu aplicativo e o que os traz de volta para ajustar seu aplicativo e fornecer excelentes experiências para aumentar seu negócio.
- Acompanhe suas métricas de uso para tomar decisões informadas sobre como comercializar seu aplicativo e atender melhor aos seus clientes.
- Meça o desempenho do aplicativo.
- Saiba quais partes do seu aplicativo o valor e o desempenho da unidade.
- Obter informações controladas por dados sobre problemas que se preocupam com a rotatividade e a retenção.

Use os serviços a seguir para habilitar a análise de aplicativos móveis.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
O [app Center Analytics](/appcenter/analytics/) permite que você aumente seu público concentrando-se no que é importante. Ele oferece relatórios e informações detalhadas sobre sessões de usuário, principais dispositivos, versões de sistema operacional e análise comportamental. Crie eventos personalizados com facilidade para controlar qualquer coisa com análise extensiva de aplicativos.

   **Principais funcionalidades**
   - Acompanhe os padrões de uso, a adoção do usuário e outras métricas de envolvimento gratuitamente.
   - Identifique tendências, o comportamento do usuário e o envolvimento por meio de eventos personalizados.
   - Obtenha métricas integradas e informações detalhadas sobre o uso do aplicativo (diário, semanal, mensal), sessões, propriedades do dispositivo e dados demográficos do usuário em um único painel.
   - Exporte continuamente todos os dados do App Center Analytics para o Azure para retenção ilimitada. O App Center Analytics dá suporte à exportação para o armazenamento de BLOBs do Azure e Aplicativo Azure insights.
   - Integre-se com o Aplicativo Azure insights para obter informações ainda mais aprofundadas, como retenção, análise de funil e coortes.
   - Use a integração do SDK de uma linha para começar em minutos.
   - Obter suporte de plataforma para iOS, Android, macOS, tvOS, Xamarin, reagir nativo, Unity e Cordova.

   **Referências**
   - [Inscreva-se com App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
   - [Introdução ao App Center Analytics](/appcenter/analytics/)

## <a name="azure-monitor"></a>Monitor do Azure
O Azure Monitor inclui [Application insights](/azure/azure-monitor/app/app-insights-overview), que fornece ferramentas para coletar e analisar a telemetria para maximizar o desempenho e monitorar seu aplicativo móvel. Você pode aproveitar Application Insights usando o App Center Analytics para configurar a exportação para Application Insights. Application Insights pode consultar, segmentar, filtrar e analisar a telemetria de eventos personalizados de seus aplicativos, além das ferramentas de análise que o App Center fornece.

**Principais funcionalidades**
   - Consulte sua telemetria de evento personalizado.
   - Filtre a telemetria de eventos com recursos avançados de segmentação.
   - Analise os padrões de conversão, retenção e navegação em seu aplicativo. Você pode usar:
     - Funils para analisar e monitorar as taxas de conversão.
     - Retenção para analisar o quão bem seu aplicativo retém os usuários ao longo do tempo.
     - Pastas de trabalho para combinar visualizações e texto em um relatório compartilhável.
     - Coortes para nomear e salvar grupos específicos de usuários ou eventos para que eles possam ser facilmente referenciados de outras ferramentas de análise.

**Referências**
- [Portal do Azure](https://portal.azure.com/)
- [Analise seu aplicativo móvel com App Center e Application Insights](/azure/azure-monitor/learn/mobile-center-quickstart)
- [Use a análise de App Center com Application Insights](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>Azure PlayFab
O [Azure PlayFab](https://playfab.com/) oferece uma plataforma de back-end completa com serviços de jogos, análise em tempo real e LiveOps de que você precisa para criar jogos conectados em nuvem de nível mundial. Esses serviços reduzem as barreiras de lançamento para desenvolvedores de jogos. Eles oferecem soluções de desenvolvimento econômicas de estúdios de grande e pequena porte que são dimensionadas com seus jogos. Os serviços podem ajudar estúdios a envolver, reter e monetizar players. Com o PlayFab, os desenvolvedores podem usar a nuvem inteligente para criar e operar jogos, analisar dados de jogos e melhorar as experiências gerais de jogos.

**Principais funcionalidades**
   - Monitore painéis em tempo real.
   - Avalie o desempenho do jogo por meio de métricas principais.
   - Examine resumos do desempenho diário e mensal de seu jogo por meio de relatórios gerados automaticamente. Você pode exibir os relatórios no Gerenciador de jogos e fazer com que eles sejam baixados ou entregues na sua caixa de entrada diariamente.
   - Use testes a/B para executar experimentos e determinar a configuração ideal para uma variável específica.
   - Use segmentação para os jogadores para definir agrupamentos automatizados de players.
    
**Referências**
- [Portal do PlayFab](https://developer.playfab.com/en-US/sign-up)
- [Análise](/gaming/playfab/#pivot=documentation&panel=analytics)
- [Inícios rápidos](/gaming/playfab/#pivot=documentation&panel=quickstarts) 
