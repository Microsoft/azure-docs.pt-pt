---
title: Configurar o início e iniciar sessão com uma conta QQ utilizando o Diretório Ativo Azure B2C
description: Forneça inscrição e inscrição aos clientes com contas QQ nas suas aplicações utilizando o Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b0f15124c64e5cca54112987d486ddadaca79452
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78187992"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Configurar o início e iniciar sessão com uma conta QQ utilizando o Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-qq-application"></a>Criar uma aplicação QQ

Para utilizar uma conta QQ como fornecedor de identidade no Azure Ative Directory B2C (Azure AD B2C), precisa de criar uma aplicação no seu inquilino que o represente. Se ainda não tiver uma conta QQ, pode inscrever-se no [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>Registe-se no programa de desenvolvimento qQ

1. Inscreva-se no portal de [desenvolvimento qQ](http://open.qq.com) com as suas credenciais de conta QQ.
1. Depois de se inscrever, vá a [https://open.qq.com/reg](https://open.qq.com/reg) para se registar como desenvolvedor.
1. Selecione **(desenvolvedor** individual).
1. Introduza as informações necessárias e selecione (próximo passo).
1. Complete o processo de verificação de e-mail. Terá de esperar alguns dias para ser aprovado depois de se registar como desenvolvedor.

### <a name="register-a-qq-application"></a>Registar um pedido qQ

1. Vá para [https://connect.qq.com/index.html](https://connect.qq.com/index.html).
1. **Selecione** (gestão de aplicações).
1. **Selecione** (criar app) e introduza as informações necessárias.
1. Insira `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp` em **(URL** de callback). Por exemplo, se o seu `tenant_name` for contoso, defino o URL para ser `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
1. **Selecione** ( criar app).
1. Na página de confirmação, selecione (gestão de aplicações) para voltar à página de gestão de aplicações.
1. **Selecione** (ver) ao lado da app que criou.
1. **Selecione** (editar).
1. Copie o ID da **APP** e **a CHAVE APP**. Precisa de ambos os valores para adicionar o fornecedor de identidade ao seu inquilino.

## <a name="configure-qq-as-an-identity-provider"></a>Configure qQ como um fornecedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Selecione o ícone **de Diretório + Subscrição** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD AD B2C.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Selecione **fornecedores de identidade**e, em seguida, selecione **QQ (Pré-visualização)** .
1. Introduza um **Nome**. Por exemplo, *QQ*.
1. Para o ID do **cliente,** insira o ID app da aplicação QQ que criou anteriormente.
1. Para o **segredo do Cliente,** introduza a CHAVE APP que gravou.
1. Selecione **Guardar**.
