---
title: Tutorial - Criar um inquilino B2C do Diretório Ativo Azure
description: Saiba como se preparar para registar as suas candidaturas criando um inquilino Azure Ative Directory B2C utilizando o portal Azure.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c31f3c4c6688af7d2142180e8d9b7100965bad96
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78186408"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Tutorial: Criar um inquilino do Azure Active Directory B2C

Antes de as suas candidaturas poderem interagir com o Azure Ative Directory B2C (Azure AD B2C), devem estar registados num inquilino que gere.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar um inquilino do Azure AD B2C
> * Ligue o seu inquilino à sua subscrição
> * Mude para o diretório contendo o seu inquilino Azure AD B2C
> * Adicione o recurso Azure AD B2C como **favorito** no portal Azure

Aprende-se a registar uma candidatura no próximo tutorial.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-azure-ad-b2c-tenant"></a>Criar um inquilino do Azure AD B2C

1. Inicie sessão no [portal do Azure](https://portal.azure.com/). Inscreva-se numa conta Azure que tenha sido atribuída pelo menos a função [De Contribuinte](../role-based-access-control/built-in-roles.md) dentro da subscrição ou de um grupo de recursos dentro da subscrição.

1. Selecione o diretório que contém a sua subscrição.

    Na barra de ferramentas do portal Azure, selecione o ícone **de Subscrição Do Diretório +** e, em seguida, selecione o diretório que contém a sua subscrição. Este diretório é diferente daquele que conterá o seu inquilino Azure AD B2C.

    ![Inquilino de assinatura, Diretoria + Filtro de assinatura com inquilino de subscrição selecionado](media/tutorial-create-tenant/portal-01-pick-directory.png)

1. No menu do portal do Azure ou a partir da **Home Page**, selecione **Criar um recurso**.
1. Procure o **Diretório Ativo Azure B2C**e, em seguida, selecione **Criar**.
1. Selecione **Criar um novo inquilino do Azure AD B2C**.

    ![Criar um novo inquilino Azure AD B2C selecionado no portal Azure](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. Introduza um **nome de Organização** e nome de domínio **inicial**. Selecione o **País ou região** (não pode ser alterado mais tarde) e, em seguida, selecione **Criar**.

    O nome de domínio é usado como parte do seu nome completo de domínio de inquilino. Neste exemplo, o nome do inquilino é *contosob2c.onmicrosoft.com:*

    ![Criar formulário de inquilino com valores de exemplo no portal Azure](media/tutorial-create-tenant/portal-03-tenant-naming.png)

1. Uma vez concluída a criação do inquilino, selecione o **novo Inquilino B2C ou Link para** o elo de inquilino existente no topo da página de criação de inquilinos.

    ![Link link de migalhas de pão de inquilino em destaque no portal Azure](media/tutorial-create-tenant/portal-04-select-link-sub-link.png)

1. Selecione **Link um Inquilino B2C Azure existente para a minha assinatura Azure**.

   ![Ligue uma seleção de subscrição existente no portal Azure](media/tutorial-create-tenant/portal-05-link-subscription.png)

1. Selecione o **Inquilino B2C Azure AD** que criou e, em seguida, selecione a sua **Subscrição**.

    Em **Grupo de recursos**, selecione **Criar novo**. Introduza um **Nome** para o grupo de recursos que conterá o inquilino, selecione a localização do **grupo Recursos,** e, em seguida, selecione **Criar**.

    ![Formulário de definições de subscrição de link no portal Azure](media/tutorial-create-tenant/portal-06-link-subscription-settings.png)

    Você pode ligar vários inquilinos Azure AD B2C a uma única subscrição Azure para efeitos de faturação.

## <a name="select-your-b2c-tenant-directory"></a>Selecione o seu diretório de inquilinos B2C

Para começar a usar o seu novo inquilino Azure AD B2C, você precisa mudar para o diretório que contém o inquilino.

Selecione o filtro de **assinatura Diretório +** no menu superior do portal Azure e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.

Se no início não vir o seu novo inquilino Azure B2C na lista, refresque a janela do seu navegador e, em seguida, selecione novamente o filtro de **subscrição Do Diretório +** no menu superior.

![B2C diretório contendo inquilino selecionado no portal Azure](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>Adicione Azure AD B2C como favorito (opcional)

Este passo opcional facilita a escolha do seu inquilino Azure AD B2C nos seguintes tutoriais e todos os tutoriais subsequentes.

Em vez de procurar o *Azure AD B2C* em **todos os serviços** sempre que quiser trabalhar com o seu inquilino, pode, em vez disso, fazer o favorito do recurso. Em seguida, pode selecioná-lo a partir da secção **favorita** do menu portal para navegar rapidamente até o seu inquilino Azure AD B2C.

Só precisas de fazer esta operação uma vez. Antes de realizar estes passos, certifique-se de que mudou para o diretório que contém o seu inquilino Azure AD B2C, conforme descrito na secção anterior, [Selecione o seu diretório de inquilinoB2C](#select-your-b2c-tenant-directory).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No menu do portal Azure, selecione **Todos os serviços.**
1. Na caixa de pesquisa de **todos os serviços,** procure **Azure AD B2C,** paire sobre o resultado da pesquisa e, em seguida, selecione o ícone da estrela na dica de ferramenta. **Azure AD B2C** aparece agora no portal Azure sob **os Favoritos.**
1. Se quiser alterar a posição do seu novo favorito, vá ao menu do portal Azure, **selecione Azure AD B2C**e, em seguida, arraste-o para cima ou para baixo para a posição desejada.

    ![Azure AD B2C, menu favorito, portal Microsoft Azure](media/tutorial-create-tenant/portal-08-b2c-favorite.png)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a:

> [!div class="checklist"]
> * Criar um inquilino do Azure AD B2C
> * Ligue o seu inquilino à sua subscrição
> * Mude para o diretório contendo o seu inquilino Azure AD B2C
> * Adicione o recurso Azure AD B2C como **favorito** no portal Azure

Em seguida, aprenda a registar uma aplicação web no seu novo inquilino.

> [!div class="nextstepaction"]
> [Registe as suas candidaturas >](tutorial-register-applications.md)
