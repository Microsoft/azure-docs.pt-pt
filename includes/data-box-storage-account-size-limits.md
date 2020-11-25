---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/24/2020
ms.author: alkohli
ms.openlocfilehash: 2868e5a53686cfa94dc206c1aab65fe866d19b6d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027420"
---
Aqui estão os limites do tamanho dos dados que são copiados na conta de armazenamento. Certifique-se de que os dados que envia estão em conformidade com estes limites. Para obter as informações mais atualizadas sobre estes limites, consulte os objetivos de [Escalabilidade e desempenho para o armazenamento blob](../articles/storage/blobs/scalability-targets.md) e [os objetivos de escalabilidade e desempenho dos Ficheiros Azure](../articles/storage/files/storage-files-scale-targets.md).

| Tamanho dos dados copiados na conta de armazenamento Azure                      | Limite predefinido          |
|---------------------------------------------------------------------|------------------------|
| Blob de bloco e bolha de página                                            | O limite máximo é o mesmo que o [limite de armazenamento definido para a Subscrição Azure](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) e inclui dados de todas as fontes, incluindo a Caixa de Dados. |
| Ficheiros do Azure                                                          | A Data Box suporta grandes ações de ficheiros (100TiB) se ativada antes da criação da encomenda Caixa de Dados. <br> Se não for ativado antes da criação da encomenda, o tamanho máximo da partilha de ficheiros suportado é de 5 TiB. <br> As ações de ficheiros premium ainda não estão suportadas.<br> Todas as pastas abaixo *StorageAccount_AzureFiles* devem seguir este limite. <br> Para obter mais informações, consulte [Ativar e criar grandes ações de ficheiros](../articles/storage/files/storage-files-how-to-create-large-file-share.md)      |