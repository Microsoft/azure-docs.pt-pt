---
title: 'Tutorial: Criar uma aplicação web de página única do Azure Time Series Insights | Documentos da Microsoft'
description: Saiba como criar uma aplicação Web de página única que consulta e apresenta dados a partir de um ambiente TSI.
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 06/14/2018
ms.author: anshan
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: a9baf36221c99ece0703e2caa3f3e6c3b9364480
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58312211"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>Tutorial: Criar uma aplicação Web de página única do Azure Time Series Insights

Este tutorial orienta-o no processo de criação da sua própria aplicação Web de página única (SPA) para aceder aos dados TSI, modelada de acordo com o [exemplo de aplicação TSI (Time Series Insights)](https://insights.timeseries.azure.com/clientsample). Neste tutorial, ficará a conhecer:

> [!div class="checklist"]
> * A conceção da aplicação
> * Como registar a aplicação com o Azure Active Directory (AD)
> * Como criar, publicar e testar a aplicação Web 

## <a name="prerequisites"></a>Pré-requisitos

Inscreva-se numa [Subscrição gratuita do Azure](https://azure.microsoft.com/free/), se ainda não tiver uma. 

Também terá de instalar o Visual Studio, se ainda não o fez. Para este tutorial, pode [transferir/instalar a versão de Comunidade gratuita ou uma versão de avaliação gratuita](https://www.visualstudio.com/downloads/).

## <a name="application-design-overview"></a>Descrição geral da conceção da aplicação

Conforme mencionado, o exemplo de aplicação TSI fornece a base para a estrutura e o código utilizados neste tutorial. O código inclui a utilização da biblioteca JavaScript do Cliente TSI. A biblioteca do Cliente TSI fornece uma abstração para duas categorias principais de API:

- **Métodos de invólucro para chamar as APIs de consulta do TSI**: APIs REST que permitem a consultar dados TSI usando expressões baseados em JSON. Os métodos estão organizados no espaço de nomes `TsiClient.server` da biblioteca.
- **Métodos para criar e Popular os vários tipos de controles de criação de gráficos**: Métodos que são utilizados para visualizar os dados TSI numa página da web. Os métodos estão organizados no espaço de nomes `TsiClient.ux` da biblioteca.

Este tutorial também irá utilizar os dados do ambiente TSI do exemplo de aplicação. Para obter detalhes sobre a estrutura do exemplo de aplicação TSI e a respetiva utilização da biblioteca de Clientes TSI, consulte o tutorial [Explorar a biblioteca de clientes JavaScript do Azure Time Series Insights](tutorial-explore-js-client-lib.md).

## <a name="register-the-application-with-azure-ad"></a>Registar a aplicação com o Azure AD 

Antes de criar a aplicação, tem de registá-la com o Azure AD. O registo fornece a configuração de identidade para uma aplicação, permitindo-lhe utilizar suporte OAuth para o início de sessão único. O OAuth requer que os SPAs utilizem a concessão de autorização "implícita", que deverá atualizar no manifesto de aplicação. Um manifesto de aplicação é uma representação JSON de configuração da identidade da aplicação. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com a sua conta de subscrição do Azure.  
1. Selecione o recurso **Azure Active Directory** no painel esquerdo, em seguida, **Registos de aplicação**, em seguida, **+ Novo registo de aplicação**:  
   
   ![Portal do Azure Registo de aplicação no Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png)

1. Na página **Criar**, preencha os parâmetros necessários:
   
   Parâmetro|Descrição
   ---|---
   **Nome** | Forneça um nome de registo significativo.  
   **Tipo de Aplicação** | Uma vez que está a criar uma aplicação Web SPA, deixe como “Aplicação Web/API”.
   **URL de início de sessão** | Introduza o URL para a página homepage /início de sessão da aplicação. Uma vez que a aplicação será alojada no serviço de aplicações do Azure (mais tarde), tem de utilizar um URL dentro de "https:\//azurewebsites.net" domínio. Neste exemplo, o nome baseia-se no nome do registo.

   Quando terminar, clique em **Criar** para criar o novo registo de aplicação.

   ![Portal do Azure Registo de aplicação no Azure AD - criação](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png)

1. As aplicações de recurso fornecem APIs REST para utilização por outras aplicações, e também são registadas com o Azure AD. As APIs fornecem acesso granular/protegido a aplicações de cliente, ao exporem os “âmbitos”. Dado que a sua aplicação irá chamar a API do “Azure Time Series Insights”, precisa de especificar a API e o âmbito, para o qual a permissão será pedida/concedida no runtime. Selecione **Definições**, em seguida, **Permissões necessárias**, em seguida, **+ Adicionar**:

   ![Portal do Azure Adicionar permissões do Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png)

1. Na página **Adicionar acesso à API**, clique em **1 Selecionar uma API** para especificar a API do TSI. Na página **Selecionar uma API**, introduza "azure time" no campo de pesquisa. Em seguida, selecione a API do “Azure Time Series Insights” na lista de resultados e clique em **Selecionar**: 

   ![Portal do Azure Adicionar permissões do Azure AD - API](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png)

1. Agora, especifique um âmbito na API. Novamente, na página **Adicionar acesso à API**, clique em **2 Selecionar permissões**. Na página **Ativar Acesso**, selecione o âmbito "Aceder ao serviço Azure Time Series Insights". Clique em **Selecionar**, que irá reencaminhá-lo para a página **Adicionar acesso à API**,onde irá clicar em **Concluído**:

   ![Portal do Azure Adicionar permissões do Azure AD - âmbito](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png)

1. Quando regressar à página **Permissões necessárias**, tenha em atenção que a API do "Azure Time Series Insights" está agora listada. Terá precisará de permissão de pré-consentimento para que a aplicação aceda à API e ao âmbito, para todos os utilizadores. Clique no botão **Conceder permissões** na parte superior e selecione **Sim**:

   ![Portal do Azure Permissões necessárias do Azure AD - consentimento](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png)

1. Como mencionado anteriormente, terá também de atualizar o manifesto de aplicação. Clique no nome da aplicação no endereço para regressar à página **Aplicação registada**. Selecione **Manifesto**, altere a propriedade `oauth2AllowImplicitFlow` para `true`, em seguida, clique em **Guardar**:

   ![Portal do Azure Atualizar manifesto do Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png)

1. Por fim, clique no endereço para regressar novamente à página **Aplicação registada** e copie as propriedades do URL da **Home page** e o **ID da aplicação** para a sua aplicação. Irá utilizar estas propriedades num passo posterior:

   ![Portal do Azure Propriedades do Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png)

## <a name="build-and-publish-the-web-application"></a>Criar e publicar a aplicação Web

1. Crie um diretório para armazenar os ficheiros do projeto da aplicação. Em seguida, navegue para cada um dos seguintes URLs, clique na ligação "Não processada" na área superior direita da página e em "Guardar como" para o diretório de projeto:

   > [!NOTE]
   > Consoante o browser, poderá ter de corrigir a extensão do ficheiro (para HTML ou CSS) antes de guardar o ficheiro.

   - **Index.html** HTML e JavaScript para a página https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html
   - **sampleStyles.css:** Folha de estilos CSS: https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css
    
1. Abra e inicie sessão no Visual Studio para criar um projeto para a aplicação Web. No menu **Ficheiro**, selecione a opção **Abrir**, **Site**. Na caixa de diálogo **Abrir Site**, selecione o diretório de trabalho onde armazenou os ficheiros HTML e CSS e, em seguida, clique em **Abrir**:

   ![VS - Ficheiro abrir site](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)

1. Abra o **Explorador de Soluções** no menu **Ver** do Visual Studio. Deverá ver a nova solução, com um projeto do site (ícone de globo), que contém os ficheiros HTML e CSS:

   ![VS - Explorador de soluções nova solução](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)

1. Antes de poder publicar a aplicação, tem de atualizar as partes do código JavaScript em **index.html**: 

   a. Em primeiro lugar, altere os caminhos para a referência dos ficheiros JavaScript e folhas de estilo no elemento `<head>`. Abra o ficheiro **index.html** na sua solução Visual Studio e localize as seguintes linhas de código JavaScript. Anule o comentário das três linhas em "PROD RESOURCE LINKS" e comente as três linhas em "DEV RESOURCE LINKS":
   
      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      O código alterado deve ter um aspeto semelhante ao seguinte exemplo:

      ```javascript
      <!-- PROD RESOURCE LINKS -->
      <link rel="stylesheet" type="text/css" href="sampleStyles.css"></link>
      <script src="https://unpkg.com/tsiclient@1.1.4/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="https://unpkg.com/tsiclient@1.1.4/tsiclient.css"></link>

      <!-- DEV RESOURCE LINKS -->
      <!-- <link rel="stylesheet" type="text/css" href="pages/samples/sampleStyles.css"></link>
      <script src="dist/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="dist/tsiclient.css"></link> -->
      ```

   b. Em seguida, altere a lógica de token de acesso para utilizar o novo registo de aplicação do Azure AD. Altere as seguintes variáveis `clientID` e `postLogoutRedirectUri` respetivamente, para utilizar o ID da Aplicação e o URL da Home Page que copiou no passo n.º 9 da secção [Registar a aplicação com o Azure AD](#register-the-application-with-azure-ad):

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      O código alterado deve ter um aspeto semelhante ao seguinte exemplo:

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ``` 

   c. Guarde o **index.html** quando tiver terminado a edição.

1. Agora publique a aplicação Web na sua subscrição do Azure como um Serviço de Aplicações do Azure:  

   > [!NOTE]
   > Vários campos das caixas de diálogo seguintes são preenchidos com os dados da sua subscrição do Azure. Como tal, pode demorar alguns segundos para cada caixa de diálogo carregar completamente, antes de poder continuar.  

   a. Clique com o botão direito do rato no nó do projeto do site no **Explorador de Soluções**e selecione **Publicar Aplicação Web**:  

      ![VS - Explorador de soluções publicar aplicação Web](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)

   b. Selecione **Serviço de Aplicações do Microsoft Azure** para criar um destino de publicação:  

      ![VS - Perfil de publicação](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)  

   c. Selecione a subscrição que gostaria de utilizar para publicar a aplicação e, em seguida, clique em "Novo": 

      ![VS - Perfil de publicação - serviço de aplicações](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)  

   d. Dê à caixa de diálogo **Criar Serviço de Aplicações** alguns segundos para que carregue todos os campos e, em seguida, modifique os seguintes campos:
   
      Campo | Descrição
      ---|---
      **Nome da Aplicação** | Mude para o nome de registo de aplicações do Azure AD que utilizou no passo n.º 3, [Registar a aplicação com o Azure AD](#register-the-application-with-azure-ad). 
      **Grupo de Recursos** | Ao utilizar o botão **Novo...**, mude para que corresponda ao campo **Nome da Aplicação**.
      **App Service Plan** (Plano do Serviço de Aplicações) | Ao utilizar o botão **Novo...**, mude para que corresponda ao campo **Nome da Aplicação**.

      Quando terminar, clique em **Criar**. O botão**Exportar** na parte inferior esquerda é substituído por “A implementar:” durante vários segundos, enquanto os recursos do Azure são criados. Assim que os recursos estiverem criados, será reencaminhado para a caixa de diálogo anterior. 

      ![VS - Perfil de publicação - adicionar novo serviço de aplicações](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service-create.png)  

   e. Quando regressar à caixa de diálogo **Publicar**, certifique-se de que **Método de publicação** está definido como "Implementação Web". Altere também o **URL de Destino** para utilizar `https` em vez de `http`, para fazer corresponder o registo de aplicações do Azure AD. Em seguida, clique em "Publicar" para implementar a aplicação Web:  

      ![VS - Publicar aplicação Web – publicar o serviço de aplicações](media/tutorial-create-tsi-sample-spa/vs-publish-publish.png)  

   f. Deverá ver um registo de publicação com êxito na janela **Saída** do Visual Studio. Após a conclusão da implementação, o Visual Studio também abrirá a aplicação Web num separador do browser, pedindo para iniciar sessão. Após o início de sessão com êxito, verá todos os controlos TSI preenchidos com os dados:  

      [![VS - Publicar aplicação web – publicar saída de registo](media/tutorial-create-tsi-sample-spa/vs-publish-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-output.png#lightbox)

      ![Aplicação de SPA do TSI - início de sessão](media/tutorial-create-tsi-sample-spa/tsispaapp-azurewebsites-net.png)  

## <a name="troubleshooting"></a>Resolução de problemas  

Código de erro/condição | Descrição
---------------------| -----------
*AADSTS50011: Nenhum endereço de resposta está registado para a aplicação.* | O registo do Azure AD tem a propriedade "URL de resposta" em falta. Vá para a página **Definições** / **URLs de Resposta** do seu registo de aplicações do Azure AD. Certifique-se de que o URL de **Início de Sessão** especificado no passo n.º 3 de [Registar a aplicação com o Azure AD](#register-the-application-with-azure-ad) está presente. 
*AADSTS50011: A resposta do url especificado no pedido não coincide com os urls de resposta configurados para a aplicação: "<Application ID GUID>'.* | O `postLogoutRedirectUri` especificado no passo n.º 4.b de [Criar e publicar a aplicação Web](#build-and-publish-the-web-application) tem de corresponder ao valor especificado na propriedade **Definições** / **URLs de Resposta** do seu registo de aplicação do AD. Certifique-se também de que altera o **URL de destino** para utilizar `https`, de acordo com o passo n.º 5.e de [Criar e publicar a aplicação Web](#build-and-publish-the-web-application).
A aplicação carrega, mas apresenta uma página de início de sessão só com texto e sem estilo, com um fundo branco. | Certifique-se de que os caminhos abordados no passo n.º 4.a de [Criar e publicar a aplicação web](#build-and-publish-the-web-application) estão corretos. Se a aplicação Web não conseguir localizar os ficheiros .css, a página não terá o estilo correto.

## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial cria vários serviços do Azure em execução. Se não planeia concluir esta série de tutoriais, recomendamos que elimine todos os recursos para evitar incorrer em custos desnecessários. 

No menu esquerdo do portal do Azure:

1. Clique no ícone **Grupos de recursos** e, em seguida, selecione o grupo de recursos que criou para o Ambiente do TSI. Na parte superior da página, clique em **Eliminar grupo de recursos**, escreva o nome do grupo de recurso e, em seguida, clique em **Eliminar**. 
1. Clique no ícone **Grupos de recursos** e, em seguida, selecione o grupo de recursos que foi criado pelo acelerador de soluções da simulação de dispositivos. Na parte superior da página, clique em **Eliminar grupo de recursos**, escreva o nome do grupo de recurso e, em seguida, clique em **Eliminar**. 

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * A conceção da aplicação
> * Como registar a aplicação com o Azure Active Directory (AD)
> * Como criar, publicar e testar a aplicação Web 

Este tutorial integra-se com o Azure AD, utilizando a identidade do utilizador com sessão iniciada para adquirir um token de acesso. Para saber como aceder à API do TSI utilizando a identidade de uma aplicação de serviço/daemon, veja:

> [!div class="nextstepaction"]
> [Autenticação e autorização para API do Azure Time Series Insights](time-series-insights-authentication-and-authorization.md)
