---
title: Configurar a inscrição e início de sessão com uma conta do Facebook - Azure Active Directory B2C | Documentos da Microsoft
description: Forneça a inscrição e início de sessão para clientes com contas do Facebook nas suas aplicações com o Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: b4182eae848fdb862b770e9707e9de31bcdb4e0a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64703526"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e início de sessão com uma conta do Facebook com o Azure Active Directory B2C

## <a name="create-a-facebook-application"></a>Criar um aplicativo do Facebook

Para utilizar uma conta do Facebook como um [fornecedor de identidade](active-directory-b2c-reference-oauth-code.md) no Azure Active Directory (Azure AD) B2C, terá de criar uma aplicação no seu inquilino que a representa. Se ainda não tiver uma conta do Facebook, pode obtê-la em [ https://www.facebook.com/ ](https://www.facebook.com/).

1. Inicie sessão no [Facebook para os desenvolvedores](https://developers.facebook.com/) com as suas credenciais de conta do Facebook.
2. Se ainda não o fez, terá de se registrar como um desenvolvedor do Facebook. Para tal, selecione **registar** no canto canto superior direito da página, aceite as políticas do Facebook e conclua os passos de registo.
3. Selecione **as minhas aplicações** e, em seguida, clique em **adicionar uma nova aplicação**. 
4. Introduza um **nome a apresentar** e válido **E-Mail de contacto**.
5. Clique em **criar ID de aplicação**. Isso pode exigir a aceitar as políticas de plataforma do Facebook e concluir uma verificação de segurança online.
6. Selecione **configurações** > **básica**.
7. Escolher uma **categoria**por exemplo, `Business and Pages`. Este valor é necessária pelo Facebook, mas não é utilizado para o Azure AD B2C.
8. Na parte inferior da página, selecione **adicionar plataforma**e, em seguida, selecione **site**.
9. Na **URL do Site**, introduza `https://your-tenant-name.b2clogin.com/` substituindo `your-tenant-name` com o nome do seu inquilino. Introduza um URL para o **URL de política de privacidade**por exemplo, `http://www.contoso.com`. O URL de política é uma página que manter para fornecer informações de privacidade para a sua aplicação.
10. Selecione **guardar alterações**.
11. Na parte superior da página, copie o valor da **ID da aplicação**. 
12. Clique em **mostrar** e copie o valor de **segredo da aplicação**. Utilizar ambos para configurar o Facebook como um fornecedor de identidade no seu inquilino. **Segredo da aplicação** é uma credencial de segurança importantes.
13. Selecione **produtos**e, em seguida, selecione **configurar** sob **início de sessão do Facebook**.
14. Selecione **configurações** sob **início de sessão do Facebook**.
15. Na **URIs de redirecionamento OAuth válido**, introduza `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Substitua `your-tenant-name` com o nome do seu inquilino. Clique em **guardar alterações** na parte inferior da página.
16. Para disponibilizar a aplicação do Facebook para o Azure AD B2C, clique no Seletor de estado na parte superior direita da página e ativá-la **nos** para tornar a aplicação pública e, em seguida, clique em **confirmar**.  Neste momento o estado deve ser alterado de **desenvolvimento** ao **Live**.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Configurar uma conta do Facebook como um fornecedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino. 
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Selecione **fornecedores de identidade**e, em seguida, selecione **Add**.
5. Introduza um **nome**. Por exemplo, introduza *Facebook*.
6. Selecione **tipo de fornecedor de identidade**, selecione **Facebook**e clique em **OK**.
7. Selecione **configurar este fornecedor de identidade** e introduza o ID da aplicação que registou anteriormente como a **ID de cliente** e introduza o segredo de aplicação que registou como o **segredo do cliente** de o aplicativo para Facebook que criou anteriormente.
8. Clique em **OK** e, em seguida, clique em **criar** para guardar a configuração do Facebook.
