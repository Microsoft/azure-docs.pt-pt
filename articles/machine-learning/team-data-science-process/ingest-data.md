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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76720542"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Carregar dados para ambientes de armazenamento para análise

O Processo de Ciência de Dados da Equipa exige que os dados sejam ingeridos ou carregados da forma mais adequada em cada fase. Os destinos de dados podem incluir armazenamento Azure Blob, bases de dados SQL Azure, SQL Server em Azure VM, HDInsight (Hadoop), Synapse Analytics e Azure Machine Learning. 

Os seguintes artigos descrevem como ingerir dados em vários ambientes-alvo onde os dados são armazenados e processados.

* De/para [o armazenamento de blob azure](move-azure-blob.md)
* Para [o Servidor SQL no Azure VM](move-sql-server-virtual-machine.md)
* Para [azure SQL Base de Dados](move-sql-azure.md)
* Para [mesas de colmeia](move-hive-tables.md)
* Para [mesas de partição SQL](parallel-load-sql-partitioned-tables.md)
* A partir do [Servidor SQL on-local](move-sql-azure-adf.md)

As necessidades técnicas e empresariais, bem como a localização inicial, formato e tamanho dos seus dados determinarão o melhor plano de ingestão de dados. Não é incomum um melhor plano ter vários passos. Esta sequência de tarefas pode incluir, por exemplo, a exploração de dados, pré-processamento, limpeza, amostragem e treino de modelos.  A Azure Data Factory é um recurso Azure recomendado para orquestrar o movimento e transformação de dados.
