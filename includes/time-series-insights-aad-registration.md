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
ms.date: 09/24/2019
ms.openlocfilehash: 7e67b9e1c611d14b78db53f8662fb4e1f493a8df
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990173"
---
> [!IMPORTANT]
> * O novo **Azure Active Directory** > folha **registros de aplicativo** substitui a folha herdada **Azure Active Directory** > **registros de aplicativo (herdada)** 2019.
> * Registros de aplicativo criados ou exibidos na folha herdada serão exibidos automaticamente na folha novo.
> * Para obter informações abrangentes sobre como migrar para a nova experiência de registro de Azure App, leia o [Guia de treinamento de registros de Azure app](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide) e [Azure Active Directory início rápido](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

1. Na [portal do Azure](https://ms.portal.azure.com/), selecione **Azure Active Directory** > **registros de aplicativo** > **novo registro**.

   [![novo registro de aplicativo no Azure Active Directory](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

   > [!TIP]
   > O novo painel de registro do aplicativo Azure Active Directory permite que você filtre os aplicativos exibidos selecionando **aplicativos pertencentes**.

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

    1. Habilite o fluxo de concessão implícita verificando **tokens de ID**.

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