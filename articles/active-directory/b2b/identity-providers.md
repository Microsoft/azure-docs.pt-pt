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
ms.openlocfilehash: 395473e70137ead2b7185d54d165f82e9b3f1f04
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598151"
---
# <a name="identity-providers-for-external-identities"></a>Fornecedores de Identidade de Identidade Externa

Um fornecedor de *identidade* cria, mantém e gere informações de identidade ao mesmo tempo que presta serviços de autenticação a aplicações. Ao partilhar as suas aplicações e recursos com utilizadores externos, o Azure AD é o fornecedor de identidade padrão para a partilha. Isto significa que quando convida utilizadores externos que já tenham uma conta Azure AD ou Microsoft, podem iniciar sessão automaticamente sem configuração adicional da sua parte.

No entanto, pode permitir que os utilizadores se inscrevam com vários fornecedores de identidade. Por exemplo, pode configurar a federação com fornecedores de identidade social que são apoiados pela Azure AD, incluindo google e Facebook. Também pode federar com qualquer fornecedor de identidade externo que suporte os protocolos SAML ou WS-Fed. Com a federação externa de fornecedores de identidade, pode oferecer aos utilizadores externos a possibilidade de iniciars sessão nas suas apps com as suas contas sociais ou empresariais existentes.

## <a name="how-it-works"></a>Como funciona

As identidades externas da AD Azure estão pré-configuradas para a federação com o Google e o Facebook. Para configurar estes fornecedores de identidade no seu inquilino Azure AD, você vai criar uma aplicação em cada fornecedor de identidade e configurar credenciais. Você obterá um cliente ou app ID e um cliente ou app secret, que você pode adicionar ao seu inquilino Azure AD.

Uma vez adicionado um fornecedor de identidade ao seu inquilino Azure AD:

- Quando convida um utilizador externo para apps ou recursos na sua organização, o utilizador externo pode inscrever-se na utilização da sua própria conta com esse fornecedor de identidade.
- Ao ativar o [autosserviço de inscrição](self-service-sign-up-overview.md) para as suas apps, os utilizadores externos podem inscrever-se nas suas aplicações utilizando as suas próprias contas com os fornecedores de identidade que adicionou. 

Ao resgatar o seu convite ou inscrever-se na sua app, o utilizador externo tem a opção de iniciar sessão e autenticar com o fornecedor de identidade social:

![Screenshot mostrando o ecrã de sessão com opções do Google e Facebook](media/identity-providers/sign-in-with-social-identity.png)

Para uma experiência de sessão ideal, federar-se com fornecedores de identidade sempre que possível para que possa dar aos seus convidados uma experiência de sessão de sem emenda quando acedem às suas apps.  

## <a name="next-steps"></a>Passos seguintes

Para saber adicionar fornecedores de identidade para iniciar sessão nas suas candidaturas, consulte os seguintes artigos:

- [Adicione](google-federation.md) o Google à sua lista de fornecedores de identidade social
- [Adicione](facebook-federation.md) o Facebook à sua lista de fornecedores de identidade social
- [Criar uma federação direta](direct-federation.md) com qualquer organização cujo fornecedor de identidade apoie o protocolo SAML 2.0 ou WS-Fed. Note que a federação direta não é uma opção para fluxos de utilizadores de inscrição de autosserviço.
