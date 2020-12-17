---
title: incluir ficheiro
description: incluir ficheiro
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 12/16/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 0a94b122f1cdd598eeac553c8cc784d2a0a5369f
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97614523"
---
| **Recurso** | **Limite predefinido** | **Limite máximo** |
| --- | --- | --- |
| Contas Azure Batch por região por subscrição | 1-3 |50 |
| Núcleos dedicados por conta Batch | 90-900 | Contactar o suporte |
| Núcleos de baixa prioridade por conta batch | 10-100 | Contactar o suporte |
| **[Empregos ativos](/rest/api/batchservice/job/get#jobstate)** e horários de trabalho por conta batch (os empregos **concluídos** não têm limite) | 100-300 | 1.000<sup>1</sup> |
| Conjuntos por conta do Batch | 20-100 | 500<sup>1</sup> |

<sup>1</sup> Para solicitar um aumento para além deste limite, contacte o Suporte Azure.

> [!NOTE]
> Os limites predefinidos variam consoante o tipo de subscrição que utiliza para criar uma conta Batch. As quotas de núcleos apresentadas são para contas Batch no modo de serviço Batch. [Veja as quotas na sua conta Batch.](../articles/batch/batch-quota-limit.md#view-batch-quotas)

> [!IMPORTANT]
> Para nos ajudar a gerir melhor a capacidade durante a pandemia global de saúde, as quotas fundamentais padrão para novas contas de Lote em algumas regiões e para alguns tipos de subscrição foram reduzidas do intervalo de valores acima, em alguns casos para núcleos zero. Quando criar uma nova conta Batch, [verifique a sua quota principal](../articles/batch/batch-quota-limit.md#view-batch-quotas) e [solicite um aumento de quota de base,](../articles/batch/batch-quota-limit.md#increase-a-quota)se necessário. Em alternativa, considere reutilizar as contas do Batch que já tenham quota suficiente.