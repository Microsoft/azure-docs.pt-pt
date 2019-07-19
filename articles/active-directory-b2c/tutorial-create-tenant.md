---
title: Tutorial – criar um locatário Azure Active Directory B2C
description: Saiba como se preparar para registrar seus aplicativos criando um locatário Azure Active Directory B2C usando o portal do Azure.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ce389d1f434fb0eb37413873b02e3ddfff8f7fba
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849402"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Tutorial: Criar um locatário Azure Active Directory B2C

Antes que seus aplicativos possam interagir com Azure Active Directory (Azure AD) B2C, eles devem ser registrados em um locatário que você gerencia.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar um inquilino do Azure AD B2C
> * Vincular seu locatário à sua assinatura

Você aprende a registrar um aplicativo no próximo tutorial.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-azure-ad-b2c-tenant"></a>Criar um inquilino do Azure AD B2C

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Verifique se você está usando o diretório que contém sua assinatura. Clique no **filtro diretório e assinatura** no menu superior e selecione o diretório que contém sua assinatura. Esse diretório é diferente daquele que conterá seu locatário Azure AD B2C.

    ![Filtro de diretório e assinatura com o locatário de assinatura selecionado](./media/tutorial-create-tenant/switch-directory-subscription.PNG)

3. Escolha **criar um recurso** no canto superior esquerdo do portal do Azure.
4. Procure e selecione **Active Directory B2C**e, em seguida, clique em **criar**.
5. Escolha **criar um novo locatário Azure ad B2C** e insira um nome de organização e um nome de domínio inicial. Selecione o país/região (ele não pode ser alterado mais tarde) e, em seguida, clique em **criar**.

    O nome de domínio inicial é usado como parte do nome do locatário. Neste exemplo, o nome do locatário é *contoso0926Tenant.onmicrosoft.com*:

    ![Página de criação de locatário B2C no portal do Azure](./media/tutorial-create-tenant/create-tenant.PNG)

6. Na página **criar novo locatário do B2C ou vincular ao locatário existente** , escolha **vincular um locatário do Azure ad B2C existente à minha assinatura do Azure**.

    Selecione o locatário que você criou e selecione sua assinatura.

    Para grupo de recursos, selecione **criar novo**. Insira um nome para o grupo de recursos que conterá o locatário, selecione o local e, em seguida, clique em **criar**.
1. Para começar a usar seu novo locatário, verifique se você está usando o diretório que contém seu locatário Azure AD B2C clicando no **filtro diretório e assinatura** no menu superior e escolhendo o diretório que o contém.

    Se, a princípio, você não vir seu novo locatário do Azure B2C na lista, atualize a janela do navegador e, em seguida, selecione o **filtro diretório e assinatura** novamente no menu superior.

    ![Filtro de diretório e assinatura com locatário B2C selecionado](./media/tutorial-create-tenant/switch-directories.PNG)

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, você aprendeu a:

> [!div class="checklist"]
> * Criar um inquilino do Azure AD B2C
> * Vincular seu locatário à sua assinatura

Em seguida, saiba como registrar um aplicativo Web em seu novo locatário.

> [!div class="nextstepaction"]
> [Registre seus aplicativos >](tutorial-register-applications.md)
