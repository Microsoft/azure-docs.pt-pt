---
title: Compreender o uso da aplicação móvel e o comportamento do utilizador com o Visual Studio App Center e os serviços Azure
description: Conheça os serviços como o App Center que o ajudam a tomar decisões de negócios inteligentes, compreendendo como os utilizadores usam a sua aplicação móvel.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: ebe07ec76e5b852dbe7d030ad8859d59ce5cd074
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/05/2020
ms.locfileid: "84451061"
---
# <a name="analyze-and-understand-mobile-application-use"></a>Analisar e compreender o uso de aplicações móveis
Até que ponto compreende como os seus utilizadores utilizam as suas aplicações? Quantos utilizadores ativos tem a sua aplicação e como é que o uso está a mudar ao longo do tempo? Quais as características que estão a usar e quais são as que são mais utilizadas? Onde estão os utilizadores? Quantos utilizadores estão a utilizar a versão mais recente da aplicação? Todas estas questões são importantes para entender para transformar a sua app num negócio de sucesso. Para responder a este tipo de questões de análise de uso, precisa de recolher dados de utilização das suas apps.

Quanto mais olhar para os dados, mais poderá encontrar formas de melhorar a sua aplicação e manter os seus utilizadores felizes. É importante usar os dados para encontrar insights accuáveis e manter os utilizadores satisfeitos.

## <a name="importance-of-analytics"></a>Importância da análise
- Compreenda os seus utilizadores, como interagem com a sua aplicação, e o que os traz de volta para afinar a sua aplicação e proporcionar grandes experiências para fazer crescer o seu negócio.
- Acompanhe as suas métricas de utilização para tomar decisões informadas sobre como comercializar a sua aplicação e servir melhor os seus clientes.
- Meça o desempenho da sua candidatura.
- Saiba quais as partes do valor e desempenho da unidade de aplicação.
- Obtenha insights orientados por dados em questões que dizem respeito a agitação e retenção.

Utilize os seguintes serviços para permitir a análise de aplicações móveis.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[O App Center Analytics](/appcenter/analytics/) permite-lhe aumentar o seu público focando-se no que é importante. Oferece relatórios profundos e insights sobre sessões de utilizadores, dispositivos de topo, versões de SO e análise comportamental. Crie facilmente eventos personalizados para rastrear qualquer coisa com análise de aplicações extensivas.

   **Principais funcionalidades**
   - Acompanhe os padrões de utilização, a adoção do utilizador e outras métricas de envolvimento gratuitamente.
   - Identificar tendências, comportamento do utilizador e envolvimento através de eventos personalizados.
   - Obtenha métricas fora da caixa e informações detalhadas sobre o uso da aplicação (diariamente, semanalmente, mensal), sessões, propriedades do dispositivo e demografia do utilizador num único dashboard.
   - Exporte continuamente todos os seus dados do App Center Analytics para o Azure para uma retenção ilimitada. O App Center Analytics suporta a exportação para o armazenamento de Azure Blob e para a Azure Application Insights.
   - Integre com a Azure Application Insights para insights ainda mais profundos, tais como retenção, análise de funil e coortes.
   - Utilize a integração SDK de uma linha para começar dentro de minutos.
   - Ganhe suporte à plataforma para iOS, Android, macOS, tvOS, Xamarin, React Native, Unity e Cordova.

   **Referências**
   - [Inscreva-se no App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
   - [Começar com App Center Analytics](/appcenter/analytics/)

## <a name="azure-monitor"></a>Azure Monitor
O Azure Monitor inclui [o Application Insights,](/azure/azure-monitor/app/app-insights-overview)que fornece ferramentas para recolher e analisar telemetria para maximizar o desempenho e monitorizar a sua aplicação móvel. Pode aproveitar o Application Insights utilizando o App Center Analytics para configurar a exportação para o Application Insights. O Application Insights pode consultar, segmentar, filtrar e analisar a telemetria de eventos personalizados a partir das suas aplicações, para além das ferramentas de análise que o App Center fornece.

**Principais funcionalidades**
   - Consulte a telemetria dos eventos personalizados.
   - Filtrar a telemetria do evento com capacidades de segmentação poderosas.
   - Analise os padrões de conversão, retenção e navegação na sua aplicação. Pode utilizar:
     - Funis para analisar e monitorizar as taxas de conversão.
     - Retenção para analisar o quão bem a sua aplicação retém os utilizadores ao longo do tempo.
     - Livros de trabalho para combinar visualizações e texto num relatório partilhável.
     - Coortes para nomear e salvar grupos específicos de utilizadores ou eventos para que possam ser facilmente referenciados a partir de outras ferramentas de análise.

**Referências**
- [Portal do Azure](https://portal.azure.com/)
- [Analise a sua aplicação móvel com App Center e Application Insights](/azure/azure-monitor/learn/mobile-center-quickstart)
- [Use App Center Analytics com Insights de Aplicações](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>Azure PlayFab
[O Azure PlayFab](https://playfab.com/) oferece uma plataforma completa de back-end com serviços de jogos, análises em tempo real e LiveOps que precisas para criar jogos ligados à nuvem de classe mundial. Estes serviços reduzem as barreiras ao lançamento para os desenvolvedores de jogos. Oferecem soluções de desenvolvimento de grandes e pequenos estúdios que escalam com os seus jogos. Os serviços podem ajudar os estúdios a envolver, reter e rentabilizar os jogadores. Com o PlayFab, os desenvolvedores podem usar a nuvem inteligente para construir e operar jogos, analisar dados de jogos e melhorar experiências de jogo em geral.

**Principais funcionalidades**
   - Monitorize os painéis em tempo real.
   - Avalie o desempenho do seu jogo através de métricas de topo.
   - Reveja resumos do desempenho diário e mensal do seu jogo através de relatórios autogerados. Pode ver os relatórios no Game Manager e descarregá-los ou ser entregues diariamente na sua caixa de entrada.
   - Utilize testes A/B para executar experiências e determine a configuração ideal para uma determinada variável.
   - Utilize a segmentação para os jogadores definirem agrupamentos automatizados de jogadores.
    
**Referências**
- [Portal PlayFab](https://developer.playfab.com/en-US/sign-up)
- [Análise](/gaming/playfab/#pivot=documentation&panel=analytics)
- [Guias de Início Rápido](/gaming/playfab/#pivot=documentation&panel=quickstarts) 
