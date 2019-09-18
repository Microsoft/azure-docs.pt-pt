---
title: Configurar a inscrição e a entrada com uma conta do Weibo usando Azure Active Directory B2C
description: Forneça inscrição e entrada para clientes com contas do Weibo em seus aplicativos usando Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 4a019c3f197f123486dd28dc2bdb46e34f6cabc4
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064910"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e a entrada com uma conta do Weibo usando Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-weibo-application"></a>Criar um aplicativo Weibo

Para usar uma conta do Weibo como um provedor de identidade no Azure Active Directory B2C (Azure AD B2C), você precisa criar um aplicativo em seu locatário que o represente. Se você ainda não tiver uma conta do Weibo, poderá se inscrever [https://weibo.com/signup/signup.php?lang=en-us](https://weibo.com/signup/signup.php?lang=en-us)em.

1. Entre no portal do [desenvolvedor do Weibo](https://open.weibo.com/) com suas credenciais de conta do Weibo.
1. Depois de entrar, selecione seu nome de exibição no canto superior direito.
1. No menu suspenso, selecione**编辑开发者信息**(Editar informações do desenvolvedor).
1. Insira as informações necessárias e selecione**提交**(enviar).
1. Conclua o processo de verificação de email.
1. Vá para a [página de verificação de identidade](https://open.weibo.com/developers/identity/edit).
1. Insira as informações necessárias e selecione**提交**(enviar).

### <a name="register-a-weibo-application"></a>Registrar um aplicativo Weibo

1. Vá para a [nova página de registro do aplicativo Weibo](https://open.weibo.com/apps/new).
1. Insira as informações necessárias do aplicativo.
1. Selecione **imagem** (criar).
1. Copie os valores da **chave do aplicativo** e do **segredo do aplicativo**. Você precisa de ambos para adicionar o provedor de identidade ao seu locatário.
1. Carregue as fotos necessárias e insira as informações necessárias.
1. Selecione**保存以上信息**(salvar).
1. Selecione**高级信息**(informações avançadas).
1. Selecione**编辑**(editar) ao lado do campo para OAuth 2.0**授权设置**(URL de redirecionamento).
1. Insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` para OAuth 2.0**授权设置**(URL de redirecionamento). Por exemplo, se o nome do locatário for contoso, defina a URL como `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
1. Selecione**提交**(enviar).

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>Configurar uma conta do Weibo como um provedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **provedores de identidade**e, em seguida, selecione **Weibo (versão prévia)** .
1. Insira um **nome**. Por exemplo, *Weibo*.
1. Para a **ID do cliente**, insira a chave de aplicativo do aplicativo Weibo que você criou anteriormente.
1. Para o **segredo do cliente**, insira o segredo do aplicativo que você registrou.
1. Selecione **Guardar**.
