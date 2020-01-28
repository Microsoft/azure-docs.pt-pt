---
title: Protocolos OAuth 2,0 e OpenID Connect-plataforma Microsoft Identity | Azure
description: Um guia para os protocolos OAuth 2,0 e OpenID Connect que são suportados pelo ponto de extremidade da plataforma Microsoft Identity.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 5fb4fa1b-8fc4-438e-b3b0-258d8c145f22
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: d7ed4dbbc3fddb2e21ed3cf5292ebd80fe1e3e23
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76698531"
---
# <a name="oauth-20-and-openid-connect-protocols-on-the-microsoft-identity-platform"></a>Protocolos OAuth 2,0 e OpenID Connect na plataforma de identidade da Microsoft

O ponto de extremidade da plataforma de identidade da Microsoft para identidade como serviço com protocolos padrão do setor, OpenID Connect e OAuth 2,0. Embora o serviço seja compatível com os padrões, pode haver diferenças sutis entre duas implementações desses protocolos. As informações aqui serão úteis se você optar por escrever seu código enviando e manipulando diretamente as solicitações HTTP ou usar uma biblioteca de software livre de terceiros, em vez de usar uma das nossas [bibliotecas de código aberto](reference-v2-libraries.md).

> [!NOTE]
> Nem todos os cenários e recursos do Azure AD têm suporte do ponto de extremidade da plataforma de identidade da Microsoft. Para determinar se você deve usar o ponto de extremidade da plataforma de identidade da Microsoft, leia sobre as [limitações da plataforma de identidade da Microsoft](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Noções básicas

Em quase todos os fluxos do OAuth 2,0 e do OpenID Connect, há quatro partes envolvidas na troca:

![Diagrama mostrando as funções do OAuth 2,0](./media/active-directory-v2-flows/protocols-roles.svg)

* O **servidor de autorização** é o ponto de extremidade da plataforma Microsoft Identity e é responsável por garantir a identidade do usuário, conceder e revogar o acesso a recursos e emitir tokens. O servidor de autorização também conhecido como provedor de identidade – ele manipula com segurança tudo o que fazer com as informações do usuário, seu acesso e as relações de confiança entre as partes em um fluxo.
* O **proprietário do recurso** é normalmente o usuário final. É a parte que possui os dados e tem o poder de permitir que terceiros acessem esses dados ou recursos.
* O **cliente OAuth** é seu aplicativo, identificado por sua ID de aplicativo. O cliente OAuth geralmente é a parte com a qual o usuário final interage e solicita tokens do servidor de autorização. O cliente deve receber permissão para acessar o recurso pelo proprietário do recurso.
* O **servidor de recursos** é onde residem o recurso ou os dados. Ele confia no servidor de autorização para autenticar e autorizar com segurança o cliente OAuth e usa tokens de acesso de portador para garantir que o acesso a um recurso possa ser concedido.

## <a name="app-registration"></a>Registo da aplicação

Cada aplicativo que deseja aceitar contas pessoais e corporativas ou de estudante deve ser registrado por meio da experiência de **registros de aplicativo** no [portal do Azure](https://aka.ms/appregistrations) antes de poder assinar esses usuários usando o OAuth 2,0 ou o OpenID Connect. O processo de registro do aplicativo coletará e atribuirá alguns valores ao seu aplicativo:

* Uma **ID de aplicativo** que identifica exclusivamente seu aplicativo
* Um **URI de redirecionamento** (opcional) que pode ser usado para direcionar as respostas de volta para seu aplicativo
* Alguns outros valores específicos do cenário.

Para obter mais detalhes, saiba como [registar uma aplicação](quickstart-register-app.md).

## <a name="endpoints"></a>Pontos Finais

Depois de registrado, o aplicativo se comunica com a plataforma de identidade da Microsoft enviando solicitações ao ponto de extremidade:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Onde o `{tenant}` pode usar um dos quatro valores diferentes:

| Valor | Descrição |
| --- | --- |
| `common` | Permite que os usuários com contas da Microsoft pessoais e contas corporativas/de estudante do Azure AD façam logon no aplicativo. |
| `organizations` | Permite que somente usuários com contas corporativas/de estudante do Azure AD entrem no aplicativo. |
| `consumers` | Permite que somente usuários com o MSA (contas pessoais da Microsoft) entrem no aplicativo. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` | Permite que somente usuários com contas corporativas/de estudante de um determinado locatário do Azure AD entrem no aplicativo. O nome de domínio amigável do locatário do Azure AD ou o identificador GUID do locatário pode ser usado. |

Para saber como interagir com esses pontos de extremidade, escolha um tipo de aplicativo específico na seção [protocolos](#protocols) e siga os links para obter mais informações.

> [!TIP]
> Qualquer aplicativo registrado no Azure AD pode usar o ponto de extremidade da plataforma de identidade da Microsoft, mesmo se eles não entrarem em contas pessoais.  Dessa forma, você pode migrar aplicativos existentes para a plataforma de identidade da Microsoft e [MSAL](reference-v2-libraries.md) sem recriar seu aplicativo.  

## <a name="tokens"></a>Tokens

A implementação da plataforma Microsoft Identity do OAuth 2,0 e do OpenID Connect fazem uso extensivo de tokens de portador, incluindo tokens de portador representados como JWTs. Um token de portador é um token de segurança leve que concede o acesso "portador" a um recurso protegido. Nesse sentido, o "portador" é qualquer parte que possa apresentar o token. Embora uma parte deva primeiro autenticar com a plataforma de identidade da Microsoft para receber o token de portador, se as etapas necessárias não forem tomadas para proteger o token na transmissão e no armazenamento, elas poderão ser interceptadas e usadas por uma parte não intencional. Embora alguns tokens de segurança tenham um mecanismo interno para impedir que partes não autorizadas os utilizem, os tokens de portador não têm esse mecanismo e devem ser transportados em um canal seguro, como o protocolo HTTPS. Se um token de portador for transmitido em claro, uma parte mal-intencionada poderá usar um ataque man-in-the-Middle para adquirir o token e usá-lo para acesso não autorizado a um recurso protegido. Os mesmos princípios de segurança se aplicam ao armazenamento ou cache de tokens de portador para uso posterior. Sempre verifique se seu aplicativo transmite e armazena tokens de portador de maneira segura. Para obter mais considerações de segurança sobre tokens de portador, consulte [RFC 6750 seção 5](https://tools.ietf.org/html/rfc6750).

Mais detalhes de diferentes tipos de tokens usados no ponto de extremidade da plataforma Microsoft Identity estão disponíveis na [referência de token do ponto de extremidade da plataforma de identidade da Microsoft](v2-id-and-access-tokens.md).

## <a name="protocols"></a>Protocolos

Se você estiver pronto para ver algumas solicitações de exemplo, comece com um dos tutoriais a seguir. Cada uma corresponde a um cenário de autenticação específico. Se precisar de ajuda para determinar qual é o fluxo certo para você, confira [os tipos de aplicativos que você pode criar com a plataforma de identidade da Microsoft](v2-app-types.md).

* [Crie aplicativos móveis e nativos com o OAuth 2,0](v2-oauth2-auth-code-flow.md)
* [Crie aplicativos Web com o OpenID Connect](v2-protocols-oidc.md)
* [Criar aplicativos de página única com o fluxo implícito do OAuth 2,0](v2-oauth2-implicit-grant-flow.md)
* [Criar daemons ou processos do lado do servidor com o fluxo de credenciais do cliente OAuth 2,0](v2-oauth2-client-creds-grant-flow.md)
* [Obter tokens em uma API Web com o fluxo em nome de do OAuth 2,0](v2-oauth2-on-behalf-of-flow.md)
