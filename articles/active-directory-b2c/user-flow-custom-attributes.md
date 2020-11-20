---
title: Defina atributos personalizados no Azure Ative Directory B2C Microsoft Docs
description: Defina atributos personalizados para a sua aplicação no Azure Ative Directory B2C para recolher informações sobre os seus clientes.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2018
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2233dbd7b0e669c23397b4bc6a84f2bfdc208391
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94952816"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Defina atributos personalizados no Azure Ative Directory B2C

 Cada aplicação virada para o cliente tem requisitos únicos para a informação que precisa de ser recolhida. O seu inquilino Azure Ative Directory B2C (Azure AD B2C) vem com um conjunto incorporado de informações armazenadas em atributos, tais como Nome Dado, Apelido, Cidade e Código Postal. Com o Azure AD B2C, pode estender o conjunto de atributos armazenados em cada conta de cliente.

 Pode criar atributos personalizados no [portal Azure](https://portal.azure.com/) e usá-los nos fluxos de utilizador de inscrição, nos fluxos de utilizador de inscrição ou de inscrição ou na edição de perfis dos fluxos de utilizador. Também pode ler e escrever estes atributos utilizando a [API](manage-user-accounts-graph-api.md)do Gráfico microsoft .

## <a name="create-a-custom-attribute"></a>Criar um atributo personalizado

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, ao trocá-lo no canto superior direito do portal do Azure. Selecione as suas informações de subscrição e, em seguida, selecione **Trocar Diretório**.

    ![Mudar para o inquilino do Azure AD B2C](./media/user-flow-custom-attributes/switch-directories.png)

    Escolha o diretório que contém o seu inquilino.

    ![Inquilino B2C em destaque no diretório e filtro de assinatura](./media/user-flow-custom-attributes/select-directory.PNG)

3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Selecione **os atributos do Utilizador** e, em seguida, selecione **Adicionar**.
5. Fornecer um **Nome** para o atributo personalizado (por exemplo, "ShoeSize")
6. Escolha um **tipo de dados.** Apenas **String,** **Boolean** e **Int** estão disponíveis.
7. Opcionalmente, introduza uma **Descrição** para fins informativos.
8. Clique em **Criar**.

O atributo personalizado está agora disponível na lista de **atributos** do Utilizador e para utilização nos fluxos do seu utilizador. Um atributo personalizado só é criado na primeira vez que é utilizado em qualquer fluxo de utilizador, e não quando o adiciona à lista de **atributos** do Utilizador .

## <a name="use-a-custom-attribute-in-your-user-flow"></a>Use um atributo personalizado no fluxo do seu utilizador

1. No seu inquilino Azure AD B2C, selecione **fluxos de utilizador**.
1. Selecione a sua política (por exemplo, "B2C_1_SignupSignin") para abri-la.
1. Selecione **os atributos do Utilizador** e, em seguida, selecione o atributo personalizado (por exemplo, "ShoeSize"). Clique em **Guardar**.
1. Selecione **as reclamações de aplicação** e, em seguida, selecione o atributo personalizado.
1. Clique em **Guardar**.

Uma vez criado um novo utilizador utilizando um fluxo de utilizador que utiliza o atributo personalizado recém-criado, o objeto pode ser consultado no [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer). Em alternativa, pode utilizar a função [de fluxo do utilizador Run](./tutorial-create-user-flows.md) no fluxo do utilizador para verificar a experiência do cliente. Deverá agora ver **o ShoeSize** na lista de atributos recolhidos durante a jornada de inscrição e vê-lo no token enviado de volta para a sua aplicação.