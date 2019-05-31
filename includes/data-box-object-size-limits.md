---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: eb12adf8f8523686b1d8deda2776eb203a76e954
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244608"
---
Aqui estão os tamanhos dos objetos do Azure que podem ser gravados. Certifique-se de que todos os ficheiros que são carregados estão em conformidade com estes limites.

| Tipo de objeto do Azure | Limite predefinido                                             |
|-------------------|-----------------------------------------------------------|
| Blob de bloco        | ~ 4.75 TiB                                                 |
| BLOBs de páginas         | 8 TiB <br> Todos os ficheiros carregados no formato de blob de página tem de ser 512 bytes alinhados (um múltiplo de integral), caso contrário o carregamento falha. <br> VHD e VHDX são alinhadas de 512 bytes. |
| Ficheiros do Azure        | 1 TiB                                                      |
| Managed disks     | 4 TiB <br> Para obter mais informações sobre o tamanho e limites, consulte: <li>[Destinos de escalabilidade do SSDs padrão](../articles/virtual-machines/windows/disks-types.md#standard-ssd)</li><li>[Destinos de escalabilidade do Premium SSDs](../articles/virtual-machines/windows/disks-types.md#standard-hdd)</li><li>[Destinos de escalabilidade de HDDs padrão](../articles/virtual-machines/windows/disks-types.md#premium-ssd)</li><li>[Preços e faturação de discos geridos](../articles/virtual-machines/windows/disks-types.md#billing)</li>  
