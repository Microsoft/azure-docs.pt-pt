---
title: JavaScript aplicativos de única página Descrição geral do cenário - plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação de página única (descrição geral do cenário) que integra-se a plataforma de identidade da Microsoft.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07a21e83f304f3e1acc0ed4033d832dd8e901ac9
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076364"
---
# <a name="scenario-single-page-application"></a>Cenário: Aplicação de página única

Saiba tudo o que precisa para criar um aplicativo de página única (SPA).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Introdução

Pode criar seu primeiro aplicativo seguindo o guia de introdução do SPA do JavaScript:

> [!div class="nextstepaction"]
> [Quickstart: Aplicação de página única](./quickstart-v2-javascript.md)

## <a name="overview"></a>Descrição geral

Muitos aplicativos web modernos são criados como aplicativos de única página do lado do cliente escritos usando o JavaScript ou uma estrutura SPA como Angular, VUE e react. js. Esses aplicativos são executados num navegador da web e têm características de autenticação diferentes que os aplicativos da web tradicional do lado do servidor. A plataforma de identidade da Microsoft permite que as aplicações de página única iniciar sessão dos utilizadores e obter tokens para aceder a serviços de back-end ou APIs da web com o [fluxo implícito de OAuth 2.0](./v2-oauth2-implicit-grant-flow.md). O fluxo implícito permite que o aplicativo obter os tokens de ID para representar o utilizador autenticado e também acessar tokens necessários para chamar as APIs protegidas.

![Aplicativos de página única](./media/scenarios/spa-app.svg)

Este fluxo de autenticação não inclui os cenários de aplicativos com estruturas JavaScript de várias plataformas, como o Bombardeador, React Native e assim por diante. uma vez que necessitam de mais recursos para interação com as plataformas nativas.

## <a name="specifics"></a>Informações específicas

Os seguintes aspetos são necessários para ativar este cenário para a sua aplicação:

* Registo de aplicação com o Azure AD envolve a ativar o fluxo implícito e definir um URI de redirecionamento para os quais tokens são devolvidos.
* Configuração da aplicação com as propriedades da aplicação registada como o ID da aplicação.
* Usando a biblioteca MSAL para fazer o fluxo de autenticação para iniciar sessão e adquirir tokens.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Registo da aplicação](scenario-spa-app-registration.md)
