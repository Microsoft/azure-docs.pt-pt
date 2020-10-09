---
title: Fichas de configuração - Azure Ative Directory B2C / Microsoft Docs
description: Saiba como configurar as definições de vida útil e compatibilidade simbólicas no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 49a5ff61e5f7a17005561e0729a9b0fcb0f954d4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85389569"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Fichas de configuração no Azure Ative Directory B2C

Neste artigo, aprende-se a configurar a [vida e a compatibilidade de um símbolo](tokens-overview.md) em Azure Ative Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Pré-requisitos

[Crie um fluxo de utilizador](tutorial-create-user-flows.md) para permitir que os utilizadores se inscrevam e se inscrevam na sua aplicação.

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

## <a name="configure-jwt-token-compatibility"></a>Configure compatibilidade simbólica JWT

1. Selecione **fluxos de utilizador (políticas)**.
2. Abra o fluxo de utilizador que criou anteriormente.
3. Selecione **Propriedades**.
4. Nas **definições de compatibilidade token,** ajuste as seguintes propriedades para se adaptar às necessidades da sua aplicação:

    ![Definições de propriedade de compatibilidade token no portal Azure](./media/configure-tokens/token-compatibility.png)

5. Clique em **Guardar**.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [solicitar fichas de acesso.](access-tokens.md)



