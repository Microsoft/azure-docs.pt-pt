---
title: Configurar a inscrição e início de sessão com uma conta de Weibo através do Azure Active Directory B2C | Documentos da Microsoft
description: Forneça a inscrição e início de sessão para clientes com contas de Weibo nas suas aplicações com o Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 808d4bc8521917b89a7265be6dfab60757baf910
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508058"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e início de sessão com uma conta de Weibo através do Azure Active Directory B2C

> [!NOTE]
> Esta funcionalidade está em pré-visualização.
> 

## <a name="create-a-weibo-application"></a>Criar uma aplicação de Weibo

Para utilizar uma conta de Weibo como fornecedor de identidade no Azure Active Directory (Azure AD) B2C, terá de criar uma aplicação no seu inquilino que a representa. Se ainda não tiver uma conta de Weibo, pode obtê-la em [ https://weibo.com/signup/signup.php?lang=en-us ](https://weibo.com/signup/signup.php?lang=en-us).

1. Inicie sessão para o [portal do programador Weibo](https://open.weibo.com/) com as suas credenciais de conta Weibo.
2. Depois de iniciar sessão, selecione o seu nome de exibição no canto superior direito.
3. Na lista pendente, selecione**编辑开发者信息**(editar informações do desenvolvedor).
4. Introduza as informações necessárias e selecione**提交**(Submeter).
5. Conclua o processo de verificação de e-mail.
6. Vá para o [página de verificação de identidade](https://open.weibo.com/developers/identity/edit).
7. Introduza as informações necessárias e selecione**提交**(Submeter).

### <a name="register-a-weibo-application"></a>Registar uma aplicação de Weibo

1. Vá para o [nova página de registo de aplicação Weibo](https://open.weibo.com/apps/new).
2. Introduza as informações de aplicativo necessário.
3. Selecione**创建**(criar).
4. Copie os valores da **chave da aplicação** e **segredo da aplicação**. Terá de ambas as para adicionar o fornecedor de identidade para o seu inquilino.
5. Carregar as fotografias que necessária e introduza as informações necessárias.
6. Selecione**保存以上信息**(guardar).
7. Selecione**高级信息**(informações avançadas).
8. Selecione**编辑**(editar) junto ao campo para OAuth2.0**授权设置**(URL de redirecionamento).
9. Introduza `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` para OAuth2.0**授权设置**(URL de redirecionamento). Por exemplo, se o nome de inquilino for contoso, definir o URL para ser `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
10. Selecione**提交**(Submeter).  

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>Configurar uma conta de Weibo como fornecedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Selecione **fornecedores de identidade**e, em seguida, selecione **Add**.
5. Fornecer um **nome**. Por exemplo, introduza *Weibo*.
6. Selecione **tipo de fornecedor de identidade**, selecione **Weibo (pré-visualização)** e clique em **OK**.
7. Selecione **configurar este fornecedor de identidade** e introduza a chave da aplicação que registou anteriormente como a **ID de cliente** e introduza o segredo de aplicação que registou como o **segredo do cliente** de o aplicativo de Weibo que criou anteriormente.
8. Clique em **OK** e, em seguida, clique em **criar** para guardar a configuração de Weibo.
