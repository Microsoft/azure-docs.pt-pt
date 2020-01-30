---
title: Defina atributos personalizados no Diretório Ativo Azure B2C  Microsoft Docs
description: Defina atributos personalizados para a sua aplicação no Azure Ative Directory B2C para recolher informações sobre os seus clientes.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 433eff8f7ec22a3484e8e7f38dab2bb1c24e2fcc
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848073"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Defina atributos personalizados no Diretório Ativo Azure B2C

 Todas as aplicações voltadas para o cliente têm requisitos únicos para a informação que precisa de ser recolhida. O seu inquilino Azure Ative Directory B2C (Azure AD B2C) vem com um conjunto incorporado de informação armazenada em atributos, tais como Nome Dado, Apelido, Cidade e Código Postal. Com o Azure AD B2C, pode alargar o conjunto de atributos armazenados em cada conta de cliente.

 Pode criar atributos personalizados no [portal Azure](https://portal.azure.com/) e utilizá-los nos fluxos de utilizador de inscrição, fluxos de utilizador de inscrição ou de inscrição ou de edição de perfis, ou fluxos de utilizador de edição de perfis. Também pode ler e escrever estes atributos utilizando a [API do Gráfico AD Azure](manage-user-accounts-graph-api.md). Os atributos personalizados em Azure AD B2C usam extensões de [Dispor APi Do Gráfico Azure API](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions).

> [!NOTE]
> O suporte para a mais recente [Microsoft Graph API](https://docs.microsoft.com/graph/overview?view=graph-rest-1.0) para consulta do inquilino Azure AD B2C ainda está em desenvolvimento.
>

## <a name="create-a-custom-attribute"></a>Criar um atributo personalizado

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, ao trocá-lo no canto superior direito do portal do Azure. Selecione as suas informações de subscrição e, em seguida, selecione **Trocar Diretório**.

    ![Mudar para o inquilino do Azure AD B2C](./media/user-flow-custom-attributes/switch-directories.png)

    Escolha o diretório que contém o seu inquilino.

    ![Inquilino B2C em destaque no filtro de Diretório e Subscrição](./media/user-flow-custom-attributes/select-directory.PNG)

3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Selecione **os atributos do Utilizador**, e, em seguida, selecione **Adicionar**.
5. Forneça um **nome** para o atributo personalizado (por exemplo, "ShoeSize")
6. Escolha um **Tipo de Dados**. Apenas **String**, **Boolean**e **Int** estão disponíveis.
7. Opcionalmente, insira uma **Descrição** para fins informativos.
8. Clique em **Criar**.

O atributo personalizado está agora disponível na lista de **atributos** do Utilizador e para utilização nos fluxos de utilizador. Um atributo personalizado só é criado na primeira vez que é usado em qualquer fluxo de utilizador, e não quando o adiciona à lista de **atributos**do Utilizador .


## <a name="use-a-custom-attribute-in-your-user-flow"></a>Use um atributo personalizado no fluxo do utilizador

1. No seu inquilino Azure AD B2C, selecione **fluxos de utilizador**.
2. Selecione a sua política (por exemplo, "B2C_1_SignupSignin") para a abrir.
4. Selecione **os atributos do Utilizador** e, em seguida, selecione o atributo personalizado (por exemplo, "ShoeSize"). Clique em **Guardar**.
5. Selecione **pedidos de aplicação** e, em seguida, selecione o atributo personalizado.
6. Clique em **Guardar**.

Uma vez criado um novo utilizador utilizando um fluxo de utilizador que utiliza o atributo personalizado recém-criado, o objeto pode ser consultado no [Azure AD Graph Explorer](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart). Em alternativa, pode utilizar a função de fluxo do [**utilizador Run**](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows) no fluxo do utilizador para verificar a experiência do cliente. Deverá agora ver **shoeSize** na lista de atributos recolhidos durante a jornada de inscrição e vê-lo no token enviado de volta para a sua aplicação.

