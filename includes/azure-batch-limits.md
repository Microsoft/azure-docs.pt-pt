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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67080911"
---
| **Recurso** | **Limite predefinido** | **Limite máximo** |
| --- | --- | --- |
| Contas do Lote Azure por região por subscrição | 1-3 |50 |
| Núcleos dedicados por conta Batch | 90-900 | Contactar o suporte |
| Núcleos de baixa prioridade por conta Batch | 10-100 | Contactar o suporte |
| **[Empregos ativos](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** e horários de trabalho por conta Batch **(empregos concluídos** não têm limite) | 100-300 | 1.000<sup>1</sup> |
| Conjuntos por conta do Batch | 20-100 | 500<sup>1</sup> |

> [!NOTE]
> Os limites de predefinição variam consoante o tipo de subscrição que utiliza para criar uma conta Batch. As quotas de cores apresentadas são para contas de Lote no modo de serviço batch. [Consulte as quotas na sua conta Batch](../articles/batch/batch-quota-limit.md#view-batch-quotas).

<sup>1</sup> Para solicitar um aumento para além deste limite, contacte o Suporte Azure.
