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
>Esta secção fornece instruções para o registo da [aplicação Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

1. No [portal Azure,](https://portal.azure.com)abra o **Azure Ative Directory** a partir do menu esquerdo expansível e, em seguida, abra o painel de inscrições da **App.** 

    [![Selecione o painel de diretório ativo Azure](./media/digital-twins-permissions/azure-portal-select-aad-pane.png)](./media/digital-twins-permissions/azure-portal-select-aad-pane.png#lightbox)

1. Selecione o botão **+ Novo de registo.**

    [![Selecione o novo botão de registo](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Dê um nome amigável para este registo de aplicação na caixa **Nome.** No âmbito da secção **Redirect URI (opcional),** escolha cliente **público/nativo (mobile & desktop)** no menu suspenso à esquerda e introduza `https://microsoft.com` na caixa de texto à direita. Selecione **Registar**.

    [![Criar o painel](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. Para se certificar de que [a aplicação está registada como **cliente público,** ](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration)abra o painel de **Autenticação** para o registo da sua aplicação e desloque-se para baixo nesse painel. Na secção **de tipo de cliente Predefinido,** escolha **sim** para **tratar aplicação como cliente público**, e bata **save**.

    1. **As URIs de redirecionamento** devem coincidir com o endereço fornecido pelo pedido de autenticação:

        * Para aplicações hospedadas num ambiente de desenvolvimento local, selecione **cliente Público (mobile & desktop)** . Certifique-se de definir o tipo de **cliente Padrão** para sim.
        * Para aplicações de página única hospedadas no Serviço de Aplicações Azure, selecione **Web**.

        Selecione **cliente público (mobile & desktop)** e introduza `http://localhost:8080/`.

        [![configurar URIs de redirecionamento](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png)](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png#lightbox)

    1. Verifique as **fichas** de acesso para configurar a definição **oauth2AllowImplicitFlow** para `true` no **Manifesto** JSON do seu recurso.

        [![configuração de configuração do cliente público](./media/digital-twins-permissions/aad-configure-public-client.png)](./media/digital-twins-permissions/aad-configure-public-client.png#lightbox)

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
