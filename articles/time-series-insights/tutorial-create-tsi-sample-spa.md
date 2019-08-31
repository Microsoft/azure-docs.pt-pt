---
title: 'Tutorial: Criar um aplicativo Web de página única Azure Time Series Insights | Microsoft Docs'
description: Saiba como criar um aplicativo Web de página única que consulta e renderiza dados de um ambiente de Azure Time Series Insights.
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 031e8074585426584d7ef63a103c9c2b4d90e6c3
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194204"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>Tutorial: Criar uma aplicação Web de página única do Azure Time Series Insights

Este tutorial orienta você pelo processo de criação de seu próprio aplicativo Web de página única (SPA) para acessar dados de Azure Time Series Insights.

Neste tutorial, ficará a conhecer:

> [!div class="checklist"]
> * A conceção da aplicação
> * Como registrar seu aplicativo com o Azure Active Directory (Azure AD)
> * Como criar, publicar e testar a aplicação Web

> [!NOTE]
> * O código-fonte deste tutorial é fornecido no [GitHub](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).
> * O [aplicativo de exemplo de cliente](https://insights.timeseries.azure.com/clientsample) Time Series insights é hospedado para mostrar o aplicativo concluído usado neste tutorial.

Inscreva-se para obter uma [assinatura gratuita do Azure](https://azure.microsoft.com/free/) se você ainda não tiver uma.

## <a name="prerequisites"></a>Pré-requisitos

* Uma cópia gratuita do Visual Studio. Baixe as [versões da comunidade 2017 ou 2019](https://www.visualstudio.com/downloads/) para começar.

* Os componentes de ferramentas principais do IIS Express, Implantação da Web e serviços de nuvem do Azure para Visual Studio. Adicione os componentes modificando a instalação do Visual Studio.

## <a name="understand-application-design"></a>Entender o design do aplicativo

O Time Series Insights de exemplo SPA é a base para o design e o código usado neste tutorial. O código usa a biblioteca de cliente Time Series Insights JavaScript. A biblioteca de cliente Time Series Insights fornece uma abstração para duas categorias de API principais:

- **Métodos de wrapper para chamar as APIs de consulta de time Series insights**: APIs REST que você pode usar para consultar dados de Time Series Insights usando expressões baseadas em JSON. Os métodos são organizados sob o namespace TsiClient. Server da biblioteca.

- **Métodos para criar e preencher vários tipos de controles de gráficos**: Métodos que você pode usar para visualizar Time Series Insights dados em uma página da Web. Os métodos são organizados sob o namespace TsiClient. UX da biblioteca.

Este tutorial também usa dados do ambiente de Time Series Insights do aplicativo de exemplo. Para obter detalhes sobre a estrutura do Time Series Insights aplicativo de exemplo e como ele usa a biblioteca de cliente do Time Series Insights, consulte o tutorial [explorar a biblioteca de cliente do Azure Time Series insights JavaScript](tutorial-explore-js-client-lib.md).

## <a name="register-with-azure-ad"></a>Registrar com o Azure AD

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

## <a name="build-and-publish"></a>Compilar e publicar

1. Crie um diretório para armazenar os ficheiros do projeto da aplicação. Em seguida, vá para cada uma das URLs a seguir. Clique com o botão direito do mouse no link **bruto** no canto superior direito da página e selecione **salvar como** para salvar os arquivos no diretório do projeto.

   - [*index. html*](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html): o HTML e o JavaScript para a página
   - [*sampleStyles. css*](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css): a folha de estilos CSS

   > [!NOTE]
   > Dependendo do navegador, talvez seja necessário alterar as extensões de arquivo para. html ou. css antes de salvar o arquivo.

1. Verifique se os componentes necessários estão instalados no Visual Studio. Os componentes de ferramentas principais do IIS Express, Implantação da Web e serviços de nuvem do Azure para Visual Studio devem ser instalados.

    [![Visual Studio – modificar os componentes instalados](media/tutorial-create-tsi-sample-spa/vs-installation.png)](media/tutorial-create-tsi-sample-spa/vs-installation.png#lightbox)

    > [!NOTE]
    > Sua experiência com o Visual Studio pode variar um pouco dos exemplos descritos, dependendo da sua versão e das definições de configuração.

1. Abra o Visual Studio e entre. Para criar um projeto para o aplicativo Web, no menu **arquivo** , selecione **abrir** > **site**.

    [![Visual Studio-criar uma nova solução](media/tutorial-create-tsi-sample-spa/vs-solution-create.png)](media/tutorial-create-tsi-sample-spa/vs-solution-create.png#lightbox)

1. No painel **abrir site** , selecione o diretório de trabalho onde você armazenou os arquivos HTML e CSS e, em seguida, selecione **abrir**.

   [![Visual Studio – o menu arquivo, com as opções abrir e site da Web](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png#lightbox)

1. No menu **Exibir** do Visual Studio, selecione **Gerenciador de soluções**. A nova solução é aberta. Ele contém um projeto de site (ícone de Globo), que contém os arquivos HTML e CSS.

   [![Visual Studio-a nova solução no Gerenciador de Soluções](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png#lightbox)

1. Antes de publicar seu aplicativo, você deve alterar as definições de configuração em *index. html*.

   1. Remova os comentários das três linhas abaixo do `"PROD RESOURCE LINKS"` comentário para mudar as dependências de desenvolvimento para produção. Comente as três linhas abaixo do comentário `"DEV RESOURCE LINKS"`.

      [!code-html[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      Suas dependências devem ser comentadas como o exemplo a seguir:

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

   1. Para configurar o aplicativo para usar sua ID de registro de aplicativo do Azure ad `clientID` , altere o valor para usar a **ID do aplicativo** que você copiou na **etapa 3** quando [registrou o aplicativo para usar o Azure ad](#register-with-azure-ad). Se você tiver criado uma **URL de logout** no Azure AD, defina esse valor como `postLogoutRedirectUri` o valor.

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      Por exemplo:

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ```

   1. Quando você terminar de fazer modificações, salve *index. html*.

1. Publique o aplicativo Web em sua assinatura do Azure como um serviço Azure App.  

   > [!NOTE]
   > Várias opções nas capturas de tela que são mostradas nas etapas a seguir são preenchidas automaticamente com dados de sua assinatura do Azure. Pode levar alguns segundos para que cada painel seja completamente carregado.  

   1. Em Gerenciador de Soluções, clique com o botão direito do mouse no nó do projeto de site e selecione **publicar aplicativo Web**.  

      [![Visual Studio-selecione a opção Gerenciador de Soluções publicar aplicativo Web](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png#lightbox)

   1. Selecione **Iniciar** para começar a publicar seu aplicativo.

      [![Visual Studio – o painel de perfil de publicação](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png#lightbox)

   1. Selecione para publicar uma nova instância de serviço Azure App ou use uma existente.

      [![Selecionar uma instância de serviço de Azure App](media/tutorial-create-tsi-sample-spa/vs-publish-select-target.png)](media/tutorial-create-tsi-sample-spa/vs-publish-select-target.png#lightbox)

   1. Selecione a assinatura que você deseja usar para publicar o aplicativo. Selecione o projeto **TsiSpaApp** . Em seguida, selecione **OK**.

      [![Visual Studio – o painel publicar serviço de aplicativo de perfil](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png#lightbox)

   1. Selecione **publicar** para implantar o aplicativo Web.

      [![Visual Studio-a opção de publicação e a saída do log de publicação](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png#lightbox)

   1. Um log de publicação bem-sucedido aparece no painel de **saída** do Visual Studio. Quando a implantação for concluída, o Visual Studio abrirá o aplicativo Web em uma guia do navegador e solicitará a entrada. Após a entrada bem-sucedida, os controles de Time Series Insights são populados com dados.

   1. Navegue até seu aplicativo Web e faça logon para exibir os dados do Visual Time Series Insights renderizados.

      [![Examinar o aplicativo Web hospedado](media/tutorial-create-tsi-sample-spa/vs-publish-hosted-app.png)](media/tutorial-create-tsi-sample-spa/vs-publish-hosted-app.png#lightbox)

## <a name="troubleshoot"></a>Resolução de problemas  

Código de erro/condição | Descrição
---------------------| -----------
*AADSTS50011: Nenhum endereço de resposta está registrado para o aplicativo.* | O registro do Azure AD não tem uma propriedade **URI** de redirecionamento. Acesse**URIs** de redirecionamento de **autenticação** > para o registro do aplicativo do Azure AD. Verifique se o **URI** de redirecionamento que você tinha a opção de especificar na **etapa 2** ou **4** quando [registrou o aplicativo para usar o Azure ad](#register-with-azure-ad) está presente.
*AADSTS50011: A URL de resposta especificada na solicitação não corresponde às URLs de resposta configuradas para o aplicativo: '\<GUID de ID de aplicativo > '.* | O `postLogoutRedirectUri` especificado na **etapa 6. b** em [Compilar e publicar o aplicativo Web](#build-and-publish) deve corresponder ao valor especificado em**URIs** de redirecionamento de **autenticação** > em seu registro de aplicativo do Azure AD. |
O aplicativo Web é carregado, mas tem uma página de entrada sem estilo, somente texto, com um plano de fundo branco. | Verifique se os caminhos discutidos na **etapa 6** em [Compilar e publicar o aplicativo Web](#build-and-publish) estão corretos. Se a aplicação Web não conseguir localizar os ficheiros .css, a página não terá o estilo correto.

## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial cria vários serviços do Azure em execução. Se você não planeja concluir esta série de tutoriais, recomendamos que exclua todos os recursos para evitar incorrer em custos desnecessários.

No menu portal do Azure à esquerda:

1. Selecione **grupos de recursos**e, em seguida, selecione o grupo de recursos que você criou para o ambiente de time Series insights. Na parte superior da página, selecione **excluir grupo de recursos**, insira o nome do grupo de recursos e, em seguida, selecione **excluir**.
1. Selecione **grupos de recursos**e, em seguida, selecione o grupo de recursos que foi criado pelo acelerador de solução de simulação de dispositivo. Na parte superior da página, selecione **excluir grupo de recursos**, insira o nome do grupo de recursos e, em seguida, selecione **excluir**.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, você aprendeu sobre:

> [!div class="checklist"]
> * A conceção da aplicação
> * Como registrar seu aplicativo com o Azure AD
> * Como criar, publicar e testar a aplicação Web

Este tutorial se integra ao Azure AD e usa a identidade do usuário que está conectado para adquirir um token de acesso. Para saber como acessar a API de Time Series Insights usando a identidade de um aplicativo de serviço ou daemon, consulte este artigo:

> [!div class="nextstepaction"]
> [Autenticação e autorização para API do Azure Time Series Insights](time-series-insights-authentication-and-authorization.md)
