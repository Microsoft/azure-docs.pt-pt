---
title: Entender o uso do aplicativo móvel e o comportamento do usuário com Visual Studio App Center e serviços do Azure
description: Saiba mais sobre os serviços como App Center que ajudam a tomar decisões de negócios inteligentes ao entender como os usuários usam seu aplicativo móvel.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: d60e9437a2f33788eb183cfcad0f3a10d71fb79d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795799"
---
# <a name="analyze-and-understand-usage-of-mobile-applications"></a>Analisar e entender o uso de aplicativos móveis
Quão bem você entende como os usuários usam seus aplicativos? Quantos usuários ativos seu aplicativo tem e como o uso é alterado ao longo do tempo? Quais recursos eles estão usando e quais são mais usados? Onde esses usuários são baseados? Quantos usuários estão usando a versão mais recente do aplicativo? Todas essas perguntas são importantes para se entender para transformar seu aplicativo em um negócio bem-sucedido. Para responder a esses tipos de perguntas de análise de uso, você precisa coletar dados de uso de seus aplicativos.

Quanto mais você examinar os dados, mais você encontrará maneiras de melhorar seu aplicativo e manter seus usuários satisfeitos. É importante usar dados para encontrar informações acionáveis e manter os usuários satisfeitos.

## <a name="importance-of-analytics"></a>Importância da análise
- **Entenda** seus usuários, como eles interagem com seu aplicativo e o que os traz de volta para ajustar seu aplicativo e fornecer excelentes experiências para aumentar seu negócio.
- **Acompanhe** suas métricas de uso para tomar decisões informadas sobre como comercializar seu aplicativo e atender melhor ao seu cliente.
- **Meça** o desempenho do aplicativo.
- **Saiba** quais partes do seu aplicativo impulsionam o valor e o desempenho.
- **Informações controladas por dados** sobre problemas relacionados à rotatividade e retenção.

Use os serviços a seguir para habilitar a análise de aplicativos móveis.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
O [app Center Analytics](/appcenter/analytics/) permite que você aumente seu público concentrando-se no que é importante, com relatórios e informações aprofundadas sobre sessões de usuário, principais dispositivos, versões de sistema operacional e análise comportamental. Crie eventos personalizados com facilidade para controlar qualquer coisa com análise extensiva de aplicativos.

   **Principais recursos**
   - **Acompanhe** os padrões de uso, a adoção do usuário e outras métricas de envolvimento gratuitamente.
   - **Identifique** tendências, o comportamento do usuário e o envolvimento por meio de eventos personalizados.
   - **Métricas integradas** e **informações detalhadas** sobre o uso do aplicativo (diário, semanal, mensal), sessões, propriedades do dispositivo e dados demográficos do usuário em um **único painel**.
   - **Exporte continuamente todos os dados de análise para o Azure** para retenção ilimitada. Dá suporte à exportação para o armazenamento de BLOBs do Azure e informações de Aplicativo Azure.
   - **Integre-se com o aplicativo Azure insights** para obter informações ainda mais aprofundadas, como retenção, análise de funil e coortes
   - **Integração do SDK de uma linha** para começar em minutos.
   - **Suporte a plataforma** -Ios, Android, MacOS, TvOS, Xamarin, reagir nativo, Unity, Cordova.

   **Referências**
   - [Inscreva-se com App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
   - [Introdução ao App Center Analytics](/appcenter/analytics/)

## <a name="azure-monitor"></a>Monitor do Azure
Azure Monitor inclui [Application insights](/azure/azure-monitor/app/app-insights-overview) que fornece ferramentas para coletar e analisar a telemetria para maximizar o desempenho e monitorar seu aplicativo móvel. Você pode aproveitar Application Insights usando App Center Analytics e configurar a exportação para Application Insights. Application Insights pode consultar, segmentar, filtrar e analisar a telemetria de eventos personalizados de seus aplicativos, além das ferramentas de análise que o App Center fornece.

**Principais recursos**
   - **Consulte** sua telemetria de evento personalizado.
   - **Filtre** a telemetria de eventos com recursos avançados de segmentação.
   - **Analise** os padrões de conversão, retenção e navegação em seu aplicativo.
     - **Funis**, para analisar e monitorizar as taxas de conversão.
     - **Retenção** para analisar o quão bem seu aplicativo retém os usuários ao longo do tempo.
     - **Livros**, para combinar visualizações e textos num relatório partilhável.
     - **Coortes**, para dar o nome e guardar grupos de utilizadores ou eventos específicos, para que outras ferramentas de análise possam referenciá-los facilmente.

**Referências**
- [Portal do Azure](https://portal.azure.com/)
- [Analise seu aplicativo móvel com App Center e Application Insights](/azure/azure-monitor/learn/mobile-center-quickstart)
- [Análise de uso com Application Insights](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>PlayFab do Azure
O [Azure PlayFab](https://playfab.com/) oferece uma plataforma completa de back-end com serviços de jogos, análise em tempo real e LiveOps que você precisa para criar jogos conectados em nuvem de classe internacional. Esses serviços reduzem as barreiras de lançamento para desenvolvedores de jogos, oferecendo soluções de desenvolvimento econômicas de grande e pequena estúdios que são dimensionadas com seus jogos e as ajudam a entrar, reter e monetizar jogadores. O PlayFab permite que os desenvolvedores usem a nuvem inteligente para criar e operar jogos, analisar dados de jogos e melhorar as experiências gerais de jogos.

**Principais recursos**
   - **Monitore** painéis em tempo real.
   - **Avalie** o desempenho do jogo por meio de métricas principais.
   - **Relatório** para examinar resumos do desempenho diário e mensal de seu jogo por meio de relatórios gerados automaticamente que podem ser exibidos no gerente de jogos e baixados ou entregues em sua caixa de entrada diariamente.
   - **Teste a/B** para executar experimentos e determinar a configuração ideal para uma variável específica.
   - **Segmentação** para players permite que você defina agrupamentos automatizados de players.
    
**Referências**
- [Portal do PlayFab](https://developer.playfab.com/en-US/sign-up)
- [Análise](/gaming/playfab/#pivot=documentation&panel=analytics)
- [Inícios rápidos](/gaming/playfab/#pivot=documentation&panel=quickstarts)    
