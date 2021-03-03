---
title: Fornecedores de identidades externas - Azure AD
description: Saiba como usar o Azure AD como fornecedor de identidade padrão para partilhar com utilizadores externos.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdef929b27c636b3908dd7a88eb93adc2382a53f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101687752"
---
# <a name="identity-providers-for-external-identities"></a>Fornecedores de Identidades Externas

> [!NOTE]
> Algumas das funcionalidades mencionadas neste artigo são características de pré-visualização públicas do Azure Ative Directory. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Um *fornecedor de identidade* cria, mantém e gere informações de identidade ao mesmo tempo que presta serviços de autenticação às aplicações. Ao partilhar as suas apps e recursos com utilizadores externos, o Azure AD é o fornecedor de identidade padrão para a partilha. Isto significa que quando convida utilizadores externos que já tenham uma conta Azure AD ou Microsoft, podem iniciar súm automaticamente sem configuração adicional da sua parte.

Além das contas AD da Azure, as Identidades Externas oferecem uma variedade de fornecedores de identidade.

- Contas microsoft (Preview): Os utilizadores **convidados** podem usar a sua própria conta pessoal da Microsoft (MSA) para resgatar os seus convites de colaboração B2B. Ao configurar um fluxo de utilizador de inscrição de autosserviço, pode adicionar [a Conta Microsoft (Pré-visualização)](microsoft-account.md) como um dos fornecedores de identidade autorizados. Não é necessária nenhuma configuração adicional para disponibilizar este fornecedor de identidade para os fluxos dos utilizadores.

- **Código de acesso único por e-mail** (Pré-visualização): Ao resgatar um convite ou aceder a um recurso partilhado, um utilizador convidado pode solicitar um código temporário, que é enviado para o seu endereço de e-mail. Depois introduzem este código para continuarem a iniciar sessão. A funcionalidade de código de acesso único do e-mail autentica os utilizadores convidados B2B quando não podem ser autenticados através de outros meios. Ao configurar um fluxo de utilizador de inscrição de autosserviço, pode adicionar **email One-Time Código de Acesso (Preview)** como um dos fornecedores de identidade permitidos. É necessária alguma configuração; ver [e-mail autenticação de código de acesso](one-time-passcode.md)único.

- **Google**: A federação do Google permite que utilizadores externos resgatem convites de si, insinundo-se nas suas apps com as suas próprias contas Gmail. A federação da Google também pode ser usada nos fluxos de utilizador de inscrição de autosserviço. Veja como adicionar o [Google como fornecedor de identidade.](google-federation.md)
   > [!IMPORTANT]
   > **A partir de 4 de janeiro de 2021, a** Google está [a depreciar o suporte de sing-in webView](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Se estiver a utilizar a federação do Google ou a inscrição de self-service com o Gmail, deverá [testar as suas aplicações nativas de linha de negócios para compatibilidade](google-federation.md#deprecation-of-webview-sign-in-support).

- **Facebook**: Ao construir uma aplicação, pode configurar a inscrição de self-service e ativar a federação do Facebook para que os utilizadores possam inscrever-se na sua aplicação usando as suas próprias contas de Facebook. O Facebook só pode ser utilizado para fluxos de utilizadores de inscrição de autosserviço e não está disponível como opção de entrada quando os utilizadores estão a resgatar convites de si. Veja como [adicionar o Facebook como fornecedor de identidade.](facebook-federation.md)

- **Federação Direta**: Pode ainda criar uma federação direta com qualquer fornecedor de identidade externa que suporte os protocolos SAML ou WS-Fed. A federação direta permite que utilizadores externos resgatem convites de si, insinando-se nas suas apps com as suas contas sociais ou empresariais existentes. Veja como [criar uma federação direta.](direct-federation.md)
   > [!NOTE]
   > Os fornecedores de identidade da federação direta não podem ser utilizados nos fluxos de utilizador de inscrição de autosserviço.

## <a name="adding-social-identity-providers"></a>Adicionar fornecedores de identidade social

O Azure AD é ativado por padrão para inscrição de autosserviço, por isso os utilizadores têm sempre a opção de se inscrever usando uma conta Azure AD. No entanto, pode ativar outros fornecedores de identidade, incluindo fornecedores de identidade social, como o Google ou o Facebook. Para configurar fornecedores de identidade social no seu inquilino Azure AD, irá criar um pedido no fornecedor de identidade e configurar credenciais. Você obtém um ID de cliente ou app e um cliente ou app secret, que você pode então adicionar ao seu inquilino AZURE AD.

Uma vez adicionado um fornecedor de identidade ao seu inquilino AZure AD:

- Quando convida um utilizador externo para apps ou recursos na sua organização, o utilizador externo pode iniciar sômss usando a sua própria conta com esse fornecedor de identidade.
- Quando ativar [a inscrição de self-service](self-service-sign-up-overview.md) para as suas apps, os utilizadores externos podem inscrever-se nas suas apps usando as suas próprias contas com os fornecedores de identidade que adicionou. Eles poderão selecionar a partir das opções de fornecedores de identidade social que disponibilizou na página de inscrição:

   ![Screenshot mostrando o ecrã de inscrição com opções do Google e Facebook](media/identity-providers/sign-in-with-social-identity.png)

Para uma experiência de inscrição ideal, federa com fornecedores de identidade sempre que possível para que possa dar aos seus convidados uma experiência de inscrição perfeita quando acedem às suas apps.  

## <a name="next-steps"></a>Passos seguintes

Para saber como adicionar fornecedores de identidade para iniciar sôns nas suas aplicações, consulte os seguintes artigos:

- [Adicionar a autenticação de código de acesso de e-mail uma vez](one-time-passcode.md)
- [Adicione o Google](google-federation.md) como um fornecedor de identidade social permitido
- [Adicione o Facebook](facebook-federation.md) como um fornecedor de identidade social permitido
- [Crie uma federação direta](direct-federation.md) com qualquer organização cujo fornecedor de identidade apoie o protocolo SAML 2.0 ou WS-Fed. Note que a federação direta não é uma opção para fluxos de utilizador de inscrição de autosserviço.
