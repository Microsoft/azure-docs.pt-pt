---
title: Configurar a inscrição e a entrada com uma conta do QQ usando Azure Active Directory B2C
description: Forneça inscrição e entrada para clientes com contas do QQ em seus aplicativos usando Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 2bda344c32d03425c7cfec4d253e18451eb1f5a1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75367508"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e a entrada com uma conta do QQ usando Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-qq-application"></a>Criar um aplicativo QQ

Para usar uma conta do QQ como um provedor de identidade no Azure Active Directory B2C (Azure AD B2C), você precisa criar um aplicativo em seu locatário que o represente. Se você ainda não tiver uma conta do QQ, poderá se inscrever em [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>Registre-se no programa de desenvolvedor do QQ

1. Entre no portal do [desenvolvedor do QQ](http://open.qq.com) com suas credenciais de conta do QQ.
1. Depois de entrar, acesse [https://open.qq.com/reg](https://open.qq.com/reg) para se registrar como desenvolvedor.
1. Selecione**个人**(desenvolvedor individual).
1. Insira as informações necessárias e selecione**下一步**(próxima etapa).
1. Conclua o processo de verificação de email. Você precisará aguardar alguns dias para ser aprovado após o registro como desenvolvedor.

### <a name="register-a-qq-application"></a>Registrar um aplicativo QQ

1. Vá para [https://connect.qq.com/index.html](https://connect.qq.com/index.html).
1. Selecione**应用管理**(gerenciamento de aplicativos).
1. Selecione**创建应用**(criar aplicativo) e insira as informações necessárias.
1. Insira `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp` em**授权回调域**(URL de retorno de chamada). Por exemplo, se o `tenant_name` for contoso, defina a URL a ser `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
1. Selecione**创建应用**(criar aplicativo).
1. Na página confirmação, selecione**应用管理**(gerenciamento de aplicativo) para retornar à página de gerenciamento de aplicativo.
1. Selecione**查看**(exibição) ao lado do aplicativo que você criou.
1. Selecione**修改**(editar).
1. Copie a **ID do aplicativo** e a **chave do aplicativo**. Você precisa desses dois valores para adicionar o provedor de identidade ao seu locatário.

## <a name="configure-qq-as-an-identity-provider"></a>Configurar o QQ como um provedor de identidade

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
1. Selecione o ícone **diretório + assinatura** na barra de ferramentas do portal e selecione o diretório que contém seu locatário Azure ad B2C.
1. Na portal do Azure, procure e selecione **Azure ad B2C**.
1. Selecione **provedores de identidade**e, em seguida, selecione **QQ (versão prévia)** .
1. Insira um **nome**. Por exemplo, *QQ*.
1. Para a **ID do cliente**, insira a ID do aplicativo QQ que você criou anteriormente.
1. Para o **segredo do cliente**, insira a chave do aplicativo que você registrou.
1. Selecione **Guardar**.
