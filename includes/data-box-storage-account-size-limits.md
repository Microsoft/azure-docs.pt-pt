---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 3e1f9c225a57e7d41f85c2a92dac989453057c51
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/03/2020
ms.locfileid: "82736982"
---
Aqui estão os limites do tamanho dos dados que são copiados para a conta de armazenamento. Certifique-se de que os dados que envia estão em conformidade com estes limites. Para obter informações mais atualizadas sobre estes limites, consulte os objetivos de [escalabilidade e desempenho para](../articles/storage/blobs/scalability-targets.md) o armazenamento blob e metas de escalabilidade e desempenho dos [Ficheiros Azure.](../articles/storage/files/storage-files-scale-targets.md)

| Tamanho dos dados copiados na conta de armazenamento do Azure                      | Limite predefinido          |
|---------------------------------------------------------------------|------------------------|
| Bloco blob e página blob                                            | 2 PB para os EUA e Europa.<br>500 TB para todas as outras regiões, incluindo o Reino Unido.  <br> Isto inclui dados de todas as fontes, incluindo data Box.|
| Ficheiros do Azure                                                          | Tamanho máximo do ficheiro Standard partilha 100TiB*, 5 TB, o ficheiro Premium partilha 100TiB por ação.<br> Todas as pastas sob *StorageAccount_AzureFiles* devem seguir este limite.       |
