---
title: Tutorial – criar um locatário Azure Active Directory B2C
description: Saiba como se preparar para registrar seus aplicativos criando um locatário Azure Active Directory B2C usando o portal do Azure.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: de29929c8fda476fe276f91d4a68ce4d632503fd
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71345222"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Tutorial: Criar um locatário Azure Active Directory B2C

Antes que seus aplicativos possam interagir com Azure Active Directory B2C (Azure AD B2C), eles devem ser registrados em um locatário que você gerencia.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar um inquilino do Azure AD B2C
> * Vincular seu locatário à sua assinatura
> * Alternar para o diretório que contém seu locatário de Azure AD B2C
> * Adicione o recurso de Azure AD B2C como um *favorito* no portal do Azure

Você aprende a registrar um aplicativo no próximo tutorial.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-azure-ad-b2c-tenant"></a>Criar um inquilino do Azure AD B2C

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Verifique se você está usando o diretório que contém sua assinatura.

    Selecione o **diretório +** filtro de assinatura no menu superior e, em seguida, selecione o diretório que contém sua assinatura. Esse diretório é diferente daquele que conterá seu locatário Azure AD B2C.

    ![Diretório + filtro de assinatura com o locatário de assinatura selecionado](media/tutorial-create-tenant/portal-01-select-directory.png)

1. Selecione **criar um recurso** no canto superior esquerdo do portal do Azure.
1. Procure e selecione **Active Directory B2C**e, em seguida, selecione **criar**.
1. Selecione **Criar um novo inquilino do Azure AD B2C**.

    ![Criar um novo locatário de Azure AD B2C selecionado em portal do Azure](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. Insira um **nome de organização** e um **nome de domínio inicial**. Selecione o **país ou região** (ele não pode ser alterado posteriormente) e, em seguida, selecione **criar**.

    O nome de domínio é usado como parte do nome de domínio completo do locatário. Neste exemplo, o nome do locatário é *contosob2c.onmicrosoft.com*:

    ![Criar formulário de locatário no com valores de exemplo no portal do Azure](media/tutorial-create-tenant/portal-03-tenant-naming.png)

1. Quando a criação do locatário for concluída, selecione o link **criar novo locatário B2C ou vincular ao locatário existente** na parte superior da página de criação do locatário.

    ![Link de navegação estrutural do locatário realçado em portal do Azure](media/tutorial-create-tenant/portal-04-select-link-sub-link.png)

1. Selecione **vincular um locatário existente do Azure ad B2C à minha assinatura do Azure**.

   ![Vincular uma seleção de assinatura existente no portal do Azure](media/tutorial-create-tenant/portal-05-link-subscription.png)

1. Selecione o **locatário de Azure ad B2C** que você criou e, em seguida, selecione sua **assinatura**.

    Em **Grupo de recursos**, selecione **Criar novo**. Insira um **nome** para o grupo de recursos que conterá o locatário, selecione o **local do grupo de recursos**e, em seguida, selecione **criar**.

    ![Formulário vincular configurações de assinatura no portal do Azure](media/tutorial-create-tenant/portal-06-link-subscription-settings.png)

## <a name="select-your-b2c-tenant-directory"></a>Selecione seu diretório de locatário B2C

Para começar a usar seu novo Azure AD B2C locatário, você precisa alternar para o diretório que contém o locatário.

Selecione o **diretório +** filtro de assinatura no menu superior da portal do Azure, em seguida, selecione o diretório que contém o locatário Azure ad B2C.

Se, a princípio, você não vir seu novo locatário do Azure B2C na lista, atualize a janela do navegador e, em seguida, selecione o filtro **diretório + assinatura** novamente no menu superior.

![Locatário B2C-diretório contendo selecionado em portal do Azure](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>Adicionar Azure AD B2C como um favorito (opcional)

Essa etapa opcional facilita a seleção de seu locatário Azure AD B2C nos seguintes e em todos os tutoriais subsequentes.

Em vez de procurar por "Azure AD B2C" em **todos os serviços** toda vez que você quiser trabalhar com seu locatário, você pode, em vez disso, favorita o recurso. Em seguida, você pode selecioná-lo no menu de **favoritos** à esquerda para navegar rapidamente até seu locatário de Azure ad B2C.

Você só precisa executar essa operação uma vez. Antes de executar essas etapas, certifique-se de ter alternado para o diretório que contém o locatário do Azure AD B2C, conforme descrito na seção anterior, [Selecione seu diretório de locatário do B2C](#select-your-b2c-tenant-directory).

1. Selecione **todos os serviços** no menu à esquerda da [portal do Azure](https://portal.azure.com)
1. Inserir *Azure ad B2C* na caixa de texto de pesquisa
1. Selecione a **estrela** para adicionar Azure ad B2C aos seus favoritos
1. *Azure ad B2C* agora aparece no menu do lado esquerdo dos **favoritos** . Você pode selecionar e arrastá-lo para cima na lista, se desejar, como mostra a imagem a seguir:

![Etapas para adicionar Azure AD B2C como um favorito no portal do Azure](media/tutorial-create-tenant/portal-08-favorite-b2c.png)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a:

> [!div class="checklist"]
> * Criar um inquilino do Azure AD B2C
> * Vincular seu locatário à sua assinatura
> * Alternar para o diretório que contém seu locatário de Azure AD B2C
> * Adicione o recurso de Azure AD B2C como um *favorito* no portal do Azure

Em seguida, saiba como registrar um aplicativo Web em seu novo locatário.

> [!div class="nextstepaction"]
> [Registre seus aplicativos >](tutorial-register-applications.md)
