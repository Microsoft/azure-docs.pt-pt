---
title: 'Tutorial: Criar uma aplicação web de página única do Azure Time Series Insights | Documentos da Microsoft'
description: Saiba como criar uma aplicação web de página única que a consulta e processa os dados de um ambiente do Azure Time Series Insights.
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 04/25/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 2f25267b95e9ed5f7d5f6e6373fb9e3807927a7f
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735352"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>Tutorial: Criar uma aplicação Web de página única do Azure Time Series Insights

Este tutorial orienta-o ao longo do processo de criação de seu próprio aplicativo web de página única (SPA) para aceder aos dados do Azure Time Series Insights. 

Neste tutorial, ficará a conhecer:

> [!div class="checklist"]
> * A conceção da aplicação
> * Como registar a sua aplicação com o Azure Active Directory (Azure AD)
> * Como criar, publicar e testar a aplicação Web

> [!NOTE]
> * O código-fonte para este tutorial é fornecido numa [GitHub](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).
> * O Time Series Insights [aplicação de exemplo de cliente](https://insights.timeseries.azure.com/clientsample) está alojado para mostrar a aplicação concluída utilizada neste tutorial.

## <a name="prerequisites"></a>Pré-requisitos

* Inscreva-se para uma [gratuitamente a subscrição do Azure](https://azure.microsoft.com/free/) se ainda não tiver uma.

* Uma cópia gratuita do Visual Studio. Transfira o [versões da Comunidade de 2017 ou 2019](https://www.visualstudio.com/downloads/) para começar a utilizar.

* Os IIS Express, Web Deploy e serviços Cloud do Azure ferramentas componentes principais para o Visual Studio. Adicione os componentes ao modificar a instalação do Visual Studio.

## <a name="application-design"></a>Design da aplicação

O exemplo de Time Series Insights SPA é a base para o design e o código utilizado neste tutorial. O código usa a biblioteca de cliente JavaScript do Time Series Insights. A biblioteca de cliente do Time Series Insights fornece uma abstração para duas categorias principais de API:

- **APIs de consulta de métodos de invólucro para chamar o Time Series Insights**: APIs REST, que pode utilizar para consultar o Time Series Insights dados usando expressões baseados em JSON. Os métodos são organizados de acordo com o espaço de nomes de TsiClient.server da biblioteca.

- **Métodos para criar e Popular os vários tipos de controles de criação de gráficos**: Métodos que pode utilizar para visualizar os dados do Time Series Insights numa página Web. Os métodos são organizados de acordo com o espaço de nomes de TsiClient.ux da biblioteca.

Este tutorial utiliza também os dados de ambiente de Time Series Insights o aplicativo de exemplo. Para obter detalhes sobre a estrutura de aplicativo de exemplo de Time Series Insights e como ele usa a biblioteca de cliente do Time Series Insights, veja o tutorial [Explore a biblioteca de cliente do Azure Time Series Insights JavaScript](tutorial-explore-js-client-lib.md).

## <a name="register-the-application-with-azure-ad"></a>Registar a aplicação com o Azure AD

Antes de criar a aplicação, tem de registá-lo com o Azure AD. Registo fornece a configuração de identidade, para que a aplicação pode utilizar o suporte do OAuth para início de sessão único. OAuth requer SPAs usar o tipo de concessão de autorização implícita. Atualizar a autorização no manifesto do aplicativo. Um manifesto de aplicação é uma representação JSON de configuração da identidade da aplicação.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) com a sua conta de subscrição do Azure.  
1. Selecione **Azure Active Directory** > **Registos das aplicações** > **Novo registo de aplicação**.

   [![Portal do Azure – registo de aplicação de Begin o Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png#lightbox)

1. Na **criar** painel, preencha os parâmetros necessários.

   Parâmetro|Descrição
   ---|---
   **Nome** | Introduza um nome de registo significativo.  
   **Tipo de Aplicação** | Deixe como a **aplicação/API Web**.
   **URL de início de sessão** | Introduza o URL para a início de sessão (home) página do aplicativo. Uma vez que a aplicação mais tarde será alojada no App Service do Azure, tem de utilizar um URL a https:\//azurewebsites.net domínio. Neste exemplo, o nome baseia-se no nome do registo.

   Selecione **criar** para criar o novo registo de aplicação.

   [![Portal do Azure - criar a opção no painel de registo de aplicações do Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png#lightbox)

1. As aplicações de recursos fornecem APIs REST, que podem utilizar outras aplicações. As APIs também são registadas com o Azure AD. APIs fornecem acesso granular e seguro para aplicações cliente por meio da exposição *âmbitos*. Uma vez que a aplicação chama a API de informações de série de tempo do Azure, tem de especificar a API e o escopo. Permissão é concedida para a API e o âmbito no tempo de execução. Selecione **configurações** > **permissões obrigatórias** > **adicionar**.

   [![Portal do Azure – adicionar a opção para adicionar permissões do Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png#lightbox)

1. Na **adicionar acesso à API** painel, selecione **1 selecionar uma API** para especificar a API de informações de série de tempo do Azure. Na **selecionar uma API** painel, na caixa de pesquisa, introduza **do azure time**. Em seguida, selecione **do Azure Time Series Insights** na lista de resultados. Escolha **Selecionar**.

   [![Portal do Azure – a opção de pesquisa para adicionar permissões do Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png#lightbox)

1. Selecionar um âmbito para a API, o **adicionar acesso à API** painel, selecione **2 permissões Select**. Na **ativar o acesso ao** painel, selecione a **serviço de acesso do Azure Time Series Insights** âmbito. Escolha **Selecionar**. Forem retornados para o **adicionar acesso à API** painel. Selecione **Done** (Concluído).

   [![Portal do Azure – definir um âmbito para adicionar permissões do Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png#lightbox)

1. Na **permissões obrigatórias** painel, a API de informações de série de tempo do Azure agora é mostrada. Terá também de fornecer permissão de consentimento prévio para a aplicação aceder à API e o âmbito para todos os utilizadores. Selecione **conceder permissões**e, em seguida, selecione **Sim**.

   [![Permissões necessárias do portal do Azure – opção a concessão de permissões para adicionar o Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png#lightbox)

1. Como discutido anteriormente, também tem de atualizar o manifesto do aplicativo. No menu horizontal na parte superior do painel (a "trilha"), selecione o nome da aplicação para retornar para o **aplicação registada** painel. Selecione **manifesto**, altere o `oauth2AllowImplicitFlow` propriedade `true`e, em seguida, selecione **guardar**.

   [![Portal do Azure – o manifesto de atualização do Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png#lightbox)

1. Seus, selecione o nome da aplicação para retornar para o **aplicação registada** painel. Copie os valores de **Home page** e **ID de aplicação** para a sua aplicação. Usar essas propriedades mais tarde no tutorial.

   [![Portal do Azure – copiar o URL da Home Page e o ID de aplicação, os valores para a sua aplicação](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png#lightbox)

## <a name="build-and-publish-the-web-application"></a>Criar e publicar a aplicação Web

1. Crie um diretório para armazenar os ficheiros do projeto da aplicação. Em seguida, aceda a cada um dos seguintes URLs. Com o botão direito a **Raw** associar no canto superior direito da página e, em seguida, selecione **guardar como** para guardar os ficheiros no seu diretório do projeto.

   - [*index.html*](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html): HTML e JavaScript para a página
   - [*sampleStyles.css*]( https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css): Folha de estilos CSS

   > [!NOTE]
   > Consoante o browser, poderá ter de alterar as extensões de arquivo. HTML ou CSS antes de guardar o ficheiro.

1. Certifique-se de que os componentes necessários estão instalados no Visual Studio. Os componentes de ferramentas de núcleo de IIS Express, Web Deploy e serviços Cloud do Azure para Visual Studio tem de ser instalados.

    [![Visual Studio – modificar os componentes instalados](media/tutorial-create-tsi-sample-spa/vs-installation.png)](media/tutorial-create-tsi-sample-spa/vs-installation.png#lightbox)

    > [!NOTE]
    > Sua experiência do Visual Studio pode variar um pouco dos exemplos descritos dependendo das suas definições de configuração e de versão.

1. Abra o Visual Studio e inicie sessão. Para criar um projeto de aplicativo web, no **arquivo** menu, selecione **aberto** > **Web Site**.

    [![Visual Studio - criar uma nova solução](media/tutorial-create-tsi-sample-spa/vs-solution-create.png)](media/tutorial-create-tsi-sample-spa/vs-solution-create.png#lightbox)

1. Na **Open Web Site do** painel, selecione o diretório de trabalho onde guardou os ficheiros HTML e CSS e, em seguida, selecione **aberto**.

   [![Visual Studio – menu Arquivo, com as opções de abertura e de Web Site](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png#lightbox)

1. No Visual Studio **View** menu, selecione **Explorador de soluções**. A nova solução é aberto. Ele contém um projeto de Web site (ícone de globo), que contém os ficheiros HTML e CSS.

   [![Visual Studio – a nova solução no Explorador de soluções](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png#lightbox)

1. Antes de publicar a aplicação, tem de alterar as definições de configuração na *Index*.

   1. Anule os comentários as três linhas sob o comentário `"PROD RESOURCE LINKS"` para mudar as dependências do desenvolvimento para a produção. Comente as três linhas sob o comentário `"DEV RESOURCE LINKS"`.

      [!code-html[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      As suas dependências devem ser comentadas semelhante ao seguinte exemplo:

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

   1. Para configurar a aplicação para utilizar o seu ID de registo de aplicação do Azure AD, altere a `clientID` e `postLogoutRedirectUri` valores a utilizar os valores para **ID de aplicação** e **Home Page do** que copiou no passo 9 [ Registar a aplicação com o Azure AD](#register-the-application-with-azure-ad).

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      Por exemplo:

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ```

   1. Quando tiver terminado de modificações, guarde *Index*.

1. Publicar a aplicação na sua subscrição do Azure como um serviço de aplicações do Azure.  

   > [!NOTE]
   > Várias opções nas capturas de ecrã que são apresentadas nos passos seguintes são preenchidas automaticamente com os dados da sua subscrição do Azure. Poderá demorar alguns segundos para cada painel carregar completamente.  

   1. No Solution Explorer, clique com o botão direito no nó do projeto de Web site e, em seguida, selecione **publicar aplicação do Web**.  

      [![Visual Studio - selecione a opção de aplicação Web de publicação do Explorador de soluções](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png#lightbox)

   1. Selecione **iniciar** para começar a publicar seu aplicativo.

      [![Visual Studio – o painel do perfil de publicação](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png#lightbox)

   1. Selecione a subscrição que pretende utilizar para publicar a aplicação. Selecione o **TsiSpaApp** projeto. Em seguida, selecione **OK**.

      [![Visual Studio – o painel do serviço de aplicações do perfil de publicação](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png#lightbox)

   1. Selecione **publicar** para implementar a aplicação web.

      [![Visual Studio - a opção de publicação e a saída de registo de publicação](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png#lightbox)

   1. Um registo de publicação bem-sucedida aparece no Visual Studio **saída** painel. Quando a implementação estiver concluída, o Visual Studio abre-se a aplicação web num separador do browser e pede-lhe para iniciar sessão. Após o início de sessão com êxito, os controles do Time Series Insights são preenchidos com dados.

## <a name="troubleshoot"></a>Resolução de problemas  

Código de erro/condição | Descrição
---------------------| -----------
*AADSTS50011: Nenhum endereço de resposta está registado para a aplicação.* | O registo do Azure AD está em falta a **URL de resposta** propriedade. Aceda a **configurações** > **URLs de resposta** para o seu registo de aplicação do Azure AD. Certifique-se de que o **início de sessão** URL especificado no passo 3 na [registar a aplicação com o Azure AD](#register-the-application-with-azure-ad) está presente.
*AADSTS50011: A resposta do url especificado no pedido não coincide com os urls de resposta configurados para a aplicação: "\<GUID de ID da aplicação >'.* | O `postLogoutRedirectUri` especificado no passo 6 no [criar e publicar a aplicação web](#build-and-publish-the-web-application) tem de corresponder ao valor especificado na **definições** > **URLs de resposta** no o registo de aplicação do Azure AD. Certifique-se de que também alterar o valor para **URL de destino** a utilizar *https* por etapa 5 [criar e publicar a aplicação](#build-and-publish-the-web-application).
O aplicativo web for carregada, mas tem uma sem estilo, somente texto início de sessão na página, com um fundo branco. | Certifique-se de que os caminhos discutidos na etapa 4 no [criar e publicar a aplicação](#build-and-publish-the-web-application) estão corretos. Se a aplicação Web não conseguir localizar os ficheiros .css, a página não terá o estilo correto.

## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial cria vários serviços do Azure em execução. Se não planear concluir esta série de tutoriais, recomendamos que elimine todos os recursos para evitar incorrer em custos desnecessários.

No menu do Azure portal esquerdo:

1. Selecione **grupos de recursos**e, em seguida, selecione o grupo de recursos que criou para o ambiente do Time Series Insights. Na parte superior da página, selecione **eliminar grupo de recursos**, introduza o nome do grupo de recursos e, em seguida, selecione **eliminar**.
1. Selecione **grupos de recursos**e, em seguida, selecione o grupo de recursos que foi criado com o solution accelerator de simulação do dispositivo. Na parte superior da página, selecione **eliminar grupo de recursos**, introduza o nome do grupo de recursos e, em seguida, selecione **eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a:

> [!div class="checklist"]
> * A conceção da aplicação
> * Como registar a sua aplicação com o Azure AD
> * Como criar, publicar e testar a aplicação Web

Este tutorial integra-se com o Azure AD e utiliza a identidade do utilizador que tenha sessão iniciada para adquirir um token de acesso. Para saber como aceder à API de informações de série de tempo utilizando a identidade de um aplicativo de serviço ou daemon, consulte este artigo:

> [!div class="nextstepaction"]
> [Autenticação e autorização para API do Azure Time Series Insights](time-series-insights-authentication-and-authorization.md)
