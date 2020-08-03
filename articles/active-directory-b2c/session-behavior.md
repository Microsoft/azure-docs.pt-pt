---
title: Configure comportamento da sessão - Azure Ative Directory B2C / Microsoft Docs
description: Configurar o comportamento da sessão no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c47b0249824a3683b8f24cc01cc5eea5ccf32585
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87482673"
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

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a sessão Azure AD B2C](session-overview.md).