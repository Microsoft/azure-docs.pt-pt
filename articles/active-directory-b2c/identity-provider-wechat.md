---
title: Configurar o início e iniciar sessão com uma conta WeChat
titleSuffix: Azure AD B2C
description: Forneça inscrição e inscrição aos clientes com contas WeChat nas suas aplicações utilizando o Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c53210939358255b20d0e976df9c4bff88580a80
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184441"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Configurar o sign-up e iniciar sessão com uma conta WeChat utilizando o Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-wechat-application"></a>Criar uma aplicação WeChat

Para utilizar uma conta WeChat como fornecedor de identidade no Azure Ative Directory B2C (Azure AD B2C), precisa de criar uma aplicação no seu inquilino que o represente. Se ainda não tiver uma conta WeChat, pode obter informações em [https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html).

### <a name="register-a-wechat-application"></a>Registe uma aplicação WeChat

1. Inscreva-se na [https://open.weixin.qq.com/](https://open.weixin.qq.com/) com as suas credenciais WeChat.
1. **Selecione** (centro de gestão).
1. Siga os passos para registar uma nova aplicação.
1. Insira `https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` em **(URL** de callback). Por exemplo, se o nome do seu inquilino for contoso, defino o URL para ser `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
1. Copie o ID da **APP** e **a CHAVE APP**. Você precisará destes para adicionar o fornecedor de identidade ao seu inquilino.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Configure o WeChat como fornecedor de identidade no seu inquilino

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro de **subscrição Do Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **fornecedores de identidade**e, em seguida, selecione **WeChat (Pré-visualização)** .
1. Introduza um **Nome**. Por exemplo, *WeChat*.
1. Para o ID do **Cliente,** insira o ID app da aplicação WeChat que criou anteriormente.
1. Para o **segredo do Cliente,** introduza a CHAVE APP que gravou.
1. Selecione **Guardar**.
