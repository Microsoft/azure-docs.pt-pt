---
title: incluir ficheiro
description: incluir ficheiro
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 05/28/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 22bfc3c86605f4c2eed4c022919b3643f394ea95
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080911"
---
| **Recurso** | **Limite predefinido** | **Limite máximo** |
| --- | --- | --- |
| Contas de Batch do Azure por região por subscrição | 1-3 |50 |
| Núcleos dedicados por conta do Batch | 90-900 | Contacte o suporte |
| Núcleos de prioridade baixa por conta do Batch | 10-100 | Contacte o suporte |
| **[Active Directory](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)**  tarefas e agendamentos de trabalhos por conta do Batch (**concluída** tarefas não têm limite) | 100-300 | 1,000<sup>1</sup> |
| Conjuntos por conta do Batch | 20-100 | 500<sup>1</sup> |

> [!NOTE]
> Limites predefinidos variam consoante o tipo de subscrição que vai utilizar para criar uma conta do Batch. As quotas de núcleos mostradas destinam-se a contas do Batch no modo de serviço do Batch. [Ver as quotas na sua conta do Batch](../articles/batch/batch-quota-limit.md#view-batch-quotas).

<sup>1</sup>para pedir um aumento que ultrapassam este limite, contacte o suporte do Azure.
