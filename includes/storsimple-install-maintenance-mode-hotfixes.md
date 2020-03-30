---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 2d0fd904dd704c704662192e1e92fe403f0971c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67184289"
---
#### <a name="to-install-maintenance-mode-hotfixes-via-windows-powershell-for-storsimple"></a>Para instalar os hotfixes do modo de manutenção através do Windows PowerShell para o StorSimple
> [!IMPORTANT]
> No modo de manutenção, é necessário aplicar o hotfix primeiro num controlador e depois no outro controlador.
> 
> 

1. Coloque o aparelho no modo de manutenção. Consulte o [passo 2: Introduza](../articles/storsimple/storsimple-update-device.md#step2) instruções de manutenção sobre como entrar no modo de manutenção.
2. Para aplicar o hotfix, escreva:
   
     `Start-HcsHotfix` 
3. Quando solicitado, forneça o caminho para a pasta partilhada pela rede que contenha os ficheiros hotfix.
4. Ser-lhe-á pedido que confirme. Digite **Y** para prosseguir com a instalação hotfix.
5. Depois de ter aplicado o hotfix num controlador, inscreva-se no outro controlador. Aplique o hotfix como fez para o controlador anterior.
6. Depois de aplicados os hotfixes, saia do modo de manutenção. Consulte o [passo 4: Modo de manutenção de saída](../articles/storsimple/storsimple-update-device.md#step4) para obter instruções.

