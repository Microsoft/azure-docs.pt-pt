---
title: Protocolos OAuth 2.0 e OpenID Connect - plataforma de identidade da Microsoft Azure
description: Um guia para os protocolos OAuth 2.0 e OpenID Connect que são suportados pelo ponto final da plataforma de identidade da Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: e94bdd6e79ff13d8ba736e140538bae74091f727
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680080"
---
# <a name="oauth-20-and-openid-connect-protocols-on-the-microsoft-identity-platform"></a>Protocolos OAuth 2.0 e OpenID Connect na plataforma de identidade da Microsoft

O ponto final da plataforma de identidade da Microsoft para identidade como serviço com protocolos padrão da indústria, OpenID Connect e OAuth 2.0. Embora o serviço seja compatível com as normas, pode haver diferenças subtis entre quaisquer duas implementações destes protocolos. As informações aqui serão úteis se optar por escrever o seu código enviando e manuseando diretamente pedidos de HTTP ou utilizar uma biblioteca de código aberto de terceiros, em vez de utilizar uma das [nossas bibliotecas de código aberto.](reference-v2-libraries.md)

## <a name="the-basics"></a>Noções básicas

Em quase todos os fluxos OAuth 2.0 e OpenID Connect, há quatro partes envolvidas na troca:

![Diagrama mostrando as funções OAuth 2.0](./media/active-directory-v2-flows/protocols-roles.svg)

* O Servidor de **Autorização** é o ponto final da plataforma de identidade da Microsoft e responsável por garantir a identidade do utilizador, conceder e revogar o acesso aos recursos e emitir fichas. O servidor de autorização também conhecido como fornecedor de identidade - lida com segurança qualquer coisa a ver com a informação do utilizador, o seu acesso e as relações de confiança entre as partes num fluxo.
* O Proprietário do **Recurso** é tipicamente o utilizador final. É o partido que detém os dados e tem o poder de permitir que terceiros acedam a esses dados ou recursos.
* O **OAuth Client** é a sua aplicação, identificada pelo seu ID de aplicação. O cliente OAuth é geralmente a parte com a qual o utilizador final interage, e solicita fichas do servidor de autorização. O cliente deve ser autorizado a aceder ao recurso pelo proprietário do recurso.
* O **Servidor de Recursos** é onde reside o recurso ou os dados. Confia no Servidor de Autorização para autenticar e autorizar de forma segura o Cliente OAuth, e utiliza fichas de acesso ao Portador para garantir que o acesso a um recurso pode ser concedido.

## <a name="app-registration"></a>Registo da aplicação

Todas as aplicações que pretendam aceitar contas pessoais e de trabalho ou escolares devem ser registadas através da experiência de **registos** da App no [portal Azure](https://aka.ms/appregistrations) antes de poder assinar estes utilizadores na utilização do OAuth 2.0 ou do OpenID Connect. O processo de registo da aplicação irá recolher e atribuir alguns valores à sua aplicação:

* Um ID de **aplicação** que identifica exclusivamente a sua aplicação
* Um **Redirecionamento URI** (opcional) que pode ser usado para direcionar respostas de volta à sua app
* Alguns outros valores específicos do cenário.

Para obter mais detalhes, saiba como [registar uma aplicação](quickstart-register-app.md).

## <a name="endpoints"></a>Pontos Finais

Uma vez registada, a aplicação comunica com a plataforma de identidade da Microsoft enviando pedidos para o ponto final:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Onde `{tenant}` a lata pode levar um de quatro valores diferentes:

| Valor | Descrição |
| --- | --- |
| `common` | Permite que os utilizadores com contas pessoais da Microsoft e contas de trabalho/escola a partir do Azure AD assinem a aplicação. |
| `organizations` | Permite que apenas os utilizadores com contas de trabalho/escola da Azure AD assinem a aplicação. |
| `consumers` | Permite que apenas utilizadores com contas pessoais da Microsoft (MSA) assinem a aplicação. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` | Permite que apenas os utilizadores com contas de trabalho/escola de um determinado inquilino da AD Azure assinem a aplicação. Ou pode ser usado o nome de domínio amigável do inquilino Azure AD ou o identificador GUID do inquilino. |

Para aprender a interagir com estes pontos finais, escolha um tipo de aplicação específico na secção [Protocolos](#protocols) e siga os links para mais informações.

> [!TIP]
> Qualquer aplicação registada no Azure AD pode utilizar o ponto final da plataforma de identidade da Microsoft, mesmo que não assinem contas pessoais.  Desta forma, pode migrar aplicações existentes para a plataforma de identidade da Microsoft e [MSAL](reference-v2-libraries.md) sem recriar a sua aplicação.

## <a name="tokens"></a>Tokens

A implementação da plataforma identitária Microsoft de OAuth 2.0 e OpenID Connect fazem uso extensivo de tokens ao portador, incluindo tokens ao portador representados como JWTs. Um símbolo portador é um símbolo de segurança leve que concede ao "portador" acesso a um recurso protegido. Neste sentido, o "portador" é qualquer partido que possa apresentar o símbolo. Embora uma parte tenha primeiro de autenticar com a plataforma de identidade da Microsoft para receber o símbolo do portador, se não forem tomadas as medidas necessárias para proteger o símbolo na transmissão e armazenamento, pode ser intercetado e usado por uma parte não intencional. Embora algumas fichas de segurança tenham um mecanismo incorporado para impedir que as partes não autorizadas as utilizem, as fichas do portador não têm este mecanismo e devem ser transportadas num canal seguro, como a segurança da camada de transporte (HTTPS). Se um token portador for transmitido à luz, uma parte maliciosa pode usar um ataque homem-no-meio para adquirir o símbolo e usá-lo para acesso não autorizado a um recurso protegido. Os mesmos princípios de segurança aplicam-se ao armazenar ou cortar fichas para utilização posterior. Certifique-se sempre de que a sua aplicação transmite e armazena fichas ao portador de forma segura. Para obter mais considerações de segurança sobre as fichas do portador, consulte [a Secção 5 rFC 6750](https://tools.ietf.org/html/rfc6750).

Mais detalhes sobre diferentes tipos de tokens utilizados no ponto final da plataforma de identidade da Microsoft estão disponíveis na [referência token de](v2-id-and-access-tokens.md)ponto final da plataforma de identidade da Microsoft .

## <a name="protocols"></a>Protocolos

Se estiver pronto para ver alguns pedidos de exemplo, inicie-se com um dos tutoriais abaixo. Cada um corresponde a um cenário de autenticação particular. Se precisar de ajuda para determinar qual é o fluxo certo para si, confira [os tipos de aplicações que pode construir com](v2-app-types.md)a plataforma de identidade da Microsoft .

* [Construa aplicação móvel e nativa com OAuth 2.0](v2-oauth2-auth-code-flow.md)
* [Construir aplicativos web com OpenID Connect](v2-protocols-oidc.md)
* [Construa aplicativos de uma página com o Fluxo Implícito OAuth 2.0](v2-oauth2-implicit-grant-flow.md)
* [Construa dados ou processos do lado do servidor com o fluxo de credenciais de cliente OAuth 2.0](v2-oauth2-client-creds-grant-flow.md)
* [Obtenha fichas numa Web API com o OAuth 2.0 em nome do Flow](v2-oauth2-on-behalf-of-flow.md)
