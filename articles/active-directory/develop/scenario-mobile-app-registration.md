---
title: Registe aplicações móveis que liguem para a web APIs [ APIs] Azure
titleSuffix: Microsoft identity platform
description: Saiba como construir uma aplicação móvel que chama APIs web (configuração de código da aplicação)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75cfd304869bfb63131dfd2afed9f925c86d32fb
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132447"
---
# <a name="register-mobile-apps-that-call-web-apis"></a>Registe aplicações móveis que liguem para apis web

Este artigo contém instruções para o ajudar a registar uma aplicação móvel que está a criar.

## <a name="supported-account-types"></a>Tipos de conta suportados

Os tipos de conta que as suas aplicações móveis suportam dependem da experiência que pretende ativar e dos fluxos que pretende utilizar.

### <a name="audience-for-interactive-token-acquisition"></a>Público para aquisição interativa de token

A maioria das aplicações móveis usam autenticação interativa. Se a sua aplicação utilizar este formulário de autenticação, pode inscrever utilizadores a partir de qualquer tipo de [conta](quickstart-register-app.md#register-a-new-application-using-the-azure-portal).

### <a name="audience-for-integrated-windows-authentication-username-password-and-b2c"></a>Público para autenticação integrada do Windows, username-password e B2C

Se tiver uma aplicação Universal Windows Platform (UWP), pode utilizar a autenticação Integrada do Windows para iniciar sessão nos utilizadores. Para utilizar a autenticação integrada do Windows ou a autenticação de palavra-passe de username, a sua aplicação precisa de iniciar sessão de informação sobre os utilizadores no seu próprio inquilino de desenvolvimento de linha de negócio (LOB). Num cenário independente de fornecedor de software (ISV), a sua aplicação pode inscrever utilizadores em organizações de Diretório Sonérrios Ativos do Azure. Esses fluxos de autenticação não têm suporte para contas pessoais da Microsoft.

Também pode inscrever os utilizadores utilizando identidades sociais que passam por uma autoridade e política B2C. Para utilizar este método, só pode utilizar autenticação interativa e autenticação de palavra-passe de utilizador. A autenticação username-password é atualmente suportada apenas em Xamarin.iOS, Xamarin.Android e UWP.

Para mais informações, consulte [Cenários e fluxos de autenticação suportados](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) e [Cenários e plataformas e idiomas suportados.](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="platform-configuration-and-redirect-uris"></a>Configuração da plataforma e redirecionamento de URIs  

### <a name="interactive-authentication"></a>Autenticação interativa

Quando se constrói uma aplicação móvel que utiliza a autenticação interativa, o passo de registo mais crítico é o redirecionamento do URI. Pode definir a autenticação interativa através da [configuração da plataforma na lâmina **de autenticação** ](https://aka.ms/MobileAppReg).

Esta experiência permitirá que a sua aplicação obtenha um único sinal (SSO) através do Microsoft Authenticator (e intune Company Portal on Android). Também apoiará as políticas de gestão de dispositivos.

O portal de registo de aplicações fornece uma experiência de pré-visualização para ajudá-lo a calcular a resposta intermediada URI para aplicações iOS e Android:

1. No portal de registo da aplicação, selecione **Autenticação** > **Experimente a nova experiência**.

   ![A lâmina de autenticação, onde escolhe uma nova experiência](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. Selecione **Adicionar uma plataforma**.

   ![Adicione uma plataforma](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. Quando a lista de plataformas for suportada, selecione **iOS**.

   ![Escolha uma aplicação móvel](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Introduza o seu ID de pacote e, em seguida, selecione **Registar**.

   ![Insira o seu ID de pacote](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Quando completar os passos, o URI redirecionamento é calculado para si, como na imagem seguinte.

![O redirecionamento uri resultante](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

Se preferir configurar manualmente o URI redirecionamento, pode fazê-lo através do manifesto de aplicação. Aqui está o formato recomendado para o manifesto:

- **iOS**: `msauth.<BUNDLE_ID>://auth` 
  - Por exemplo, introduza `msauth.com.yourcompany.appName://auth`
- **Android**: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Pode gerar o hash de assinatura Android utilizando a chave de lançamento ou chave de depuração através do comando KeyTool.

### <a name="username-password-authentication"></a>Autenticação de palavra-passe-nome de utilizador

Se a sua aplicação utilizar apenas a autenticação de username-password, não precisa de registar um URI redirecionado para a sua aplicação. Este fluxo faz uma viagem de ida e volta à versão 2.0 final da plataforma de identidade da Microsoft. Seu aplicativo não será chamado de volta em nenhum URI específico. 

No entanto, precisa identificar a sua aplicação como uma aplicação de cliente público. Para isso, comece na secção **autenticação** da sua aplicação. Na subsecção **de configurações Avançadas,** no parágrafo **do tipo de cliente Predefinido,** para a pergunta **Tratar aplicação como cliente público,** selecione **Sim**.

## <a name="api-permissions"></a>Permissões DaPI

As aplicações móveis ligam para APIs em nome do utilizador inscrito. A sua aplicação precisa de solicitar permissões delegadas. Estas permissões também são chamadas de âmbitos. Dependendo da experiência que deseja, pode solicitar permissões delegadas estáticamente através do portal Azure. Ou pode solicitá-los dinamicamente no tempo de execução. 

Ao registar estáticamente permissões, permite que os administradores aprovem facilmente a sua aplicação. Recomenda-se o registo estático.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Configuração do código](scenario-mobile-app-configuration.md)
