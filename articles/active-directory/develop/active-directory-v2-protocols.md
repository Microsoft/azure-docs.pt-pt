---
title: Protocolos OAuth 2.0 e OpenID Connect na plataforma de identidade da Microsoft Rio Azure
titleSuffix: Microsoft identity platform
description: Um guia para os protocolos OAuth 2.0 e OpenID Connect que são suportados pelo ponto final da plataforma de identidade da Microsoft.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 0bb7812d75fa3276b52a182f9184e28a21a910ae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83737491"
---
# <a name="oauth-20-and-openid-connect-protocols-on-microsoft-identity-platform"></a>Protocolos OAuth 2.0 e OpenID Connect na plataforma de identidade da Microsoft

O ponto final da plataforma de identidade da Microsoft para a autenticação e autorização de identidade como serviço implementa a autenticação e autorização com os protocolos padrão da indústria OpenID Connect (OIDC) e OAuth 2.0, respectivamente. Embora o serviço seja conforme com as normas, pode haver diferenças subtis entre qualquer implementação destes protocolos. As informações aqui serão úteis se optar por escrever o seu código enviando e manuseando diretamente pedidos HTTP ou utilizando uma biblioteca de código aberto de terceiros, em vez de utilizar uma das [nossas bibliotecas de código aberto.](reference-v2-libraries.md)

## <a name="the-basics"></a>Noções básicas

Em quase todos os fluxos OAuth 2.0 e OpenID Connect, há quatro partes envolvidas na troca:

![Diagrama mostrando as funções OAuth 2.0](./media/active-directory-v2-flows/protocols-roles.svg)

* O **Servidor de Autorização** é o ponto final da plataforma de identidade da Microsoft e é responsável por garantir a identidade do utilizador, conceder e revogar o acesso aos recursos e emitir fichas. O servidor de autorização também conhecido como fornecedor de identidade - lida com segurança tudo o que tem a ver com a informação do utilizador, o seu acesso e as relações de confiança entre as partes num fluxo.
* O **Proprietário de Recursos** é normalmente o utilizador final. É o partido que detém os dados e tem o poder de permitir que terceiros acedam a esses dados ou recursos.
* O **Cliente OAuth** é a sua aplicação, identificada pelo seu ID de aplicação. O cliente OAuth é geralmente a parte com a qual o utilizador final interage, e solicita fichas do servidor de autorização. O cliente deve ter permissão para aceder ao recurso pelo proprietário do recurso.
* O **Servidor de Recursos** é onde reside o recurso ou dados. Confia no Servidor de Autorização para autenticar e autorizar de forma segura o Cliente OAuth, e utiliza fichas de acesso ao Portador para garantir que o acesso a um recurso pode ser concedido.

## <a name="app-registration"></a>Registo de aplicações

Todas as aplicações que queiram aceitar contas pessoais e de trabalho ou escolares devem ser **registadas** através da experiência de registos da App no [portal Azure](https://aka.ms/appregistrations) antes de poder assinar estes utilizadores através da OAuth 2.0 ou OpenID Connect. O processo de registo da aplicação irá recolher e atribuir alguns valores à sua aplicação:

* Um **ID de aplicação** que identifica exclusivamente a sua aplicação
* Um **URI redirecionado** (opcional) que pode ser usado para direcionar respostas de volta à sua app
* Alguns outros valores específicos do cenário.

Para obter mais detalhes, saiba como [registar uma aplicação](quickstart-register-app.md).

## <a name="endpoints"></a>Pontos Finais

Uma vez registada, a aplicação comunica com a plataforma de identidade da Microsoft enviando pedidos para o ponto final:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Onde pode `{tenant}` levar um de quatro valores diferentes:

| Valor | Descrição |
| --- | --- |
| `common` | Permite que os utilizadores com contas pessoais da Microsoft e contas de trabalho/escola a partir do Azure AD assinem a aplicação. |
| `organizations` | Permite que apenas utilizadores com contas de trabalho/escola da Azure AD assinem a aplicação. |
| `consumers` | Permite que apenas utilizadores com contas pessoais da Microsoft (MSA) assinem a aplicação. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` | Permite apenas que os utilizadores com contas de trabalho/escola de um inquilino particular da AD AD se inscrevam na aplicação. Ou o nome de domínio amigável do inquilino AZure AD ou o identificador GUID do inquilino podem ser usados. |

Para aprender a interagir com estes pontos finais, escolha um determinado tipo de aplicação na secção [Protocolos](#protocols) e siga os links para obter mais informações.

> [!TIP]
> Qualquer aplicação registada no Azure AD pode utilizar o ponto final da plataforma de identidade da Microsoft, mesmo que não assinem em contas pessoais.  Desta forma, pode migrar as aplicações existentes para a plataforma de identidade da Microsoft e [para a MSAL](reference-v2-libraries.md) sem recriar novamente a sua aplicação.

## <a name="tokens"></a>Tokens

A implementação da plataforma de identidade da Microsoft de OAuth 2.0 e OpenID Connect faz uso extensivo de tokens portadores, incluindo tokens ao portador representados como JWTs (JSON Web Tokens). Um símbolo de portador é um símbolo de segurança leve que concede ao "portador" acesso a um recurso protegido. Neste sentido, o "portador" é qualquer partido que possa apresentar o símbolo. Embora uma parte tenha primeiro de autenticar com a plataforma de identidade da Microsoft para receber o token do portador, se não forem tomadas as medidas necessárias para garantir o token na transmissão e armazenamento, pode ser intercetado e utilizado por uma parte não intencional. Embora alguns tokens de segurança tenham um mecanismo incorporado para impedir que as partes não autorizadas as utilizem, as fichas ao portador não têm este mecanismo e devem ser transportadas num canal seguro, como a segurança da camada de transporte (HTTPS). Se um símbolo portador for transmitido de forma clara, uma parte maliciosa pode usar um ataque homem-no-meio para adquirir o símbolo e usá-lo para acesso não autorizado a um recurso protegido. Os mesmos princípios de segurança aplicam-se ao armazenar ou caching tokens portadores para posterior utilização. Certifique-se sempre de que a sua aplicação transmite e armazena fichas ao portador de forma segura. Para obter mais considerações de segurança sobre fichas ao portador, consulte [a secção 5 do RFC 6750](https://tools.ietf.org/html/rfc6750).

Outros detalhes de diferentes tipos de fichas utilizados no ponto final da plataforma de identidade da Microsoft estão disponíveis na [referência token do ponto final da plataforma de identidade da Microsoft.](v2-id-and-access-tokens.md)

## <a name="protocols"></a>Protocolos

Se estiver pronto para ver alguns pedidos de exemplo, começa com um dos tutoriais abaixo. Cada um corresponde a um cenário de autenticação particular. Se precisar de ajuda para determinar qual é o fluxo certo para si, confira [os tipos de aplicações que pode construir com](v2-app-types.md)a plataforma de identidade da Microsoft.

* [Construir aplicação móvel e nativa com OAuth 2.0](v2-oauth2-auth-code-flow.md)
* [Construa aplicativos web com OpenID Connect](v2-protocols-oidc.md)
* [Construa aplicativos de uma página única com o Fluxo Implícito OAuth 2.0](v2-oauth2-implicit-grant-flow.md)
* [Construa daemons ou processos do lado do servidor com o fluxo de credenciais de cliente OAuth 2.0](v2-oauth2-client-creds-grant-flow.md)
* [Obtenha fichas numa API web com o OAuth 2.0 em nome do Flow](v2-oauth2-on-behalf-of-flow.md)
