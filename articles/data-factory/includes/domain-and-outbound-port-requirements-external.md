---
title: incluir ficheiro
description: incluir ficheiro
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: 4ef7bc7ed1ab255bffdfec10c07bc040019d9f1e
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121680"
---
| Nomes de domínio                  | Portas de saída | Descrição                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.core.windows.net`          | 443            | Utilizado pelo tempo de integração auto-hospedado para ligar à conta de armazenamento Azure quando utilizar a função de cópia encenada. |
| `*.database.windows.net`      | 1433           | Requerido apenas quando copiar de ou para Azure SQL Database ou Azure Synapse Analytics e opcional de outra forma. Utilize a função de cópia encenada para copiar dados para a Base de Dados SQL ou Azure Synapse Analytics sem abrir a porta 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Requerido apenas quando copiar de ou para Azure Data Lake Store e opcional de outra forma. |
