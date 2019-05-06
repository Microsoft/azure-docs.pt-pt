---
title: O daemon aplicação chamadas das APIs web (descrição geral) – plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação de daemon que chama a APIs web
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 578b7cdb38b7df3fab5885d773354a36f76a4cfb
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075884"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>Cenário: Aplicação de daemon que chamadas de APIs web

Saiba tudo o que precisa para criar um aplicativo de daemon que chama a web APIs.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="overview"></a>Descrição geral

Seu aplicativo pode adquirir um token para chamar uma API web em nome próprio (não em nome de um utilizador). Este cenário é útil para aplicativos de daemon. Está a utilizar o OAuth 2.0 padrão [credenciais de cliente](v2-oauth2-client-creds-grant-flow.md) conceder.

![Aplicações daemon](./media/scenario-daemon-app/daemon-app.svg)

Aqui estão alguns exemplos de casos de utilização para aplicações daemon:

- Aplicações Web que são utilizados para aprovisionar, administrar utilizadores ou processos num diretório em lote
- Aplicativos de Desktop, como (serviços do windows no Windows) ou processos de daemons no Linux que realizam tarefas de lote ou um serviço de sistema operativo em execução em segundo plano
- Web APIs que preciso manipular a diretórios, os utilizadores não específicos

Há outro caso comum em que aplicativos não-daemon utilizam credenciais de cliente:, mesmo quando eles agirem em nome dos utilizadores, que precisam para aceder a uma API web ou um recurso com a respetiva identidade por motivos técnicos. Um exemplo é o acesso a segredos no Cofre de chaves ou de uma base de dados SQL do Azure para uma cache.

Aplicativos que adquirir um token para suas próprias identidades:

- São aplicativos cliente confidencial. Estas aplicações, uma vez que acederem aos recursos, independentemente de um utilizador, tem de provar a sua identidade. Elas também são aplicações confidenciais em vez disso, o que precisam de ser aprovadas por administradores de inquilinos do Azure Active Directory (Azure AD).
- Se registrou um segredo (palavra-passe de aplicação ou certificado) com o Azure AD. Este segredo é passado durante a chamada para o Azure AD para obter um token.

## <a name="specifics"></a>Informações específicas

> [!IMPORTANT]
>
> - Interação do utilizador não é possível com uma aplicação de daemon. Um aplicativo de daemon requer sua própria identidade. Este tipo de aplicativo solicita um token de acesso ao utilizar a sua identidade da aplicação e apresentar o seu ID da aplicação, a credencial (palavra-passe ou certificado) e o aplicativo URI de ID para o Azure AD. Após a autenticação com êxito, o daemon de recebe um token de acesso (e um token de atualização) do Microsoft identity platform ponto final, que, em seguida, é utilizado para chamar a API web (e é atualizado conforme necessário).
> - Porque a interação do utilizador não for possível, consentimento incremental não será possível. Todas as permissões necessárias da API tem de ser configuradas no registo de aplicação e o código da aplicação apenas solicita as permissões definidas estaticamente. Isso também significa que as aplicações de daemon não suportam o consentimento incremental.

Para os desenvolvedores, a experiência de ponta a ponta para este cenário tem os seguintes aspetos:

- Aplicações daemon só podem trabalhar em inquilinos do Azure AD. Não faria sentido criar uma aplicação de daemon que tenta manipular contas pessoais da Microsoft. Se for um programador de aplicações linha de negócio (LOB), irá criar a sua aplicação de daemon no seu inquilino. Se for um ISV, pode querer criar uma aplicação de daemon de multi-inquilino. Terá de ser dado consentimento por cada administrador de inquilinos.
- Durante a [registo de aplicação](./scenario-daemon-app-registration.md), o **URI de resposta** não é necessária. Precisa compartilhar segredos ou certificados com o Azure AD, e precisa para solicitar permissões de aplicações e conceder o consentimento de administrador para utilizar essas permissões de aplicação.
- O [configuração da aplicação](./scenario-daemon-app-configuration.md) tem de fornecer credenciais de cliente como partilhado com o Azure AD durante o registo de aplicação.
- O [âmbito](scenario-daemon-acquire-token.md#scopes-to-request) utilizados para adquirir um token com as credenciais de cliente fluxo tem de ser um âmbito estático.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Aplicação de daemon - registo de aplicações](./scenario-daemon-app-registration.md)
