---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66110574"
---
Como com os tokens de acesso, se não estiver configurado um token do Azure AD, tem manipular o evento de TokenRequired, ou implementar o método de tokenRequired no protocolo delegado.

Pode processar o evento de forma síncrona, definindo a propriedade nos argumentos do evento.
