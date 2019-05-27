---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 560c9c177bfa693580979101e5b9343fcff7fe40
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66171158"
---
### <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Instale as atualizações de modo de manutenção através do Windows PowerShell para StorSimple

Quando aplica as atualizações de modo de manutenção para o dispositivo StorSimple, todos os pedidos de e/s são colocadas em pausa. Serviços, tais como a memória de acesso de aleatório não volátil (NVRAM) ou o serviço de clustering são paradas. Os dois controladores de reinício quando entrem ou saia neste modo. Quando sair deste modo, todos os serviços retomam e estão em bom Estados. (Pode demorar alguns minutos.)

> [!IMPORTANT]
> * Antes de entrar no modo de manutenção, certifique-se de que os dois controladores de dispositivo estão em bom Estados no portal do Azure. Se o controlador não está em bom estado, [contacte o suporte da Microsoft](../articles/storsimple/storsimple-8000-contact-microsoft-support.md) para os passos seguintes.
> * Quando estiver no modo de manutenção, tem primeiro de atualizar um controlador e, em seguida, o controlador de outro.

1. Utilize o PuTTY para ligar à consola de série. Siga as instruções detalhadas em [Utilizar o PuTTY para ligar à consola de série](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). Na linha de comandos, prima **Enter**. Selecione a opção 1, **iniciar sessão com acesso total**.

2. Para colocar o controlador em modo de manutenção, escreva:
    
    `Enter-HcsMaintenanceMode`

    Ambos os controladores reiniciam em modo de manutenção.

3. Instale as atualizações de modo de manutenção. Escreva:

    `Start-HcsUpdate`

    For pedida a confirmação. Depois de confirmar as atualizações, serão instaladas no controlador que está atualmente a aceder. Depois das atualizações são instaladas, reinicia o controlador.

4. Monitorize o estado das atualizações. À medida que o controlador atual está a ser atualizada e não é capaz de processar todos os outros comandos, inicie sessão no controlador de ponto a ponto. Escreva:

    `Get-HcsUpdateStatus`

    Se o `RunInProgress` é `True`, a atualização ainda está em curso. Se `RunInProgress` é `False`, ele indica que a atualização foi concluída.

5. Depois das atualizações de firmware do disco forem aplicadas com êxito e reiniciou o controlador atualizado, verifique se a versão de firmware do disco. No controlador de atualizados, escreva:

    `Get-HcsFirmwareVersion`
   
    As versões de firmware do disco esperadas são:  `XMGJ, XGEG, KZ50, F6C2, VR08, N003, 0107`

6. Sair do modo de manutenção. Escreva o seguinte comando para cada controlador de dispositivo:

    `Exit-HcsMaintenanceMode`

    Os controladores reiniciam quando sair do modo de manutenção.

7. Regresse ao portal do Azure. O portal não pode mostrar que instalou as atualizações de modo de manutenção durante 24 horas.