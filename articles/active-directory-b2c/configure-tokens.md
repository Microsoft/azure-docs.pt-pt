---
title: Fichas de configuração - Diretório Ativo Azure B2C / Microsoft Docs
description: Saiba como configurar as definições de vida simbólica e compatibilidade no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 160898f8535d3dad4811af016ebca779b7ef43a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189622"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Configure fichas no Diretório Ativo Azure B2C

Neste artigo, aprende-se a configurar a vida e a [compatibilidade de um símbolo](tokens-overview.md) no Azure Ative Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Pré-requisitos

[Crie um fluxo de utilizador](tutorial-create-user-flows.md) para permitir que os utilizadores se inscrevam e inscrevam-se na sua aplicação.

## <a name="configure-token-lifetime"></a>Configure token lifetime

Pode configurar a vida útil do token em qualquer fluxo do utilizador.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro de **subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino Azure AD B2C.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
4. Selecione **fluxos de utilizador (políticas)**.
5. Abra o fluxo de utilizador que criou anteriormente.
6. Selecione **Propriedades**.
7. Ao longo **da vida token,** ajuste as seguintes propriedades para se adaptar às necessidades da sua aplicação:

    ![Configurações de propriedade vitalícia simbólica no portal Azure](./media/configure-tokens/token-lifetime.png)

8. Clique em **Guardar**.

## <a name="configure-token-compatibility"></a>Configure compatibilidade simbólica

1. Selecione **fluxos de utilizador (políticas)**.
2. Abra o fluxo de utilizador que criou anteriormente.
3. Selecione **Propriedades**.
4. Sob as definições de **compatibilidade token,** ajuste as seguintes propriedades para se adaptar às necessidades da sua aplicação:

    ![Configurações de propriedade de compatibilidade token no portal Azure](./media/configure-tokens/token-compatibility.png)

5. Clique em **Guardar**.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [usar fichas](access-tokens.md)de acesso.



