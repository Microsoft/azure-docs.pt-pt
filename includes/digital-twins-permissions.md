---
title: incluir ficheiro
description: incluir ficheiro
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/23/2020
ms.custom: include file
ms.openlocfilehash: a1576e4a97af5de0b936c662de636aae542a19b5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748976"
---
>[!NOTE]
>Esta seção fornece instruções para o [registro de aplicativo do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

1. No [portal do Azure](https://portal.azure.com), abra **Azure Active Directory** no menu expansível à esquerda e, em seguida, abra o painel **registros de aplicativo** . 

    [![Selecione o painel de diretório ativo Azure](./media/digital-twins-permissions/azure-portal-select-aad-pane.png)](./media/digital-twins-permissions/azure-portal-select-aad-pane.png#lightbox)

1. Selecione o botão **+ novo registro** .

    [![Selecione o novo botão de registo](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Dê um nome amigável para esse registro de aplicativo na caixa **nome** . Na seção **URI de redirecionamento (opcional)** , escolha **cliente público/nativo (Mobile & Desktop)** no menu suspenso à esquerda e digite `https://microsoft.com` na caixa de texto à direita. Selecione **Registar**.

    [![Criar o painel](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. Para certificar-se [de que o aplicativo está registrado como um **cliente público**](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration), abra o painel de **autenticação** para o registro do aplicativo e role para baixo nesse painel. Na seção **tipo de cliente padrão** , escolha **Sim** para **tratar aplicativo como um cliente público**e clique em **salvar**.

    1. **As URIs de redirecionamento** devem coincidir com o endereço fornecido pelo pedido de autenticação:

        * Para aplicações hospedadas num ambiente de desenvolvimento local, selecione **cliente Público (mobile & desktop)** . Certifique-se de definir o tipo de **cliente Padrão** para sim.
        * Para aplicações de página única hospedadas no Serviço de Aplicações Azure, selecione **Web**.

        Selecione **cliente público (mobile & desktop)** e introduza `http://localhost:8080/`.

        [![configurar URIs de redirecionamento](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png)](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png#lightbox)

    1. Verifique as **fichas** de acesso para configurar a definição **oauth2AllowImplicitFlow** para `true` no **Manifesto** JSON do seu recurso.

        [![configuração de configuração do cliente público](./media/digital-twins-permissions/aad-configure-public-client.png)](./media/digital-twins-permissions/aad-configure-public-client.png#lightbox)

1.  Abra o painel **visão geral** do seu aplicativo registrado e copie os valores das entidades a seguir para um arquivo temporário. Você usará esses valores para configurar o aplicativo de exemplo nas seções a seguir.

    - **ID do aplicativo (cliente)**
    - **ID do diretório (locatário)**

    [ID de aplicação de diretório ativo ![Azure](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. Abra o painel **permissões de API** para o registro do aplicativo. Selecione **+ Adicionar um botão de permissão** . No painel **solicitar permissões de API** , selecione a guia **APIs que minha organização usa** e, em seguida, pesquise um dos seguintes:
    
    1. `Azure Digital Twins`. Selecione a API de **gêmeos digital do Azure** .

        [![Pesquisa API ou Gémeos Digitais Azure](./media/digital-twins-permissions/aad-aap-search-api-dt.png)](./media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. Como alternativa, pesquise por `Azure Smart Spaces Service`. Selecione a API de **serviço de espaços inteligentes do Azure** .

        [![Pesquisa API para espaços inteligentes Azure](./media/digital-twins-permissions/aad-app-search-api.png)](./media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > O nome e a ID da API do Azure AD que serão exibidos dependerá do seu locatário:
    > * As contas de locatário e cliente de teste devem Pesquisar `Azure Digital Twins`.
    > * Outras contas da Microsoft devem Pesquisar `Azure Smart Spaces Service`.

1. A API será exibida como **Azure digital gêmeos** no mesmo painel de **permissões de API de solicitação** , uma vez selecionada. Selecione a opção lista suspensa **ler** e, em seguida, marque a caixa de seleção **ler. gravar** . Selecione o botão **adicionar permissões** .

    [![Adicionar permissões de APIs](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. Dependendo das configurações da sua organização, talvez seja necessário executar etapas adicionais para conceder acesso de administrador a essa API. Contate o administrador para obter mais informações. Depois que o acesso de administrador for aprovado, a coluna **consentimento de administrador necessário** no painel **permissões de API** exibirá suas permissões. 

    [aprovação de consentimento do Administrador ![](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

    Verifique se o **Azure digital gêmeos** é exibido.
