---
title: Configurar a inscrição e início de sessão com uma conta QQ através do Azure Active Directory B2C | Documentos da Microsoft
description: Forneça a inscrição e início de sessão para clientes com contas QQ nas suas aplicações com o Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 38e17308f7aab5b28509d7a3509be0de844ee94f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60316124"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e início de sessão com uma conta QQ através do Azure Active Directory B2C

> [!NOTE]
> Esta funcionalidade está em pré-visualização.
> 

## <a name="create-a-qq-application"></a>Criar uma aplicação de QQ

Para utilizar uma conta QQ como fornecedor de identidade no Azure Active Directory (Azure AD) B2C, terá de criar uma aplicação no seu inquilino que a representa. Se ainda não tiver uma conta QQ, pode obtê-la em [ https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033 ](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>Registre-se para o programa de desenvolvedores QQ

1. Inicie sessão para o [portal do programador QQ](http://open.qq.com) com as suas credenciais de conta QQ.
2. Depois de iniciar sessão, vá para [ http://open.qq.com/reg ](http://open.qq.com/reg) para registar-se como desenvolvedor.
3. Selecione**个人**(programadores individuais).
4. Introduza as informações necessárias e selecione**下一步**(o passo seguinte).
5. Conclua o processo de verificação de e-mail. Terá de aguardar alguns dias até ser aprovado após o Registro como um desenvolvedor. 

### <a name="register-a-qq-application"></a>Registar uma aplicação de QQ

1. Vá para [https://connect.qq.com/index.html](https://connect.qq.com/index.html).
2. Selecione**应用管理**(gestão de aplicações).
5. Selecione**创建应用**(criar a aplicação) e introduza as informações necessárias.
7. Introduza `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp` no**授权回调域**(URL de retorno de chamada). Por exemplo, se sua `tenant_name` é contoso, definir o URL a ser `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
8. Selecione**创建应用**(criar a aplicação).
9. Na página de confirmação, selecione**应用管理**(gestão de aplicações) para regressar à página de gestão da aplicação.
10. Selecione**查看**(exibir) junto à aplicação que criou.
11. Selecione**修改**(editar).
12. Copiar o **ID da aplicação** e **chave da aplicação**. Precisa, esses valores para adicionar o fornecedor de identidade para o seu inquilino.

## <a name="configure-qq-as-an-identity-provider"></a>Configurar QQ como fornecedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Selecione **fornecedores de identidade**e, em seguida, selecione **Add**.
5. Fornecer um **nome**. Por exemplo, introduza *QQ*.
6. Selecione **tipo de fornecedor de identidade**, selecione **QQ (pré-visualização)** e clique em **OK**.
7. Selecione **configurar este fornecedor de identidade** e introduza o ID da aplicação que registou anteriormente como a **ID de cliente** e introduza a chave da aplicação que registou como o **segredo do cliente** do QQ aplicação que criou anteriormente.
8. Clique em **OK** e, em seguida, clique em **criar** para guardar a configuração de QQ.

