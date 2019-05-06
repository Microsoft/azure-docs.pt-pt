---
title: Aplicativo de Desktop que chamadas de web APIs (registo da aplicação) - plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação de ambiente de trabalho que chamadas de web APIs (registo da aplicação)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5da934709274d90668d94dfea3a9c223e191d032
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076064"
---
# <a name="desktop-app-that-calls-web-apis---app-registration"></a>Aplicativo de Desktop que chamadas de web APIs - registo de aplicações

Este artigo contém os specificities de registo de aplicação para um aplicativo de desktop.

## <a name="supported-accounts-types"></a>Tipos de contas suportadas

Os tipos de conta suportados no aplicativo de desktop dependem a experiência de que pretende animados, e por isso, nos fluxos que pretende utilizar.

### <a name="audience-for-interactive-token-acquisition"></a>Público-alvo para a aquisição do token interativa

Se o aplicativo de área de trabalho utilizar a autenticação interativa, pode entrar nos utilizadores de qualquer [tipo de conta](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)

### <a name="audience-for-desktop-app-silent-flows"></a>Público-alvo para os fluxos silenciosa do aplicativo de desktop

- Se pretende usar a autenticação integrada Windows ou o nome de utilizador/palavra-passe, a aplicação tem de iniciar sessão dos utilizadores no seu próprio inquilino (desenvolvedor LOB) ou no Azure Active directory que as organizações (cenário de ISV). Estes fluxos de autenticação não são suportados para contas pessoais da Microsoft
- Se pretender utilizar o fluxo de código de dispositivo, não pode iniciar sessão dos utilizadores com contas pessoais da Microsoft ainda
- Se iniciar sessão dos utilizadores com identidades sociais, passando uma autoridade de B2C e a política, só pode utilizar a autenticação interativa e nome de utilizador-palavra-passe.

## <a name="redirect-uris"></a>URIs de Redirecionamento

Novamente o redirecionamento URIs para utilizar no aplicativo de desktop dependerá o fluxo que pretende utilizar.

- Se estiver a utilizar a autenticação interativa, desejará usar `https://login.microsoftonline.com/common/oauth2/nativeclient`. Atingirá essa configuração clicando no URL correspondente no **autenticação** secção para a sua aplicação
  
  > [!IMPORTANT]
  > Atualmente o MSAL.NET utiliza o URI de redirecionamento outra por padrão em aplicativos de desktop em execução no Windows (`urn:ietf:wg:oauth:2.0:oob`). No futuro, é preferível alterar esta predefinição e, por conseguinte, recomendamos que utilize `https://login.microsoftonline.com/common/oauth2/nativeclient`

- Se a sua aplicação estiver a utilizar apenas autenticação integrada Windows, nome de utilizador/palavra-passe ou o fluxo de código de dispositivo, não precisa de registar um URI de redirecionamento para a sua aplicação. Na verdade, estes fluxos fazer uma ida e volta para o ponto de final de v2.0 de plataforma de identidade do Microsoft e a sua aplicação não será chamada de volta em qualquer URI específico. Para poder diferenciá-los a partir de um fluxo de aplicação de cliente confidencial, que não tem URIs de redirecionamento de qualquer (o cliente credencial fluxo usado em aplicativos de daemon), precisa expressar o que seu aplicativo é um aplicativo de cliente público. Esta configuração é obtida ao aceder a **autenticação** seção para a sua aplicação e na **definições avançadas** subsecção, escolha **Sim**, à pergunta **Tratar o aplicativo como um cliente público** (no **predefinido a tipo de cliente** parágrafo)

  ![Permitir que o cliente público](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>Permissões de API

Aplicativos de Desktop chamam APIs em nome do utilizador com sessão iniciada. Eles precisam solicitar permissões delegadas. Eles não podem solicitar permissões de aplicação (que só é processado no [aplicações daemon](scenario-daemon-overview.md))

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Aplicativo de Desktop - configuração de aplicações](scenario-desktop-app-configuration.md)
