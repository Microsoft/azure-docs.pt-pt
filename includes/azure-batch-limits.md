---
title: incluir ficheiro
description: incluir ficheiro
services: batch
author: LauraBrenner
ms.service: batch
ms.topic: include
ms.date: 05/28/2019
ms.author: labrenne
ms.custom: include file
ms.openlocfilehash: a0d74aa38dffdd41fbe617066391ef271a507349
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81734680"
---
| **Recurso** | **Limite predefinido** | **Limite máximo** |
| --- | --- | --- |
| Contas do Lote Azure por região por subscrição | 1-3 |50 |
| Núcleos dedicados por conta Batch | 90-900 | Contactar o suporte |
| Núcleos de baixa prioridade por conta Batch | 10-100 | Contactar o suporte |
| **[Empregos ativos](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** e horários de trabalho por conta Batch **(empregos concluídos** não têm limite) | 100-300 | 1.000<sup>1</sup> |
| Conjuntos por conta do Batch | 20-100 | 500<sup>1</sup> |

<sup>1</sup> Para solicitar um aumento para além deste limite, contacte o Suporte Azure.

> [!NOTE]
> Os limites de predefinição variam consoante o tipo de subscrição que utiliza para criar uma conta Batch. As quotas de cores apresentadas são para contas de Lote no modo de serviço batch. [Consulte as quotas na sua conta Batch](../articles/batch/batch-quota-limit.md#view-batch-quotas).

> [!IMPORTANT]
> Para nos ajudar a gerir melhor a capacidade durante a pandemia global de saúde, as quotas centrais padrão para novas contas do Lote em algumas regiões e para alguns tipos de subscrição foram reduzidas do intervalo acima de valores, em alguns casos para zero núcleos. Quando criar uma nova conta De Lote, [verifique a sua quota central](../articles/batch/batch-quota-limit.md#view-batch-quotas) e [solicite um aumento de quota central,](../articles/batch/batch-quota-limit.md#increase-a-quota)se necessário. 
