---
title: incluir ficheiro
description: incluir ficheiro
author: lrtoyou1223
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: d24e8957dc63024a46495e8a66bad7dbb3790f38
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100389513"
---
| Nomes de domínio                  | Portas de saída | Description                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.core.windows.net`          | 443            | Utilizado pelo tempo de integração auto-hospedado para ligar à conta de armazenamento Azure quando utilizar a função de cópia encenada. |
| `*.database.windows.net`      | 1433           | Requerido apenas quando copiar de ou para Azure SQL Database ou Azure Synapse Analytics e opcional de outra forma. Utilize a função de cópia encenada para copiar dados para a Base de Dados SQL ou Azure Synapse Analytics sem abrir a porta 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Requerido apenas quando copiar de ou para Azure Data Lake Store e opcional de outra forma. |
