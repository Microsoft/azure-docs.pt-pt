---
title: Tutorial - criar um inquilino do Azure Active Directory B2C | Documentos da Microsoft
description: Aprenda a preparar para registar as suas aplicações através da criação de um inquilino do Azure Active Directory B2C no portal do Azure.
services: B2C
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 5f406242197c15348cfd557fb9c960ad7d4bed19
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2019
ms.locfileid: "54846851"
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
2. Certifique-se de que está a utilizar o diretório que contém a sua subscrição ao clicar o **filtro de diretório e subscrição** no menu superior e escolher o diretório que o contém. Este diretório é diferente daquela que irá conter o seu inquilino do Azure AD B2C.

    ![Mude para o diretório da subscrição](./media/tutorial-create-tenant/switch-directory-subscription.png)

3. Escolher **criar um recurso** no canto superior esquerdo do portal do Azure.
4. Procure e selecione **Active Directory B2C**e, em seguida, clique em **criar**.
5. Escolher **criar um novo inquilino do Azure AD B2C**, insira um nome de organização e o nome de domínio inicial, o que é utilizado no nome do inquilino, selecione o país (não é possível alterar mais tarde) e, em seguida, clique em **criar**.

    ![Criar um inquilino](./media/tutorial-create-tenant/create-tenant.png)

    Neste exemplo, o nome do inquilino é contoso0926Tenant.onmicrosoft.com

6. Sobre o **criar novo inquilino de B2C ou uma ligação a um inquilino existente** página, selecione **inquilino de ligação do Azure AD B2C existente, a minha subscrição do Azure**, selecione o inquilino que criou, selecione a sua subscrição e, em seguida, Clique em **criar novo**.
7. Introduza um nome para o grupo de recursos que irá conter o inquilino, selecione a localização e, em seguida, clique em **criar**.
8. Para começar a utilizar o novo inquilino, certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que o contém.

    ![Mude para o diretório de inquilinos](./media/tutorial-create-tenant/switch-directories.png)

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu como:

> [!div class="checklist"]
> * Criar um inquilino do Azure AD B2C
> * Ligar o seu inquilino para a sua subscrição

> [!div class="nextstepaction"]
> [Registar as suas aplicações](tutorial-register-applications.md)