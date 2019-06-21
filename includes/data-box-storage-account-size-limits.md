---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 0975df8ee9da4a239dbd72e468b967c88ab3feed
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67277399"
---
Aqui estão os limites no tamanho dos dados que são copiados para a conta de armazenamento. Certifique-se de que os dados carregados estão de acordo com estes limites. Para obter as informações mais atualizadas sobre estes limites, aceda a [metas de dimensionamento de armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) e [alvos de dimensionamento de ficheiros do Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Tamanho dos dados copiados para a conta de armazenamento do Azure                      | Limite predefinido          |
|---------------------------------------------------------------------|------------------------|
| Blob de blocos e BLOBs de páginas                                            | 2 PB dos EUA e Europa Ocidental.<br>500 TB para todas as outras regiões, que inclui o Reino Unido.  <br> Isto inclui dados de todas as origens, incluindo o Data Box.|
| Ficheiros do Azure                                                          | 5 TB por partilha.<br> Todas as pastas sob *StorageAccount_AzureFiles* tem de seguir este limite.       |
