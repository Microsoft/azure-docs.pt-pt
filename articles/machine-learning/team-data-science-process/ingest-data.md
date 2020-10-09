---
title: Carregar dados em ambientes de armazenamento Azure - Processo de Ciência de Dados de Equipa
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76720542"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Carregar dados para ambientes de armazenamento para análise

O Processo de Ciência de Dados da Equipa exige que os dados sejam ingeridos ou carregados da forma mais apropriada em cada etapa. Os destinos de dados podem incluir Azure Blob Storage, bases de dados SQL Azure, SQL Server em Azure VM, HDInsight (Hadoop), Synapse Analytics e Azure Machine Learning. 

Os seguintes artigos descrevem como ingerir dados em vários ambientes-alvo onde os dados são armazenados e tratados.

* De/Para O Armazenamento de [Blob Azure](move-azure-blob.md)
* Para [o SQL Server em Azure VM](move-sql-server-virtual-machine.md)
* Para [a base de dados Azure SQL](move-sql-azure.md)
* Para [as mesas de colmeia](move-hive-tables.md)
* Para [as mesas divisórias SQL](parallel-load-sql-partitioned-tables.md)
* A partir [de instalações SQL Server](move-sql-azure-adf.md)

As necessidades técnicas e empresariais, bem como a localização inicial, formato e dimensão dos seus dados determinarão o melhor plano de ingestão de dados. Não é incomum para um melhor plano ter vários passos. Esta sequência de tarefas pode incluir, por exemplo, a exploração de dados, pré-processamento, limpeza, amostragem e formação de modelos.  AZure Data Factory é um recurso azure recomendado para orquestrar o movimento e transformação de dados.
