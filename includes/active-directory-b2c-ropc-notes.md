---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/27/2020
ms.author: mimart
ms.openlocfilehash: f4944881e6eff3e9a56a74bc997c280f9fff6be8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "78187010"
---
## <a name="ropc-flow-notes"></a>Notas de fluxo ROPC
No Azure Ative Directory B2C (Azure AD B2C), são suportadas as seguintes opções:

- **Cliente Nativo**: A interação do utilizador durante a autenticação ocorre quando o código é executado num dispositivo do lado do utilizador. O dispositivo pode ser uma aplicação móvel que está a ser operada num sistema operativo nativo, como o Android e o iOS.
- **Fluxo de cliente público**: Apenas as credenciais de utilizador, recolhidas por uma aplicação, são enviadas na chamada da API. As credenciais do pedido não são enviadas.
- **Adicionar novas reclamações**: O conteúdo do iD token pode ser alterado para adicionar novas reclamações.

Os seguintes fluxos não são suportados:

- **Servidor-a-servidor**: O sistema de proteção de identidade necessita de um endereço IP fiável recolhido do chamador (o cliente nativo) como parte da interação. Numa chamada API do lado do servidor, apenas o endereço IP do servidor é utilizado. Se um limiar dinâmico de autenticações falhadas for ultrapassado, o sistema de proteção de identidade pode identificar um endereço IP repetido como intruso.
- **Fluxo de cliente confidencial**: O ID do cliente de aplicação é validado, mas o segredo da aplicação não é validado.

Ao utilizar o fluxo ROPC, considere o seguinte:

- ROPC não funciona quando há qualquer interrupção no fluxo de autenticação que precisa de interação do utilizador. Por exemplo, quando uma palavra-passe expirou ou precisa de ser alterada, é necessária a autenticação de vários fatores ou quando mais informações precisam de ser recolhidas durante a sinvação (por exemplo, consentimento do utilizador).
- A ROPC suporta apenas contas locais. Os utilizadores não podem entrar com fornecedores de identidade federados como Microsoft, Google+, Twitter, AD-FS ou Facebook.
- A Gestão de Sessão, incluindo manter-me assinado (KMSI), não é aplicável.
