---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183961"
---
Como com os tokens de acesso, se não estiver configurado um token do Azure AD, tem manipular o evento de TokenRequired, ou implementar o método de tokenRequired no protocolo delegado.

Pode processar o evento de forma síncrona, definindo a propriedade nos argumentos do evento.
