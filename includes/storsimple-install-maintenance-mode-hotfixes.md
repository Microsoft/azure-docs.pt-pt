---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 2d0fd904dd704c704662192e1e92fe403f0971c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184289"
---
#### <a name="to-install-maintenance-mode-hotfixes-via-windows-powershell-for-storsimple"></a>Para instalar hotfixes do modo de manutenção através do Windows PowerShell para StorSimple
> [!IMPORTANT]
> No modo manutenção, é necessário aplicar primeiro o hotfix num controlador e depois no outro controlador.
> 
> 

1. Coloque o aparelho no modo manutenção. Consulte [o passo 2: Introduza o modo manutenção](../articles/storsimple/storsimple-update-device.md#step2) para obter instruções sobre como entrar no modo manutenção.
2. Para aplicar o hotfix, escreva:
   
     `Start-HcsHotfix` 
3. Quando solicitado, forneça o caminho para a pasta partilhada na rede que contém os ficheiros hotfix.
4. Ser-lhe-á pedido que confirme. Tipo **Y** para proceder à instalação de hotfix.
5. Depois de ter aplicado o hotfix num controlador, inscreva-se no outro controlador. Aplique o hotfix como fez para o controlador anterior.
6. Depois de aplicados os hotfixes, saída Modo manutenção. Consulte [o passo 4: Modo de manutenção de saída](../articles/storsimple/storsimple-update-device.md#step4) para obter instruções.

