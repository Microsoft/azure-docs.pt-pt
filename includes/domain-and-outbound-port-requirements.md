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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74559306"
---
| Nomes de domínio                  | Portas de saída | Descrição                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Exigido pelo tempo de execução de integração auto-hospedado para ligar aos serviços de movimento de dados na Azure Data Factory. |
| `*.frontend.clouddatahub.net` | 443            | Exigido pelo tempo de execução de integração auto-hospedado para ligar ao serviço data Factory. |
| `download.microsoft.com`    | 443            | Exigido pelo tempo de execução de integração auto-hospedado para descarregar as atualizações. Se tiver desativado a atualização automática, pode ignorar a configuração deste domínio. |
| `*.core.windows.net`          | 443            | Usado pelo tempo de execução de integração auto-hospedado para ligar à conta de armazenamento Azure quando utilizar a função de [cópia encenada.](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy) |
| `*.database.windows.net`      | 1433           | Só é necessário quando copia de ou para a Base de Dados Azure SQL ou para o Armazém de Dados Azure SQL e opcional de outra forma. Utilize a função de cópia encenou para copiar dados para a Base de Dados SQL ou para o Armazém de Dados SQL sem abrir a porta 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Só é necessário quando copia de ou para a Azure Data Lake Store e opcional de outra forma. |
