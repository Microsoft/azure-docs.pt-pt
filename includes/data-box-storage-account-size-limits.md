---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: 7b46360886387bc4ab48f764edb5d01dc52dc812
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244593"
---
Aqui estão os limites no tamanho dos dados que são copiados para a conta de armazenamento. Certifique-se de que os dados carregados estão de acordo com estes limites. Para obter as informações mais atualizadas sobre estes limites, aceda a [metas de dimensionamento de armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) e [alvos de dimensionamento de ficheiros do Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Tamanho dos dados copiados para a conta de armazenamento do Azure                      | Limite predefinido          |
|---------------------------------------------------------------------|------------------------|
| Blob de blocos e BLOBs de páginas                                            | 500 TiB por conta de armazenamento. <br> Isto inclui dados de todas as origens, incluindo o Data Box.|
| Ficheiros do Azure                                                          | De 5 TiB por partilha.<br> Todas as pastas sob *StorageAccount_AzureFiles* tem de seguir este limite.       |
