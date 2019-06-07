---
title: Protocolos de autenticação no Azure Active Directory B2C | Documentos da Microsoft
description: Como criar aplicações diretamente, usando os protocolos que são suportados pelo Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f1953535a19be1a6aa3963776515b1f2c0d979c1
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508952"
---
# <a name="azure-ad-b2c-authentication-protocols"></a>Azure AD B2C: Protocolos de autenticação
O Azure Active Directory B2C (Azure AD B2C) fornece identidade como um serviço para as suas aplicações com suporte a dois protocolos de norma da indústria: OpenID Connect e OAuth 2.0. O serviço é compatível com os padrões, mas qualquer duas implementações desses protocolos podem ter ligeiras diferenças. 

As informações neste guia são útil se escrever seu código com o envio diretamente e processar os pedidos HTTP, em vez de através da utilização de uma biblioteca de código-fonte aberto. Recomendamos que leia esta página antes de aprofundar os detalhes de cada protocolo específico. Mas se já estiver familiarizado com o Azure AD B2C, pode ir diretamente para [os guias de referência do protocolo](#protocols).

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>Noções básicas
Todas as aplicações que utiliza o Azure AD B2C tem de ser registado no seu diretório do B2C no [portal do Azure](https://portal.azure.com). O processo de registo de aplicação recolhe e atribui alguns valores à sua aplicação:

* Uma **ID de Aplicação** que identifica de modo exclusivo a aplicação.
* R **URI de redirecionamento** ou **identificador de pacote** que podem ser utilizadas para direcionar as respostas de volta à sua aplicação.
* Alguns outros valores específicos de cenário. Para obter mais informações, saiba [como registar a sua aplicação](active-directory-b2c-app-registration.md).

Depois de registar a sua aplicação, ele se comunica com o Azure Active Directory (Azure AD) ao enviar pedidos para o ponto final:

```
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token
```

Em quase todos os fluxos do OAuth e OpenID Connect, quatro partes estão envolvidas do exchange:

![Funções de OAuth 2.0](./media/active-directory-b2c-reference-protocols/protocols_roles.png)

* O **servidor de autorização** é o ponto de final do Azure AD. Em segurança processa qualquer coisa relacionada às informações de utilizador e de acesso. Ele também manipula as relações de confiança entre as partes num fluxo. Ele é responsável por verificar a identidade do utilizador, conceder a revogar o acesso aos recursos e a emissão de tokens. Ele também é conhecido como o fornecedor de identidade.

* O **proprietário do recurso** é, normalmente, o utilizador final. É a parte que detém os dados e tem o poder para permitir que terceiros acessar esse dados ou recursos.

* O **OAuth cliente** é a sua aplicação. Ele é identificado pelo seu ID da aplicação. Normalmente, é a parte que os utilizadores finais interagem com. Ele também solicita tokens do servidor de autorização. O proprietário do recurso tem de conceder a permissão de cliente para aceder ao recurso.

* O **servidor do recurso** é onde residem o recurso ou dados. Ele confia o servidor de autorização de forma segura autenticar e autorizar o cliente de OAuth. Ele também usa os tokens de acesso de portador para se certificar de que pode ser concedido acesso a um recurso.

## <a name="policies-and-user-flows"></a>Fluxos de utilizador e de políticas
Indiscutivelmente, políticas do Azure AD B2C são os recursos mais importantes do serviço. O Azure AD B2C expande os protocolos padrão de OAuth 2.0 e OpenID Connect, introduzindo as políticas. Eles permitem que o Azure AD B2C para realizar muito mais do que a autenticação e autorização simples. 

Para ajudá-lo a configurar as tarefas mais comuns de identidade, o portal do Azure AD B2C inclui políticas predefinidas e configuráveis chamadas **fluxos de utilizador**. Fluxos de utilizador totalmente descrevem as experiências de identidade do consumidor, incluindo a inscrição, início de sessão e edição de perfil. Fluxos de utilizador podem ser definidos numa interface do Usuário administrativo. Eles podem ser executados com um parâmetro de consulta especial nos pedidos de autenticação HTTP. 

As políticas e fluxos de utilizador não são recursos padrão de OAuth 2.0 e OpenID Connect, para que deve separar um tempo para entendê-las. Para obter mais informações, consulte a [guia de referência de fluxo de utilizador do Azure AD B2C](active-directory-b2c-reference-policies.md).

## <a name="tokens"></a>Tokens
A implementação do Azure AD B2C do OAuth 2.0 e OpenID Connect faz uso extensivo de tokens de portador, incluindo os tokens de portador são representados como tokens de web JSON (JWTs). Um token de portador é um token de segurança simples que concede o acesso de "bearer" a um recurso protegido.

O portador é capaz de apresentar o token de terceiros. Do Azure AD tem de autenticar primeiro uma parte confiável, antes de pode receber um token de portador. Mas se os passos necessários não serão direcionados para proteger o token na transmissão e o armazenamento, pode ser interceptado e utilizado por uma entidade não-intencionais.

Alguns tokens de segurança tem mecanismos internos que impedem que partes não autorizadas a utilizá-los, mas os tokens de portador não tem esse mecanismo. Eles devam ser transportados num canal seguro como, por exemplo, uma segurança de camada de transporte (HTTPS). 

Se um token de portador é transmitido fora de um canal seguro, mal-intencionados podem utilizar um ataque man-in-the-middle adquirir o token e utilizá-lo a obter acesso não autorizado a um recurso protegido. Os mesmos princípios de segurança aplicam-se quando os tokens de portador são armazenados ou colocados em cache para utilização posterior. Certifique-se sempre de que a aplicação transmite e armazena os tokens de portador de forma segura.

Para considerações de segurança de token de portador adicionais, consulte [RFC 6750 secção 5](https://tools.ietf.org/html/rfc6750).

Obter mais informações sobre os diferentes tipos de tokens que são utilizados no Azure AD B2C estão disponíveis no [a referência de token do Azure AD](active-directory-b2c-reference-tokens.md).

## <a name="protocols"></a>Protocolos
Quando estiver pronto para rever alguns pedidos de exemplo, pode começar com um dos seguintes tutoriais. Cada corresponde a um cenário de autenticação específico. Se precisar de ajuda para determinar qual o fluxo é adequado para si, confira [os tipos de aplicações, pode criar com o Azure AD B2C](active-directory-b2c-apps.md).

* [Criar aplicações móveis e nativas com OAuth 2.0](active-directory-b2c-reference-oauth-code.md)
* [Criar aplicações web com OpenID Connect](active-directory-b2c-reference-oidc.md)
* [Crie aplicações de página única com o fluxo implícito de OAuth 2.0](active-directory-b2c-reference-spa.md)

