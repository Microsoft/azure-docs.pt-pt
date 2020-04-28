---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/27/2020
ms.author: mimart
ms.openlocfilehash: f4944881e6eff3e9a56a74bc997c280f9fff6be8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78187010"
---
## <a name="ropc-flow-notes"></a>Notas de fluxo ROPC
No Diretório Ativo Azure B2C (Azure AD B2C), são suportadas as seguintes opções:

- **Cliente Nativo**: A interação do utilizador durante a autenticação ocorre quando o código é executado num dispositivo do lado do utilizador. O dispositivo pode ser uma aplicação móvel que está a funcionar num sistema operativo nativo, como android e iOS.
- **Fluxo público do cliente**: Apenas as credenciais de utilizador, recolhidas por uma aplicação, são enviadas na chamada DaPI. As credenciais do pedido não são enviadas.
- **Adicione novas reclamações**: O conteúdo do token ID pode ser alterado para adicionar novas reclamações.

Os seguintes fluxos não são suportados:

- **Servidor-a-servidor**: O sistema de proteção de identidade necessita de um endereço IP fiável recolhido do chamador (o cliente nativo) como parte da interação. Numa chamada API do lado do servidor, apenas é utilizado o endereço IP do servidor. Se for ultrapassado um limiar dinâmico de autenticações falhadas, o sistema de proteção de identidade pode identificar um endereço IP repetido como um intruso.
- **Fluxo confidencial do cliente**: O ID do cliente da aplicação é validado, mas o segredo da aplicação não é validado.

Ao utilizar o fluxo ROPC, considere o seguinte:

- O ROPC não funciona quando há qualquer interrupção do fluxo de autenticação que necessita de interação do utilizador. Por exemplo, quando uma palavra-passe expirou ou precisa de ser alterada, é necessária a autenticação de vários fatores, ou quando mais informações precisam de ser recolhidas durante o sessão (por exemplo, consentimento do utilizador).
- A ROPC apoia apenas as contas locais. Os utilizadores não podem iniciar sessão com fornecedores de identidade federados como microsoft, Google+, Twitter, AD-FS ou Facebook.
- A Gestão de Sessões, incluindo manter-me inscrito (KMSI), não é aplicável.
