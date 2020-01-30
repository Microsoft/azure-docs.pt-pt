---
title: Construa uma app daemon que chama APIs web - plataforma de identidade microsoft / Azure
description: Saiba como construir uma app daemon que chama APIs web
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: ffbad5981f29ade9f27a434a9273969af4ed82de
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773414"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>Cenário: Aplicação Daemon que chama APIs web

Aprenda tudo o que precisa para construir uma aplicação daemon que chama APIs web.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="overview"></a>Visão geral

A sua aplicação pode adquirir um símbolo para chamar uma API web em nome de si mesma (não em nome de um utilizador). Este cenário é útil para aplicações daemon. Usa a bolsa padrão de [credenciais](v2-oauth2-client-creds-grant-flow.md) de clientes OAuth 2.0.

![Aplicações daemon](./media/scenario-daemon-app/daemon-app.svg)

Aqui estão alguns exemplos de casos de uso para aplicações daemon:

- Aplicações web que são usadas para fornecer ou administrar utilizadores ou fazer processos de lote num diretório
- Aplicações de desktop (como serviços Windows em processos Windows ou Daemon no Linux) que realizam trabalhos de lote, ou um serviço de sistema operativo que funciona em segundo plano
- APIs web que precisam manipular diretórios, não utilizadores específicos

Há outro caso comum em que as aplicações não-daemon usam credenciais de cliente: mesmo quando atuam em nome dos utilizadores, precisam de aceder a uma API web ou a um recurso sob a sua identidade por razões técnicas. Um exemplo é o acesso a segredos no Cofre de Chaves Azure ou uma base de dados Azure SQL para uma cache.

Aplicações que adquirem um símbolo para as suas próprias identidades:

- São pedidos confidenciais de clientes. Estas aplicações, dado que acedem a recursos independentemente dos utilizadores, precisam de provar a sua identidade. Também são aplicações bastante sensíveis. Têm de ser aprovados pelos administradores de inquilinos do Azure Ative Directory (Azure AD).
- Tenha registado um segredo (palavra-passe de candidatura ou certificado) com a AD Azure. Este segredo é passado durante a chamada para a AD Azure para obter um símbolo.

## <a name="specifics"></a>Especificações

> [!IMPORTANT]
>
> - Os utilizadores não podem interagir com uma aplicação daemon. Um pedido de daemon requer a sua própria identidade. Este tipo de aplicação solicita um sinal de acesso utilizando a sua identidade de aplicação e apresentando o seu ID de aplicação, credencial (senha ou certificado) e id de aplicação URI à AD Azure. Após a autenticação bem sucedida, o daemon recebe um token de acesso (e um token de atualização) do ponto final da plataforma de identidade da Microsoft. Esta ficha é então usada para chamar a Web API (e é refrescada conforme necessário).
> - Como os utilizadores não podem interagir com aplicações daemon, o consentimento incremental não é possível. Todas as permissões API necessárias devem ser configuradas no registo da aplicação. O código da aplicação apenas solicita permissões estáticas definidas. Isto também significa que as aplicações dada não suportam o consentimento incremental.

Para os desenvolvedores, a experiência de ponta a ponta para este cenário tem os seguintes aspetos:

- As aplicações da Daemon só podem funcionar em inquilinos da AD Azure. Não faria sentido construir uma aplicação daemon que tentasse manipular as contas pessoais da Microsoft. Se você é um desenvolvedor de aplicativos de linha de negócio (LOB), você vai criar sua app daemon no seu inquilino. Se você é um ISV, você pode querer criar uma aplicação de daemon multiinquilino. Cada administrador de inquilino terá de dar consentimento.
- Durante o registo de [candidaturas,](./scenario-daemon-app-registration.md)a resposta URI não é necessária. Você precisa partilhar segredos ou certificados ou afirmações assinadas com AD Azure. Também precisa de solicitar permissões de pedido e conceder consentimento ao administrador para utilizar essas permissões de aplicações.
- A configuração da [aplicação](./scenario-daemon-app-configuration.md) precisa de fornecer credenciais de cliente partilhadas com a Azure AD durante o registo da candidatura.
- O [âmbito](scenario-daemon-acquire-token.md#scopes-to-request) utilizado para adquirir um símbolo com o fluxo de credenciais do cliente tem de ser um âmbito estático.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [App Daemon - registo de aplicativos](./scenario-daemon-app-registration.md)
