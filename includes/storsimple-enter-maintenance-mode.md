---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d35f0ef783a2c48f8211657bc8829635c19495aa
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184286"
---
#### <a name="to-enter-maintenance-mode"></a>Para entrar no modo de manutenção
1. No menu da consola de série, selecione a opção 1, **iniciar sessão com acesso total**.
2. Escreva a palavra-passe. A palavra-passe predefinido é **Password1**.
3. Na linha de comandos, escreva
   
     `Enter-HcsMaintenanceMode`
4. Verá uma mensagem de aviso informando que o modo de manutenção irá interromper todos os pedidos de e/s e a ligação ao portal clássico do Azure do servidor, e será solicitado para confirmação. Tipo **Y** para entrar no modo de manutenção.
   
    Os dois controladores serão reiniciado. Quando o reinício estiver concluído, a outra mensagem será apresentada indicando que o dispositivo estiver no modo de manutenção.

