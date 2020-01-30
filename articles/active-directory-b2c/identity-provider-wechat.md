---
title: Configurar o início e iniciar sessão com uma conta WeChat
titleSuffix: Azure AD B2C
description: Forneça inscrição e inscrição aos clientes com contas WeChat nas suas aplicações utilizando o Azure Ative Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7cbc41247d7b99eb63730ae4326808e64c663e8f
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76849529"
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
1. Copie a **ID do aplicativo** e a **chave do aplicativo**. Você precisará destes para adicionar o fornecedor de identidade ao seu inquilino.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Configure o WeChat como fornecedor de identidade no seu inquilino

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **fornecedores de identidade**e, em seguida, selecione **WeChat (Pré-visualização)** .
1. Insira um **nome**. Por exemplo, *WeChat*.
1. Para o ID do **Cliente,** insira o ID app da aplicação WeChat que criou anteriormente.
1. Para o **segredo do cliente**, insira a chave do aplicativo que você registrou.
1. Selecione **Guardar**.
