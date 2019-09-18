---
title: Configurar tokens-Azure Active Directory B2C | Microsoft Docs
description: Saiba como configurar a vida útil do token e as configurações de compatibilidade no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 83f8051fa31b6431d4a8515e2c0912cc1872a402
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064376"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Configurar tokens no Azure Active Directory B2C

Neste artigo, você aprenderá a configurar o [tempo de vida e a compatibilidade de um token](active-directory-b2c-reference-tokens.md) no Azure Active Directory B2C (Azure ad B2C).

## <a name="prerequisites"></a>Pré-requisitos

[Crie um fluxo de usuário](tutorial-create-user-flows.md) para permitir que os usuários se inscrevam e entrem no seu aplicativo.

## <a name="configure-token-lifetime"></a>Configurar tempo de vida do token

Você pode configurar o tempo de vida do token em qualquer fluxo de usuário.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C. Selecione o **diretório +** filtro de assinatura no menu superior e escolha o diretório que contém seu locatário de Azure ad B2C.
3. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
4. Selecione **fluxos de usuário (políticas)** .
5. Abra o fluxo de usuário que você criou anteriormente.
6. Selecione **propriedades**.
7. Em **tempo de vida do token**, ajuste as propriedades a seguir para atender às necessidades do seu aplicativo:

    ![Configurações de propriedade de tempo de vida do token no portal do Azure](./media/configure-tokens/token-lifetime.png)

8. Clique em **Guardar**.

## <a name="configure-token-compatibility"></a>Configurar a compatibilidade de token

1. Selecione **fluxos de usuário (políticas)** .
2. Abra o fluxo de usuário que você criou anteriormente.
3. Selecione **propriedades**.
4. Em **configurações de compatibilidade de token**, ajuste as propriedades a seguir para atender às necessidades do seu aplicativo:

    ![Configurações de propriedade de compatibilidade de token no portal do Azure](./media/configure-tokens/token-compatibility.png)

5. Clique em **Guardar**.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [usar tokens de acesso](active-directory-b2c-access-tokens.md).



