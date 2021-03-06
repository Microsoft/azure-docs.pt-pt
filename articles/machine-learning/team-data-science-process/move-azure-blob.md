---
title: Mover dados de e para o armazenamento de Azure Blob - Processo de Ciência de Dados de Equipa
description: Mover dados de e para o armazenamento Azure Blob usando Azure Storage Explorer, AzCopy, Python e SSIS.
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
ms.openlocfilehash: 5148084fa22266b1352046c7d8737b9804c5f4d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "93311861"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Mover dados de e para o armazenamento da Azure Blob

O Processo de Ciência de Dados de Equipa exige que os dados sejam ingeridos ou carregados numa variedade de diferentes ambientes de armazenamento para serem processados ou analisados da forma mais adequada em cada fase do processo.

## <a name="different-technologies-for-moving-data"></a>Diferentes tecnologias para dados em movimento

Os seguintes artigos descrevem como mover dados de e para o armazenamento Azure Blob usando diferentes tecnologias.

* [Azure Storage-Explorer](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [AzCopy](../../storage/common/storage-use-azcopy-v10.md)
* [Python](../../storage/blobs/storage-quickstart-blobs-python.md)
* [SSIS](move-data-to-azure-blob-using-ssis.md)

Qual o método melhor para si depende do seu cenário. Os [Cenários para análises avançadas no artigo Azure Machine Learning](plan-sample-scenarios.md) ajudam-no a determinar os recursos necessários para uma variedade de fluxos de trabalho de ciência de dados utilizados no processo de análise avançada.

> [!NOTE]
> Para uma introdução completa ao armazenamento de blob Azure, consulte [a Azure Blob Basics](../../storage/blobs/storage-quickstart-blobs-dotnet.md) e o [Azure Blob Service](/rest/api/storageservices/Blob-Service-Concepts).
> 
> 

## <a name="using-azure-data-factory"></a>Utilizar o Azure Data Factory

Como alternativa, pode utilizar [a Azure Data Factory](https://azure.microsoft.com/services/data-factory/) para: 

* criar e agendar um oleoduto que descarregue dados a partir do armazenamento de blob Azure, 
* passá-lo para um serviço web Azure Machine Learning publicado, 
* receber os resultados de análise preditiva, e 
* fazer o upload dos resultados para o armazenamento. 

Para obter mais informações, consulte [Criar oleodutos preditivos utilizando a Azure Data Factory e a Azure Machine Learning.](../../data-factory/transform-data-using-machine-learning.md)

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que tem uma subscrição Azure, uma conta de armazenamento e a chave de armazenamento correspondente para essa conta. Antes de carregar/descarregar dados, deve conhecer o nome da sua conta e a chave da conta do Azure Storage.

* Para configurar uma subscrição do Azure, consulte [o teste gratuito de um mês](https://azure.microsoft.com/pricing/free-trial/).
* Para obter instruções sobre a criação de uma conta de armazenamento e para obter informações de conta e informações fundamentais, consulte [as contas de Armazenamento Azure](../../storage/common/storage-account-create.md).