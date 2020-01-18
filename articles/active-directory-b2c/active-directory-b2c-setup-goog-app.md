---
title: Configurar a inscrição e a entrada com uma conta do Google
titleSuffix: Azure AD B2C
description: Forneça inscrição e entrada para clientes com contas do Google em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 147de090411309a442ad07711ce62ec7fd64b3fa
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76261216"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e a entrada com uma conta do Google usando Azure Active Directory B2C

## <a name="create-a-google-application"></a>Criar um aplicativo do Google

Para usar uma conta do Google como um [provedor de identidade](active-directory-b2c-reference-oauth-code.md) no Azure Active Directory B2C (Azure ad B2C), você precisa criar um aplicativo no console do Google Developers. Se você ainda não tiver uma conta do Google, poderá se inscrever em [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Entre no console do [Google Developers](https://console.developers.google.com/) com suas credenciais de conta do Google.
1. No canto superior esquerdo da página, selecione a lista projeto e, em seguida, selecione **novo projeto**.
1. Insira um **nome de projeto**, selecione **criar**.
1. Verifique se você está usando o novo projeto selecionando a lista suspensa projeto no canto superior esquerdo da tela, selecione o projeto por nome e, em seguida, selecione **abrir**.
1. Selecione a **tela de consentimento do OAuth** no menu à esquerda, selecione **externo**e, em seguida, selecione **criar**.
Insira um **nome** para seu aplicativo. Insira *b2clogin.com* na seção **domínios autorizados** e selecione **salvar**.
1. Selecione **credenciais** no menu à esquerda e, em seguida, selecione **criar credenciais** > **ID do cliente OAuth**.
1. Em **tipo de aplicativo**, selecione **aplicativo Web**.
1. Insira um **nome** para seu aplicativo, insira `https://your-tenant-name.b2clogin.com` em **origens de JavaScript autorizadas**e `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` em **URIs de redirecionamento autorizados**. Substitua `your-tenant-name` pelo nome do seu locatário. Você precisa usar todas as letras minúsculas ao inserir o nome do locatário, mesmo que o locatário seja definido com letras maiúsculas no Azure AD B2C.
1. Clique em **Criar**.
1. Copie os valores da **ID do cliente** e do **segredo do cliente**. Você precisará de ambos para configurar o Google como um provedor de identidade em seu locatário. O **segredo do cliente** é uma credencial de segurança importante.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Configurar uma conta do Google como um provedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **provedores de identidade**e, em seguida, selecione **Google**.
1. Insira um **nome**. Por exemplo, *Google*.
1. Para a **ID do cliente**, insira a ID do cliente do aplicativo do Google que você criou anteriormente.
1. Para o **segredo do cliente**, insira o segredo do cliente que você registrou.
1. Selecione **Guardar**.
