---
title: Fichas de configuração - Azure Ative Directory B2C / Microsoft Docs
description: Saiba como configurar as definições de vida útil e compatibilidade simbólicas no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 67bc9d6b35d4841999721a00592a6bbe23bff10f
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340228"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Fichas de configuração no Azure Ative Directory B2C

Neste artigo, aprende-se a configurar a [vida e a compatibilidade de um símbolo](tokens-overview.md) em Azure Ative Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Pré-requisitos

[Criar um fluxo de utilizador](tutorial-create-user-flows.md) para permitir que os utilizadores se inscrevam e iniciem sessão na sua aplicação.

## <a name="configure-jwt-token-lifetime"></a>Configurar a vida útil simbólica da JWT

Pode configurar a vida útil do símbolo em qualquer fluxo de utilizador.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro **de subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino Azure AD B2C.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
4. Selecione **fluxos de utilizador (políticas)**.
5. Abra o fluxo de utilizador que criou anteriormente.
6. Selecione **Propriedades**.
7. Ao abrigo **da vida útil da Token,** ajuste as seguintes propriedades para se adaptar às necessidades da sua aplicação:

    ![Definições de propriedade vitalícia simbólicas no portal Azure](./media/configure-tokens/token-lifetime.png)

8. Clique em **Guardar**.

> [!NOTE]
> As aplicações de uma página única que utilizam o fluxo de código de autorização com O PKCE têm sempre um prazo de vida útil de 24 horas. [Saiba mais sobre as implicações de segurança dos tokens de atualização no navegador.](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser)

## <a name="configure-jwt-token-compatibility"></a>Configure compatibilidade simbólica JWT

1. Selecione **fluxos de utilizador (políticas)**.
2. Abra o fluxo de utilizador que criou anteriormente.
3. Selecione **Propriedades**.
4. Nas **definições de compatibilidade token,** ajuste as seguintes propriedades para se adaptar às necessidades da sua aplicação:

    ![Definições de propriedade de compatibilidade token no portal Azure](./media/configure-tokens/token-compatibility.png)

5. Clique em **Guardar**.

## <a name="provide-optional-claims-to-your-app"></a>Fornecer reclamações opcionais à sua app

As reclamações da aplicação são valores que são devolvidos ao pedido. Atualize o fluxo do utilizador para conter as reclamações desejadas.

1. Selecione **fluxos de utilizador (políticas)**.
1. Abra o fluxo de utilizador que criou anteriormente.
1. Selecione **Afirmações de aplicação**.
1. Escolha as reclamações e atributos que deseja enviar de volta para a sua aplicação.
1. Clique em **Guardar**.


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [solicitar fichas de acesso.](access-tokens.md)



