---
title: Configurar a inscrição e início de sessão com uma conta de WeChat através do Azure Active Directory B2C | Documentos da Microsoft
description: Forneça a inscrição e início de sessão para clientes com contas de WeChat nas suas aplicações com o Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: aa44edcf009d381894a581172ea5edffefe946a0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508142"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e início de sessão com uma conta de WeChat através do Azure Active Directory B2C

> [!NOTE]
> Esta funcionalidade está em pré-visualização.
> 

## <a name="create-a-wechat-application"></a>Criar uma aplicação de WeChat

Para utilizar uma conta de WeChat como fornecedor de identidade no Azure Active Directory (Azure AD) B2C, terá de criar uma aplicação no seu inquilino que a representa. Se ainda não tiver uma conta de WeChat, pode obter informações em [ https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html ](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html).

### <a name="register-a-wechat-application"></a>Registar uma aplicação de WeChat

1. Inicie sessão no [ https://open.weixin.qq.com/ ](https://open.weixin.qq.com/) com as suas credenciais de WeChat.
2. Selecione**管理中心**(Centro de gestão).
3. Siga os passos para registar uma nova aplicação.
4. Introduza `https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` no**授权回调域**(URL de retorno de chamada). Por exemplo, se o nome de inquilino for contoso, definir o URL para ser `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
5. Copiar o **ID da aplicação** e **chave da aplicação**. Estes são necessários para adicionar o fornecedor de identidade para o seu inquilino.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Configurar WeChat como fornecedor de identidade no seu inquilino

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Selecione **fornecedores de identidade**e, em seguida, selecione **Add**.
5. Fornecer um **nome**. Por exemplo, introduza *WeChat*.
6. Selecione **tipo de fornecedor de identidade**, selecione **WeChat (pré-visualização)** e clique em **OK**.
7. Selecione **configurar este fornecedor de identidade** e introduza o ID da aplicação que registou anteriormente como a **ID de cliente** e introduza a chave da aplicação que registou como o **segredo do cliente** dos Aplicação de WeChat que criou anteriormente.
8. Clique em **OK** e, em seguida, clique em **criar** para guardar a configuração de WeChat.

