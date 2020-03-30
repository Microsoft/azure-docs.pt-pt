---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d35f0ef783a2c48f8211657bc8829635c19495aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67184286"
---
#### <a name="to-enter-maintenance-mode"></a>Para entrar no modo de manutenção
1. No menu de consola em série, escolha a opção 1, **inicie sessão com acesso total**.
2. Digite a senha. A palavra-passe padrão é **Password1**.
3. No pedido de comando, tipo
   
     `Enter-HcsMaintenanceMode`
4. Verá uma mensagem de aviso a dizer-lhe que o modo de manutenção irá interromper todos os pedidos de I/O e cortar a ligação ao portal clássico Azure, e será solicitado para confirmação. Digite **Y** para entrar no modo de manutenção.
   
    Ambos os controladores vão reiniciar. Quando o reinício estiver concluído, aparecerá outra mensagem indicando que o dispositivo se encontra no modo de manutenção.

