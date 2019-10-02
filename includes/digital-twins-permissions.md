---
title: incluir ficheiro
description: incluir ficheiro
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: include
ms.date: 09/30/2019
ms.author: dkshir
ms.custom: include file
ms.openlocfilehash: 6e808fb64d004f117a3c25946ad97e26ddfadaa3
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802380"
---
>[!NOTE]
>Esta seção fornece instruções para o [novo registro de aplicativo do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app). Se você ainda tiver um registro de aplicativo nativo herdado, poderá usá-lo desde que ele tenha suporte. Além disso, se, por algum motivo, a nova maneira de novo registro de aplicativo não estiver funcionando na sua configuração, você poderá tentar criar um aplicativo AAD nativo herdado. Leia [registrar seu aplicativo gêmeos digital do Azure com Azure Active Directory herdado](../articles/digital-twins/how-to-use-legacy-aad.md) para obter mais instruções. 

1. No [portal do Azure](https://portal.azure.com), abra **Azure Active Directory** no painel esquerdo e, em seguida, abra o painel **registros de aplicativo** . Selecione o botão **novo registro** .

    [![Aplicativo registrado](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Dê um nome amigável para esse registro de aplicativo na caixa **nome** . Na seção **URI de redirecionamento (opcional)** , escolha **cliente público (Mobile & Desktop)** na lista suspensa à esquerda e digite `https://microsoft.com` na caixa de texto à direita. Selecione **Registar**.

    [![Criar painel](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. Para certificar-se [de que o aplicativo está registrado como um *aplicativo nativo*](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration), abra o painel de **autenticação** para o registro do aplicativo e role para baixo nesse painel. Na seção **tipo de cliente padrão** , escolha **Sim** para **tratar aplicativo como um cliente público**. 

    [![Padrão nativo](./media/digital-twins-permissions/aad-app-default-native.png)](./media/digital-twins-permissions/aad-app-default-native.png#lightbox)

1.  Abra o painel **visão geral** do seu aplicativo registrado e copie os valores das entidades a seguir para um arquivo temporário. Você usará esses valores para configurar o aplicativo de exemplo nas seções a seguir.

    - **ID do aplicativo (cliente)**
    - **ID do diretório (locatário)**

    [![ID do aplicativo Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. Abra o painel **permissões de API** para o registro do aplicativo. Selecione **Adicionar um botão de permissão** . No painel **solicitar permissões de API** , selecione a guia **APIs que minha organização usa** e, em seguida, pesquise por:
    
    1. `Azure Digital Twins`. Selecione a API de **gêmeos digital do Azure** .

        [![API de pesquisa ou gêmeos digital do Azure](./media/digital-twins-permissions/aad-aap-search-api-dt.png)](./media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. Como alternativa, pesquise `Azure Smart Spaces Service`. Selecione a API de **serviço de espaços inteligentes do Azure** .

        [![Pesquisar a API para espaços inteligentes do Azure](./media/digital-twins-permissions/aad-app-search-api.png)](./media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > O nome e a ID da API do Azure AD que serão exibidos dependerá do seu locatário:
    > * As contas de locatário e cliente de teste devem Pesquisar `Azure Digital Twins`.
    > * Outras contas da Microsoft devem Pesquisar `Azure Smart Spaces Service`.

1. A API selecionada aparece como **Azure digital gêmeos** no mesmo painel de **permissões de API de solicitação** . Selecione a lista suspensa **ler (1)** e, em seguida, marque a caixa de seleção **ler. gravar** . Selecione o botão **adicionar permissões** .

    [![Adicionar permissões de APIs](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. Dependendo das configurações da sua organização, talvez seja necessário executar etapas adicionais para conceder acesso de administrador a essa API. Contate o administrador para obter mais informações. Depois que o acesso de administrador for aprovado, a coluna **consentimento de administrador necessário** no painel **permissões de API** mostrará semelhante ao seguinte para suas APIs:

    [![Adicionar permissões de APIs](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

