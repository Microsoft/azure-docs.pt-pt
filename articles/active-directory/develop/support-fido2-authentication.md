---
title: Suporte a autenticação sem palavras-passe com teclas FIDO2 em aplicações que desenvolve | Rio Azure
titleSuffix: Microsoft identity platform
description: Este guia de implementação explica como suportar a autenticação sem palavras-passe com chaves de segurança FIDO2 nas aplicações que desenvolve
services: active-directory
author: knicholasa
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 1/29/2021
ms.author: nichola
ms.custom: aaddev
ms.openlocfilehash: 5abece0e272d4b72ba6f787ad44b091df5d45226
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417544"
---
# <a name="support-passwordless-authentication-with-fido2-keys-in-apps-you-develop"></a>Suporte autenticação sem palavras-passe com teclas FIDO2 em apps que desenvolve

Estas configurações e boas práticas irão ajudá-lo a evitar cenários comuns que bloqueiam a [autenticação sem palavras-passe do FIDO2](../../active-directory/authentication/concept-authentication-passwordless.md) de estarem disponíveis para os utilizadores das suas aplicações.

## <a name="general-best-practices"></a>Melhores práticas gerais

### <a name="domain-hints"></a>Dicas de domínio

Não use uma dica de domínio para contornar a [descoberta do reino doméstico.](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) Esta funcionalidade destina-se a tornar as inscrições mais simplificadas, mas o fornecedor de identidade federado pode não suportar a autenticação sem palavras-passe.

### <a name="requiring-specific-credentials"></a>Requerendo credenciais específicas

Se estiver a utilizar o SAML, não especifique que é necessária uma palavra-passe [utilizando o elemento RequestEdAuthnContext](single-sign-on-saml-protocol.md#requestauthncontext).

O elemento RequestAuthnContext é opcional, pelo que para resolver isto pode removê-lo dos seus pedidos de autenticação SAML. Esta é uma boa prática geral, uma vez que a utilização deste elemento também pode impedir que outras opções de autenticação, como a autenticação de vários fatores, funcionem corretamente.

### <a name="using-the-most-recently-used-authentication-method"></a>Usando o método de autenticação mais recentemente usado

O método de inscrição que foi mais recentemente utilizado por um utilizador será-lhes apresentado primeiro. Isto pode causar confusão quando os utilizadores acreditam que devem usar a primeira opção apresentada. No entanto, podem escolher outra opção selecionando "Outras formas de iniciar sposição" como mostrado abaixo.

:::image type="content" source="./media/support-fido2-authentication/most-recently-used-method.png" alt-text="Imagem da experiência de autenticação do utilizador realçando o botão que permite ao utilizador alterar o método de autenticação.":::

## <a name="platform-specific-best-practices"></a>Melhores práticas específicas da plataforma

### <a name="desktop"></a>Ambiente de trabalho

As opções recomendadas para a implementação da autenticação são, por ordem:

- .NET aplicações para desktop que estão a usar a Biblioteca de Autenticação do Microsoft (MSAL) devem utilizar o Gestor de Autenticação do Windows (WAM). Esta integração e os seus benefícios estão [documentados no GitHub.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/wam)
- Utilize [o WebView2](https://docs.microsoft.com/microsoft-edge/webview2/) para suportar o FIDO2 num browser incorporado.
- Use o navegador do sistema. As bibliotecas MSAL para plataformas de desktop utilizam este método por padrão. Pode consultar a nossa página sobre a compatibilidade do navegador FIDO2 para garantir que o navegador que utiliza suporta a autenticação FIDO2.

### <a name="mobile"></a>Móvel

A partir de fevereiro de 2020, o FIDO2 não é atualmente suportado para aplicações nativas do iOS ou Android, mas está em desenvolvimento.

Para preparar aplicações para a sua disponibilidade, e como uma das melhores práticas gerais, as aplicações iOS e Android devem utilizar o MSAL com a sua configuração padrão de utilização do navegador web do sistema.

Se não estiver a utilizar o MSAL, deverá ainda utilizar o navegador web do sistema para autenticação. Funcionalidades como o acesso único e condicional dependem de uma superfície web partilhada fornecida pelo navegador web do sistema. Isto significa utilizar [separadores personalizados do Chrome](https://developer.chrome.com/docs/multidevice/android/customtabs/) (Android) ou [autenticar um utilizador através de um serviço web | Documentação do Desenvolvedor da Apple](https://developer.apple.com/documentation/authenticationservices/authenticating_a_user_through_a_web_service) (iOS).

### <a name="web-and-single-page-apps"></a>Aplicativos web e de página única

A disponibilidade de autenticação sem palavras-passe FIDO2 para aplicações que executam num navegador web dependerá da combinação de navegador e plataforma. Pode consultar a nossa [matriz de compatibilidade FIDO2](../authentication/fido2-compatibility.md) para verificar se a combinação que os seus utilizadores encontrarão é suportada.

## <a name="next-steps"></a>Passos seguintes

[Opções de autenticação sem palavras-passe para Azure Ative Directory](../../active-directory/authentication/concept-authentication-passwordless.md)
