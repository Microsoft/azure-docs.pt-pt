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
ms.date: 10/02/2020
ms.openlocfilehash: 22411e5a80f555a3ead05d39466a7a175923d9bc
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104204"
---
* Depois de selecionar a plataforma adequada no passo 4 das definições da [plataforma Configure,](../articles/active-directory/develop/quickstart-register-app.md#configure-platform-settings) configufique os **URIs de redirecionamento** e **os Tokens de acesso** no painel lateral à direita da interface do utilizador.

    * **Os URIs de redirecionamento** devem corresponder ao endereço fornecido pelo pedido de autenticação:

        * Para aplicações hospedadas num ambiente de desenvolvimento local, selecione **Cliente Público (mobile & desktop)**. Certifique-se de definir **o cliente público** para **Sim**.
        * Para Single-Page aplicações hospedadas no Azure App Service, selecione **Web**.

    * Determine se um **URL logout** é apropriado.

    * Ativar o fluxo de subvenção implícito verificando **fichas de acesso** ou **fichas de identificação**.

    [![Criar URIs de redirecionamento](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png)](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png#lightbox)

    Clique **em Configurar** e, em seguida, **Guarde**.

* Associe a sua app Azure Ative Directory Azure Time Series Insights. Selecione **permissões API**  >  **Adicione uma permissão**  >  **APIs que a minha organização usa.**

    [![Associe uma API com a sua app Azure Ative Directory](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Digite `Azure Time Series Insights` na barra de pesquisa e, em seguida, selecione `Azure Time Series Insights` .

* Em seguida, especifique o tipo de permissão da API que a sua aplicação necessita. Por predefinição, as **permissões delegadas** serão destacadas. Escolha um tipo de permissão então, **selecione Adicionar permissões**.

    [![Especifique o tipo de permissão API que a sua aplicação requer](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)

* [Adicione credenciais](../articles/active-directory/develop/quickstart-register-app.md#add-credentials) se a aplicação chamar as APIs do seu ambiente como si mesma. As credenciais permitem que a sua aplicação autença como ele próprio, não requerendo qualquer interação de um utilizador em tempo de execução.