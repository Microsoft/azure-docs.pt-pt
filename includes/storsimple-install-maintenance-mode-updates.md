---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 720288aff462b0590bb9da509096a9305b9b6cc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "67184294"
---
#### <a name="to-install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Para instalar atualizações do modo manutenção via Windows PowerShell para StorSimple
1. Se ainda não o fez, aceda à consola em série do dispositivo e selecione a opção 1, **faça login com acesso total**. 
2. Digite a senha. A palavra-passe predefinida é **Palavra-passe1**.
3. Na linha de comandos, escreva:
   
     `Get-HcsUpdateAvailability` 
4. Será notificado se as atualizações estiverem disponíveis e se as atualizações forem disruptivas ou não disruptivas. Para aplicar atualizações disruptivas, é necessário colocar o dispositivo no modo manutenção. Ver [Passo 2: Introduza o modo manutenção](../articles/storsimple/storsimple-update-device.md#step2) para obter instruções.
5. Quando o seu dispositivo estiver em modo manutenção, na indicação de comando, escreva: `Start-HcsUpdate`
6. Ser-lhe-á pedido que confirme. Depois de confirmar as atualizações, estas serão instaladas no controlador a que está atualmente a aceder. Após a instalação das atualizações, o controlador reiniciará. 
7. Monitorize o estado das atualizações. Escreva:
   
    `Get-HcsUpdateStatus`
   
    Se `RunInProgress` `True` for, a atualização ainda está em curso. Se `RunInProgress` `False` for, indica que a atualização foi concluída.  
8. Quando a atualização for instalada no controlador atual e tiver sido reiniciada, ligue-se ao outro controlador e execute os passos 1 a 6.
9. Depois de atualizados os dois controladores, saia do modo manutenção. Consulte [o passo 4: Modo de manutenção de saída](../articles/storsimple/storsimple-update-device.md#step4) para obter instruções.

