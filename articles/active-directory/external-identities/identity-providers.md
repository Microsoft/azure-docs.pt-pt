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
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42ed42a1fc9a2750cc928b5fd03eb4b32f770276
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644090"
---
# <a name="identity-providers-for-external-identities"></a>Fornecedores de Identidades Externas

Um *fornecedor de identidade* cria, mantém e gere informações de identidade ao mesmo tempo que presta serviços de autenticação às aplicações. Ao partilhar as suas apps e recursos com utilizadores externos, o Azure AD é o fornecedor de identidade padrão para a partilha. Isto significa que quando convida utilizadores externos que já tenham uma conta Azure AD ou Microsoft, podem iniciar súm automaticamente sem configuração adicional da sua parte.

No entanto, pode permitir que os utilizadores entrem em s90 com vários fornecedores de identidade.

- **Google**: A federação do Google permite que utilizadores externos resgatem convites de si, insinundo-se nas suas apps com as suas próprias contas Gmail. A federação da Google também pode ser usada nos fluxos de utilizador de inscrição de autosserviço.
   > [!IMPORTANT]
   > **A partir de 4 de janeiro de 2021, a** Google está [a depreciar o suporte de sing-in webView](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Se estiver a utilizar a federação do Google ou a inscrição de self-service com o Gmail, deverá [testar as suas aplicações nativas de linha de negócios para compatibilidade](google-federation.md#deprecation-of-webview-sign-in-support).

- **Facebook**: Ao construir uma aplicação, pode configurar a inscrição de self-service e ativar a federação do Facebook para que os utilizadores possam inscrever-se na sua aplicação usando as suas próprias contas de Facebook. O Facebook só pode ser utilizado para fluxos de utilizadores de inscrição de autosserviço e não está disponível como opção de entrada quando os utilizadores estão a resgatar convites de si.

- **Federação Direta**: Pode ainda criar uma federação direta com qualquer fornecedor de identidade externa que suporte os protocolos SAML ou WS-Fed. A federação direta permite que utilizadores externos resgatem convites de si, insinando-se nas suas apps com as suas contas sociais ou empresariais existentes. 
   > [!NOTE]
   > Os fornecedores de identidade da federação direta não podem ser utilizados nos fluxos de utilizador de inscrição de autosserviço.


## <a name="how-it-works"></a>Como funciona

A funcionalidade de auto-atendimento de identidades externas Azure AD permite que os utilizadores se inscrevam na sua conta Azure AD, Google ou Facebook. Para configurar fornecedores de identidade social no seu inquilino Azure AD, irá criar um pedido em cada fornecedor de identidade e configurar credenciais. Você obtém um ID de cliente ou app e um cliente ou app secret, que você pode então adicionar ao seu inquilino AZURE AD.

Uma vez adicionado um fornecedor de identidade ao seu inquilino AZure AD:

- Quando convida um utilizador externo para apps ou recursos na sua organização, o utilizador externo pode iniciar sômss usando a sua própria conta com esse fornecedor de identidade.
- Quando ativar [a inscrição de self-service](self-service-sign-up-overview.md) para as suas apps, os utilizadores externos podem inscrever-se nas suas apps usando as suas próprias contas com os fornecedores de identidade que adicionou.

> [!NOTE]
> O Azure AD é ativado por padrão para inscrição de autosserviço, por isso os utilizadores têm sempre a opção de se inscrever usando uma conta Azure AD.

Ao resgatar o seu convite ou inscrever-se na sua app, o utilizador externo tem a opção de iniciar sessão e autenticar com o provedor de identidade social:

![Screenshot mostrando o ecrã de inscrição com opções do Google e Facebook](media/identity-providers/sign-in-with-social-identity.png)

Para uma experiência de inscrição ideal, federa com fornecedores de identidade sempre que possível para que possa dar aos seus convidados uma experiência de inscrição perfeita quando acedem às suas apps.  

## <a name="next-steps"></a>Passos seguintes

Para saber como adicionar fornecedores de identidade para iniciar sôns nas suas aplicações, consulte os seguintes artigos:

- [Adicione o Google](google-federation.md) à sua lista de fornecedores de identidade social
- [Adicione](facebook-federation.md) o Facebook à sua lista de fornecedores de identidade social
- [Crie uma federação direta](direct-federation.md) com qualquer organização cujo fornecedor de identidade apoie o protocolo SAML 2.0 ou WS-Fed. Note que a federação direta não é uma opção para fluxos de utilizador de inscrição de autosserviço.
