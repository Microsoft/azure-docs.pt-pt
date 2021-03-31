---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: dc50f94ae9b207961a71480c2fc172e88db79cf4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "67184292"
---
#### <a name="to-install-regular-updates-via-windows-powershell-for-storsimple"></a>Para instalar atualizações regulares via Windows PowerShell para StorSimple
1. Abra a consola em série do dispositivo e selecione a opção 1, **faça login com acesso total**. Digite a senha. A palavra-passe predefinida é *Palavra-passe1*. 
2. Na linha de comandos, escreva:
   
     `Get-HcsUpdateAvailability`
   
    Será notificado se as atualizações estiverem disponíveis e se as atualizações forem disruptivas ou não disruptivas.
3. Na linha de comandos, escreva:
   
     `Start-HcsUpdate`
   
    O processo de atualização começará.

> [!IMPORTANT]
> * Este comando aplica-se apenas a atualizações regulares. Executar este comando num único controlador, mas ambos os controladores serão atualizados. 
> * Pode notar um falha no controlador durante o processo de atualização; no entanto, a falha não afetará a disponibilidade ou o funcionamento do sistema.
> 
> 

