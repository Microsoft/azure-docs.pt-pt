---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/25/2020
ms.locfileid: "67183961"
---
Tal como acontece com os tokens de acesso, se não estiver definido um token AD Ad, tens de lidar com o evento TokenRequired ou implementar o método tokenRequired no protocolo de delegado.

Você pode lidar com o evento sincronizadamente definindo a propriedade nos argumentos do evento.
