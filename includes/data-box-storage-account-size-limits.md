---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 01/12/2021
ms.author: alkohli
ms.openlocfilehash: 6cabac4c59b09d146c1e42762402043622edb60e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98225365"
---
Aqui estão os limites do tamanho dos dados que são copiados numa conta de armazenamento. Certifique-se de que os dados que envia estão em conformidade com estes limites. Para obter as informações mais atualizadas sobre estes limites, consulte os objetivos de [Escalabilidade e desempenho para o armazenamento blob](../articles/storage/blobs/scalability-targets.md) e [os objetivos de escalabilidade e desempenho dos Ficheiros Azure](../articles/storage/files/storage-files-scale-targets.md).

| Tamanho dos dados copiados na conta de armazenamento Azure                      | Limite predefinido          |
|---------------------------------------------------------------------|------------------------|
| Blob de bloco e bolha de página                                            | O limite máximo é o mesmo que o [limite de armazenamento definido para a Subscrição Azure](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) e inclui dados de todas as fontes, incluindo a Caixa de Dados.   |
| Ficheiros do Azure                                                          | A Data Box suporta grandes ações de ficheiros (100 TiB) se ativada antes da criação da encomenda Caixa de Dados. <br> Se não for ativado antes da criação da encomenda, o tamanho máximo da partilha de ficheiros suportado é de 5 TiB. <br> A Data Box suporta ações de ficheiro Azure Premium que permitem um total de 100 TiB para todas as ações da conta de armazenamento. <br> A capacidade máxima utilizável é ligeiramente menor devido ao espaço que os registos de cópia e os registos de auditoria utilizam. Um mínimo de 100 GiB cada é reservado para o registo de cópias e registo de auditoria. Para obter mais informações, consulte [os registos de auditoria para Azure Data Box, Azure Data Box Heavy](../articles/databox/data-box-audit-logs.md). <br> Todas as pastas abaixo *StorageAccount_AzureFiles* devem seguir este limite. <br> Para obter mais informações, consulte [Ativar e criar grandes ações de ficheiros](../articles/storage/files/storage-files-how-to-create-large-file-share.md)      |
