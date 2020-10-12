---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 06/03/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 2a35180e4c5ca26d53be135718e345f9657af6a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88655195"
---
| **Recurso** | **Limite predefinido** | **Limite máximo** |
| --- | --- | --- |
| Contas Azure Batch por região por subscrição | 1-3 |50 |
| Núcleos dedicados por conta Batch | 90-900 | Contactar o suporte |
| Núcleos de baixa prioridade por conta batch | 10-100 | Contactar o suporte |
| **[Empregos ativos](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** e horários de trabalho por conta batch (os empregos**concluídos** não têm limite) | 100-300 | 1.000<sup>1</sup> |
| Conjuntos por conta do Batch | 20-100 | 500<sup>1</sup> |

<sup>1</sup> Para solicitar um aumento para além deste limite, contacte o Suporte Azure.

> [!IMPORTANT]
> Estamos a mudar a forma como solicita e gere quota dedicada.  O total de vCPUs dedicados é o valor atualmente aplicado, mas em breve iremos impor quotas dedicadas por série VM. A quota de baixa prioridade continuará a ser aplicada com base no limite total; não será aplicada pela série VM.

> [!NOTE]
> Os limites predefinidos variam consoante o tipo de subscrição que utiliza para criar uma conta Batch. As quotas de núcleos apresentadas são para contas Batch no modo de serviço Batch. [Veja as quotas na sua conta Batch.](../articles/batch/batch-quota-limit.md#view-batch-quotas)

> [!IMPORTANT]
> Para nos ajudar a gerir melhor a capacidade durante a pandemia global de saúde, as quotas fundamentais padrão para novas contas de Lote em algumas regiões e para alguns tipos de subscrição foram reduzidas do intervalo de valores acima, em alguns casos para núcleos zero. Quando criar uma nova conta Batch, [verifique a sua quota principal](../articles/batch/batch-quota-limit.md#view-batch-quotas) e [solicite um aumento de quota de base,](../articles/batch/batch-quota-limit.md#increase-a-quota)se necessário. Em alternativa, considere reutilizar as contas do Batch que já tenham quota suficiente. 
