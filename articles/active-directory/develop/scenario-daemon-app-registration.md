---
title: Aplicativo de daemon chamando APIs Web (registro de aplicativo)-plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo daemon que chama APIs da Web-registro de aplicativo
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f590010a655fb01529a4a59b5540cc03068f2b8
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056482"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>Aplicativo daemon que chama APIs Web-registro de aplicativo

Para um aplicativo daemon, veja o que você precisa saber ao registrar o aplicativo.

## <a name="supported-account-types"></a>Tipos de contas suportadas

Considerando que os aplicativos daemon fazem sentido apenas no locatário do Azure AD, ao criar o aplicativo, você precisará escolher:

- qualquer uma das **contas neste diretório organizacional**. Essa opção é o caso mais comum, pois os aplicativos daemon são geralmente escritos por desenvolvedores de linha de negócios (LOB).
- ou **contas em qualquer diretório organizacional**. Você fará essa escolha se for um ISV que fornece uma ferramenta de utilitário para seus clientes. Você precisará dos administradores de locatários do cliente para aprová-lo.

## <a name="authentication---no-reply-uri-needed"></a>Autenticação-nenhum URI de resposta necessário

No caso em que seu aplicativo cliente confidencial usa **apenas** o fluxo de credenciais do cliente, a URL de resposta não precisa ser registrada. Não é necessário para a configuração/construção do aplicativo. O fluxo de credenciais do cliente não o utiliza.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>Permissões de API-permissões de aplicativo e consentimento de administrador

Um aplicativo daemon só pode solicitar permissões de aplicativo para APIs (permissões não delegadas). Na página **permissão de API** para o registro do aplicativo, depois de selecionar **Adicionar uma permissão** e escolher a família de API, escolha **permissões de aplicativo**e, em seguida, selecione suas permissões

![Permissões do aplicativo e consentimento do administrador](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

Os aplicativos daemon exigem ter um administrador de locatários antes do consentimento para o aplicativo que chama a API da Web. Esse consentimento é fornecido na mesma página de **permissão de API** , por um administrador de locatários selecionando **conceder consentimento de administrador para *nossa organização***

Se você for um ISV criando um aplicativo multilocatário, convém verificar o parágrafo de [aplicativos do daemon de vários locatários de implantação](scenario-daemon-production.md#deployment---case-of-multi-tenant-daemon-apps) .

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Aplicativo daemon-configuração de código do aplicativo](./scenario-daemon-app-configuration.md)
