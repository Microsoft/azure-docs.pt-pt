---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/11/2020
ms.author: mimart
ms.openlocfilehash: ef08f1adc15475cd5dd38548ed39dc57532c0ef5
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78185845"
---
## <a name="guidelines-for-using-javascript"></a>Diretrizes para usar o JavaScript

Siga estas diretrizes quando personalizar a interface do seu aplicativo usando JavaScript:

- Não ligue um evento de clique em `<a>` elementos HTML.
- Não se uma dependência no código do Azure AD B2C ou comentários.
- Não altere a ordem ou uma hierarquia de elementos HTML do Azure AD B2C. Utilize uma política do Azure AD B2C para controlar a ordem dos elementos da interface do Usuário.
- Pode chamar qualquer serviço RESTful com estas considerações:
    - Poderá ter de definir o seu serviço RESTful CORS para permitir chamadas HTTP do lado do cliente.
    - Certifique-se de que o seu serviço RESTful é protegido e utiliza apenas o protocolo HTTPS.
    - Não utilize o JavaScript diretamente para chamar pontos finais do Azure AD B2C.
- Pode incorporar o JavaScript ou pode ligar a arquivos JavaScript externos. Ao usar um arquivo JavaScript externo, certifique-se utilizar o URL absoluto e não um URL relativo.
- Estruturas do JavaScript:
    - O Azure AD B2C utiliza uma versão específica do jQuery. Não inclua a outra versão da jQuery. Utilizar mais de uma versão na mesma página faz com que os problemas.
    - Usar o RequireJS não é suportada.
    - A maioria das estruturas de JavaScript não são suportadas pelo Azure AD B2C.
- As definições de AD B2C do Azure podem ser lidas através da chamada `window.SETTINGS`, `window.CONTENT` objetos, como a língua ui atual. Não altere o valor desses objetos.
- Para personalizar a mensagem de erro do Azure AD B2C, use a localização numa política.
- Se nada pode ser obtido com uma política, geralmente é a forma recomendada.
