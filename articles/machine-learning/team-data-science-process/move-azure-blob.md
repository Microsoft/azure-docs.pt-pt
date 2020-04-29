---
title: Mover dados de e para o armazenamento de Azure Blob - Team Data Science Process
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76717586"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Mover dados de e para o armazenamento da Blob Azure

O Processo de Ciência de Dados da Equipa exige que os dados sejam ingeridos ou carregados numa variedade de diferentes ambientes de armazenamento para serem processados ou analisados da forma mais adequada em cada fase do processo.

## <a name="different-technologies-for-moving-data"></a>Diferentes tecnologias para mover dados

Os seguintes artigos descrevem como mover dados de e para o armazenamento do Azure Blob utilizando diferentes tecnologias.

* [Azure Storage-Explorer](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)
* [Python](move-data-to-azure-blob-using-python.md)
* [SSIS](move-data-to-azure-blob-using-ssis.md)

Qual o melhor método para si depende do seu cenário. Os [Cenários para análiseavançada no artigo de Aprendizagem automática de Azure](plan-sample-scenarios.md) ajudam-no a determinar os recursos necessários para uma variedade de fluxos de trabalho de ciência de dados utilizados no processo de análise avançada.

> [!NOTE]
> Para uma introdução completa ao armazenamento de blob Azure, consulte [o Azure Blob Basics](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) e o [Serviço Blob Azure.](https://msdn.microsoft.com/library/azure/dd179376.aspx)
> 
> 

## <a name="using-azure-data-factory"></a>Utilizar o Azure Data Factory

Como alternativa, pode utilizar a [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) para: 

* criar e programar um oleoduto que descarrega dados do armazenamento de blob Azure, 
* passá-lo para um serviço web azure machine learning publicado, 
* receber os resultados preditivos da análise, e 
* carregar os resultados para armazenamento. 

Para mais informações, consulte [Create predive pipelines utilizando a Azure Data Factory e a Azure Machine Learning](../../data-factory/transform-data-using-machine-learning.md).

## <a name="prerequisites"></a>Pré-requisitos
Este artigo assume que tem uma subscrição Azure, uma conta de armazenamento e a chave de armazenamento correspondente para essa conta. Antes de fazer o upload/download de dados, deve saber o nome da sua conta de Armazenamento Azure e a chave da conta.

* Para configurar uma subscrição Azure, consulte o [teste gratuito de um mês.](https://azure.microsoft.com/pricing/free-trial/)
* Para obter instruções sobre a criação de uma conta de armazenamento e para obter informações de conta e chave, consulte as contas de [Armazenamento Azure](../../storage/common/storage-create-storage-account.md).

