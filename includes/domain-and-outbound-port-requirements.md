---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 0b2831321c9f4d855a19605c1ce5ace9fa11c88b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89596124"
---
| Nomes de domínio                  | Portas de saída | Descrição                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Requerido pelo tempo de integração auto-hospedado para se ligar aos serviços de movimento de dados na Azure Data Factory. |
| `{datafactory}.{region}.datafactory.azure.net`<br> ou `*.frontend.clouddatahub.net` | 443            | Requerido pelo tempo de integração auto-hospedado para ligar ao serviço Data Factory. <br>Para a nova Fábrica de Dados criada, encontre o FQDN a partir da sua chave de runtime de integração auto-hospedada que está em formato {datafactory}. {region}.datafactory.azure.net. Para a antiga fábrica de dados, se não vir o FQDN na sua chave de integração auto-hospedada, utilize *.frontend.clouddatahub.net em vez disso. |
| `download.microsoft.com`    | 443            | Requerido pelo tempo de integração auto-hospedado para descarregar as atualizações. Se tiver desativado a atualização automática, pode saltar para configurar este domínio. |
| `*.core.windows.net`          | 443            | Utilizado pelo tempo de integração auto-hospedado para ligar à conta de armazenamento Azure quando utilizar a função [de cópia encenada.](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy) |
| `*.database.windows.net`      | 1433           | Requerido apenas quando copiar de ou para Azure SQL Database ou Azure Synapse Analytics e opcional de outra forma. Utilize a função de cópia encenada para copiar dados para a Base de Dados SQL ou sinapse Analytics sem abrir a porta 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Requerido apenas quando copiar de ou para Azure Data Lake Store e opcional de outra forma. |
