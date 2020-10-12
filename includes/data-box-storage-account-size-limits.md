---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/24/2020
ms.author: alkohli
ms.openlocfilehash: acaebcea59e765f5544f1bfbd692c6508f66a84a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91025000"
---
Aqui estão os limites do tamanho dos dados que são copiados na conta de armazenamento. Certifique-se de que os dados que envia estão em conformidade com estes limites. Para obter as informações mais atualizadas sobre estes limites, consulte os objetivos de [Escalabilidade e desempenho para o armazenamento blob](../articles/storage/blobs/scalability-targets.md) e [os objetivos de escalabilidade e desempenho dos Ficheiros Azure](../articles/storage/files/storage-files-scale-targets.md).

| Tamanho dos dados copiados na conta de armazenamento Azure                      | Limite predefinido          |
|---------------------------------------------------------------------|------------------------|
| Blob de bloco e bolha de página                                            | O limite máximo é o mesmo que o [limite de armazenamento definido para a Subscrição Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits) e inclui dados de todas as fontes, incluindo a Caixa de Dados. |
| Ficheiros do Azure                                                          | A Data Box suporta grandes ações de ficheiros (100TiB) se ativada antes da criação da encomenda Caixa de Dados. <br> Se não for ativado antes da criação da encomenda, o tamanho máximo da partilha de ficheiros suportado é de 5 TiB. <br> As ações de ficheiros premium ainda não estão suportadas.<br> Todas as pastas abaixo *StorageAccount_AzureFiles* devem seguir este limite. <br> Para obter mais informações, consulte [Ativar e criar grandes ações de ficheiros](../articles/storage/files/storage-files-how-to-create-large-file-share.md)      |
