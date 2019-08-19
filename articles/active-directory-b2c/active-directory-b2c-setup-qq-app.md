---
title: Configurar a inscrição e a entrada com uma conta do QQ usando Azure Active Directory B2C | Microsoft Docs
description: Forneça inscrição e entrada para clientes com contas do QQ em seus aplicativos usando Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9679e3216184c0dc5b8dee241c30fc69d5423aeb
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68963764"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e a entrada com uma conta do QQ usando Azure Active Directory B2C

> [!NOTE]
> Esta funcionalidade está em pré-visualização.
> 

## <a name="create-a-qq-application"></a>Criar um aplicativo QQ

Para usar uma conta do QQ como um provedor de identidade no Azure Active Directory (Azure AD) B2C, você precisa criar um aplicativo em seu locatário que o represente. Se você ainda não tiver uma conta do QQ, poderá obtê-la [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033)em.

### <a name="register-for-the-qq-developer-program"></a>Registre-se no programa de desenvolvedor do QQ

1. Entre no portal do [desenvolvedor do QQ](http://open.qq.com) com suas credenciais de conta do QQ.
2. Depois de entrar, acesse [https://open.qq.com/reg](https://open.qq.com/reg) para registrar-se como desenvolvedor.
3. Selecione**个人**(desenvolvedor individual).
4. Insira as informações necessárias e selecione**下一步**(próxima etapa).
5. Conclua o processo de verificação de email. Você precisará aguardar alguns dias para ser aprovado após o registro como desenvolvedor. 

### <a name="register-a-qq-application"></a>Registrar um aplicativo QQ

1. Vá para [https://connect.qq.com/index.html](https://connect.qq.com/index.html).
2. Selecione**应用管理**(gerenciamento de aplicativos).
5. Selecione**创建应用**(criar aplicativo) e insira as informações necessárias.
7. Insira `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp` em**授权回调域**(URL de retorno de chamada). Por exemplo, se o `tenant_name` for contoso, defina a URL `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`como.
8. Selecione**创建应用**(criar aplicativo).
9. Na página confirmação, selecione**应用管理**(gerenciamento de aplicativo) para retornar à página de gerenciamento de aplicativo.
10. Selecione**查看**(exibição) ao lado do aplicativo que você criou.
11. Selecione**修改**(editar).
12. Copie a **ID do aplicativo** e a **chave do aplicativo**. Você precisa desses dois valores para adicionar o provedor de identidade ao seu locatário.

## <a name="configure-qq-as-an-identity-provider"></a>Configurar o QQ como um provedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Selecione **fornecedores de identidade**e, em seguida, selecione **Add**.
5. Fornecer um **nome**. Por exemplo, digite *QQ*.
6. Selecione **tipo de provedor de identidade**, selecione **QQ (versão prévia)** e clique em **OK**.
7. Selecione **configurar este provedor de identidade** e insira a ID do aplicativo que você registrou anteriormente como a **ID do cliente** e insira a chave do aplicativo que você registrou como o **segredo do cliente** do aplicativo QQ que você criou anteriormente.
8. Clique em **OK** e em **criar** para salvar a configuração do QQ.

