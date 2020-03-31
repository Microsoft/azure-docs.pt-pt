---
title: Compreender o uso de aplicações móveis e o comportamento do utilizador com o Visual Studio App Center e os serviços Azure
description: Conheça os serviços como o App Center que o ajudam a tomar decisões inteligentes de negócios, compreendendo como os utilizadores usam a sua aplicação móvel.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 4dc3cea4497c55dda0d8da2ca29201615dadff19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241084"
---
# <a name="analyze-and-understand-mobile-application-use"></a>Analisar e compreender o uso de aplicações móveis
Quão bem entende como os seus utilizadores utilizam as suas aplicações? Quantos utilizadores ativos a sua aplicação tem, e como o uso está a mudar ao longo do tempo? Que funcionalidades estão a usar e quais são mais utilizadas? Onde estão sediados estes utilizadores? Quantos utilizadores estão a utilizar a versão mais recente da aplicação? Todas estas questões são importantes para entender para transformar a sua app num negócio de sucesso. Para responder a este tipo de questões de análise de uso, precisa de recolher dados de utilização das suas apps.

Quanto mais olhar para os dados, mais poderá encontrar formas de melhorar a sua aplicação e manter os seus utilizadores felizes. É importante usar dados para encontrar insights atuais e manter os utilizadores satisfeitos.

## <a name="importance-of-analytics"></a>Importância da análise
- Compreenda os seus utilizadores, como interagem com a sua aplicação e o que os traz de volta para afinar a sua aplicação e proporcionar grandes experiências para fazer crescer o seu negócio.
- Acompanhe as suas métricas de utilização para tomar decisões informadas sobre como comercializar a sua aplicação e servir melhor os seus clientes.
- Meça o desempenho da sua candidatura.
- Saiba quais as partes da sua aplicação, valor e desempenho.
- Obtenha informações baseadas em dados sobre questões que dizem respeito à agitação e retenção.

Utilize os seguintes serviços para permitir a análise de aplicações móveis.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[O App Center Analytics](/appcenter/analytics/) permite-lhe crescer o seu público focando-se no que é importante. Oferece relatórios profundos e insights sobre sessões de utilizadores, dispositivos de topo, versões de SO e análise comportamental. Facilmente crie eventos personalizados para rastrear qualquer coisa com análise extensiva de aplicações.

   **Principais funcionalidades**
   - Acompanhe gratuitamente os padrões de utilização, a adoção do utilizador e outras métricas de envolvimento.
   - Identifique tendências, comportamento do utilizador e envolvimento através de eventos personalizados.
   - Obtenha métricas fora da caixa e informações detalhadas sobre o uso da aplicação (diariamente, semanalmente, mensalmente), sessões, propriedades do dispositivo e demografia do utilizador num único dashboard.
   - Exporte continuamente todos os dados do Seu App Center Analytics para o Azure para retenção ilimitada. O App Center Analytics apoia a exportação para o armazenamento azure blob e para a Azure Application Insights.
   - Integre com insights de aplicação Azure para insights ainda mais profundos, tais como retenção, análise de funil e coortes.
   - Utilize a integração sDK de uma linha para começar dentro de minutos.
   - Ganhe suporte de plataforma para iOS, Android, macOS, tvOS, Xamarin, React Native, Unity e Cordova.

   **Referências**
   - [Inscreva-se no App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
   - [Começar com app center Analytics](/appcenter/analytics/)

## <a name="azure-monitor"></a>Azure Monitor
O Azure Monitor inclui [os Application Insights,](/azure/azure-monitor/app/app-insights-overview)que fornece ferramentas para recolher e analisar a telemetria para maximizar o desempenho e monitorizar a sua aplicação móvel. Pode aproveitar as Informações de Aplicação utilizando o App Center Analytics para configurar a exportação para A aplicação Insights. Aplicações Insights podem consultar, segmentar, filtrar e analisar a telemetria de eventos personalizados a partir das suas aplicações, além das ferramentas de análise que o App Center fornece.

**Principais funcionalidades**
   - Consulte a telemetria dos eventos personalizados.
   - Filtrar telemetria de eventos com poderosas capacidades de segmentação.
   - Analise os padrões de conversão, retenção e navegação na sua aplicação. Pode utilizar:
     - Funis para analisar e monitorizar as taxas de conversão.
     - Retenção para analisar a forma como a sua aplicação mantém os utilizadores ao longo do tempo.
     - Livros de trabalho para combinar visualizações e texto num relatório partilhável.
     - Coortes para nomear e guardar grupos específicos de utilizadores ou eventos para que possam ser facilmente referenciados a partir de outras ferramentas de análise.

**Referências**
- [Portal Azure](https://portal.azure.com/)
- [Analise a sua aplicação móvel com o App Center e os Insights de Aplicação](/azure/azure-monitor/learn/mobile-center-quickstart)
- [Use app center Analytics com insights de aplicação](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>Azure PlayFab
[O Azure PlayFab](https://playfab.com/) oferece uma plataforma completa de back-end com serviços de jogo, análise em tempo real e LiveOps que precisas para criar jogos ligados à nuvem de classe mundial. Estes serviços reduzem as barreiras ao lançamento para os desenvolvedores de jogos. Oferecem soluções de desenvolvimento rentáveis e de grande dimensão nos estúdios que escalam com os seus jogos. Os serviços podem ajudar os estúdios a envolver, reter e rentabilizar os jogadores. Com o PlayFab, os desenvolvedores podem usar a nuvem inteligente para construir e operar jogos, analisar dados de jogos e melhorar as experiências globais de jogo.

**Principais funcionalidades**
   - Monitorize os dashboards em tempo real.
   - Avalie o desempenho do seu jogo através das métricas de topo.
   - Reveja os resumos do desempenho diário e mensal do seu jogo através de relatórios autogerados. Pode ver os relatórios no Game Manager e transferi-los diariamente para a sua caixa de entrada.
   - Utilize testes A/B para executar experiências e determinar a definição ideal para uma determinada variável.
   - Utilize a segmentação para os jogadores definirem agrupamentos automatizados de jogadores.
    
**Referências**
- [Portal PlayFab](https://developer.playfab.com/en-US/sign-up)
- [Análise](/gaming/playfab/#pivot=documentation&panel=analytics)
- [Arranques rápidos](/gaming/playfab/#pivot=documentation&panel=quickstarts) 
