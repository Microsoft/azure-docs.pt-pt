---
title: Carregue dados em ambientes de armazenamento do Azure - Processo de Ciência de Dados da Equipa
description: Saiba como ingerir dados em vários ambientes-alvo onde os dados são armazenados e processados.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f8eab59d810fb825dbebf80d01d8efd2dd0a9841
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720542"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Carregar dados para ambientes de armazenamento para análise

O Processo de Ciência de Dados da Equipa exige que os dados sejam ingeridos ou carregados da forma mais adequada em cada fase. Os destinos de dados podem incluir armazenamento Azure Blob, bases de dados SQL Azure, SQL Server em Azure VM, HDInsight (Hadoop), Synapse Analytics e Azure Machine Learning. 

Os seguintes artigos descrevem como a ingestão de dados em vários ambientes de destino onde os dados são armazenados e processados.

* De/para [armazenamento de Blobs do Azure](move-azure-blob.md)
* Para [SQL Server numa VM do Azure](move-sql-server-virtual-machine.md)
* Para [azure SQL Base de Dados](move-sql-azure.md)
* Para [tabelas do Hive](move-hive-tables.md)
* Para [tabelas particionadas do SQL](parallel-load-sql-partitioned-tables.md)
* De [no local do SQL Server](move-sql-azure-adf.md)

As necessidades técnicas e empresariais, bem como a localização inicial, formato e tamanho dos seus dados determinarão o melhor plano de ingestão de dados. Não é incomum um melhor plano ter vários passos. Esta sequência de tarefas pode incluir, por exemplo, exploração de dados, processamento prévio de, limpeza, amostragem de baixo e preparação de modelos.  A Azure Data Factory é um recurso Azure recomendado para orquestrar o movimento e transformação de dados.
