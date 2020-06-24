---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: eb12adf8f8523686b1d8deda2776eb203a76e954
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "66244608"
---
Aqui estão os tamanhos dos objetos Azure que podem ser escritos. Certifique-se de que todos os ficheiros que são carregados estão em conformidade com estes limites.

| Tipo de objeto azul | Limite predefinido                                             |
|-------------------|-----------------------------------------------------------|
| Blob de blocos        | ~ 4.75 TiB                                                 |
| Blob de páginas         | 8 TiB <br> Cada ficheiro carregado no formato blob de página deve estar alinhado 512 bytes (um múltiplo integral), caso contrário o upload falha. <br> VHD e VHDX estão alinhados 512 bytes. |
| Ficheiros do Azure        | 1 TiB                                                      |
| Managed disks     | 4 TiB <br> Para obter mais informações sobre o tamanho e os limites, consulte: <li>[Metas de escalabilidade dos SSDs standard](../articles/virtual-machines/windows/disks-types.md#standard-ssd)</li><li>[Metas de escalabilidade dos SSDs Premium](../articles/virtual-machines/windows/disks-types.md#standard-hdd)</li><li>[Metas de escalabilidade dos HDDs standard](../articles/virtual-machines/windows/disks-types.md#premium-ssd)</li><li>[Preços e faturação de discos geridos](../articles/virtual-machines/windows/disks-types.md#billing)</li>  
