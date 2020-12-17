---
title: Configurar um fluxo de reset de palavra-passe
titleSuffix: Azure AD B2C
description: Saiba como configurar um fluxo de reset de palavra-passe no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 6e6e4aa4ece781f415308b638323f8d4d4b34038
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97618829"
---
# <a name="set-up-a-password-reset-flow-in-azure-active-directory-b2c"></a>Configurar um fluxo de reset de palavra-passe no Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="password-rest-flow"></a>Fluxo de descanso de senha

A política de reset da palavra-passe permite que os utilizadores repuvam a sua própria palavra-passe esquecida. O fluxo de reset da palavra-passe envolve os seguintes passos: 
1. A partir da página de inscrição e de entrada, o utilizador clica na "Esqueci-me da palavra-passe?" . Azure AD B2C devolve o código de erro AADB2C90118 à sua aplicação. A aplicação trata deste código de erro invocando a política de reset da palavra-passe. 
1. Os utilizadores fornecem e verificam o seu e-mail com uma senha de tempo cronometrada.
1. Introduza uma nova senha.

![Fluxo de reset de palavra-passe](./media/add-password-reset-policy/password-reset-flow.png)

## <a name="prerequisites"></a>Pré-requisitos

Se ainda não o fez, [registe uma aplicação web no Azure Ative Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-password-reset-user-flow"></a>Criar um fluxo de utilizador de redefinição de palavra-passe

Para permitir que os utilizadores da sua aplicação repuvam a sua palavra-passe, utilize uma palavra-passe para redefinir o fluxo do utilizador.

1. No menu geral do inquilino Azure AD B2C, selecione **os fluxos do utilizador** e, em seguida, selecione Novo fluxo de **utilizador**.
1. Na página De fluxo do **utilizador,** selecione o fluxo do utilizador **de redefinição de palavra-passe.** 
1. Em **Selecione uma versão**, selecione **Recomendado** e, em seguida, selecione **Criar**.
1. Introduza um **Nome** para o fluxo do utilizador. Por exemplo, *passwordreste1*.
1. Para **os fornecedores de identidade,** ative **a palavra-passe Reset utilizando o endereço de e-mail.**
2. Nas reclamações da Aplicação, clique **em Mostrar mais** e escolha as reclamações que pretende devolvidas nos tokens de autorização enviados de volta para a sua aplicação. Por exemplo, selecione **ID de Objeto do Utilizador**.
3. Clique em **OK**.
4. Clique **em Criar** para adicionar o fluxo do utilizador. Um prefixo de *B2C_1* é automaticamente anexado ao nome.

### <a name="test-the-user-flow"></a>Teste o fluxo do utilizador

1. Selecione o fluxo de utilizador criado para abrir a sua página de visão geral e, em seguida, selecione **Executar o fluxo do utilizador**.
1. Para **Aplicação**, selecione a aplicação web chamada *webapp1* que registou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Clique **no fluxo do utilizador** executar, verifique o endereço de e-mail da conta que criou anteriormente e selecione **Continue**.
1. Tem agora a oportunidade de alterar a palavra-passe para o utilizador. Altere a palavra-passe e **selecione Continue**. O símbolo é devolvido `https://jwt.ms` e deve ser exibido para si.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-password-reset-policy"></a>Criar uma política de reposição de palavra-passe

As políticas personalizadas são um conjunto de ficheiros XML que envia para o seu inquilino Azure AD B2C para definir viagens de utilizador. Fornecemos pacotes de iniciação com várias políticas pré-construídas, incluindo: inscrição e início de sessão, reset de palavra-passe e política de edição de perfis. Para obter mais informações, consulte [Começar com políticas personalizadas em Azure AD B2C.](custom-policy-get-started.md)

::: zone-end

## <a name="next-steps"></a>Passos seguintes

Configurar um [fluxo de edição de perfil.](add-profile-editing-policy.md)