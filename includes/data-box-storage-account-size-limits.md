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
Aqui estão os limites do tamanho dos dados que são copiados na conta de armazenamento. Certifique-se de que os dados que envia estão em conformidade com estes limites. Para obter as informações mais atualizadas sobre estes limites, consulte os objetivos de [Escalabilidade e desempenho para o armazenamento blob](../articles/storage/blobs/scalability-targets.md) e [os objetivos de escalabilidade e desempenho dos Ficheiros Azure](../articles/storage/files/storage-files-scale-targets.md).

| Tamanho dos dados copiados na conta de armazenamento Azure                      | Limite predefinido          |
|---------------------------------------------------------------------|------------------------|
| Blob de bloco e bolha de página                                            | 2 PB para os EUA e Europa.<br>500 TB para todas as outras regiões, incluindo o Reino Unido.  <br> Isto inclui dados de todas as fontes, incluindo a Caixa de Dados.|
| Ficheiros do Azure                                                          | Tamanho máximo das ações de ficheiro Standard 100TiB*, 5 TB, Ficha Premium 100TiB por ação.<br> Todas as pastas abaixo *StorageAccount_AzureFiles* devem seguir este limite.       |
