---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/11/2020
ms.author: mimart
ms.openlocfilehash: ef08f1adc15475cd5dd38548ed39dc57532c0ef5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "78185845"
---
## <a name="guidelines-for-using-javascript"></a>Diretrizes para a utilização do JavaScript

Siga estas diretrizes quando personalizar a interface da sua aplicação utilizando o JavaScript:

- Não ligue um evento de clique em `<a>` elementos HTML.
- Não assuma uma dependência do código B2C Azure AD B2C ou comentários.
- Não altere a ordem ou hierarquia dos elementos HTML Azure AD B2C. Utilize uma política Azure AD B2C para controlar a ordem dos elementos da UI.
- Pode ligar para qualquer serviço RESTful com estas considerações:
    - Poderá ser necessário definir o seu serviço RESTful CORS para permitir chamadas HTTP do lado do cliente.
    - Certifique-se de que o seu serviço RESTful está seguro e utiliza apenas o protocolo HTTPS.
    - Não utilize o JavaScript diretamente para ligar para os pontos finais Azure AD B2C.
- Pode incorporar o JavaScript ou pode ligar-se a ficheiros JavaScript externos. Ao utilizar um ficheiro JavaScript externo, certifique-se de que utiliza o URL absoluto e não um URL relativo.
- Estruturas JavaScript:
    - Azure AD B2C utiliza uma versão específica do jQuery. Não inclua outra versão do jQuery. Usar mais do que uma versão na mesma página causa problemas.
    - A utilização do RequireJS não é suportada.
    - A maioria das estruturas JavaScript não são suportadas pelo Azure AD B2C.
- As definições AZURE AD B2C podem ser lidas `window.SETTINGS` chamando, `window.CONTENT` objetos, como a língua ui atual. Não mude o valor destes objetos.
- Para personalizar a mensagem de erro Azure AD B2C, utilize a localização numa política.
- Se algo pode ser alcançado usando uma política, geralmente é a maneira recomendada.
