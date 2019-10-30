---
title: Carregar dados em ambientes de armazenamento do Azure-processo de ciência de dados de equipe
description: Saiba mais sobre como ingerir dados em vários ambientes de destino nos quais os dados são armazenados e processados.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 515decfafe46ad0c1b5b90743688abc26a975903
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053265"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Carregar dados para ambientes de armazenamento para análise

O processo de ciência de dados de equipe exige que os dados sejam ingeridos ou carregados em uma variedade de diferentes ambientes de armazenamento para serem processados ou analisados da maneira mais apropriada em cada estágio do processo. Os destinos de dados geralmente usados para processamento incluem armazenamento de BLOBs do Azure, SQL Azure bancos de dados, SQL Server na VM do Azure, HDInsight (Hadoop) e Azure Machine Learning. 

Os artigos a seguir descrevem como ingerir dados em vários ambientes de destino nos quais os dados são armazenados e processados.

* De/para o [armazenamento de BLOBs do Azure](move-azure-blob.md)
* Para [SQL Server na VM do Azure](move-sql-server-virtual-machine.md)
* Para o [banco de dados SQL do Azure](move-sql-azure.md)
* Para [tabelas do hive](move-hive-tables.md)
* Para [tabelas particionadas do SQL](parallel-load-sql-partitioned-tables.md)
* Do [SQL Server local](move-sql-azure-adf.md)

As necessidades técnicas e de negócios, bem como o local inicial, o formato e o tamanho de seus dados, determinarão os ambientes de destino nos quais os dados precisam ser ingeridos para atingir as metas de sua análise. Não é incomum um cenário exigir que os dados sejam movidos entre vários ambientes para alcançar a variedade de tarefas necessárias para construir um modelo de previsão. Essa sequência de tarefas pode incluir, por exemplo, exploração de dados, pré-processamento, limpeza, redução de amostragem e treinamento de modelo.
