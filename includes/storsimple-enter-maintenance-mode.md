---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d35f0ef783a2c48f8211657bc8829635c19495aa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184286"
---
#### <a name="to-enter-maintenance-mode"></a>Para entrar no modo manutenção
1. No menu de consola em série, escolha a opção 1, **faça login com acesso total**.
2. Digite a senha. A palavra-passe predefinida é **Palavra-passe1**.
3. Na solicitação de comando, escreva
   
     `Enter-HcsMaintenanceMode`
4. Verá uma mensagem de aviso a dizer-lhe que o modo manutenção irá interromper todos os pedidos de E/S e cortar a ligação ao portal clássico Azure, e será solicitado para confirmação. Tipo **Y** para entrar no modo manutenção.
   
    Ambos os controladores vão reiniciar. Quando o reinício estiver concluído, aparecerá outra mensagem indicando que o dispositivo se encontra no modo manutenção.

