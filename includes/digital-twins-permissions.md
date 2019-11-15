---
title: incluir ficheiro
description: incluir ficheiro
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2019
ms.custom: include file
ms.openlocfilehash: 832c0e6080b82f3c38beaf051669fbdacd37081c
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74101358"
---
>[!NOTE]
>Esta seção fornece instruções para o [registro de aplicativo do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

1. No [portal do Azure](https://portal.azure.com), abra **Azure Active Directory** no menu expansível à esquerda e, em seguida, abra o painel **registros de aplicativo** . 

    [![selecionar o painel de Azure Active Directory](./media/digital-twins-permissions/select-aad-pane.png)](./media/digital-twins-permissions/select-aad-pane.png#lightbox)

1. Selecione o botão **+ novo registro** .

    [![selecionar o novo botão de registro](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Dê um nome amigável para esse registro de aplicativo na caixa **nome** . Na seção **URI de redirecionamento (opcional)** , escolha **cliente público/nativo (Mobile & Desktop)** no menu suspenso à esquerda e digite `https://microsoft.com` na caixa de texto à direita. Selecione **Registar**.

    [![criar painel](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. Para certificar-se [de que o aplicativo está registrado como um **cliente público**](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration), abra o painel de **autenticação** para o registro do aplicativo e role para baixo nesse painel. Na seção **tipo de cliente padrão** , escolha **Sim** para **tratar aplicativo como um cliente público**e clique em **salvar**.

    Verifique os **tokens de acesso** para habilitar a configuração **Oauth2AllowImplicitFlow** em seu manifest. JSON.

    [![definição de configuração de cliente público](./media/digital-twins-permissions/aad-public-client.png)](./media/digital-twins-permissions/aad-public-client.png#lightbox)

1.  Abra o painel **visão geral** do seu aplicativo registrado e copie os valores das entidades a seguir para um arquivo temporário. Você usará esses valores para configurar o aplicativo de exemplo nas seções a seguir.

    - **ID do aplicativo (cliente)**
    - **ID do diretório (locatário)**

    [ID do aplicativo de ![Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. Abra o painel **permissões de API** para o registro do aplicativo. Selecione **+ Adicionar um botão de permissão** . No painel **solicitar permissões de API** , selecione a guia **APIs que minha organização usa** e, em seguida, pesquise um dos seguintes:
    
    1. `Azure Digital Twins`. Selecione a API de **gêmeos digital do Azure** .

        [API de pesquisa ![ou gêmeos digital do Azure](./media/digital-twins-permissions/aad-aap-search-api-dt.png)](./media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. Como alternativa, pesquise por `Azure Smart Spaces Service`. Selecione a API de **serviço de espaços inteligentes do Azure** .

        [API de pesquisa do ![para espaços inteligentes do Azure](./media/digital-twins-permissions/aad-app-search-api.png)](./media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > O nome e a ID da API do Azure AD que serão exibidos dependerá do seu locatário:
    > * As contas de locatário e cliente de teste devem Pesquisar `Azure Digital Twins`.
    > * Outras contas da Microsoft devem Pesquisar `Azure Smart Spaces Service`.

1. A API, uma vez selecionada, aparece como **gêmeos digital do Azure** no mesmo painel de **permissões de API de solicitação** . Selecione a opção lista suspensa **ler** e, em seguida, marque a caixa de seleção **ler. gravar** . Selecione o botão **adicionar permissões** .

    [![Adicionar permissões de APIs](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. Dependendo das configurações da sua organização, talvez seja necessário executar etapas adicionais para conceder acesso de administrador a essa API. Contate o administrador para obter mais informações. Depois que o acesso de administrador for aprovado, a coluna **consentimento de administrador necessário** no painel **permissões de API** exibirá suas permissões. 

    [aprovação de consentimento do administrador ![](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

    Verifique se o **Azure digital gêmeos** é exibido.
