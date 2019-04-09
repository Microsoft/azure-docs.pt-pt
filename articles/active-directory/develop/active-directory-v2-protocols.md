---
title: Saiba mais sobre os protocolos de autorização suportados pelo Azure AD v2.0 | Documentos da Microsoft
description: Um guia para protocolos suportados pelo ponto final v2.0 do Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5fb4fa1b-8fc4-438e-b3b0-258d8c145f22
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c56970091da74cfc389d60ad91f430fcb64d4bba
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59266975"
---
# <a name="v20-protocols---oauth-20-and-openid-connect"></a>Ligar de protocolos - OAuth 2.0 e OpenID v2.0

O ponto final v2.0 pode utilizar o Azure Active Directory (Azure AD) para identidade-como-serviço com protocolos padrão da indústria, OpenID Connect e OAuth 2.0. Embora o serviço seja compatível com os padrões, pode haver diferenças sutis entre quaisquer duas implementações desses protocolos. As informações aqui será útil se optar por escrever seu código com o envio e a processar os pedidos HTTP de diretamente ou usar uma biblioteca de código-fonte aberto de terceiros, em vez de usar um dos nossos [bibliotecas de código-fonte aberto](reference-v2-libraries.md).

> [!NOTE]
> Observe que todos os cenários e funcionalidades do Azure AD são compatíveis com o ponto final do v2.0. Para determinar se deve utilizar o ponto final v2.0, leia sobre [v2.0 limitações](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Noções básicas

Em quase todos os fluxos do OAuth 2.0 e OpenID Connect, há quatro partes envolvidas na troca de:

![Funções de OAuth 2.0](../../media/active-directory-v2-flows/protocols_roles.png)

* O **servidor de autorização** é o ponto final v2.0 e responsável por assegurar a identidade do utilizador, conceder, revogar o acesso aos recursos e emissão de tokens. O servidor de autorização, também conhecido como o fornecedor de identidade --lo em segurança processa algo a ver com as informações do utilizador, o acesso e as relações de confiança entre partes num fluxo.
* O **proprietário do recurso** é, normalmente, o utilizador final. É a parte que tem os dados e tem o poder para permitir que terceiros acessar esse dados ou recursos.
* O **OAuth cliente** é a sua aplicação, identificada pelo seu ID da aplicação. O cliente de OAuth costuma ser a parte que o usuário final interage com e ele solicita tokens do servidor de autorização. O cliente deve ser concedido permissão para aceder ao recurso pelo proprietário do recurso.
* O **servidor do recurso** é onde residem o recurso ou dados. O servidor de autorização para autenticar e autorizar o cliente de OAuth de forma segura de confianças de entidades e utiliza os tokens de acesso de portador para se certificar de que pode ser concedido acesso a um recurso.

## <a name="app-registration"></a>Registo de aplicações

Todas as aplicações que queira aceitar tanto pessoais e contas escolares ou profissionais têm de estar registadas por meio do novo **registos de aplicações (pré-visualização)** experiência no [portal do Azure](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) antes de ele pode iniciar sessão a estes utilizadores utilizar o OAuth 2.0 ou OpenID Connect. O processo de registo de aplicação irá recolher e atribuir alguns valores à sua aplicação:

* Uma **ID da aplicação** que identifica exclusivamente a sua aplicação
* R **URI de redirecionamento** ou **identificador de pacote** que podem ser utilizadas para direcionar as respostas de volta à sua aplicação
* Alguns outros valores específicos de cenário.

Para obter mais detalhes, saiba como [registar uma aplicação](quickstart-v2-register-an-app.md).

## <a name="endpoints"></a>Pontos finais

Depois de registado, o aplicativo se comunica com o Azure AD ao enviar pedidos para o ponto final v2.0:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Onde o `{tenant}` pode efetuar uma das quatro valores diferente:

| Valor | Descrição |
| --- | --- |
| `common` | Permite aos utilizadores com contas Microsoft pessoais e contas escolares/do Azure AD para iniciar sessão na aplicação. |
| `organizations` | Permite que apenas os utilizadores com contas escolares/do Azure AD para iniciar sessão na aplicação. |
| `consumers` | Permite que apenas os utilizadores com contas pessoais da Microsoft (MSA) para iniciar sessão na aplicação. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` | Permite que apenas os utilizadores com contas escolares/de um Azure AD específico de inquilino para iniciar sessão na aplicação. O nome de domínio amigável de inquilino do Azure AD ou identificador GUID do inquilino pode ser utilizado. |

Para saber como interagir com estes pontos finais, escolha um tipo de aplicação específica no [protocolos](#protocols) secção e siga as ligações para obter mais informações.

> [!TIP]
> Qualquer aplicação registada no Azure AD pode utilizar o ponto final v2.0, mesmo que eles não iniciar sessão em contas pessoais.  Dessa forma, é possível migrar aplicações existentes para a versão 2.0 e [MSAL](reference-v2-libraries.md) sem voltar a criar seu aplicativo.  

## <a name="tokens"></a>Tokens

A implementação de v2.0 de OAuth 2.0 e OpenID Connect fazem amplo uso de tokens de portador, incluindo os tokens de portador representados como JWTs. Um token de portador é um token de segurança simples que concede o acesso de "bearer" a um recurso protegido. Nesse sentido, "bearer" é capaz de apresentar o token de terceiros. Embora uma parte deve primeiro autenticar com o Azure AD para receber o token de portador, se não são tidas nos passos necessários para proteger o token na transmissão e o armazenamento, podem ser intercetado e utilizado por uma entidade não-intencionais. Embora alguns tokens de segurança tem um mecanismo interno para impedir que partes não autorizadas a utilizá-los, os tokens de portador não tem esse mecanismo e devam ser transportados num canal seguro, como a segurança de camada de transporte (HTTPS). Se um token de portador é transmitido transparente, mal-intencionados podem utilizar um ataque man-in-the-middle para adquirir o token e utilizá-lo para o acesso não autorizado a um recurso protegido. Os mesmos princípios de segurança se aplicam ao armazenar ou de colocação em cache os tokens de portador para utilização posterior. Certifique-se sempre de que a aplicação transmite e armazena os tokens de portador de forma segura. Para obter mais considerações de segurança em tokens de portador, consulte [RFC 6750 secção 5](https://tools.ietf.org/html/rfc6750).

Existem mais detalhes de diferentes tipos de tokens utilizados no ponto final v2.0 está disponível no [a referência de token de ponto final de v2.0](v2-id-and-access-tokens.md).

## <a name="protocols"></a>Protocolos

Se estiver pronto para ver alguns pedidos de exemplo, começar com um do abaixo tutoriais. Cada uma delas corresponde a um cenário de autenticação específico. Se precisar de ajuda para determinar o que é o fluxo certo para, confira [os tipos de aplicações que criar com a versão 2.0](v2-app-types.md).

* [Criar aplicações móveis e nativas com OAuth 2.0](v2-oauth2-auth-code-flow.md)
* [Criar aplicações web com OpenID Connect](v2-protocols-oidc.md)
* [Criar aplicações de página única com o OAuth 2.0 implícita de fluxo](v2-oauth2-implicit-grant-flow.md)
* [Criar daemons ou processos de servidor com o fluxo de credenciais de cliente OAuth 2.0](v2-oauth2-client-creds-grant-flow.md)
* [Obter os tokens numa API web o fluxo em-nome-do OAuth 2.0](v2-oauth2-on-behalf-of-flow.md)
