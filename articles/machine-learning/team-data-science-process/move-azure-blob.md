---
title: Mover dados para e do armazenamento de Blobs do Azure - Team Data Science Process
description: Mover dados para e do armazenamento de Blobs do Azure
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
ms.openlocfilehash: ca5a75ec61a0f75b3a008762561fed8231fce33d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60811584"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Mover dados para e do armazenamento de Blobs do Azure

O processo de ciência de dados de equipa exige que o dados ser ingeridos ou carregados para uma variedade de ambientes de armazenamento diferentes a serem processados ou analisados da forma mais adequada em cada fase do processo.

## <a name="different-technologies-for-moving-data"></a>Diferentes tecnologias para mover dados

Os seguintes artigos descrevem como mover dados para e do armazenamento de Blobs do Azure com diferentes tecnologias.

* [Explorador de armazenamento do Azure](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [AZCopy](move-data-to-azure-blob-using-azcopy.md)
* [Python](move-data-to-azure-blob-using-python.md)
* [SSIS](move-data-to-azure-blob-using-ssis.md)

Qual é o método melhor para depende do seu cenário. O [cenários de análises avançadas no Azure Machine Learning](plan-sample-scenarios.md) artigo ajuda-o a determinar os recursos necessários para uma variedade de fluxos de trabalho do ciência de dados usados no processo de análise avançada.

> [!NOTE]
> Para obter uma introdução completa para o armazenamento de Blobs do Azure, consulte [Noções básicas de Blobs do Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) e, a [serviço de Blobs do Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="using-azure-data-factory"></a>Utilizar o Azure Data Factory

Como alternativa, pode utilizar [do Azure Data Factory](https://azure.microsoft.com/services/data-factory/) para: 

* criar e agendar um pipeline que transfere dados a partir do armazenamento de Blobs do Azure, 
* passá-lo para um serviço web Azure Machine Learning publicado, 
* receber os resultados de Análise Preditiva, e 
* carregar os resultados para o armazenamento. 

Para obter mais informações, consulte [crie pipelines previsíveis utilizando o Azure Data Factory e o Azure Machine Learning](../../data-factory/transform-data-using-machine-learning.md).

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que tem uma subscrição do Azure, uma conta de armazenamento e a chave de armazenamento correspondente para essa conta. Antes de carregamento/transferência de dados, tem de saber a sua chave de conta e o nome da conta de armazenamento do Azure.

* Para configurar uma subscrição do Azure, veja [durante um mês avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Para obter instruções sobre como criar uma conta de armazenamento e obtenção de conta e informações da chave, consulte [sobre as contas de armazenamento](../../storage/common/storage-create-storage-account.md).

