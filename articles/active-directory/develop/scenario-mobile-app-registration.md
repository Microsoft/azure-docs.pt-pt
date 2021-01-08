---
title: Registar aplicações móveis que chamam APIs web Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como construir uma aplicação móvel que chame APIs web (registo da app)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: cdd32185d1ffddb4c6bc3d62b91690f68dec17ca
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98014727"
---
# <a name="register-mobile-apps-that-call-web-apis"></a>Registar aplicativos móveis que chamam APIs web

Este artigo contém instruções para o ajudar a registar uma aplicação móvel que está a criar.

## <a name="supported-account-types"></a>Tipos de conta suportados

Os tipos de conta que as suas aplicações móveis suportam dependem da experiência que pretende ativar e dos fluxos que pretende utilizar.

### <a name="audience-for-interactive-token-acquisition"></a>Público para aquisição de fichas interativas

A maioria das aplicações móveis usam a autenticação interativa. Se a sua aplicação utilizar esta forma de autenticação, pode iniciar sação nos utilizadores a partir de qualquer tipo de [conta.](quickstart-register-app.md)

### <a name="audience-for-integrated-windows-authentication-username-password-and-b2c"></a>Público para autenticação integrada do Windows, username-password e B2C

Se tiver uma aplicação Universal Windows Platform (UWP), pode utilizar a autenticação Integrada do Windows para iniciar súpido nos utilizadores. Para utilizar a autenticação integrada do Windows ou a autenticação de senha de utilizador, a sua aplicação precisa de se inscrever nos utilizadores no seu próprio inquilino de desenvolvimento de linha de negócios (LOB). Num cenário de fornecedor de software independente (ISV), a sua aplicação pode iniciar súm em utilizadores em organizações do Azure Ative Directory. Estes fluxos de autenticação não são suportados para contas pessoais da Microsoft.

Também pode assinar nos utilizadores usando identidades sociais que passam por uma autoridade e política B2C. Para utilizar este método, pode utilizar apenas a autenticação interativa e a autenticação de senha de utilizador. A autenticação username-password é atualmente suportada apenas em Xamarin.iOS, Xamarin.Android e UWP.

Para mais informações, consulte [Cenários e fluxos](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) e Cenários de autenticação [suportados e plataformas e idiomas suportados.](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="platform-configuration-and-redirect-uris"></a>Configuração da plataforma e REdireccionamento uris

### <a name="interactive-authentication"></a>Autenticação interativa

Quando se constrói uma aplicação móvel que utiliza a autenticação interativa, o passo de registo mais crítico é o URI redirecionamento. Pode definir a autenticação interativa através da configuração da [plataforma na lâmina de **autenticação**](https://aka.ms/MobileAppReg).

Esta experiência permitirá que a sua aplicação obtenha um único sign-on (SSO) através do Microsoft Authenticator (e do Portal da Empresa Intune no Android). Também apoiará as políticas de gestão de dispositivos.

O portal de registo de aplicações proporciona uma experiência de pré-visualização para ajudá-lo a calcular a resposta intermediada URI para aplicações iOS e Android:

1. No portal de registo de aplicações, selecione **Autenticação**  >  **Experimente a nova experiência.**

   ![A lâmina de autenticação, onde se escolhe uma nova experiência](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. **Selecione Adicionar uma plataforma**.

   ![Adicionar uma plataforma](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. Quando a lista de plataformas for suportada, selecione **iOS**.

   ![Escolha uma aplicação móvel](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Introduza o seu ID do pacote e, em seguida, **selecione Registar- se**.

   ![Insira o seu iD do pacote](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Quando completar os passos, o URI de redirecionamento é calculado para si, como na imagem seguinte.

![O URI redirecionado resultante](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

Se preferir configurar manualmente o URI de redirecionamento, pode fazê-lo através do manifesto de aplicação. Aqui está o formato recomendado para o manifesto:

- **iOS:**`msauth.<BUNDLE_ID>://auth`
  - Por exemplo, introduzir `msauth.com.yourcompany.appName://auth`
- **Android:**`msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Pode gerar o hash de assinatura Android utilizando a chave de desbloqueio ou depuração através do comando KeyTool.

### <a name="username-password-authentication"></a>Autenticação de nome de utilizador-senha

Se a sua aplicação utilizar apenas a autenticação de nome de utilizador-password, não precisa de registar um URI de redirecionamento para a sua aplicação. Este fluxo faz uma viagem de ida e volta à versão 2.0 da plataforma de identidade da Microsoft. A sua candidatura não será chamada de volta em nenhum URI específico.

No entanto, tem de identificar a sua aplicação como uma aplicação de cliente público. Para tal:

1. Ainda no <a href="https://portal.azure.com/" target="_blank">portal <span class="docon docon-navigate-external x-hidden-focus"></span> Azure</a>, selecione a sua aplicação nas **inscrições da App** e, em seguida, selecione **Autenticação**.
1. Em **Definições Avançadas**  >  **Permita fluxos de clientes públicos** Ative os  >  **seguintes fluxos móveis e de ambiente de trabalho:**, selecione **Sim**.

   :::image type="content" source="media/scenarios/default-client-type.png" alt-text="Ativar a configuração do cliente público no painel de autenticação no portal Azure":::

## <a name="api-permissions"></a>Permissões de API

As aplicações móveis chamam APIs em nome do utilizador inscrito. A sua aplicação precisa de solicitar permissões delegadas. Estas permissões também são chamadas de âmbitos. Dependendo da experiência que pretende, pode solicitar permissões delegadas estáticamente através do portal Azure. Ou podes pedir-lhes dinamicamente no tempo de execução.

Ao registar estáticamente permissões, permite que os administradores aprovem facilmente a sua aplicação. Recomenda-se o registo estático.

## <a name="next-steps"></a>Próximos passos

Passe para o próximo artigo neste cenário, [configuração do código de aplicação](scenario-mobile-app-configuration.md).
