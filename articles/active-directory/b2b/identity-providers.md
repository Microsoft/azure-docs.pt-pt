---
title: Fornecedores de identidade para identidades externas - Azure AD
description: A colaboração azure Ative Directory B2B apoia a autenticação de vários fatores (MFA) para acesso seletivo às suas aplicações corporativas
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2284d015b451872753dd0855cac42e6f1926545c
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83712167"
---
# <a name="identity-providers-for-external-identities"></a>Fornecedores de Identidade de Identidade Externa

Um fornecedor de *identidade* cria, mantém e gere informações de identidade ao mesmo tempo que presta serviços de autenticação a aplicações. Ao partilhar as suas aplicações e recursos com utilizadores externos, o Azure AD é o fornecedor de identidade padrão para a partilha. Isto significa que quando convida utilizadores externos que já tenham uma conta Azure AD ou Microsoft, podem iniciar sessão automaticamente sem configuração adicional da sua parte.

No entanto, pode permitir que os utilizadores se inscrevam com vários fornecedores de identidade.

- **Google**: A federação da Google permite que os utilizadores externos redimam convites de si, inserindo nas suas aplicações com as suas próprias contas gmail. A federação da Google também pode ser usada nos fluxos de utilizadores de inscrição de autosserviço.
   > [!NOTE]
   > Na pré-visualização de inscrição de self-service atual, se um fluxo de utilizador estiver associado a uma aplicação e enviar um convite a um utilizador para essa aplicação, o utilizador não poderá utilizar uma conta do Gmail para resgatar o convite. Como uma suver, o utilizador pode passar pelo processo de inscrição de self-service. Ou podem resgatar o convite acedendo a uma aplicação diferente ou utilizando o portal My Apps em https://myapps.microsoft.com .

- **Facebook**: Ao construir uma aplicação, pode configurar o autosserviço e ativar a federação do Facebook para que os utilizadores possam inscrever-se na sua aplicação usando as suas próprias contas de Facebook. O Facebook só pode ser utilizado para fluxos de utilizadores de auto-atendimento e não está disponível como opção de entrada quando os utilizadores estão a resgatar convites de si.

- **Federação Direta**: Também pode criar uma federação direta com qualquer fornecedor de identidade externa que suporte os protocolos SAML ou WS-Fed. A Federação Direta permite que os utilizadores externos redimam convites de si, inserindo nas suas apps as suas contas sociais ou empresariais existentes. 
   > [!NOTE]
   > Os fornecedores de identidade da federação direta não podem ser utilizados nos fluxos de utilizadores de inscrição de autosserviço.


## <a name="how-it-works"></a>Como funciona

A funcionalidade de inscrição de autosserviço da Azure AD External Identities permite que os utilizadores se inscrevam na sua conta Azure AD, Google ou Facebook. Para criar fornecedores de identidade social no seu inquilino Azure AD, você vai criar uma aplicação em cada fornecedor de identidade e configurar credenciais. Você obterá um cliente ou app ID e um cliente ou app secret, que você pode adicionar ao seu inquilino Azure AD.

Uma vez adicionado um fornecedor de identidade ao seu inquilino Azure AD:

- Quando convida um utilizador externo para apps ou recursos na sua organização, o utilizador externo pode inscrever-se na utilização da sua própria conta com esse fornecedor de identidade.
- Ao ativar o [autosserviço de inscrição](self-service-sign-up-overview.md) para as suas apps, os utilizadores externos podem inscrever-se nas suas aplicações utilizando as suas próprias contas com os fornecedores de identidade que adicionou.

> [!NOTE]
> O Azure AD está ativado por predefinição para o autosserviço, pelo que os utilizadores têm sempre a opção de se inscreverem através de uma conta Azure AD.

Ao resgatar o seu convite ou inscrever-se na sua app, o utilizador externo tem a opção de iniciar sessão e autenticar com o fornecedor de identidade social:

![Screenshot mostrando o ecrã de sessão com opções do Google e Facebook](media/identity-providers/sign-in-with-social-identity.png)

Para uma experiência de sessão ideal, federar-se com fornecedores de identidade sempre que possível para que possa dar aos seus convidados uma experiência de sessão de sem emenda quando acedem às suas apps.  

## <a name="next-steps"></a>Próximos passos

Para saber adicionar fornecedores de identidade para iniciar sessão nas suas candidaturas, consulte os seguintes artigos:

- [Adicione](google-federation.md) o Google à sua lista de fornecedores de identidade social
- [Adicione](facebook-federation.md) o Facebook à sua lista de fornecedores de identidade social
- [Criar uma federação direta](direct-federation.md) com qualquer organização cujo fornecedor de identidade apoie o protocolo SAML 2.0 ou WS-Fed. Note que a federação direta não é uma opção para fluxos de utilizadores de inscrição de autosserviço.
