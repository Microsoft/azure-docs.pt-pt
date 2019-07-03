---
title: incluir ficheiro
description: incluir ficheiro
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 07/02/2019
ms.openlocfilehash: a463e3cf475909c34054717460dc10dbba4ad8f0
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544002"
---
> [!IMPORTANT]
> * A nova **do Azure Active Directory** > **registos das aplicações** painel substitui o legado **do Azure Active Directory** > **aplicação registos (Legado)** painel Maio de 2019.
> * Registos de aplicações apresentada no painel de legado ou criada irão aparecer automaticamente no novo painel.
> * Para obter informações abrangentes sobre como migrar para a nova experiência de registo de aplicação do Azure, leia os [guia de treinamento de registos de aplicação do Azure](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide) e [guia de introdução do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

1. Na [portal do Azure](https://ms.portal.azure.com/), selecione **Azure Active Directory** > **registos das aplicações** > **novo registo**.

   [![Novo registo de aplicação no Azure Active Directory](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

   > [!TIP]
   > O novo painel de registo de aplicação do Active Directory do Azure permite-lhe filtrar as aplicações apresentadas selecionando **pertencentes à empresa aplicativos**.

    A aplicação será listada aqui depois de registá-lo.

1. Dar ao aplicativo, um nome e selecionar **contas apenas neste diretório organizacional** para especificar a **tipos de conta suportados** que pode aceder à API. Escolher um URI válido para redirecionar os utilizadores depois de se autenticar, em seguida, **registar**.

   [![Criar a aplicação no Azure Active Directory](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. Informações da aplicação do Azure Active Directory importantes são apresentadas na sua aplicação listada **descrição geral** painel. Selecione a sua aplicação sob **pertencentes à empresa aplicativos**, em seguida, **descrição geral**.

   [![Copie o ID da aplicação](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   Copiar seus **ID da aplicação (cliente)** para utilizar na sua aplicação de cliente.

1. O **autenticação** painel Especifica as definições de configuração de autenticação importante. 

    1. **URIs de redirecionamento** tem de corresponder ao endereço fornecido pelo pedido de autenticação:

        * Para aplicações alojadas num ambiente de desenvolvimento local, selecione **cliente público (móvel e ambiente de trabalho)** . Certifique-se de definir o **predefinido a tipo de cliente** como Sim.
        * Para aplicações de página única alojadas no App Service do Azure, selecione **Web**.

    1. Habilitar o fluxo de concessão implícita marcando **tokens de ID**.

   [![Criar um novo segredo do cliente](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

   Clique em **Guardar**.

1. Associe a sua aplicação do Azure Active Directory Azure TIme Series Insights. Selecione **permissões de API** > **adicionar uma permissão** > **APIs minha organização utiliza**. 

    [![Associar uma API com a sua aplicação do Azure Active Directory](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Tipo `Azure Time Series Insights` para a pesquisa de barra em seguida, selecione `Azure Time Series Insights`.

1. Em seguida, especifique o tipo de permissão de API requer a sua aplicação. Por predefinição, **permissões delegadas** serão realçadas. Escolha um tipo de permissão em seguida, selecione **adicionar permissões**.

    [![Especifique o tipo de permissão de API que requer a sua aplicação](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)