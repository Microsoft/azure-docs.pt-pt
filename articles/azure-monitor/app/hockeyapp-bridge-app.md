---
title: Explorando dados do HockeyApp no Aplicativo Azure insights | Microsoft Docs
description: Analise o uso e o desempenho do seu aplicativo do Azure com o Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 03/30/2017
ms.openlocfilehash: b14cd38a1db6804a00883ded0b38511fa46c3a52
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819575"
---
# <a name="exploring-hockeyapp-data-in-application-insights"></a>Explorando dados do HockeyApp no Application Insights

> [!NOTE]
> O HockeyApp não está mais disponível para novos aplicativos. As implantações HockeyApp existentes continuarão funcionando. Visual Studio App Center agora é o serviço recomendado da Microsoft para monitorar novos aplicativos móveis. [Saiba como configurar seus aplicativos com app Center e Application insights](../../azure-monitor/learn/mobile-center-quickstart.md).

O [HockeyApp](https://azure.microsoft.com/services/hockeyapp/) é um serviço para monitorar aplicativos móveis e de área de trabalho ao vivo. Em HockeyApp, você pode enviar telemetria personalizada e de rastreamento para monitorar o uso e auxiliar no diagnóstico (além de obter dados de falha). Esse fluxo de telemetria pode ser consultado usando o poderoso recurso de [análise](../../azure-monitor/app/analytics.md) do [aplicativo Azure insights](../../azure-monitor/app/app-insights-overview.md). Além disso, você pode [exportar a telemetria personalizada e de rastreamento](export-telemetry.md). Para habilitar esses recursos, você configura uma ponte que retransmite dados personalizados HockeyApp para Application Insights.

## <a name="the-hockeyapp-bridge-app"></a>O aplicativo HockeyApp Bridge
O aplicativo HockeyApp Bridge é o principal recurso que permite que você acesse sua telemetria personalizada e de rastreamento do HockeyApp em Application Insights por meio dos recursos de análise e exportação contínua. Eventos personalizados e de rastreamento coletados por HockeyApp após a criação do aplicativo HockeyApp Bridge poderão ser acessados por esses recursos. Vejamos como configurar um desses aplicativos de ponte.

No HockeyApp, abra configurações de conta, [tokens de API](https://rink.hockeyapp.net/manage/auth_tokens). Crie um novo token ou reutilize um existente. Os direitos mínimos necessários são "somente leitura". Faça uma cópia do token da API.

![Obter um token de API do HockeyApp](./media/hockeyapp-bridge-app/01.png)

Abra o portal do Microsoft Azure e [crie um recurso de Application insights](../../azure-monitor/app/create-new-resource.md ). Defina o tipo de aplicativo como "aplicativo de ponte HockeyApp":

![Novo recurso de Application Insights](./media/hockeyapp-bridge-app/02.png)

Você não precisa definir um nome-isso será automaticamente definido a partir do nome do HockeyApp.

Os campos de ponte HockeyApp são exibidos. 

![Inserir campos de ponte](./media/hockeyapp-bridge-app/03.png)

Insira o token HockeyApp que você anotou anteriormente. Essa ação popula o menu suspenso "aplicativo HockeyApp" com todos os seus aplicativos HockeyApp. Selecione aquele que você deseja usar e conclua o restante dos campos. 

Abra o novo recurso. 

Observe que os dados demoram um pouco para começar a fluir.

![Application Insights recurso aguardando dados](./media/hockeyapp-bridge-app/04.png)

Já está! Os dados personalizados e de rastreamento coletados em seu aplicativo instrumentado por HockeyApp deste ponto em diante agora também estão disponíveis para você nos recursos de análise e exportação contínua do Application Insights.

Vamos examinar brevemente cada um desses recursos agora disponíveis para você.

## <a name="analytics"></a>Análise
O Analytics é uma ferramenta poderosa para consulta ad hoc de seus dados, permitindo que você diagnostique e analise sua telemetria e descubra rapidamente as causas e os padrões de raiz.

![Análise](./media/hockeyapp-bridge-app/05.png)

* [Saiba mais sobre a análise](../../azure-monitor/log-query/get-started-portal.md)

## <a name="continuous-export"></a>Exportação contínua
A exportação contínua permite que você exporte seus dados para um contêiner de armazenamento de BLOBs do Azure. Isso é muito útil se você precisar manter seus dados por mais tempo do que o período de retenção atualmente oferecido pelo Application Insights. Você pode manter os dados no armazenamento de BLOBs, processá-los em um banco de dados SQL ou em sua solução de data warehouse preferida.

[Saiba mais sobre a exportação contínua](export-telemetry.md)

## <a name="next-steps"></a>Passos seguintes
* [Aplicar análise aos seus dados](../../azure-monitor/log-query/get-started-portal.md)

