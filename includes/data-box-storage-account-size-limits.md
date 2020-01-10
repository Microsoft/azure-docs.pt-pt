---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 6a9c31eb46a457433834d5940b3f7e190ebe1476
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467720"
---
Aqui estão os limites do tamanho dos dados que são copiados na conta de armazenamento. Verifique se os dados carregados estão em conformidade com esses limites. Para obter as informações mais atualizadas sobre esses limites, consulte [escalabilidade e metas de desempenho para armazenamento de BLOBs](../articles/storage/blobs/scalability-targets.md) e [metas de desempenho e escalabilidade de arquivos do Azure](../articles/storage/files/storage-files-scale-targets.md).

| Tamanho dos dados copiados para a conta de armazenamento do Azure                      | Limite predefinido          |
|---------------------------------------------------------------------|------------------------|
| Blob de blocos e BLOB de páginas                                            | 2 PB para nós e Europa.<br>500 TB para todas as outras regiões, que inclui o Reino Unido.  <br> Isso inclui dados de todas as fontes, incluindo Data Box.|
| Ficheiros do Azure                                                          | 5 TB por compartilhamento.<br> Todas as pastas em *StorageAccount_AzureFiles* devem seguir esse limite.       |
