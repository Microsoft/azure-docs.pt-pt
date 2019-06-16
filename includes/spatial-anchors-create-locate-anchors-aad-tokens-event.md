---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66110574"
---
Como com os tokens de acesso, se não estiver configurado um token do Azure AD, tem manipular o evento de TokenRequired, ou implementar o método de tokenRequired no protocolo delegado.

Pode processar o evento de forma síncrona, definindo a propriedade nos argumentos do evento.
