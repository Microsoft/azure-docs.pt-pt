---
title: Limites do serviço
titleSuffix: Azure Digital Twins
description: Gráfico mostrando os limites do serviço Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 05/05/2020
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 46f378baad51e959f8b3c074cc24e5bbdfdd95d4
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389609"
---
# <a name="azure-digital-twins-service-limits"></a>Limites de serviço Azure Digital Twins

Estes são os limites de serviço da Azure Digital Twins.

> [!NOTE]
> Algumas áreas deste serviço têm limites ajustáveis. Isto está representado nas tabelas abaixo com a coluna *ajustável?* Quando o limite pode ser ajustado, o valor *ajustável* é *Sim*.
>
> Se a sua empresa necessitar de elevar um limite ou quota ajustável acima do limite de incumprimento, pode solicitar recursos adicionais [abrindo um bilhete de apoio.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="limits-by-type"></a>Limites por tipo

[!INCLUDE [Azure Digital Twins limits](../../includes/digital-twins-limits.md)]

## <a name="working-with-limits"></a>Trabalhar com limites

Quando um limite é atingido, o serviço acelera pedidos adicionais. Isto resultará numa resposta de erro de 404 destes pedidos.

Para gerir isto, eis algumas recomendações para trabalhar com limites.
* **Use a lógica de relemgar.** Os [SDKs Azure Digital Twins](how-to-use-apis-sdks.md) implementam a lógica de relembaraçamento para pedidos falhados, por isso, se estiver a trabalhar com um SDK fornecido, este já está incorporado. Caso contrário, considere implementar a lógica de relagem na sua própria aplicação. O serviço envia um `Retry-After` cabeçalho na resposta de avaria, que pode usar para determinar quanto tempo esperar antes de voltar a tentar.
* **Utilize limiares e notificações para alertar para a aproximação dos limites.** Alguns dos limites de serviço para a Azure Digital Twins têm [métricas correspondentes](troubleshoot-metrics.md) que podem ser usadas para acompanhar o uso nestas áreas. Para configurar limiares e configurar um alerta em qualquer métrica quando um limiar é abordado, consulte as instruções em [*Resolução de Problemas: Configurar alertas*](troubleshoot-alerts.md). Para configurar notificações para outros limites onde as métricas não são fornecidas, considere implementar esta lógica no seu próprio código de aplicação.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o lançamento atual da Azure Digital Twins na visão geral do serviço:
* [*Visão geral: O que é Azure Digital Twins?*](overview.md)
