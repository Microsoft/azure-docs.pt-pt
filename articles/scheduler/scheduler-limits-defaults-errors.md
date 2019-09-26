---
title: Limites, cotas e limites no Agendador do Azure
description: Saiba mais sobre limites, cotas, valores padrão e limites de limitação para o Agendador do Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 88f4a3e9-6dbd-4943-8543-f0649d423061
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 293cd956f8270a4863fcc657f58c970096cec1e3
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300910"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Limites, cotas e limites de limitação no Agendador do Azure

> [!IMPORTANT]
> O [aplicativo lógico do Azure](../logic-apps/logic-apps-overview.md) está substituindo o Agendador do Azure, que está [sendo desativado](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Para continuar trabalhando com os trabalhos que você configurou no Agendador, [migre para o aplicativo lógico do Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md) assim que possível.

## <a name="limits-quotas-and-thresholds"></a>Limites, cotas e limites

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>cabeçalho x-MS-Request-ID

Cada solicitação feita no serviço de Agendador retorna um cabeçalho de resposta chamado **x-MS-Request-ID**. Esse cabeçalho contém um valor opaco que identifica exclusivamente a solicitação. Portanto, se uma solicitação falhar de forma consistente e você tiver confirmado que a solicitação está formatada corretamente, você poderá relatar o erro à Microsoft fornecendo o valor do cabeçalho de resposta **x-MS-Request-ID** e incluindo estes detalhes: 

* O valor **x-MS-Request-ID**
* A hora aproximada em que a solicitação foi feita 
* Os identificadores para a assinatura do Azure, a coleção de trabalhos e o trabalho 
* O tipo de operação que a solicitação tentou

## <a name="see-also"></a>Consulte também

* [O que é o Microsoft Azure Scheduler?](scheduler-intro.md)
* [Conceitos, terminologia e hierarquia de entidades do Azure Scheduler](scheduler-concepts-terms.md)
