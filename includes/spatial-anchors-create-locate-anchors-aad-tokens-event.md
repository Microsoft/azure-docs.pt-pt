---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232411"
---
Como com os tokens de acesso, se não estiver configurado um token do Azure AD, tem manipular o evento de TokenRequired, ou implementar o método de tokenRequired no protocolo delegado.

Pode processar o evento de forma síncrona, definindo a propriedade nos argumentos do evento.
