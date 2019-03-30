---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632608"
---
Como com os tokens de acesso, se não estiver configurado um token do Azure AD, tem manipular o evento de TokenRequired, ou implementar o método de tokenRequired no protocolo delegado.

Pode processar o evento de forma síncrona, definindo a propriedade nos argumentos do evento.
