---
title: Configure comportamento de sessão - Diretório Ativo Azure B2C / Microsoft Docs
description: Configure o comportamento da sessão no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f5400b47c1e0b4657e40d2c57f8212711bbdaf3f
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82927076"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Configure comportamento de sessão no Diretório Ativo Azure B2C

A gestão da sessão de sessão de início de [sessão (SSO)](session-overview.md) no Azure Ative Directory B2C (Azure AD B2C) permite a um administrador controlar a interação com um utilizador depois de o utilizador já ter autenticado. Por exemplo, o administrador pode controlar se a seleção dos fornecedores de identidade é apresentada ou se os detalhes da conta precisam de ser novamente introduzidos. Este artigo descreve como configurar as definições sSO para Azure AD B2C.

## <a name="session-behavior-properties"></a>Propriedades do comportamento da sessão

Pode utilizar as seguintes propriedades para gerir sessões de aplicação web:

- Vida útil da sessão da **aplicação web (minutos)** - A vida útil do cookie de sessão do Azure AD B2C armazenado no navegador do utilizador após a autenticação bem sucedida.
    - Predefinição = 1440 minutos.
    - Mínimo (inclusivo) = 15 minutos.
    - Máximo (inclusivo) = 1440 minutos.
- **Timeout** de sessão de aplicação web - O tipo de expiração da [sessão,](session-overview.md#session-expiry-type) *Rolling*, ou *Absolute*. 
- **Configuração de inscrição única** - O âmbito da [sessão](session-overview.md#session-scope) do comportamento de entrada única (SSO) em várias aplicações e fluxos de utilizadores no seu inquilino Azure AD B2C. 


## <a name="configure-the-properties"></a>Configure as propriedades

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro de **subscrição Do Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino Azure AD B2C.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
4. Selecione **fluxos de utilizador (políticas)**.
5. Abra o fluxo de utilizador que criou anteriormente.
6. Selecione **Propriedades**.
7. Configure o tempo de vida da sessão da **web (minutos)**, tempo limite de sessão de **aplicações web**, **configuração de início de sessão individual**e exija ID Token em pedidos de **logout** conforme necessário.

    ![Configurações de propriedade de comportamento de sessão no portal Azure](./media/session-behavior/session-behavior.png)

8. Clique em **Guardar**.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [sessão Azure AD B2C.](session-overview.md)