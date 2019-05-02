---
title: 'Tutorial: Criar uma aplicação web de página única do Azure Time Series Insights | Documentos da Microsoft'
description: Saiba como criar uma aplicação Web de página única que consulta e apresenta dados a partir de um ambiente TSI.
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 04/25/2019
ms.author: anshan
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 18f5c14a9427f4d7e34a802b2bcc0612a51a804a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64573273"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>Tutorial: Criar uma aplicação Web de página única do Azure Time Series Insights

Este tutorial orienta-o ao longo do processo de criação de seu próprio aplicativo web de página única para aceder aos dados do Time Series Insights. Especificamente, aprenderá sobre:

> [!div class="checklist"]
> * A conceção da aplicação
> * Como registar a aplicação com o Azure Active Directory (AD)
> * Como criar, publicar e testar a aplicação Web

> [!NOTE]
> * O código-fonte para este tutorial é fornecido numa [GitHub](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).
> * O Time Series Insights [aplicação de exemplo de cliente](https://insights.timeseries.azure.com/clientsample) está alojado para mostrar a aplicação concluída utilizada neste Tutorial.

## <a name="prerequisites"></a>Pré-requisitos

* Inscreva-se numa [Subscrição gratuita do Azure](https://azure.microsoft.com/free/), se ainda não tiver uma.

* Também terá uma cópia gratuita do Visual Studio. Transfira o [versão de Comunidade de 2017 ou 2019](https://www.visualstudio.com/downloads/) para começar a utilizar.

* Também precisará a **IIS Express**, **Web Deploy**, e **ferramentas de núcleo de serviços Cloud do Azure** componentes para o Visual Studio. Adicioná-las ao modificar a instalação do Visual Studio.

## <a name="application-design-overview"></a>Descrição geral da conceção da aplicação

A aplicação de exemplo de página única do Time Series Insights fornece a base para o design e o código utilizado neste tutorial. O código inclui a utilização da biblioteca JavaScript do Cliente TSI. A biblioteca do Cliente TSI fornece uma abstração para duas categorias principais de API:

- **Métodos de invólucro para chamar as APIs de consulta do TSI**: APIs REST que permitem a consultar dados TSI usando expressões baseados em JSON. Os métodos estão organizados no espaço de nomes `TsiClient.server` da biblioteca.

- **Métodos para criar e Popular os vários tipos de controles de criação de gráficos**: Métodos que são utilizados para visualizar os dados TSI numa página da web. Os métodos estão organizados no espaço de nomes `TsiClient.ux` da biblioteca.

Este tutorial também irá utilizar os dados do ambiente TSI do exemplo de aplicação. Para obter detalhes sobre a estrutura do exemplo de aplicação TSI e a respetiva utilização da biblioteca de Clientes TSI, consulte o tutorial [Explorar a biblioteca de clientes JavaScript do Azure Time Series Insights](tutorial-explore-js-client-lib.md).

## <a name="register-the-application-with-azure-ad"></a>Registar a aplicação com o Azure AD

Antes de criar a aplicação, tem de registá-la com o Azure AD. O registo fornece a configuração de identidade para uma aplicação, permitindo-lhe utilizar suporte OAuth para o início de sessão único. O OAuth requer que os SPAs utilizem a concessão de autorização "implícita", que deverá atualizar no manifesto de aplicação. Um manifesto de aplicação é uma representação JSON de configuração da identidade da aplicação.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com a sua conta de subscrição do Azure.  
1. Selecione o **do Azure Active Directory** recurso no painel esquerdo, em seguida, **registos das aplicações**, em seguida, **+ novo registo de aplicação**.

   [![Registo de aplicação do portal do Azure do Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png#lightbox)

1. Sobre o **criar** página, preencha os parâmetros necessários.

   Parâmetro|Descrição
   ---|---
   **Nome** | Forneça um nome de registo significativo.  
   **Tipo de Aplicação** | Uma vez que está a criar uma aplicação Web SPA, deixe como “Aplicação Web/API”.
   **URL de início de sessão** | Introduza o URL para a página homepage /início de sessão da aplicação. Uma vez que a aplicação será alojada no serviço de aplicações do Azure (mais tarde), tem de utilizar um URL dentro de "https:\//azurewebsites.net" domínio. Neste exemplo, o nome baseia-se no nome do registo.

   Quando terminar, clique em **Criar** para criar o novo registo de aplicação.

   [![Registo de aplicação do portal do Azure AD do Azure - criação](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png#lightbox)

1. As aplicações de recurso fornecem APIs REST para utilização por outras aplicações, e também são registadas com o Azure AD. As APIs fornecem acesso granular/protegido a aplicações de cliente, ao exporem os “âmbitos”. Dado que a sua aplicação irá chamar a API do “Azure Time Series Insights”, precisa de especificar a API e o âmbito, para o qual a permissão será pedida/concedida no runtime. Selecione **configurações**, em seguida, **permissões obrigatórias**, em seguida, **+ adicionar**.

   [![Portal do Azure do Azure AD adicionar permissões](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png#lightbox)

1. Na página **Adicionar acesso à API**, clique em **1 Selecionar uma API** para especificar a API do TSI. Na página **Selecionar uma API**, introduza "azure time" no campo de pesquisa. Em seguida, selecione o "Azure Time Series Insights" API na lista de resultados e clique em **selecione**.

   [![Permissões - API de adicionar o portal do Azure do Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png#lightbox)

1. Agora, especifique um âmbito na API. Novamente, na página **Adicionar acesso à API**, clique em **2 Selecionar permissões**. Na página **Ativar Acesso**, selecione o âmbito "Aceder ao serviço Azure Time Series Insights". Clique em **selecionar**, que irá devolver ao **adicionar acesso à API** página, em que clique **feito**.

   [![Portal do Azure do Azure AD adicionar permissões - escopo](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png#lightbox)

1. Quando regressar à página **Permissões necessárias**, tenha em atenção que a API do "Azure Time Series Insights" está agora listada. Terá precisará de permissão de pré-consentimento para que a aplicação aceda à API e ao âmbito, para todos os utilizadores. Clique nas **conceder permissões** botão na parte superior e selecione **Sim**.

   [![Permissões - consentimento de necessárias do portal do Azure do Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png#lightbox)

1. Como mencionado anteriormente, terá também de atualizar o manifesto de aplicação. Clique no nome da aplicação no endereço para regressar à página **Aplicação registada**. Selecione **manifesto**, alterar a `oauth2AllowImplicitFlow` propriedade para `true`, em seguida, clique em **guardar**.

   [![Manifesto de atualização do portal do Azure do Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png#lightbox)

1. Por fim, clique no endereço para regressar novamente à página **Aplicação registada** e copie as propriedades do URL da **Home page** e o **ID da aplicação** para a sua aplicação. Irá utilizar estas propriedades num passo posterior.

   [![Propriedades do portal do Azure do Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png#lightbox)

## <a name="build-and-publish-the-web-application"></a>Criar e publicar a aplicação Web

1. Crie um diretório para armazenar os ficheiros do projeto da aplicação. Em seguida, navegue para cada um dos seguintes URLs, faça duplo clique no link "Raw" na área superior direita da página e "Guardar como" no seu diretório do projeto.

   > [!NOTE]
   > Consoante o browser, poderá ter de corrigir a extensão do ficheiro (para HTML ou CSS) antes de guardar o ficheiro.

   - [**index.html**](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html): HTML e JavaScript para a página.
   - [**sampleStyles.css**]( https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css): Folha de estilos CSS.

1. Certifique-se de que o Visual Studio tem os componentes necessários instalados.

    [![VS - modificar componentes instalados](media/tutorial-create-tsi-sample-spa/vs-installation.png)](media/tutorial-create-tsi-sample-spa/vs-installation.png#lightbox)

    * Terá do **IIS Express**, **Web Deploy**, e **ferramentas de núcleo de serviços Cloud do Azure** componentes para o Visual Studio.

    > [!NOTE]
    > Sua experiência do Visual Studio pode divergir ligeiramente dos exemplos descritos, consoante a versão e definições de configuração.

1. Abra e inicie sessão no Visual Studio para criar um projeto para a aplicação Web. No menu **Ficheiro**, selecione a opção **Abrir**, **Site**.

    [![VS - criar nova solução](media/tutorial-create-tsi-sample-spa/vs-solution-create.png)](media/tutorial-create-tsi-sample-spa/vs-solution-create.png#lightbox)

1. Sobre o **Open Web Site do** caixa de diálogo, selecione o diretório de trabalho onde guardou os ficheiros HTML e CSS, em seguida, clique em **aberto**.

   [![VS - file open web site](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png#lightbox)

1. Abra o **Explorador de Soluções** no menu **Ver** do Visual Studio. Deverá ver a nova solução, que contém um projeto de web site (ícone de globo), que contém os ficheiros HTML e CSS.

   [![VS - nova solução de solution explorer](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png#lightbox)

1. Antes de publicar a sua aplicação, terá de alterar as definições de configuração na **Index**.

   a. Mude as dependências do desenvolvimento para a produção removendo os comentários as três linhas sob o comentário `"PROD RESOURCE LINKS"`. Comente as três linhas sob o comentário `"DEV RESOURCE LINKS"`.

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      As suas dependências devem ser inserido um comentário em conformidade:

      ```HTML
      <!-- PROD RESOURCE LINKS -->
      <link rel="stylesheet" type="text/css" href="./sampleStyles.css">
      <script src="https://unpkg.com/tsiclient@1.1.4/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="https://unpkg.com/tsiclient@1.1.4/tsiclient.css">

      <!-- DEV RESOURCE LINKS -->
      <!-- <link rel="stylesheet" type="text/css" href="./sampleStyles.css">
      <script src="../../dist/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="../../dist/tsiclient.css"> -->
      ```

   b. Em seguida, configure a aplicação para utilizar o seu ID de registo de aplicação do Azure Active Directory. Alterações a `clientID` e `postLogoutRedirectUri` campos para utilizar o **ID de aplicação** e **URL da Home Page** que copiou no **passo 9** de [registar o aplicação com o Azure AD](#register-the-application-with-azure-ad).

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      Por exemplo:

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ```

   c. Guarde **Index** quando terminar de fazer essas modificações.

1. Agora, publicar a aplicação na sua subscrição do Azure como um serviço de aplicações do Azure:  

   > [!NOTE]
   > Vários campos das caixas de diálogo seguintes são preenchidos com os dados da sua subscrição do Azure. Como tal, pode demorar alguns segundos para cada caixa de diálogo carregar completamente, antes de poder continuar.  

   a. Clique com o botão direito do rato no nó do projeto de web site no **Explorador de soluções**e selecione **publicar aplicação do Web**.  

      [![VS - Explorador de soluções do publicar a aplicação web](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png#lightbox)

   b. Selecione **iniciar** para começar a publicar seu aplicativo.

      [![VS - perfil de publicação](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png#lightbox)

   c. Selecione a subscrição que pretende utilizar para publicar a aplicação. Selecione o **TsiSpaApp** projeto. Em seguida, **OK**:

      [![VS - perfil - serviço de aplicações de publicação](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png#lightbox)

   d. Clique em **publicar** para implementar a aplicação web.

      [![VS - Publicar aplicação web – publicar saída de registo](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png#lightbox)

   e. Deverá ver um registo de publicação com êxito na janela **Saída** do Visual Studio. Após a conclusão da implementação, o Visual Studio também abrirá a aplicação Web num separador do browser, pedindo para iniciar sessão. Após o início de sessão com êxito, verá todos os controles TSI preenchidos com dados.

## <a name="troubleshooting"></a>Resolução de problemas  

Código de erro/condição | Descrição
---------------------| -----------
*AADSTS50011: Nenhum endereço de resposta está registado para a aplicação.* | O registo do Azure AD está em falta a **URL de resposta** propriedade. Vá para a página **Definições** / **URLs de Resposta** do seu registo de aplicações do Azure AD. Certifique-se de que o **início de sessão** URL especificada na **passo 3** de [registar a aplicação com o Azure AD](#register-the-application-with-azure-ad) está presente.
*AADSTS50011: A resposta do url especificado no pedido não coincide com os urls de resposta configurados para a aplicação: "\<GUID de ID da aplicação >'.* | O `postLogoutRedirectUri` especificado no passo n.º 4.b de [Criar e publicar a aplicação Web](#build-and-publish-the-web-application) tem de corresponder ao valor especificado na propriedade **Definições** / **URLs de Resposta** do seu registo de aplicação do AD. Certifique-se de que também alterar **URL de destino** a utilizar `https`, por **passo 5** de [criar e publicar a aplicação](#build-and-publish-the-web-application).
A aplicação carrega, mas apresenta uma página de início de sessão só com texto e sem estilo, com um fundo branco. | Certifique-se de que os caminhos discutidos **passo 4** dos [criar e publicar a aplicação](#build-and-publish-the-web-application) estão corretos. Se a aplicação Web não conseguir localizar os ficheiros .css, a página não terá o estilo correto.

## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial cria vários serviços do Azure em execução. Se não planeia concluir esta série de tutoriais, recomendamos que elimine todos os recursos para evitar incorrer em custos desnecessários.

No menu esquerdo do portal do Azure:

1. Selecione **grupos de recursos**, em seguida, selecione o grupo de recursos que criou para o ambiente do TSI. Na parte superior da página, clique em **Eliminar grupo de recursos**, escreva o nome do grupo de recurso e, em seguida, clique em **Eliminar**.
1. Selecione **grupos de recursos**, em seguida, selecione o grupo de recursos que foi criado com o solution accelerator de simulação do dispositivo. Na parte superior da página, clique em **Eliminar grupo de recursos**, escreva o nome do grupo de recurso e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * A conceção da aplicação
> * Como registar a aplicação com o Azure Active Directory (AD)
> * Como criar, publicar e testar a aplicação Web

Este tutorial integra-se com o Azure AD, utilizando a identidade do utilizador com sessão iniciada para adquirir um token de acesso. Para saber como aceder à API do TSI utilizando a identidade de uma aplicação de serviço/daemon, veja:

> [!div class="nextstepaction"]
> [Autenticação e autorização para API do Azure Time Series Insights](time-series-insights-authentication-and-authorization.md)