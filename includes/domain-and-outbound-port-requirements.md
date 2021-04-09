---
title: incluir ficheiro
description: incluir ficheiro
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 626074940ced57bdb1ae93f494b7a3847ef83a81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96445004"
---
| Nomes de domínio                  | Portas de saída | Description                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Exigido pelo tempo de integração auto-hospedado para a autoria interativa. |
| `{datafactory}.{region}.datafactory.azure.net`<br> ou `*.frontend.clouddatahub.net` | 443            | Requerido pelo tempo de integração auto-hospedado para ligar ao serviço Data Factory. <br>Para a nova Fábrica de Dados criada, encontre o FQDN a partir da sua chave de runtime de integração auto-hospedada que está em formato {datafactory}. {região}.datafactory.azure.net. Para a antiga fábrica de dados, se não vir o FQDN na sua chave de integração auto-hospedada, utilize *.frontend.clouddatahub.net em vez disso. |
| `download.microsoft.com`    | 443            | Requerido pelo tempo de integração auto-hospedado para descarregar as atualizações. Se tiver desativado a atualização automática, pode saltar para configurar este domínio. |
| `*.core.windows.net`          | 443            | Utilizado pelo tempo de integração auto-hospedado para ligar à conta de armazenamento Azure quando utilizar a função [de cópia encenada.](../articles/data-factory/copy-activity-performance.md#staged-copy) |
| `*.database.windows.net`      | 1433           | Requerido apenas quando copiar de ou para Azure SQL Database ou Azure Synapse Analytics e opcional de outra forma. Utilize a função de cópia encenada para copiar dados para a Base de Dados SQL ou Azure Synapse Analytics sem abrir a porta 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Requerido apenas quando copiar de ou para Azure Data Lake Store e opcional de outra forma. |
