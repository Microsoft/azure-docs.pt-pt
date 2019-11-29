---
title: incluir ficheiro
description: incluir ficheiro
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 6af6795fefb41f1d8f2b56e0aa1fb367fc18cee2
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74559306"
---
| Nomes de domínio                  | Portas de saída | Descrição                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Exigido pelo tempo de execução de integração auto-hospedado para se conectar aos serviços de movimentação de dados no Azure Data Factory. |
| `*.frontend.clouddatahub.net` | 443            | Exigido pelo tempo de execução de integração auto-hospedado para se conectar ao serviço de Data Factory. |
| `download.microsoft.com`    | 443            | Exigido pelo tempo de execução de integração auto-hospedado para baixar as atualizações. Se você tiver desabilitado a atualização automática, poderá ignorar a configuração desse domínio. |
| `*.core.windows.net`          | 443            | Usado pelo tempo de execução de integração auto-hospedado para se conectar à conta de armazenamento do Azure quando você usa o recurso de [cópia em etapas](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy) . |
| `*.database.windows.net`      | 1433           | Necessário somente quando você copia de ou para o banco de dados SQL do Azure ou o Azure SQL Data Warehouse e opcional caso contrário. Use o recurso de cópia em etapas para copiar dados para o banco de dados SQL ou SQL Data Warehouse sem abrir a porta 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Necessário somente quando você copia de ou para Azure Data Lake Store e opcional caso contrário. |
