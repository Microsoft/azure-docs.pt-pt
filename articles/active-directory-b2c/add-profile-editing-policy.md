---
title: Configurar um fluxo de edição de perfil
titleSuffix: Azure AD B2C
description: Saiba como configurar um fluxo de edição de perfil no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a0f4f785feed022226ed533d378a8fa52080b9ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104581903"
---
# <a name="set-up-a-profile-editing-flow-in-azure-active-directory-b2c"></a>Configurar um fluxo de edição de perfil no Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="profile-editing-flow"></a>Fluxo de edição de perfis

A política de edição de perfis permite que os utilizadores gerem os seus atributos de perfil, como nome de exibição, apelido, nome, cidade e outros. O fluxo de edição de perfis envolve os seguintes passos: 

1. Inscrição ou inscrição, com conta local ou social. Se a sessão ainda estiver ativa, o Azure AD B2C autoriza o utilizador e salta para o passo seguinte.
1. Azure AD B2C lê o perfil do utilizador a partir do diretório e deixa o utilizador editar os atributos.

![Fluxo de edição de perfis](./media/add-profile-editing-policy/profile-editing-flow.png)


## <a name="prerequisites"></a>Pré-requisitos

Se ainda não o fez, [registe uma aplicação web no Azure Ative Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-profile-editing-user-flow"></a>Criar um fluxo de utilizador de edição de perfil

Se pretender que os utilizadores editem o seu perfil na sua aplicação, utilize um fluxo de utilizador de edição de perfil.

1. No menu da página geral do inquilino Azure AD B2C, selecione **os fluxos do Utilizador** e, em seguida, selecione Novo fluxo de **utilizador**.
1. Na página De fluxo do **utilizador,** selecione o fluxo do utilizador **de edição de perfil.** 
1. Em **Selecione uma versão**, selecione **Recomendado** e, em seguida, selecione **Criar**.
1. Introduza um **Nome** para o fluxo do utilizador. Por exemplo, *perfilando1*.
1. Para **fornecedores de identidade**, selecione **e-mail de sismo**.
1. Para **os atributos do Utilizador,** escolha os atributos que pretende que o cliente possa editar no seu perfil. Por exemplo, selecione **Mostrar mais**, e, em seguida, escolha ambos os atributos e reclamações para o nome **de Exibição** e **o título de Job**. Clique em **OK**.
1. Clique **em Criar** para adicionar o fluxo do utilizador. Um prefixo de *B2C_1* é automaticamente anexado ao nome.

### <a name="test-the-user-flow"></a>Teste o fluxo do utilizador

1. Selecione o fluxo de utilizador criado para abrir a sua página de visão geral e, em seguida, selecione **Executar o fluxo do utilizador**.
1. Para **Aplicação**, selecione a aplicação web chamada *webapp1* que registou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Clique no **fluxo do utilizador executar** e, em seguida, iniciar scontabilidade com a conta que criou anteriormente.
1. Tem agora a oportunidade de alterar o nome de exibição e o título de trabalho para o utilizador. Clique em **Continue** (Continuar). O símbolo é devolvido `https://jwt.ms` e deve ser exibido para si.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-profile-editing-policy"></a>Criar uma política de edição de perfil

As políticas personalizadas são um conjunto de ficheiros XML que envia para o seu inquilino Azure AD B2C para definir viagens de utilizador. Fornecemos pacotes de iniciação com várias políticas pré-construídas, incluindo: inscrição e início de sessão, reset de palavra-passe e política de edição de perfis. Para obter mais informações, consulte [Começar com políticas personalizadas em Azure AD B2C.](custom-policy-get-started.md)

::: zone-end

## <a name="next-steps"></a>Passos seguintes

* Adicione uma [sindens de saúde com o provedor de identidade social.](add-identity-provider.md)