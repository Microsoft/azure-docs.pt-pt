---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 6af6795fefb41f1d8f2b56e0aa1fb367fc18cee2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74559306"
---
| Nomes de domínio                  | Portas de saída | Descrição                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Requerido pelo tempo de integração auto-hospedado para se ligar aos serviços de movimento de dados na Azure Data Factory. |
| `*.frontend.clouddatahub.net` | 443            | Requerido pelo tempo de integração auto-hospedado para ligar ao serviço Data Factory. |
| `download.microsoft.com`    | 443            | Requerido pelo tempo de integração auto-hospedado para descarregar as atualizações. Se tiver desativado a atualização automática, pode saltar para configurar este domínio. |
| `*.core.windows.net`          | 443            | Utilizado pelo tempo de integração auto-hospedado para ligar à conta de armazenamento Azure quando utilizar a função [de cópia encenada.](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy) |
| `*.database.windows.net`      | 1433           | Requerido apenas quando copiar de ou para Azure SQL Database ou Azure SQL Data Warehouse e opcional de outra forma. Utilize a função de cópia encenada para copiar dados para a Base de Dados SQL ou para o SQL Data Warehouse sem abrir a porta 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Requerido apenas quando copiar de ou para Azure Data Lake Store e opcional de outra forma. |
