---
title: Configurar inscrição e iniciar sôm-in com uma conta WeChat
titleSuffix: Azure AD B2C
description: Forneça inscrição e inscrição a clientes com contas WeChat nas suas aplicações utilizando o Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 462e33c836d8ca0a904e8f7b2e833dc7103311fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85387903"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Configurar inscrição e inscrição com uma conta WeChat utilizando o Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-wechat-application"></a>Criar uma aplicação WeChat

Para utilizar uma conta WeChat como fornecedor de identidade no Azure Ative Directory B2C (Azure AD B2C), precisa de criar uma aplicação no seu inquilino que a represente. Se ainda não tiver uma conta WeChat, pode obter informações em [https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html) .

### <a name="register-a-wechat-application"></a>Registar uma aplicação WeChat

1. Inscreva-se [https://open.weixin.qq.com/](https://open.weixin.qq.com/) com as suas credenciais WeChat.
1. Selecione **管理中心** (centro de gestão).
1. Siga os passos para registar uma nova candidatura.
1. Introduza `https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` em **授权回调域** (URL de retorno). Por exemplo, se o nome do seu inquilino for contoso, desemosse o `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp` URL.
1. Copie o **ID app** e **a CHAVE APP.** Você precisará destes para adicionar o fornecedor de identidade ao seu inquilino.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Configure o WeChat como fornecedor de identidade no seu inquilino

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **os fornecedores de identidade**e, em seguida, selecione **WeChat (Preview)**.
1. Insira um **nome**. Por exemplo, *WeChat*.
1. Para o ID do **Cliente,** insira o ID app da aplicação WeChat que criou anteriormente.
1. Para o segredo do **Cliente,** insira a CHAVE APP que gravou.
1. Selecione **Guardar**.
