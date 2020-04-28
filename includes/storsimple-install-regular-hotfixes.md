---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8cc5dbb907c342b766cebe6da36cf580ddac5e2c
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "67184291"
---
#### <a name="to-install-regular-hotfixes-via-windows-powershell-for-storsimple"></a>Para instalar hotfixes regulares via Windows PowerShell para StorSimple
1. Ligue-se à consola em série do dispositivo. Para mais informações, consulte [passo 1: Ligue-se à consola em série](../articles/storsimple/storsimple-update-device.md#step1).
2. No menu de consola em série, selecione a opção 1, **inicie sessão com acesso total**. Digite a senha. A palavra-passe padrão é **Password1**.
3. Na linha de comandos, escreva:
   
    ```
    Start-HcsHotfix
    ```
   
    > [!IMPORTANT]
    >
    > Este comando aplica-se apenas a fixações regulares. Manere este comando num único controlador, mas ambos os controladores serão atualizados.
    > Pode notar uma falha no controlador durante o processo de atualização; no entanto, a falha não afetará a disponibilidade ou operação do sistema.

4. Quando solicitado, forneça o caminho para a pasta partilhada pela rede que contenha os ficheiros hotfix.
5. Ser-lhe-á pedido que confirme. Digite **Y** para prosseguir com a instalação hotfix.

