---
title: Visão geral do cenário do aplicativo de página única JavaScript – plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo de página única (visão geral do cenário) que integra a plataforma de identidade da Microsoft.
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
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3c4e671473ff6c6fecdc13fe61dbde1d3fb3809
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852524"
---
# <a name="scenario-single-page-application"></a>Cenário: Aplicação de página única

Saiba tudo o que você precisa para criar um aplicativo de página única (SPA).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Introdução

Você pode criar seu primeiro aplicativo seguindo o início rápido de SPA do JavaScript:

> [!div class="nextstepaction"]
> [Quickstart: Aplicativo de página única](./quickstart-v2-javascript.md)

## <a name="overview"></a>Descrição geral

Muitos aplicativos Web modernos são criados como aplicativos de página única do lado do cliente escritos usando JavaScript ou uma estrutura SPA como angular, Vue. js e reagir. js. Esses aplicativos são executados em um navegador da Web e têm características de autenticação diferentes dos aplicativos Web tradicionais do lado do servidor. A plataforma de identidade da Microsoft permite que aplicativos de página única conectem usuários e obtenham tokens para acessar serviços de back-end ou APIs Web usando o [fluxo implícito do OAuth 2,0](./v2-oauth2-implicit-grant-flow.md). O fluxo implícito permite que o aplicativo obtenha tokens de ID para representar o usuário autenticado e também os tokens de acesso necessários para chamar APIs protegidas.

![Aplicativos de página única](./media/scenarios/spa-app.svg)

Esse fluxo de autenticação não inclui cenários de aplicativos que usam estruturas JavaScript de plataforma cruzada, como o ar-nativo, reagir e assim por diante. como eles exigem mais recursos para interação com as plataformas nativas.

## <a name="specifics"></a>Especificações

Os seguintes aspectos são necessários para habilitar esse cenário para seu aplicativo:

* O registro de aplicativo com o AD do Azure envolve habilitar o fluxo implícito e definir um URI de redirecionamento para o qual os tokens são retornados.
* Configuração de aplicativo com as propriedades do aplicativo registrado, como a ID do aplicativo.
* Usando a biblioteca MSAL para fazer o fluxo de autenticação para entrar e adquirir tokens.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Registo da aplicação](scenario-spa-app-registration.md)
