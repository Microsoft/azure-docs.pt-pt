---
title: Configurar a inscrição e a entrada com uma conta do Facebook
titleSuffix: Azure AD B2C
description: Forneça inscrição e entrada para clientes com contas do Facebook em seus aplicativos usando Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c85ba3079fe09078d3e68eab070317c199242d81
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74947693"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e a entrada com uma conta do Facebook usando Azure Active Directory B2C

## <a name="create-a-facebook-application"></a>Criar um aplicativo do Facebook

Para usar uma conta do Facebook como um [provedor de identidade](active-directory-b2c-reference-oauth-code.md) no Azure Active Directory B2C (Azure ad B2C), você precisa criar um aplicativo em seu locatário que o represente. Se você ainda não tiver uma conta do Facebook, poderá se inscrever em [https://www.facebook.com/](https://www.facebook.com/).

1. Entre no [Facebook para desenvolvedores](https://developers.facebook.com/) com suas credenciais de conta do Facebook.
1. Se ainda não tiver feito isso, você precisará se registrar como um desenvolvedor do Facebook. Para fazer isso **, selecione introdução** no canto superior direito da página, aceite as políticas do Facebook e conclua as etapas de registro.
1. Selecione **meus aplicativos** e **criar aplicativo**.
1. Insira um **nome de exibição** e um **email de contato**válido.
1. Selecione **criar ID do aplicativo**. Isso pode exigir que você aceite as políticas da plataforma do Facebook e conclua uma verificação de segurança online.
1. Selecione **configurações** > **básico**.
1. Escolha uma **categoria**, por exemplo `Business and Pages`. Esse valor é exigido pelo Facebook, mas não é usado para Azure AD B2C.
1. Na parte inferior da página, selecione **Adicionar plataforma**e, em seguida, selecione **site**.
1. Em **URL do site**, insira `https://your-tenant-name.b2clogin.com/` substituindo `your-tenant-name` pelo nome do seu locatário. Insira uma URL para a **URL da política de privacidade**, por exemplo `http://www.contoso.com`. A URL da política é uma página que você mantém para fornecer informações de privacidade para seu aplicativo.
1. Selecione **salvar alterações**.
1. Na parte superior da página, copie o valor da **ID do aplicativo**.
1. Selecione **Mostrar** e copie o valor do **segredo do aplicativo**. Você usa ambos para configurar o Facebook como um provedor de identidade em seu locatário. O **segredo do aplicativo** é uma credencial de segurança importante.
1. Selecione o sinal de adição ao lado de **produtos**e, em seguida, selecione **Configurar** em **logon do Facebook**.
1. Em **logon do Facebook**, selecione **configurações**.
1. Em **URIs de redirecionamento OAuth válidos**, insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Substitua `your-tenant-name` pelo nome do seu locatário. Selecione **salvar alterações** na parte inferior da página.
1. Para disponibilizar seu aplicativo do Facebook para Azure AD B2C, selecione o seletor de status na parte superior direita da página e **ative-o** para tornar o aplicativo público e selecione **modo de comutação**.  Neste ponto, o status deve mudar de **desenvolvimento** para **ativo**.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Configurar uma conta do Facebook como um provedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **provedores de identidade**e, em seguida, selecione **Facebook**.
1. Insira um **nome**. Por exemplo, *Facebook*.
1. Para a **ID do cliente**, insira a ID do aplicativo do Facebook que você criou anteriormente.
1. Para o **segredo do cliente**, insira o segredo do aplicativo que você registrou.
1. Selecione **Guardar**.
