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
ms.date: 11/14/2019
ms.openlocfilehash: fa6921f8c4309f17cbd63e242a5416e7e81bb6c3
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74133585"
---
1. Na [portal do Azure](https://ms.portal.azure.com/), selecione **Azure Active Directory** > **registros de aplicativo** > **novo registro**.

   [![novo registro de aplicativo no Azure Active Directory](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

    Seu aplicativo será listado aqui depois que você registrá-lo.

1. Dê um nome ao aplicativo e selecione **contas neste diretório organizacional somente** para especificar os **tipos de conta com suporte** que podem acessar a API. Escolha um URI válido para redirecionar os usuários para depois de autenticar e, em seguida, **Registre-se**.

   [![criar o aplicativo no Azure Active Directory](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. Importante Azure Active Directory informações do aplicativo são exibidas na folha de **visão geral** do aplicativo listado. Selecione seu aplicativo em **aplicativos de propriedade**e, em seguida, **visão geral**.

   [![copiar a ID do aplicativo](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   Copie sua **ID de aplicativo (cliente)** para usar em seu aplicativo cliente.

1. A folha **autenticação** especifica definições de configuração de autenticação importantes. 

    1. Os **URIs de redirecionamento** devem corresponder ao endereço fornecido pela solicitação de autenticação:

        * Para aplicativos hospedados em um ambiente de desenvolvimento local, selecione **cliente público (mobile & Desktop)** . Certifique-se de definir o **tipo de cliente padrão** como Sim.
        * Para aplicativos de página única hospedados no serviço Azure App, selecione **Web**.

    1. Habilite o fluxo de concessão implícita verificando **tokens de acesso** ou **tokens de ID**.

   [![criar um novo segredo do cliente](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

   Clique em **Guardar**.

1. Selecione **certificados & segredos** e, em seguida, **novo segredo do cliente** para criar uma senha de aplicativo que o cliente possa usar para provar sua identidade.

   [![criar um novo segredo do cliente](media/time-series-insights-aad-registration/active-directory-application-keys-save.png)](media/time-series-insights-aad-registration/active-directory-application-keys-save.png#lightbox)

   A senha do segredo do cliente será exibida. Copie a chave para seu editor de texto favorito.

   > [!NOTE]
   > Em vez disso, você tem a capacidade de importar um certificado. Para aumentar a segurança, é recomendável um certificado. Para usar um certificado, selecione **carregar certificado**.

1. Associe seu aplicativo Azure Active Directory Azure TIme Series insights. Selecione **permissões de API** > **adicionar uma permissão** > **APIs que minha organização usa**. 

    [![associar uma API ao seu aplicativo Azure Active Directory](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Digite `Azure Time Series Insights` na barra de pesquisa e, em seguida, selecione `Azure Time Series Insights`.

1. Em seguida, especifique o tipo de permissão de API que seu aplicativo requer. Por padrão, **as permissões delegadas** serão realçadas. Escolha um tipo de permissão e, em seguida, selecione **adicionar permissões**.

    [![especificar o tipo de permissão de API que seu aplicativo requer](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)