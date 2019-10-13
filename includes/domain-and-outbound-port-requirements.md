---
title: incluir ficheiro
description: incluir ficheiro
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: f634ebb4d8c3f443b5d843b0093c955b384a9f5f
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285603"
---
| Nomes de domínio                  | Portas de saída | Descrição                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Exigido pelo tempo de execução de integração auto-hospedado para se conectar aos serviços de movimentação de dados no Data Factory. |
| `*.frontend.clouddatahub.net` | 443            | Exigido pelo tempo de execução de integração auto-hospedado para se conectar ao serviço de Data Factory. |
| `download.microsoft.com`    | 443            | Exigido pelo tempo de execução de integração auto-hospedado para baixar as atualizações. Se você tiver desabilitado a atualização automática, poderá ignorar isso. |
| `*.core.windows.net`          | 443            | Usado pelo tempo de execução de integração auto-hospedado para se conectar à conta de armazenamento do Azure quando você usa o recurso de [cópia em etapas](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy) . |
| `*.database.windows.net`      | 1433           | Adicional Necessário ao copiar de ou para o banco de dados SQL do Azure ou o Azure SQL Data Warehouse. Use o recurso de cópia em etapas para copiar dados para o Azure SQL Database ou para o SQL Data Warehouse de recursos de backup sem abrir a porta 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Adicional Necessário quando você copia de ou para Azure Data Lake Store. |
