---
title: Executar tarefas de fundo com WebJobs
description: Aprenda a usar WebJobs para executar tarefas de fundo no Azure App Service. Escolha entre uma variedade de formatos de script e execute-os com expressões CRON.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ms.reviewer: msangapu;suwatch;pbatum;naren.soni
ms.custom: seodec18
zone_pivot_groups: app-service-webjob
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 0d49323e2bc3c0522b1fb9ad49ffcc14f476e2dc
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452803"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Executar tarefas de fundo com WebJobs no Azure App Service

O conceito de executar tarefas de fundo no Azure é fornecido com [trabalhos](./webjobs-create-ieux-conceptual.md) web de serviço da Azure App. Saiba como implementar <abbr title="Um programa ou script no mesmo caso de uma aplicação web, app API ou aplicativo móvel.">WebJobs</abbr> utilizando o [portal Azure](https://portal.azure.com) para carregar um executável ou script. 

Três WebJobs suportados incluem:

* **Continuando:** Começa imediatamente, normalmente a correr num ciclo interminável.
* **Agendado**: Começa a partir do gatilho programado
* **Manual**: Começa a partir do gatilho manual

::: zone pivot="webjob-type-continuous"

[!INCLUDE [Continuous](./includes/webjobs-create-ieux-continuous.md)]

::: zone-end

::: zone pivot="webjob-type-scheduled"

[!INCLUDE [Scheduled](./includes/webjobs-create-ieux-scheduled.md)]

::: zone-end

::: zone pivot="webjob-type-manual"

[!INCLUDE [Manual](./includes/webjobs-create-ieux-manual.md)]

::: zone-end
   
## <a name="next-steps"></a><a name="NextSteps"></a> Próximos passos

* [Saiba mais sobre tarefas de fundo como webjobs](./webjobs-create-ieux-conceptual.md)
* [Ver histórico de registo de WebJobs](./webjobs-create-ieux-view-log.md)

* Utilize o [WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) para simplificar muitas tarefas de programação

* Aprenda a [desenvolver e implementar WebJobs com Visual Studio](webjobs-dotnet-deploy-vs.md)
