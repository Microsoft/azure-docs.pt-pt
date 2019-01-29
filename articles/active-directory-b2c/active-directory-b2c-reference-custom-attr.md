---
title: Definir atributos personalizados no Azure Active Directory B2C | Documentos da Microsoft
description: Defina atributos personalizados para a sua aplicação no Azure Active Directory B2C para recolher informações sobre os seus clientes.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 53d9aec5a689babb637d2eff6b36ea3b8bd8d97f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55173943"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Definir atributos personalizados no Azure Active Directory B2C

 Todos os aplicativos do lado do cliente tem requisitos exclusivos para as informações de que tem de ser recolhidos. O inquilino B2C do Azure Active Directory (Azure AD) é fornecido com um conjunto interno de informações armazenadas em atributos, como o nome próprio, apelido, cidade e Código Postal. Com o Azure AD B2C, pode estender o conjunto de atributos armazenados em cada conta de cliente. 
 
 Pode criar atributos personalizados no [portal do Azure](https://portal.azure.com/) e usá-los em seus fluxos de utilizador de inscrição, fluxos de utilizador de inscrição ou início de sessão ou fluxos de utilizador de edição de perfil. Também pode ler e gravar esses atributos utilizando o [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md). Utilizam atributos personalizados no Azure AD B2C [do Azure AD Graph API Extensões de esquema do](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions).

## <a name="create-a-custom-attribute"></a>Criar um atributo personalizado

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, ao trocá-lo no canto superior direito do portal do Azure. Selecione as suas informações de subscrição e, em seguida, selecione **Trocar Diretório**. 

    ![Mudar para o inquilino do Azure AD B2C](./media/active-directory-b2c-reference-custom-attr/switch-directories.png)

    Escolha o diretório que contém o seu inquilino.

    ![Selecionar o diretório](./media/active-directory-b2c-reference-custom-attr/select-directory.png)

3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Selecione **atributos de utilizador**e, em seguida, selecione **Add**.
5. Fornecer um **nome** para o atributo personalizado (por exemplo, "ShoeSize")
6. Escolher uma **tipo de dados**. Apenas **cadeia de caracteres**, **booleano**, e **Int** estão disponíveis.
7. Opcionalmente, introduza um **Descrição** para fins informativos. 
8. Clique em **Criar**.

O atributo personalizado está agora disponível na lista de **atributos de utilizador** para uso em seus fluxos de utilizador. Um atributo personalizado é criado na primeira vez que é utilizado em qualquer fluxo de utilizador, e somente não quando o adicionar à lista de **atributos de utilizador**.

## <a name="use-a-custom-attribute-in-your-user-flow"></a>Utilizar um atributo personalizado do fluxo de utilizador

1. No seu inquilino do Azure AD B2C, selecione **fluxos de utilizador**.
2. Selecione a política (por exemplo, "B2C_1_SignupSignin") para abri-lo. 
4. Selecione **atributos de utilizador** e, em seguida, selecione o atributo personalizado (por exemplo, "ShoeSize"). Clique em **Guardar**.
5. Selecione **afirmações de aplicação** e, em seguida, selecione o vlastní atribut. 
6. Clique em **Guardar**.

Pode utilizar o **executar o fluxo de utilizador** funcionalidade no fluxo de utilizador para verificar a experiência do cliente. Deverá ver agora **ShoeSize** na lista de atributos recolhidos durante a viagem de inscrição e vê-lo no token enviado para a sua aplicação.

