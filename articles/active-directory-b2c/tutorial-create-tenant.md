---
title: Tutorial - criar um inquilino do Azure Active Directory B2C
description: Aprenda a preparar para registar as suas aplicações através da criação de um inquilino do Azure Active Directory B2C no portal do Azure.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 711b9152f9f3fa1b3573e39d1950f18b628c268a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056315"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Tutorial: Criar um inquilino do Azure Active Directory B2C

Antes de seus aplicativos podem interagir com o Azure Active Directory (Azure AD) B2C, tem de estar registados num inquilino que gere.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar um inquilino do Azure AD B2C
> * Ligar o seu inquilino para a sua subscrição

Saiba como registar uma aplicação no tutorial seguinte.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-azure-ad-b2c-tenant"></a>Criar um inquilino do Azure AD B2C

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Certifique-se de que está a utilizar o diretório que contém a sua subscrição. Clique nas **filtro de diretório e subscrição** no menu superior, em seguida, selecione o diretório que contém a sua subscrição. Este diretório é diferente do que irá conter o seu inquilino do Azure AD B2C.

    ![Mude para o diretório da subscrição](./media/tutorial-create-tenant/switch-directory-subscription.PNG)

3. Escolher **criar um recurso** no canto superior esquerdo do portal do Azure.
4. Procure e selecione **Active Directory B2C**e, em seguida, clique em **criar**.
5. Escolher **criar um novo inquilino do Azure AD B2C** e introduza um nome de organização e o nome de domínio inicial. Selecione o país/região (não é possível alterar mais tarde) e, em seguida, clique em **criar**.

    O nome de domínio inicial é utilizado como parte do nome do seu inquilino. Neste exemplo, é o nome do inquilino *contoso0926Tenant.onmicrosoft.com*:

    ![Criar um inquilino](./media/tutorial-create-tenant/create-tenant.PNG)

6. Sobre o **criar novo inquilino de B2C ou uma ligação ao inquilino existente** página, selecione **inquilino de ligação do Azure AD B2C existente, a minha subscrição do Azure**.

    Selecione o inquilino que criou e selecione a sua subscrição.

    Grupo de recursos, selecione **criar novo**. Introduza um nome para o grupo de recursos que irá conter o inquilino, selecione a localização e, em seguida, clique em **criar**.
1. Para começar a utilizar o novo inquilino, certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que o contém.

    Se não vir o seu novo inquilino de B2C do Azure na lista em primeiro lugar, atualize a janela do browser, em seguida, selecione o **filtro de diretório e subscrição** novamente no menu superior.

    ![Mude para o diretório de inquilinos](./media/tutorial-create-tenant/switch-directories.PNG)

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu como:

> [!div class="checklist"]
> * Criar um inquilino do Azure AD B2C
> * Ligar o seu inquilino para a sua subscrição

Em seguida, saiba como registar uma aplicação web no seu inquilino novo.

> [!div class="nextstepaction"]
> [Registar as suas aplicações >](tutorial-register-applications.md)
