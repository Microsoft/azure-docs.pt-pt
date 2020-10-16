---
title: Configurar inscrição e inscrição com uma conta QQ utilizando o Azure Ative Directory B2C
description: Forneça inscrição e inscrição a clientes com contas QQ nas suas aplicações utilizando o Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d230bc8a1e9bf388e1cca4e3a3a691223146d734
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85387988"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Configurar inscrição e inscrição com uma conta QQ utilizando o Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-qq-application"></a>Criar uma aplicação QQ

Para utilizar uma conta QQ como fornecedor de identidade no Azure Ative Directory B2C (Azure AD B2C), você precisa criar uma aplicação no seu inquilino que a represente. Se ainda não tiver uma conta QQ, pode inscrever-se em [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033) .

### <a name="register-for-the-qq-developer-program"></a>Registe-se no programa de desenvolvimento QQ

1. Inscreva-se no portal do [desenvolvedor QQ](http://open.qq.com) com as suas credenciais de conta QQ.
1. Depois de se inscrever, vá [https://open.qq.com/reg](https://open.qq.com/reg) registar-se como um desenvolvedor.
1. Selecione **个人** (desenvolvedor individual).
1. Introduza as informações necessárias e selecione **下一步** (passo seguinte).
1. Complete o processo de verificação de e-mail. Terá de esperar alguns dias para ser aprovado depois de se registar como desenvolvedor.

### <a name="register-a-qq-application"></a>Registar uma aplicação QQ

1. Vai [https://connect.qq.com/index.html](https://connect.qq.com/index.html) para.
1. Selecione **应用管理** (gestão de aplicações).
1. Selecione **创建应用** (criar app) e introduza as informações necessárias.
1. Introduza `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp` em **授权回调域** (URL de retorno). Por exemplo, se o seu `tenant_name` é contoso, desemosse o `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp` URL.
1. Selecione **创建应用** (criar app).
1. Na página de confirmação, selecione **应用管理** (gestão de aplicações) para voltar à página de gestão de aplicações.
1. Selecione **查看** (vista) ao lado da aplicação que criou.
1. Selecione **修改** (editar).
1. Copie o **ID app** e **a CHAVE APP.** Precisa de ambos estes valores para adicionar o fornecedor de identidade ao seu inquilino.

## <a name="configure-qq-as-an-identity-provider"></a>Configure qQ como fornecedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Selecione **os fornecedores de identidade**e, em seguida, selecione **QQ (Pré-visualização)**.
1. Insira um **nome**. Por exemplo, *QQ*.
1. Para o ID do **Cliente,** insira o ID app da aplicação QQ que criou anteriormente.
1. Para o segredo do **Cliente,** insira a CHAVE APP que gravou.
1. Selecione **Guardar**.
