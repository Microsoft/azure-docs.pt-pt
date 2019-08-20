---
title: Configurar a inscrição e a entrada com uma conta do WeChat usando Azure Active Directory B2C
description: Forneça inscrição e entrada para clientes com contas do WeChat em seus aplicativos usando Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 377fd5c2be9e49e077303aac2a48fa2a0b8288ef
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622146"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e a entrada com uma conta do WeChat usando Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-wechat-application"></a>Criar um aplicativo WeChat

Para usar uma conta do WeChat como um provedor de identidade no Azure Active Directory (Azure AD) B2C, você precisa criar um aplicativo em seu locatário que o represente. Se você ainda não tiver uma conta do WeChat, poderá obter informações em [https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html).

### <a name="register-a-wechat-application"></a>Registrar um aplicativo WeChat

1. Entre no com suas credenciais do WeChat. [https://open.weixin.qq.com/](https://open.weixin.qq.com/)
1. Selecione**管理中心**(centro de gerenciamento).
1. Siga as etapas para registrar um novo aplicativo.
1. Insira `https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` em**授权回调域**(URL de retorno de chamada). Por exemplo, se o nome do locatário for contoso, defina a URL como `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
1. Copie a **ID do aplicativo** e a **chave do aplicativo**. Você precisará delas para adicionar o provedor de identidade ao seu locatário.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Configurar o WeChat como um provedor de identidade em seu locatário

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **provedores de identidade**e, em seguida, selecione **WeChat (versão prévia)** .
1. Insira um **nome**. Por exemplo, *WeChat*.
1. Para a **ID do cliente**, insira a ID do aplicativo WeChat que você criou anteriormente.
1. Para o **segredo do cliente**, insira a chave do aplicativo que você registrou.
1. Selecione **Guardar**.
