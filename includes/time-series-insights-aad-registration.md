---
title: ficheiro de inclusão
description: ficheiro de inclusão
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 10/02/2020
ms.openlocfilehash: 7de4dc21391f7dbd817c56ce51606a808cf9e3c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91665821"
---
1. No [portal Azure,](https://ms.portal.azure.com/)selecione **Azure Ative Directory**  >  **App registos**  >  **Novo registo**.

   [![Novo registo de candidaturas no Azure Ative Directory](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

    A sua aplicação será listada aqui depois de a registar.

1. Dar à aplicação um nome e selecionar **Contas neste diretório organizacional apenas** para especificar os **tipos de conta suportado** que podem aceder à API. Se estiver a criar uma [aplicação para clientes públicos,](https://docs.microsoft.com/azure/active-directory/develop/msal-client-application-configuration#redirect-uri)adicione um URI de redirecionamento válido, então **Registe-se**.

   [![Crie a aplicação no Azure Ative Directory](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. As informações importantes da aplicação Azure Ative Directory são apresentadas na lâmina de **visão geral** da sua aplicação listada. Selecione a sua aplicação em **aplicações próprias**e, em seguida, **visão geral**.

   [![Copiar o ID da aplicação](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   Copie o seu **ID de Aplicação (cliente)** para utilizar na sua aplicação de cliente.

1. A lâmina **de autenticação** especifica configurações importantes de configuração de autenticação.

    1. Adicione **URIs de redirecionamento** e configurar **tokens de acesso** selecionando + Adicionar uma **plataforma**.

    1. Determine se a aplicação é um **cliente público** ou não selecionando **Sim** ou **Não**.

    1. Verifique quais as contas e inquilinos que são suportados.

    [![Concessão implícita de configuração](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

1. Depois de selecionar a plataforma apropriada, configufique os **URIs de redirecionamento** e os **tokens de acesso** no painel lateral à direita da interface do utilizador.

    1. **Os URIs de redirecionamento** devem corresponder ao endereço fornecido pelo pedido de autenticação:

        * Para aplicações hospedadas num ambiente de desenvolvimento local, selecione **Cliente Público (mobile & desktop)**. Certifique-se de definir **o cliente público** para **Sim**.
        * Para Single-Page aplicações hospedadas no Azure App Service, selecione **Web**.

    1. Determine se um **URL logout** é apropriado.

    1. Ativar o fluxo de subvenção implícito verificando **fichas de acesso** ou **fichas de identificação**.

    [![Criar URIs de redirecionamento](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png)](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png#lightbox)

    Clique **em Configurar**e, em seguida, **Guarde**.

1. Selecione **Certificados & segredos** então **Novo cliente secreto** para criar uma senha de aplicação que a sua aplicação cliente pode usar para provar a sua identidade.

   [![Criar um novo segredo de cliente](media/time-series-insights-aad-registration/active-directory-application-keys-save.png)](media/time-series-insights-aad-registration/active-directory-application-keys-save.png#lightbox)

   A palavra-passe secreta do seu cliente será então exibida. Copie a chave do seu editor de texto favorito.

   > [!NOTE]
   > Em vez disso, tem a capacidade de importar um certificado. Para uma segurança reforçada, recomenda-se um certificado. Para utilizar um certificado, selecione **o certificado de upload**.

1. Associe a sua app Azure Ative Directory Azure Time Series Insights. Selecione **permissões API**  >  **Adicione uma permissão**  >  **APIs que a minha organização usa.**

    [![Associe uma API com a sua app Azure Ative Directory](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Digite `Azure Time Series Insights` na barra de pesquisa e, em seguida, selecione `Azure Time Series Insights` .

1. Em seguida, especifique o tipo de permissão da API que a sua aplicação necessita. Por predefinição, as **permissões delegadas** serão destacadas. Escolha um tipo de permissão então, **selecione Adicionar permissões**.

    [![Especifique o tipo de permissão API que a sua aplicação requer](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)
