---
title: Fichas de configuração - Diretório Ativo Azure B2C / Microsoft Docs
description: Saiba como configurar as definições de vida simbólica e compatibilidade no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dca235374ca20ef8a17e685706f952127d5ece2a
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83869440"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Configure fichas no Diretório Ativo Azure B2C

Neste artigo, aprende-se a configurar a vida e a [compatibilidade de um símbolo](tokens-overview.md) no Azure Ative Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Pré-requisitos

[Crie um fluxo de utilizador](tutorial-create-user-flows.md) para permitir que os utilizadores se inscrevam e inscrevam-se na sua aplicação.

## <a name="configure-jwt-token-lifetime"></a>Configure a vida útil do símbolo JWT

Pode configurar a vida útil do token em qualquer fluxo do utilizador.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro de **subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino Azure AD B2C.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
4. Selecione **fluxos de utilizador (políticas)**.
5. Abra o fluxo de utilizador que criou anteriormente.
6. Selecione **Propriedades**.
7. Ao longo **da vida token,** ajuste as seguintes propriedades para se adaptar às necessidades da sua aplicação:

    ![Configurações de propriedade vitalícia simbólica no portal Azure](./media/configure-tokens/token-lifetime.png)

8. Clique em **Guardar**.

## <a name="configure-jwt-token-compatibility"></a>Configure compatibilidade simbólica JWT

1. Selecione **fluxos de utilizador (políticas)**.
2. Abra o fluxo de utilizador que criou anteriormente.
3. Selecione **Propriedades**.
4. Sob as definições de **compatibilidade token,** ajuste as seguintes propriedades para se adaptar às necessidades da sua aplicação:

    ![Configurações de propriedade de compatibilidade token no portal Azure](./media/configure-tokens/token-compatibility.png)

5. Clique em **Guardar**.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [solicitar fichas](access-tokens.md)de acesso.



