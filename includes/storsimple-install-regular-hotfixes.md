---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8cc5dbb907c342b766cebe6da36cf580ddac5e2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "67184291"
---
#### <a name="to-install-regular-hotfixes-via-windows-powershell-for-storsimple"></a>Para instalar hotfixes regulares via Windows PowerShell para StorSimple
1. Ligue-se à consola em série do dispositivo. Para obter mais informações, consulte [o Passo 1: Ligue-se à consola em série](../articles/storsimple/storsimple-update-device.md#step1).
2. No menu de consola em série, selecione a opção 1, **faça login com acesso total**. Digite a senha. A palavra-passe predefinida é **Palavra-passe1**.
3. Na linha de comandos, escreva:
   
    ```
    Start-HcsHotfix
    ```
   
    > [!IMPORTANT]
    >
    > Este comando aplica-se apenas a hotfixes regulares. Executar este comando num único controlador, mas ambos os controladores serão atualizados.
    > Pode notar um falha no controlador durante o processo de atualização; no entanto, a falha não afetará a disponibilidade ou o funcionamento do sistema.

4. Quando solicitado, forneça o caminho para a pasta partilhada na rede que contém os ficheiros hotfix.
5. Ser-lhe-á pedido que confirme. Tipo **Y** para proceder à instalação de hotfix.

