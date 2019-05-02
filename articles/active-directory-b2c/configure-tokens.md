---
title: Configurar tokens - Azure Active Directory B2C | Documentos da Microsoft
description: Saiba como configurar as definições de compatibilidade e tempo de vida token no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 69a6284ea7b8905bb5efdb1f4c9f26027bd6f9d9
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64689613"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Configurar tokens no Azure Active Directory B2C

Neste artigo, irá aprender a configurar o [duração e a compatibilidade de um token](active-directory-b2c-reference-tokens.md) no Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Pré-requisitos

[Criar um fluxo de utilizador](tutorial-create-user-flows.md) para permitir aos utilizadores inscrever-se e iniciar sessão na sua aplicação.

## <a name="configure-token-lifetime"></a>Configurar a duração do token

Pode configurar a duração do token em qualquer fluxo de utilizador.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C. Selecione o **filtro de diretório e subscrição** no menu superior e selecione o diretório que contém o seu inquilino do Azure AD B2C.
3. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
4. Selecione **fluxos de utilizador (diretivas)**.
5. Abra o fluxo de utilizador que criou anteriormente. 
6. Selecione **propriedades**.
7. Sob **tempo de vida do Token**, ajuste as propriedades seguintes para atender às necessidades da sua aplicação:

    ![Configurar a duração do token](./media/configure-tokens/token-lifetime.png)

8. Clique em **Guardar**.

## <a name="configure-token-compatibility"></a>Configurar a compatibilidade do token

1. Selecione **fluxos de utilizador (diretivas)**.
2. Abra o fluxo de utilizador que criou anteriormente. 
3. Selecione **propriedades**.
4. Sob **definições de compatibilidade do Token**, ajuste as propriedades seguintes para atender às necessidades da sua aplicação:

    ![Configurar a compatibilidade do token](./media/configure-tokens/token-compatibility.png)

5. Clique em **Guardar**.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre como [utilizar tokens de acesso](active-directory-b2c-access-tokens.md).



