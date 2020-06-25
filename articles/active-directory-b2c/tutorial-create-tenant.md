---
title: Tutorial - Criar um inquilino Azure Ative Directory B2C
description: Saiba como se preparar para registar as suas aplicações criando um inquilino Azure Ative Directory B2C utilizando o portal Azure.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 807cc4647a27cf62109885ba1adaeaf5058a20c4
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85355425"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Tutorial: Criar um inquilino do Azure Active Directory B2C

Antes de as suas candidaturas poderem interagir com o Azure Ative Directory B2C (Azure AD B2C), devem estar registadas num inquilino que gere.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar um inquilino do Azure AD B2C
> * Ligue o seu inquilino à sua subscrição
> * Mude para o diretório que contém o seu inquilino Azure AD B2C
> * Adicione o recurso Azure AD B2C como **favorito** no portal Azure

Aprende-se a registar uma candidatura no próximo tutorial.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-azure-ad-b2c-tenant"></a>Criar um inquilino do Azure AD B2C

1. Inicie sessão no [portal do Azure](https://portal.azure.com/). Inscreva-se com uma conta Azure que foi atribuída pelo menos a função [de Contribuinte](../role-based-access-control/built-in-roles.md) dentro da subscrição ou um grupo de recursos dentro da subscrição.

1. Selecione o diretório que contém a sua subscrição.

    Na barra de ferramentas do portal Azure, selecione o ícone **Diretório + Subscrição** e, em seguida, selecione o diretório que contém a sua subscrição. Este diretório é diferente daquele que vai conter o seu inquilino Azure AD B2C.

    ![Inquilino de assinatura, Diretório + Filtro de subscrição com inquilino de assinatura selecionado](media/tutorial-create-tenant/portal-01-pick-directory.png)

1. No menu do portal do Azure ou a partir da **Home Page**, selecione **Criar um recurso**.
1. Procure por **Azure Ative Directory B2C**e, em seguida, selecione **Create**.
1. Selecione **Criar um novo inquilino do Azure AD B2C**.

    ![Criar um novo inquilino Azure AD B2C selecionado no portal Azure](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. Na página **Criar um diretório,** insira o seguinte:

   - **Nome da organização** - Insira o nome da sua organização.
   - **Nome de domínio inicial** - Introduza um nome de domínio. Por predefinição, este nome é anexado a *.onmicrosoft.com*. Pode alterá-lo mais tarde adicionando um nome de domínio que a sua organização já utiliza, como "contoso.com".
   - **País ou região** - Selecione o seu país ou região da lista. Esta seleção não pode ser mudada mais tarde.
   - **Subscrição** - Selecione a sua subscrição da lista.
   - **Grupo de recursos** - Selecione um grupo de recursos que contenha o inquilino. Ou **selecione Criar novo,** insira um **Nome** para o grupo de recursos, selecione a **localização**do grupo de Recursos e, em seguida, selecione **OK**.

    ![Crie forma de inquilino com valores de exemplo no portal Azure](media/tutorial-create-tenant/review-and-create-tenant.png)

1. Selecione **Rever + criar**.
1. Reveja as definições do diretório. Em seguida, selecione **Criar**.

Você pode ligar vários inquilinos Azure AD B2C a uma única subscrição Azure para efeitos de faturação. Para ligar um inquilino, você deve ser um administrador no inquilino Azure AD B2C e ser atribuído pelo menos um papel de Contribuinte dentro da subscrição Azure. Consulte [link um inquilino Azure AD B2C para uma subscrição](billing.md#link-an-azure-ad-b2c-tenant-to-a-subscription).

## <a name="select-your-b2c-tenant-directory"></a>Selecione o seu diretório de inquilinos B2C

Para começar a usar o seu novo inquilino Azure AD B2C, você precisa mudar para o diretório que contém o inquilino.

Selecione o filtro **de subscrição Diretório +** no menu superior do portal Azure e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.

Se no início não vir o seu novo inquilino Azure B2C na lista, refresque a janela do seu navegador e, em seguida, selecione novamente o filtro **de subscrição Diretório +** no menu superior.

![Diretório contendo inquilino b2C selecionado no portal Azure](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>Adicione Azure AD B2C como favorito (opcional)

Este passo opcional facilita a seleção do seu inquilino Azure AD B2C nos seguintes e em todos os tutoriais subsequentes.

Em vez de procurar *o Azure AD B2C* em **todos os serviços** sempre que quiser trabalhar com o seu inquilino, pode, em vez disso, favorito o recurso. Em seguida, pode selecioná-lo na secção **Favoritos** do menu do portal para navegar rapidamente até ao seu inquilino Azure AD B2C.

Só precisas de fazer esta operação uma vez. Antes de realizar estes passos, certifique-se de que mudou para o diretório contendo o seu inquilino Azure AD B2C, conforme descrito na secção anterior, [Selecione o seu diretório de inquilinos B2C](#select-your-b2c-tenant-directory).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No menu do portal Azure, selecione **Todos os serviços**.
1. Na caixa de pesquisa de **todos os serviços,** procure **Azure AD B2C,** sobressaure sobre o resultado da pesquisa e, em seguida, selecione o ícone estrela na ponta da ferramenta. **Azure AD B2C** aparece agora no portal Azure sob **os Favoritos**.
1. Se quiser alterar a posição do seu novo favorito, vá ao menu do portal Azure, selecione **Azure AD B2C**e, em seguida, arraste-o para cima ou para baixo para a posição desejada.

    ![Azure AD B2C, menu favorito, portal Microsoft Azure](media/tutorial-create-tenant/portal-08-b2c-favorite.png)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a:

> [!div class="checklist"]
> * Criar um inquilino do Azure AD B2C
> * Ligue o seu inquilino à sua subscrição
> * Mude para o diretório que contém o seu inquilino Azure AD B2C
> * Adicione o recurso Azure AD B2C como **favorito** no portal Azure

Em seguida, aprenda a registar uma aplicação web no seu novo inquilino.

> [!div class="nextstepaction"]
> [Registe as suas candidaturas >](tutorial-register-applications.md)
