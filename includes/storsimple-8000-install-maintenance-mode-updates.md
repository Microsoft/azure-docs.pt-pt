---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 560c9c177bfa693580979101e5b9343fcff7fe40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67184455"
---
### <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Instale atualizações do modo de manutenção via Windows PowerShell para StorSimple

Quando aplica atualizações do modo de manutenção no dispositivo StorSimple, todos os pedidos de I/O são interrompidos. Serviços como a memória de acesso aleatório não volátil (NVRAM) ou o serviço de agrupamento são interrompidos. Ambos os controladores reiniciam quando entram ou saem deste modo. Quando sai deste modo, todos os serviços retomam e são saudáveis. (Isto pode levar alguns minutos.)

> [!IMPORTANT]
> * Antes de entrar no modo de manutenção, verifique se ambos os controladores do dispositivo estão saudáveis no portal Azure. Se o controlador não estiver saudável, [contacte](../articles/storsimple/storsimple-8000-contact-microsoft-support.md) o Suporte da Microsoft para os próximos passos.
> * Quando estiver em modo de manutenção, tem de atualizar primeiro um controlador e depois o outro controlador.

1. Utilize o PuTTY para se ligar à consola em série. Siga as instruções detalhadas em [Utilizar o PuTTY para ligar à consola de série](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). Na linha de comandos, prima **Enter**. Selecione Opção 1, **Inicie sessão com acesso completo**.

2. Para colocar o controlador no modo de manutenção, escreva:
    
    `Enter-HcsMaintenanceMode`

    Ambos os controladores reiniciam no modo de manutenção.

3. Instale as atualizações do modo de manutenção. Escreva:

    `Start-HcsUpdate`

    Foi solicitado para confirmação. Depois de confirmar as atualizações, estas estão instaladas no controlador a que está a aceder. Após a instalação das atualizações, o controlador reinicia.

4. Monitorize o estado das atualizações. Inscreva-se no controlador de pares, uma vez que o controlador atual está a atualizar-se e não é capaz de processar quaisquer outros comandos. Escreva:

    `Get-HcsUpdateStatus`

    Se `RunInProgress` `True`for, a atualização ainda está em curso. Se `RunInProgress` `False`estiver, indica que a atualização está concluída.

5. Depois de as atualizações do firmware do disco serem aplicadas com sucesso e o controlador atualizado ter reiniciado, verifique a versão firmware do disco. No controlador atualizado, escreva:

    `Get-HcsFirmwareVersion`
   
    As versões de firmware de disco esperadas são:`XMGJ, XGEG, KZ50, F6C2, VR08, N003, 0107`

6. Saia do modo de manutenção. Digite o seguinte comando para cada controlador de dispositivo:

    `Exit-HcsMaintenanceMode`

    Os controladores reiniciam quando sair do modo de manutenção.

7. Regresse ao portal do Azure. O portal pode não mostrar que instalou as atualizações do modo de manutenção durante 24 horas.