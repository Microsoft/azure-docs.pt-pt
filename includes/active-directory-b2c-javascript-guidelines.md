---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/11/2020
ms.author: mimart
ms.openlocfilehash: ef08f1adc15475cd5dd38548ed39dc57532c0ef5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78185845"
---
## <a name="guidelines-for-using-javascript"></a>Diretrizes para a utilização do JavaScript

Siga estas diretrizes quando personalizar a interface da sua aplicação utilizando o JavaScript:

- Não ligue um evento `<a>` de clique em elementos HTML.
- Não assuma a dependência do código B2C da Azure AD ou comentários.
- Não altere a ordem ou a hierarquia dos elementos Azure AD B2C HTML. Utilize uma política Azure AD AD B2C para controlar a ordem dos elementos UI.
- Pode ligar para qualquer serviço RESTful com estas considerações:
    - Poderá ter de definir o seu serviço RESTful CORS para permitir chamadas HTTP do lado do cliente.
    - Certifique-se de que o seu serviço RESTful está seguro e utiliza apenas o protocolo HTTPS.
    - Não utilize o JavaScript diretamente para ligar para os pontos finais do Azure AD B2C.
- Pode incorporar o seu JavaScript ou pode ligar-se a ficheiros JavaScript externos. Ao utilizar um ficheiro JavaScript externo, certifique-se de utilizar o URL absoluto e não um URL relativo.
- Quadros JavaScript:
    - O Azure AD B2C utiliza uma versão específica do jQuery. Não inclua outra versão do jQuery. Usar mais do que uma versão na mesma página causa problemas.
    - A utilização do RequirejS não é suportada.
    - A maioria dos quadros JavaScript não são suportados pelo Azure AD B2C.
- As definições de AD B2C `window.SETTINGS` `window.CONTENT` do Azure podem ser lidas através de chamadas, objetos, como a língua ui atual. Não mude o valor destes objetos.
- Para personalizar a mensagem de erro Azure AD B2C, utilize a localização numa política.
- Se algo pode ser alcançado usando uma política, geralmente é a maneira recomendada.
