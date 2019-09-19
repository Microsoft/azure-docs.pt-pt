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
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "67080911"
---
| **Recurso** | **Limite predefinido** | **Limite máximo** |
| --- | --- | --- |
| Contas do lote do Azure por região por assinatura | 1-3 |50 |
| Núcleos dedicados por conta do lote | 90-900 | Contacte o suporte |
| Núcleos de baixa prioridade por conta do lote | 10-100 | Contacte o suporte |
| Trabalhos **[ativos](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** e agendas de trabalho por conta do lote (trabalhos**concluídos** não têm limite) | 100-300 | 1\.000<sup>1</sup> |
| Conjuntos por conta do Batch | 20-100 | 500<sup>1</sup> |

> [!NOTE]
> Os limites padrão variam dependendo do tipo de assinatura que você usa para criar uma conta do lote. As cotas de núcleos mostradas são para contas do lote no modo de serviço do lote. [Exiba as cotas em sua conta do lote](../articles/batch/batch-quota-limit.md#view-batch-quotas).

<sup>1</sup> Para solicitar um aumento além desse limite, entre em contato com o suporte do Azure.
