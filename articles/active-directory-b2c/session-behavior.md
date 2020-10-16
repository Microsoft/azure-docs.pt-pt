---
title: Configure comportamento da sessão - Azure Ative Directory B2C / Microsoft Docs
description: Saiba como configurar o comportamento da sessão no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 091704fabb7b50a0c83625c6ae46d9a807f01ffc
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961039"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Configurar comportamento de sessão no Azure Ative Directory B2C

A gestão [de sessão de sessão de sessão de sessão de sessão de sessão de sessão](session-overview.md) em Azure Ative Directory B2C (Azure AD B2C) permite que um administrador controle a interação com um utilizador depois de o utilizador já ter autenticado. Por exemplo, o administrador pode controlar se a seleção de fornecedores de identidade é apresentada ou se os detalhes da conta precisam de ser introduzidos novamente. Este artigo descreve como configurar as definições SSO para Azure AD B2C.

## <a name="session-behavior-properties"></a>Propriedades de comportamento de sessão

Pode utilizar as seguintes propriedades para gerir sessões de aplicações web:

- **Duração da sessão de aplicações web (minutos)** - A vida útil do cookie de sessão do Azure AD B2C armazenado no navegador do utilizador após a autenticação bem sucedida.
    - Predefinição = 1440 minutos.
    - Mínimo (inclusivo) = 15 minutos.
    - Máximo (inclusivo) = 1440 minutos.
- **Tempo limite de sessão de aplicações web** - O [tipo de sessão expirado,](session-overview.md#session-expiry-type) *Rolling,* ou *Absoluto*. 
- **Configuração única de entrada** - O âmbito de [sessão](session-overview.md#session-scope) do comportamento de sessão única (SSO) em várias aplicações e fluxos de utilizador no seu inquilino Azure AD B2C.


## <a name="configure-the-properties"></a>Configure as propriedades

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino Azure AD B2C.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
4. Selecione **fluxos de utilizador**.
5. Abra o fluxo de utilizador que criou anteriormente.
6. Selecione **Propriedades**.
7. Configure a **vida útil da sessão de aplicações Web (minutos)**, o tempo limite de sessão de **aplicações web,** **a configuração de inscrição única**e o **ID Token em pedidos de logout,** conforme necessário.

    ![Definições de propriedade de comportamento de sessão no portal Azure](./media/session-behavior/session-behavior.png)

8. Clique em **Guardar**.

## <a name="configure-sign-out-behavior"></a>Configurar o comportamento de assinatura

### <a name="secure-your-logout-redirect"></a>Fixe o seu redirecionamento de logout

Após o logout, o utilizador é redirecionado para o URI especificado no `post_logout_redirect_uri` parâmetro, independentemente dos URLs de resposta especificados para a aplicação. No entanto, se um válido `id_token_hint` for aprovado e o **Token de ID requere em pedidos de logout** é ligado, Azure AD B2C verifica que o valor da `post_logout_redirect_uri` correspondência corresponde a um dos URIs de redirecionamento configurados da aplicação antes de efetuar o redirecionamento. Se não tiver sido configurado um URL de resposta correspondente para a aplicação, é apresentada uma mensagem de erro e o utilizador não é redirecionado. Para exigir um ID Token em pedidos de logout:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino Azure AD B2C.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. Selecione **fluxos de utilizador**.
1. Abra o fluxo de utilizador que criou anteriormente.
1. Selecione **Propriedades**.
1. Ativar o **Token ID Do Requerendo em pedidos de logout**.
1. Volte para  **Azure AD B2C**.
1. Selecione **as inscrições da App**e, em seguida, selecione a sua aplicação.
1. Selecione **Autenticação**.
1. Na caixa de texto **URL logout,** digite o seu logout post redirecione URI e, em seguida, selecione **Save**.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a sessão Azure AD B2C](session-overview.md).
