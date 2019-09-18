---
title: Definir atributos personalizados no Azure Active Directory B2C | Microsoft Docs
description: Defina atributos personalizados para seu aplicativo no Azure Active Directory B2C para coletar informações sobre seus clientes.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e6b0eac0b8cf7f61d76f90a4f769ba11abab6999
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065678"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Definir atributos personalizados no Azure Active Directory B2C

 Cada aplicativo voltado para o cliente tem requisitos exclusivos para as informações que precisam ser coletadas. Seu locatário de Azure Active Directory B2C (Azure AD B2C) vem com um conjunto interno de informações armazenadas em atributos, como nome, sobrenome, cidade e CEP fornecidos. Com Azure AD B2C, você pode estender o conjunto de atributos armazenados em cada conta de cliente.

 Você pode criar atributos personalizados no [portal do Azure](https://portal.azure.com/) e usá-los em seus fluxos de usuário de inscrição, entrada ou entrada de fluxos de usuário ou fluxos de usuário de edição de perfil. Você também pode ler e gravar esses atributos usando o [API do Graph do Azure ad](active-directory-b2c-devquickstarts-graph-dotnet.md). Os atributos personalizados no Azure AD B2C usam [extensões de esquema de diretório do Azure AD API do Graph](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions).

> [!NOTE]
> O suporte para [API de Microsoft Graph](https://docs.microsoft.com/graph/overview?view=graph-rest-1.0) mais recente para consultar Azure ad B2C locatário ainda está em desenvolvimento.
>

## <a name="create-a-custom-attribute"></a>Criar um atributo personalizado

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, ao trocá-lo no canto superior direito do portal do Azure. Selecione as suas informações de subscrição e, em seguida, selecione **Trocar Diretório**.

    ![Mudar para o inquilino do Azure AD B2C](./media/active-directory-b2c-reference-custom-attr/switch-directories.png)

    Escolha o diretório que contém o seu inquilino.

    ![Locatário do B2C realçado no filtro de diretório e assinatura](./media/active-directory-b2c-reference-custom-attr/select-directory.PNG)

3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Selecione **atributos de usuário**e, em seguida, selecione **Adicionar**.
5. Forneça um **nome** para o atributo personalizado (por exemplo, "calçado")
6. Escolha um **tipo de dados**. Somente **String**, **Boolean**e **int** estão disponíveis.
7. Opcionalmente, insira uma **Descrição** para fins informativos.
8. Clique em **Criar**.

O atributo personalizado agora está disponível na lista de **atributos de usuário** e para uso em seus fluxos de usuário. Um atributo personalizado só é criado na primeira vez em que é usado em qualquer fluxo de usuário, e não quando você o adiciona à lista de **atributos de usuário**.


## <a name="use-a-custom-attribute-in-your-user-flow"></a>Usar um atributo personalizado em seu fluxo de usuário

1. No locatário do Azure AD B2C, selecione **fluxos de usuário**.
2. Selecione sua política (por exemplo, "B2C_1_SignupSignin") para abri-la.
4. Selecione **atributos de usuário** e, em seguida, selecione o atributo personalizado (por exemplo, "sapato"). Clique em **Guardar**.
5. Selecione **declarações do aplicativo** e, em seguida, selecione o atributo personalizado.
6. Clique em **Guardar**.

Depois de criar um novo usuário usando um fluxo de usuário que usa o atributo personalizado recém-criado, o objeto pode ser consultado no [Explorador do Azure ad Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart). Como alternativa, você pode usar o recurso [**executar fluxo de usuário**](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows) no fluxo do usuário para verificar a experiência do cliente. Agora você deve ver **calçados** na lista de atributos coletados durante a jornada de inscrição e vê-los no token enviado de volta para seu aplicativo.

