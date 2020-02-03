---
title: Mover dados para e do armazenamento de Blobs do Azure - Team Data Science Process
description: Mova dados de e para o armazenamento Azure Blob utilizando o Azure Storage Explorer, AzCopy, Python e SSIS.
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
ms.openlocfilehash: fc58651bcb3b266b981fb953fd7341427d47fb2c
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717586"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Mover dados para e do armazenamento de Blobs do Azure

O processo de ciência de dados de equipa exige que o dados ser ingeridos ou carregados para uma variedade de ambientes de armazenamento diferentes a serem processados ou analisados da forma mais adequada em cada fase do processo.

## <a name="different-technologies-for-moving-data"></a>Diferentes tecnologias para mover dados

Os seguintes artigos descrevem como mover dados para e do armazenamento de Blobs do Azure com diferentes tecnologias.

* [Azure Storage-Explorer](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [AZCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)
* [python](move-data-to-azure-blob-using-python.md)
* [SSIS](move-data-to-azure-blob-using-ssis.md)

Qual é o método melhor para depende do seu cenário. Os [Cenários para análiseavançada no artigo de Aprendizagem automática de Azure](plan-sample-scenarios.md) ajudam-no a determinar os recursos necessários para uma variedade de fluxos de trabalho de ciência de dados utilizados no processo de análise avançada.

> [!NOTE]
> Para uma introdução completa ao armazenamento de blob Azure, consulte [o Azure Blob Basics](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) e o [Serviço Blob Azure.](https://msdn.microsoft.com/library/azure/dd179376.aspx)
> 
> 

## <a name="using-azure-data-factory"></a>Utilizar o Azure Data Factory

Como alternativa, pode utilizar a [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) para: 

* criar e agendar um pipeline que transfere dados a partir do armazenamento de Blobs do Azure, 
* passá-lo para um serviço web Azure Machine Learning publicado, 
* receber os resultados de Análise Preditiva, e 
* carregar os resultados para o armazenamento. 

Para mais informações, consulte [Create predive pipelines utilizando a Azure Data Factory e a Azure Machine Learning](../../data-factory/transform-data-using-machine-learning.md).

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que tem uma subscrição do Azure, uma conta de armazenamento e a chave de armazenamento correspondente para essa conta. Antes de fazer o upload/download de dados, deve saber o nome da sua conta de Armazenamento Azure e a chave da conta.

* Para configurar uma subscrição Azure, consulte o [teste gratuito de um mês.](https://azure.microsoft.com/pricing/free-trial/)
* Para obter instruções sobre a criação de uma conta de armazenamento e para obter informações de conta e chave, consulte as contas de [Armazenamento Azure](../../storage/common/storage-create-storage-account.md).

