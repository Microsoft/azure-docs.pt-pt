---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 2d0fd904dd704c704662192e1e92fe403f0971c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61409956"
---
#### <a name="to-install-maintenance-mode-hotfixes-via-windows-powershell-for-storsimple"></a>Para instalar as correções do modo de manutenção através do Windows PowerShell para StorSimple
> [!IMPORTANT]
> No modo de manutenção, terá de aplicar a correção primeiro num controlador e, em seguida, no outro controlador.
> 
> 

1. Coloque o dispositivo no modo de manutenção. Consulte [passo 2: Introduza o modo de manutenção](../articles/storsimple/storsimple-update-device.md#step2) para obter instruções sobre como inserir o modo de manutenção.
2. Para aplicar a correção, escreva:
   
     `Start-HcsHotfix` 
3. Quando lhe for pedido, forneça o caminho para a pasta compartilhada de rede que contém os ficheiros de correção.
4. Será solicitado para confirmação. Tipo **Y** para prosseguir com a instalação da correção.
5. Depois de ter aplicado a correção num controlador, inicie sessão no outro controlador. Aplique a correção, tal como fez para o controlador anterior.
6. Depois das correções são aplicadas, saia do modo de manutenção. Consulte [passo 4: Sair do modo de manutenção](../articles/storsimple/storsimple-update-device.md#step4) para obter instruções.

