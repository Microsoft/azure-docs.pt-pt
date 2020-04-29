---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 6a9c31eb46a457433834d5940b3f7e190ebe1476
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75467720"
---
Aqui estão os limites do tamanho dos dados que são copiados para a conta de armazenamento. Certifique-se de que os dados que envia estão em conformidade com estes limites. Para obter informações mais atualizadas sobre estes limites, consulte os objetivos de [escalabilidade e desempenho para](../articles/storage/blobs/scalability-targets.md) o armazenamento blob e metas de escalabilidade e desempenho dos [Ficheiros Azure.](../articles/storage/files/storage-files-scale-targets.md)

| Tamanho dos dados copiados na conta de armazenamento do Azure                      | Limite predefinido          |
|---------------------------------------------------------------------|------------------------|
| Bloco blob e página blob                                            | 2 PB para os EUA e Europa.<br>500 TB para todas as outras regiões, incluindo o Reino Unido.  <br> Isto inclui dados de todas as fontes, incluindo data Box.|
| Ficheiros do Azure                                                          | 5 TB por ação.<br> Todas as pastas sob *StorageAccount_AzureFiles* devem seguir este limite.       |
