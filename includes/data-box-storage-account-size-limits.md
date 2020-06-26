---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/24/2020
ms.author: alkohli
ms.openlocfilehash: 5c5732a825116d4762c3a27bcbf9eac195327afb
ms.sourcegitcommit: dfa5f7f7d2881a37572160a70bac8ed1e03990ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85378561"
---
Aqui estão os limites do tamanho dos dados que são copiados na conta de armazenamento. Certifique-se de que os dados que envia estão em conformidade com estes limites. Para obter as informações mais atualizadas sobre estes limites, consulte os objetivos de [Escalabilidade e desempenho para o armazenamento blob](../articles/storage/blobs/scalability-targets.md) e [os objetivos de escalabilidade e desempenho dos Ficheiros Azure](../articles/storage/files/storage-files-scale-targets.md).

| Tamanho dos dados copiados na conta de armazenamento Azure                      | Limite predefinido          |
|---------------------------------------------------------------------|------------------------|
| Blob de bloco e bolha de página                                            | O limite máximo é o mesmo que o [limite de armazenamento definido para a Subscrição Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits) e inclui dados de todas as fontes, incluindo a Caixa de Dados.|
| Ficheiros do Azure                                                          | O tamanho máximo das ações de ficheiros Standard é de 5 TB <br> O tamanho máximo das ações de ficheiro Premium é de 100TiB por ação.<br> Todas as pastas abaixo *StorageAccount_AzureFiles* devem seguir este limite.       |
