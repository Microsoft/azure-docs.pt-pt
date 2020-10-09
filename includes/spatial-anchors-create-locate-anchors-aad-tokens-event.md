---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183961"
---
Tal como acontece com os tokens de acesso, se não estiver definido um token AD Ad, tens de lidar com o evento TokenRequired ou implementar o método tokenRequired no protocolo de delegado.

Você pode lidar com o evento sincronizadamente definindo a propriedade nos argumentos do evento.
