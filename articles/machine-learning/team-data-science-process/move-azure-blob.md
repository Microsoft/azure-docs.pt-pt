---
title: Mover dados de e para o armazenamento de BLOBs do Azure-processo de ciência de dados da equipe
description: Mover dados de e para o armazenamento de BLOBs do Azure usando Gerenciador de Armazenamento do Azure, AzCopy, Python e SSIS.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d885a7fad6e958507e7d9df34bd2b1fb222c6f86
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053667"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Mover dados de e para o armazenamento de BLOBs do Azure

O processo de ciência de dados de equipe exige que os dados sejam ingeridos ou carregados em uma variedade de diferentes ambientes de armazenamento para serem processados ou analisados da maneira mais apropriada em cada estágio do processo.

## <a name="different-technologies-for-moving-data"></a>Tecnologias diferentes para mover dados

Os artigos a seguir descrevem como mover dados de e para o armazenamento de BLOBs do Azure usando tecnologias diferentes.

* [Gerenciador de armazenamento do Azure](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [AZCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)
* [Python](move-data-to-azure-blob-using-python.md)
* [SSIS](move-data-to-azure-blob-using-ssis.md)

Qual método é melhor para você depende do seu cenário. Os [cenários de análise avançada no artigo Azure Machine Learning](plan-sample-scenarios.md) ajudam a determinar os recursos necessários para uma variedade de fluxos de trabalho de ciência de dados usados no processo de análise avançada.

> [!NOTE]
> Para obter uma introdução completa ao armazenamento de BLOBs do Azure, consulte [noções básicas de blob do Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) e [serviço blob do Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="using-azure-data-factory"></a>Utilizar o Azure Data Factory

Como alternativa, você pode usar [Azure data Factory](https://azure.microsoft.com/services/data-factory/) para: 

* criar e agendar um pipeline que baixa dados do armazenamento de BLOBs do Azure, 
* passá-lo para um serviço Web publicado Azure Machine Learning, 
* receber os resultados da análise preditiva e 
* Carregue os resultados no armazenamento. 

Para obter mais informações, consulte [criar pipelines preditivas usando Azure data Factory e Azure Machine Learning](../../data-factory/transform-data-using-machine-learning.md).

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você tenha uma assinatura do Azure, uma conta de armazenamento e a chave de armazenamento correspondente para essa conta. Antes de carregar/baixar dados, você deve saber o nome da conta de armazenamento do Azure e a chave de conta.

* Para configurar uma assinatura do Azure, consulte [avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/).
* Para obter instruções sobre como criar uma conta de armazenamento e obter informações de conta e chave, consulte [sobre contas de armazenamento do Azure](../../storage/common/storage-create-storage-account.md).

