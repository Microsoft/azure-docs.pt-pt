---
title: incluir ficheiro
description: incluir ficheiro
services: batch
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 10/11/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6b48dbfc33890df12209c3a242d812ad2103e07a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553794"
---
| **Recurso** | **Limite predefinido** | **Limite máximo** |
| --- | --- | --- |
| Contas de Batch do Azure por região por subscrição | 1-3 |50 |
| Núcleos dedicados por conta do Batch | 10-100 | N/A<sup>1</sup> |
| Núcleos de prioridade baixa por conta do Batch | 10-100 | N/A<sup>2</sup> |
| Tarefas ativas e agendas de trabalhos<sup>3</sup> por conta do Batch | 100-300 | 1,000<sup>4</sup> |
| Conjuntos por conta do Batch | 20-100 | 500<sup>4</sup> |

> [!NOTE]
> Limites predefinidos variam consoante o tipo de subscrição que vai utilizar para criar uma conta do Batch. As quotas de núcleos mostradas destinam-se a contas do Batch no modo de serviço do Batch. [Ver as quotas na sua conta do Batch](../articles/batch/batch-quota-limit.md#view-batch-quotas). 

<sup>1</sup>pode ser aumentado o número de núcleos dedicados por conta do Batch, mas o número máximo é não especificado. Para discutir as opções de aumento, contacte o suporte do Azure.

<sup>2</sup>pode ser aumentado o número de núcleos de prioridade baixa por conta do Batch, mas o número máximo é não especificado. Para discutir as opções de aumento, contacte o suporte do Azure.

<sup>3</sup>tarefas concluídas e agendas de tarefas não estão limitadas.

<sup>4</sup>para pedir um aumento que ultrapassam este limite, contacte o suporte do Azure.
