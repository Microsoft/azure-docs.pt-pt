---
title: Protocolos de autenticação no Azure Ative Directory B2C | Microsoft Docs
description: Como construir aplicações diretamente utilizando os protocolos que são suportados pelo Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ed393f721d4461ebadea41f8dad707d4881865cd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "78183910"
---
# <a name="azure-ad-b2c-authentication-protocols"></a>Azure AD B2C: Protocolos de autenticação
O Azure Ative Directory B2C (Azure AD B2C) fornece a identidade como um serviço para as suas apps, suportando dois protocolos padrão da indústria: OpenID Connect e OAuth 2.0. O serviço é conforme com as normas, mas qualquer duas implementações destes protocolos podem ter diferenças subtis.

As informações neste guia são úteis se escrever o seu código enviando e manuseando diretamente pedidos HTTP, em vez de utilizar uma biblioteca de código aberto. Recomendamos que leia esta página antes de mergulhar nos detalhes de cada protocolo específico. Mas se já está familiarizado com o Azure AD B2C, pode ir diretamente para [os guias de referência do protocolo.](#protocols)

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>Noções básicas
Todas as aplicações que utilizam o Azure AD B2C precisam de estar registadas no seu diretório B2C no [portal Azure.](https://portal.azure.com) O processo de registo de aplicação recolhe e atribui alguns valores à sua aplicação:

* Uma **ID de Aplicação** que identifica de modo exclusivo a aplicação.
* Um **URI redirecionado** ou **identificador de pacotes** que pode ser usado para direcionar respostas de volta para a sua app.
* Alguns outros valores específicos do cenário. Para mais informações, [saiba como registar a sua candidatura.](tutorial-register-applications.md)

Depois de registar a sua aplicação, comunica com o Azure Ative Directory (Azure AD) enviando pedidos para o ponto final:

```
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token
```

Em quase todos os fluxos OAuth e OpenID Connect, quatro partes estão envolvidas na troca:

![Diagrama mostrando os quatro papéis de OAuth 2.0](./media/protocols-overview/protocols_roles.png)

* O **servidor de autorização** é o ponto final Azure AD. Trata de forma segura qualquer coisa relacionada com a informação e o acesso do utilizador. Também lida com as relações de confiança entre as partes num fluxo. É responsável por verificar a identidade do utilizador, conceder e revogar o acesso aos recursos e emitir fichas. É também conhecido como o fornecedor de identidade.

* O **titular do recurso** é normalmente o utilizador final. É o partido que detém os dados, e tem o poder de permitir que terceiros acedam a esses dados ou recursos.

* O **cliente OAuth** é a sua aplicação. É identificado pela sua identificação de aplicação. Normalmente é a festa com a qual os utilizadores finais interagem. Também solicita fichas do servidor de autorização. O titular do recurso deve conceder ao cliente permissão para aceder ao recurso.

* O **servidor de recursos** é onde reside o recurso ou dados. Confia no servidor de autorização para autenticar e autorizar o cliente OAuth. Também utiliza fichas de acesso ao portador para garantir que o acesso a um recurso pode ser concedido.

## <a name="policies-and-user-flows"></a>Políticas e fluxos de utilizadores
Indiscutivelmente, as políticas Azure AD B2C são as características mais importantes do serviço. A Azure AD B2C alarga os protocolos padrão OAuth 2.0 e OpenID Connect introduzindo políticas. Estes permitem que a Azure AD B2C execute muito mais do que simples autenticação e autorização.

Para ajudá-lo a configurar as tarefas de identidade mais comuns, o portal Azure AD B2C inclui políticas predefinidas e configuráveis chamadas **fluxos de utilizador.** Os fluxos de utilizador descrevem totalmente as experiências de identidade dos consumidores, incluindo inscrição, inscrição e edição de perfis. Os fluxos de utilizador podem ser definidos numa UI administrativa. Podem ser executados utilizando um parâmetro de consulta especial em pedidos de autenticação HTTP.

As políticas e os fluxos de utilizador não são funcionalidades padrão de OAuth 2.0 e OpenID Connect, pelo que deve ter tempo para os compreender. Para mais informações, consulte o guia de referência do [fluxo do utilizador Azure AD B2C](user-flow-overview.md).

## <a name="tokens"></a>Tokens
A implementação Azure AD B2C da OAuth 2.0 e OpenID Connect faz uso extensivo de fichas ao portador, incluindo fichas ao portador que são representadas como fichas web JSON (JWTs). Um símbolo de portador é um símbolo de segurança leve que concede ao "portador" acesso a um recurso protegido.

O portador é qualquer parte que possa apresentar o símbolo. A Azure AD deve primeiro autenticar uma festa antes de poder receber um token portador. Mas se as medidas necessárias não forem tomadas para garantir o símbolo na transmissão e armazenamento, pode ser intercetado e utilizado por uma parte não intencional.

Alguns símbolos de segurança têm mecanismos incorporados que impedem as partes não autorizadas de as utilizar, mas as fichas ao portador não têm este mecanismo. Devem ser transportados num canal seguro, como a segurança da camada de transporte (HTTPS).

Se um símbolo portador for transmitido fora de um canal seguro, uma parte maliciosa pode usar um ataque homem-no-meio para adquirir o token e usá-lo para obter acesso não autorizado a um recurso protegido. Os mesmos princípios de segurança aplicam-se quando as fichas ao portador são armazenadas ou em cache para utilização posterior. Certifique-se sempre de que a sua aplicação transmite e armazena fichas ao portador de forma segura.

Para obter considerações adicionais de segurança, consulte [a secção 5 do RFC 6750](https://tools.ietf.org/html/rfc6750).

Mais informações sobre os diferentes tipos de fichas que são utilizadas no Azure AD B2C estão disponíveis [na referência simbólica Azure AD](tokens-overview.md).

## <a name="protocols"></a>Protocolos
Quando estiver pronto para rever alguns pedidos de exemplo, pode começar com um dos seguintes tutoriais. Cada um corresponde a um cenário de autenticação particular. Se precisar de ajuda para determinar qual o fluxo adequado para si, confira [os tipos de aplicações que pode construir utilizando o Azure AD B2C](application-types.md).

* [Construa aplicações móveis e nativas utilizando o OAuth 2.0](authorization-code-flow.md)
* [Construa aplicativos web utilizando o OpenID Connect](openid-connect.md)
* [Construa aplicativos de uma página única usando o fluxo implícito OAuth 2.0](implicit-flow-single-page-application.md)

