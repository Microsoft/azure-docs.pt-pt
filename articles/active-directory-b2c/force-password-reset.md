---
title: Configure um fluxo de reset de senha de força em Azure AD B2C
titleSuffix: Azure AD B2C
description: Saiba como configurar um fluxo de reset de palavra-passe forçada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/08/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 7fac7df0978b23e535d8761b436b14e2f41e5f91
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107209507"
---
# <a name="set-up-a-force-password-reset-flow-in-azure-active-directory-b2c"></a>Configurar um fluxo de reset de palavra-passe de força no Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

> [!IMPORTANT]
> O reset da palavra-passe de força é uma funcionalidade de pré-visualização pública do Azure AD B2C. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Descrição Geral
Como administrador, pode [redefinir a palavra-passe de um utilizador](manage-users-portal.md#reset-a-users-password) se o utilizador esquecer a sua palavra-passe. Ou gostaria de forçá-los a redefinir a senha. Neste artigo, você vai aprender a forçar uma palavra-passe reset nestes cenários.

Quando um administrador reinicia a palavra-passe de um utilizador através do portal Azure, o valor do atributo [forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) é definido para `true` . A [jornada de entrada e inscrição](add-sign-up-and-sign-in-policy.md) verifica o valor deste atributo. Depois de o utilizador completar a inscrição, se o atributo estiver definido, o utilizador deve redefinir a `true` sua palavra-passe. Em seguida, o valor do atributo é definido para trás `false` .

![Fluxo de reset de senha de força](./media/force-password-reset/force-password-reset-flow.png)

O fluxo de reset da palavra-passe é aplicável às contas locais em Azure AD B2C que usam um [endereço de e-mail](identity-provider-local.md#email-sign-in) ou [nome de utilizador](identity-provider-local.md#username-sign-in) com uma palavra-passe para iniciar sposição.

::: zone pivot="b2c-user-flow"

### <a name="force-a-password-reset-after-90-days"></a>Forçar uma palavra-passe reposta após 90 dias

Como administrador, pode definir a validade da palavra-passe de um utilizador para 90 dias, utilizando [o Ms Graph](microsoft-graph-operations.md). Após 90 dias, o valor do atributo [forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) é automaticamente definido para `true` . Para obter mais informações sobre como definir a política de validade da palavra-passe de um utilizador, consulte [o atributo da política de palavra-passe](user-profile-attributes.md#password-policy-attribute).

Uma vez definida uma política de expiração da palavra-passe, também deve configurar o fluxo de reset da palavra-passe de força, conforme descrito neste artigo.  

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="configure-your-policy"></a>Configure a sua política

Para ativar a definição de **reset da palavra-passe forçada** num fluxo de utilizador de inscrição ou de inscrição:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Selecione **fluxos de utilizador**.
1. Selecione a inscrição e o inseósi, ou faça o sê-lo (do tipo **Recomendado)** que pretende personalizar.
1. No menu esquerdo em **Definições**, selecione **Propriedades**.
1. Sob **a complexidade da palavra-passe**, selecione **Reposição de palavras-passe forçadas**.
1. Selecione **Guardar**.

### <a name="test-the-user-flow"></a>Teste o fluxo do utilizador

1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador de utilizador ou administrador de password. Para obter mais informações sobre as funções disponíveis, consulte [as funções de administrador de atribuição no Diretório Ativo Azure](../active-directory/roles/permissions-reference.md#all-roles).
1. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Selecione **Utilizadores**. Procure e selecione o utilizador que utilizará para testar o reset da palavra-passe e, em seguida, selecione **Redefinir a Palavra-passe**.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Selecione **fluxos de utilizador**.
1. Selecione um fluxo de utilizador de inscrição ou de inscrição (do tipo **Recomendado)** que pretende testar.
1. Selecione **Executar o fluxo do utilizador**.
1. Para **Aplicação**, selecione a aplicação web chamada *webapp1* que registou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Selecione **Executar o fluxo do utilizador**.
1. Faça o sômis com a conta de utilizador para a qual repõe a palavra-passe.
1. Tem agora de alterar a palavra-passe para o utilizador. Altere a palavra-passe e **selecione Continue**. O símbolo é devolvido `https://jwt.ms` e deve ser exibido para si.

::: zone-end

::: zone pivot="b2c-custom-policy"

Atualmente esta funcionalidade apenas se encontra disponível para Fluxos de Utilização. Para etapas de configuração, escolha **o Fluxo do Utilizador** acima.

::: zone-end

## <a name="next-steps"></a>Passos seguintes

Configurar uma [redefinição da palavra-passe de autosserviço.](add-password-reset-policy.md)
