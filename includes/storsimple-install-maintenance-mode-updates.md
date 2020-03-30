---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 720288aff462b0590bb9da509096a9305b9b6cc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67184294"
---
#### <a name="to-install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Para instalar atualizações do modo de manutenção via Windows PowerShell para StorSimple
1. Se ainda não o fez, aceda à consola em série do dispositivo e selecione a opção 1, **inicie sessão com acesso total**. 
2. Digite a senha. A palavra-passe padrão é **Password1**.
3. Na linha de comandos, escreva:
   
     `Get-HcsUpdateAvailability` 
4. Será notificado se as atualizações estiverem disponíveis e se as atualizações são disruptivas ou não disruptivas. Para aplicar atualizações disruptivas, é necessário colocar o dispositivo no modo de manutenção. Consulte o [passo 2: Introduza](../articles/storsimple/storsimple-update-device.md#step2) o modo de manutenção para obter instruções.
5. Quando o seu dispositivo estiver no modo de manutenção, no pedido de comando, escreva:`Start-HcsUpdate`
6. Ser-lhe-á pedido que confirme. Depois de confirmar as atualizações, estas serão instaladas no controlador a que está a aceder. Após a instalação das atualizações, o controlador reiniciará. 
7. Monitorize o estado das atualizações. Escreva:
   
    `Get-HcsUpdateStatus`
   
    Se `RunInProgress` `True`for, a atualização ainda está em curso. Se `RunInProgress` `False`estiver, indica que a atualização está concluída.  
8. Quando a atualização for instalada no controlador atual e tiver reiniciado, ligue-se ao outro controlador e execute os passos 1 a 6.
9. Depois de ambos os controladores serem atualizados, saia do modo de manutenção. Consulte o [passo 4: Modo de manutenção de saída](../articles/storsimple/storsimple-update-device.md#step4) para obter instruções.

