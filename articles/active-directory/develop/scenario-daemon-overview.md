---
title: Construa uma app daemon que chama APIs web | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como construir uma app daemon que chama APIs web
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/31/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 00a70b585ddf522a25e81703fe5bdf55efbcb7e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99582779"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>Cenário: Aplicação Daemon que chama APIs web

Aprenda tudo o que precisa para construir uma aplicação daemon que chama APIs web.

## <a name="overview"></a>Descrição Geral

A sua aplicação pode adquirir um token para ligar para uma API web em nome de si próprio (não em nome de um utilizador). Este cenário é útil para aplicações daemon. Utiliza a concessão padrão de [credenciais](v2-oauth2-client-creds-grant-flow.md) de cliente 2.0 da OAuth.

![Aplicações daemon](./media/scenario-daemon-app/daemon-app.svg)

Aqui estão alguns exemplos de casos de uso para aplicações daemon:

- Aplicações web que são usadas para provisão ou administração de utilizadores ou para fazer processos de lote num diretório
- Aplicações para desktop (como serviços Windows em processos Windows ou daemon no Linux) que realizam trabalhos de lote, ou um serviço de sistema operativo que funciona em segundo plano
- APIs web que precisam de manipular diretórios, não utilizadores específicos

Há outro caso comum em que as aplicações não-daemon usam credenciais de clientes: mesmo quando agem em nome dos utilizadores, precisam de aceder a uma API web ou a um recurso sob a sua própria identidade por razões técnicas. Um exemplo é o acesso a segredos no Azure Key Vault ou na Base de Dados Azure SQL para obter uma cache.

Aplicações que adquirem um símbolo para as suas próprias identidades:

- São aplicações confidenciais de clientes. Estas aplicações, tendo em conta que acedem a recursos independentemente dos utilizadores, precisam de provar a sua identidade. Também são aplicações bastante sensíveis. Têm de ser aprovados pelos administradores inquilinos do Azure Ative Directory (Azure AD).
- Tenha registado um segredo (senha de aplicação ou certificado) com Azure AD. Este segredo é passado durante a chamada para Azure AD para obter um símbolo.

## <a name="specifics"></a>Especificidades

> [!IMPORTANT]
>
> - Os utilizadores não podem interagir com uma aplicação Daemon. Um pedido de daemon requer a sua própria identidade. Este tipo de aplicação solicita um token de acesso utilizando a sua identidade de aplicação e apresentando o seu ID de aplicação, credencial (senha ou certificado) e iD URI de aplicação a Azure AD. Após a autenticação bem sucedida, o daemon recebe um token de acesso (e um token de atualização) da plataforma de identidade da Microsoft. Este token é então usado para chamar a API web (e é atualizado conforme necessário).
> - Como os utilizadores não podem interagir com aplicações daemon, o consentimento incremental não é possível. Todas as permissões de API necessárias devem ser configuradas no registo de inscrição. O código da aplicação apenas solicita permissões estáticas definidas. Isto também significa que as aplicações daemon não suportam o consentimento incremental.

Para os desenvolvedores, a experiência de ponta a ponta para este cenário tem os seguintes aspetos:

- As aplicações da Daemon só podem funcionar em inquilinos da AD Azure. Não faria sentido construir uma aplicação daemon que tentasse manipular as contas pessoais da Microsoft. Se você é um desenvolvedor de aplicativos line-of-business (LOB), você vai criar a sua app Daemon no seu inquilino. Se é um ISV, talvez queira criar uma aplicação multitenante de daemon. Cada administrador inquilino terá de dar consentimento.
- Durante o registo de [pedidos,](./scenario-daemon-app-registration.md)a resposta URI não é necessária. Partilhe segredos ou certificados ou afirmações assinadas com a Azure AD. Também precisa de solicitar permissões de pedidos e conceder consentimento administrativo para usar essas permissões de aplicações.
- A configuração da [aplicação](./scenario-daemon-app-configuration.md) necessita de fornecer credenciais de clientes partilhadas com a Azure AD durante o registo da candidatura.
- O [âmbito](scenario-daemon-acquire-token.md#scopes-to-request) utilizado para adquirir um símbolo com o fluxo de credenciais do cliente tem de ser um âmbito estático.

## <a name="recommended-reading"></a>Leitura recomendada

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Passos seguintes

Passe para o próximo artigo neste cenário, [registo de aplicações.](./scenario-daemon-app-registration.md)
