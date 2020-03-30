---
title: incluir ficheiro
description: incluir ficheiro
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 02/03/2020
ms.openlocfilehash: 5be6e7937a6e1f710b8e2576a9058963413fb6c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76984594"
---
1. No [portal Azure,](https://ms.portal.azure.com/)selecione Registos de Aplicações de **Diretório** > Ativo Azure Novas**inscrições.** > **New registration**

   [![Novo registo de candidaturas no Diretório Ativo do Azure](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

    A sua aplicação ficará listada aqui depois de a registar.

1. Dê à aplicação um nome e **selecione Contas neste diretório organizacional apenas** para especificar os tipos de **conta Suportados** que podem aceder à API. Escolha um URI válido para redirecionar os utilizadores depois de autenticarem e, em seguida, **registar**.

   [![Criar a aplicação no Diretório Ativo azure](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. As informações importantes da aplicação Azure Ative Directory são apresentadas na lâmina de **visão geral** da sua aplicação listada. Selecione a sua aplicação em **aplicações próprias**e, em seguida, **visão geral**.

   [![Copiar o ID da aplicação](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   Copie o seu ID de **Aplicação (cliente)** para utilizar na sua aplicação de cliente.

1. A lâmina **de autenticação** especifica definições importantes de configuração de autenticação. 

    1. Adicione **URIs redirecionamento** e configure **tokens** de acesso selecionando **+ Adicione uma plataforma**.

    1. Determine se a aplicação é ou não um **cliente público** selecionando **Sim** ou **Não**.

    1. Verifique quais as contas e inquilinos apoiados.

    [![Configure subvenção implícita](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

1. Depois de selecionar a plataforma apropriada, configure os seus **URIs de Redirecionamento** e **Tokens** de Acesso no painel lateral à direita da interface do utilizador.

    1. **As URIs de redirecionamento** devem coincidir com o endereço fornecido pelo pedido de autenticação:

        * Para aplicações hospedadas num ambiente de desenvolvimento local, selecione **cliente Público (mobile & desktop)**. Certifique-se de definir o **cliente público** para **Sim**.
        * Para aplicações de página única hospedadas no Serviço de Aplicações Azure, selecione **Web**.

    1. Determine se um URL de **logout** é apropriado.

    1. Ativar o fluxo implícito da subvenção verificando **fichas** de acesso ou **fichas de identificação**.

    [![Criar URIs redirecionais](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png)](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png#lightbox)

    Clique em **Configurar** **e,** em seguida, guardar .

1. Selecione **Certificados & segredos** e depois **novo segredo do cliente** para criar uma senha de aplicação que a sua aplicação de cliente pode usar para provar a sua identidade.

   [![Criar um novo segredo de cliente](media/time-series-insights-aad-registration/active-directory-application-keys-save.png)](media/time-series-insights-aad-registration/active-directory-application-keys-save.png#lightbox)

   A sua senha secreta do cliente será então exibida. Copie a chave para o seu editor de texto favorito.

   > [!NOTE]
   > Em vez disso, tem a capacidade de importar um certificado. Para uma maior segurança, recomenda-se um certificado. Para utilizar um certificado, selecione **o certificado de upload**.

1. Associe a sua app Azure Ative Directory Azure TIme Series Insights. Selecione **permissões** > API**Adicione uma permissão** > **apis que a minha organização utiliza.** 

    [![Associe uma API à sua app Azure Ative Directory](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Digite `Azure Time Series Insights` na barra `Azure Time Series Insights`de pesquisa e selecione .

1. Em seguida, especifique o tipo de permissão da API que a sua aplicação necessita. Por defeito, serão **destacadas permissões delegadas.** Escolha um tipo de permissão então, **selecione Adicionar permissões**.

    [![Especifique o tipo de permissão da API que a sua aplicação requer](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)
