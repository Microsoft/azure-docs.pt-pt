---
title: incluir ficheiro
description: incluir ficheiro
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 02/03/2020
ms.custom: include file
ms.openlocfilehash: cfe3eb4c0ac1378b7c519b3b34094945612d8508
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77029078"
---
>[!NOTE]
>Esta secção fornece instruções para o registo da [aplicação Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

1. No [portal Azure,](https://portal.azure.com)abra o **Azure Ative Directory** a partir do menu esquerdo expansível e, em seguida, abra o painel de inscrições da **App.** 

    [![Selecione o painel de diretório ativo Azure](./media/digital-twins-permissions/azure-portal-select-aad-pane.png)](./media/digital-twins-permissions/azure-portal-select-aad-pane.png#lightbox)

1. Selecione o botão **+ Novo de registo.**

    [![Selecione o novo botão de registo](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Dê um nome amigável para este registo de aplicação na caixa **Nome.** 

    1. Na secção **Redirect URI (opcional),** introduza `https://microsoft.com` na caixa de texto.     

    1. Verifique quais as contas e inquilinos que são apoiados pela sua aplicação Azure Ative Directory.

    1. Selecione **Registar**.

    [![Criar o painel](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. A lâmina **de autenticação** especifica definições importantes de configuração de autenticação. 

    1. Adicione **URIs redirecionamento** e configure **tokens** de acesso selecionando **+ Adicione uma plataforma**.

    1. Selecione **Sim** para especificar que a aplicação é um **cliente público.**

    1. Verifique quais as contas e inquilinos que são apoiados pela sua aplicação Azure Ative Directory.

    [![configuração de configuração do cliente público](./media/digital-twins-permissions/aad-configure-public-client.png)](./media/digital-twins-permissions/aad-configure-public-client.png#lightbox)

1. Depois de selecionar a plataforma apropriada, configure os seus **URIs de Redirecionamento** e **Tokens** de Acesso no painel lateral à direita da interface do utilizador.

    1. **As URIs de redirecionamento** devem coincidir com o endereço fornecido pelo pedido de autenticação:

        * Para aplicações hospedadas num ambiente de desenvolvimento local, selecione **cliente Público (mobile & desktop)** . Certifique-se de definir o **cliente público** para **Sim**.
        * Para aplicações de página única hospedadas no Serviço de Aplicações Azure, selecione **Web**.

    1. Determine se um URL de **logout** é apropriado.

    1. Ativar o fluxo implícito da subvenção verificando **fichas** de acesso ou **fichas de identificação**.
                
    [![configurar URIs de redirecionamento](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png)](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png#lightbox)

    Clique em **Configurar** **e,** em seguida, guardar .

1.  Abra o painel de **visão geral** da sua app registada e copie os valores das seguintes entidades para um ficheiro temporário. Você usará esses valores para configurar o aplicativo de exemplo nas seções a seguir.

    - **Id de aplicação (cliente)**
    - **ID do diretório (inquilino)**

    [ID de aplicação de diretório ativo ![Azure](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. Abra o painel de **permissões DaPI** para o registo da sua aplicação. Selecione + Adicione um botão **de permissão.** No painel de **permissões Request API,** selecione as **APIs** que a minha organização usa aba e, em seguida, procure por uma das seguintes:
    
    1. `Azure Digital Twins`. Selecione a API de **Gémeos Digitais Azure.**

        [![Pesquisa API ou Gémeos Digitais Azure](./media/digital-twins-permissions/aad-aap-search-api-dt.png)](./media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. Alternativamente, procure `Azure Smart Spaces Service`. Selecione a API **de Serviço de Espaços Inteligentes Azure.**

        [![Pesquisa API para espaços inteligentes Azure](./media/digital-twins-permissions/aad-app-search-api.png)](./media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > O nome e a ID da API do Azure AD que serão exibidos dependerá do seu locatário:
    > * O inquilino de teste e as contas dos clientes devem procurar `Azure Digital Twins`.
    > * Outras contas da Microsoft devem procurar por `Azure Smart Spaces Service`.

1. Ou a API aparecerá como **Azure Digital Twins** no mesmo painel de **permissões Request API** uma vez selecionado. Selecione a opção "Ler"drop-down" e, em seguida, selecione a caixa de verificação **Read.Write.** Selecione o botão **adicionar permissões.**

    [![Adicionar permissões de APIs](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. Dependendo das configurações da sua organização, talvez seja necessário executar etapas adicionais para conceder acesso de administrador a essa API. Contate o administrador para obter mais informações. Uma vez aprovado o acesso ao administrador, a coluna **Requerida** de Consentimento do Administrador no painel de **permissões DaPI** apresentará as suas permissões. 

    [aprovação de consentimento do Administrador ![](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

    Verifique se surgem **gémeos digitais Azure.**
