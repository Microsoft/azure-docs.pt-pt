---
title: 'Tutorial: conteúdo moderado do Facebook – Content Moderator'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você aprenderá a usar o Content Moderator baseado em aprendizado de máquina para ajudar postagens moderadas do Facebook e comentários.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 5aa4cc24484a4ba1da608da9676ade492db35b6c
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72936015"
---
# <a name="tutorial-moderate-facebook-posts-and-commands-with-azure-content-moderator"></a>Tutorial: postagens moderadas do Facebook e comandos com o Azure Content Moderator

Neste tutorial, você aprenderá a usar o Azure Content Moderator para ajudar a moderar as postagens e os comentários em uma página do Facebook. O Facebook enviará o conteúdo postado pelos visitantes para o serviço de Content Moderator. Em seguida, seus fluxos de trabalho de Content Moderator publicarão o conteúdo ou criarão revisões dentro da ferramenta de revisão, dependendo das pontuações e dos limites de conteúdo. Consulte o [vídeo de demonstração de Build 2017](https://channel9.msdn.com/Events/Build/2017/T6033) para obter um exemplo de trabalho desse cenário.

Este tutorial mostrar-lhe como:

> [!div class="checklist"]
> * Criar uma equipa do Content Moderator.
> * Criar Funções do Azure que escutam eventos HTTP do Content Moderator e do Facebook.
> * Vincule uma página do Facebook a Content Moderator usando um aplicativo do Facebook.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Este diagrama ilustra cada componente deste cenário:

![Diagrama de Content Moderator recebimento de informações do Facebook por meio de "FBListener" e envio de informações por meio de "CMListener"](images/tutorial-facebook-moderation.png)

> [!IMPORTANT]
> Em 2018, o Facebook implementou um habilitação mais estrito de aplicativos do Facebook. Não será possível concluir as etapas deste tutorial se seu aplicativo não tiver sido revisado e aprovado pela equipe de revisão do Facebook.

## <a name="prerequisites"></a>Pré-requisitos

- A chave de uma subscrição do Content Moderator. Siga as instruções em [criar uma conta de serviços cognitivas](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar o serviço de Content moderator e obter sua chave.
- Uma [conta do Facebook](https://www.facebook.com/).

## <a name="create-a-review-team"></a>Criar uma equipe de revisão

Consulte o guia de início rápido do [Content moderator na Web](quick-start.md) para obter instruções sobre como se inscrever na [ferramenta de revisão de Content moderator](https://contentmoderator.cognitive.microsoft.com/) e criar uma equipe de revisão. Anote o valor da **ID da equipe** na página **credenciais** .

## <a name="configure-image-moderation-workflow"></a>Configurar fluxo de trabalho de moderação de imagem

Consulte o guia [definir, testar e usar fluxos de trabalho](review-tool-user-guide/workflows.md) para criar um fluxo de trabalho de imagem personalizada. Content Moderator usará esse fluxo de trabalho para verificar automaticamente as imagens no Facebook e enviará algumas para a ferramenta de revisão. Anote o **nome**do fluxo de trabalho.

## <a name="configure-text-moderation-workflow"></a>Configurar fluxo de trabalho de moderação de texto

Novamente, consulte o guia [definir, testar e usar fluxos de trabalho](review-tool-user-guide/workflows.md) ; desta vez, crie um fluxo de trabalho de texto personalizado. Content Moderator usará esse fluxo de trabalho para verificar automaticamente o conteúdo do texto. Anote o **nome**do fluxo de trabalho.

![Configurar o Fluxo de Trabalho de Texto](images/text-workflow-configure.PNG)

Teste seu fluxo de trabalho usando o botão **executar fluxo de trabalho** .

![Testar o Fluxo de Trabalho de Texto](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Criar Funções do Azure

Entre no [portal do Azure](https://portal.azure.com/) e siga estas etapas:

1. Crie uma Function App do Azure, tal como mostra a página [Funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal).
1. Vá para o Aplicativo de funções recém-criado.
1. No aplicativo, vá para a guia **recursos da plataforma** e selecione **configuração**. Na seção **configurações do aplicativo** da página seguinte, selecione **nova configuração de aplicativo** para adicionar os seguintes pares de chave/valor:
    
    | Nome da configuração do aplicativo | valor   | 
    | -------------------- |-------------|
    | cm:TeamId   | O TeamId do Content Moderator  | 
    | cm:SubscriptionKey | A chave de subscrição do Content Moderator. Veja [Credenciais](review-tool-user-guide/credentials.md) |
    | cm:Region | O nome da região do Content Moderator, sem os espaços. Você pode encontrá-lo no campo **local** da guia **visão geral** do recurso do Azure.|
    | cm:ImageWorkflow | Nome do fluxo de trabalho a executar em Imagens |
    | cm:TextWorkflow | Nome do fluxo de trabalho a executar em Texto |
    | cm:CallbackEndpoint | URL para o Aplicativo de funções CMListener que será criado posteriormente neste guia |
    | fb:VerificationToken | Um token secreto que você cria, usado para assinar os eventos do feed do Facebook |
    | fb:PageAccessToken | O token de acesso da Graph API do Facebook não expira e permite que a função Ocultar/Eliminar mensagens em seu nome. Você receberá esse token em uma etapa posterior. |

    Clique no botão **salvar** na parte superior da página.

1. Volte para a guia **recursos da plataforma** . Use o botão **+** no painel esquerdo para abrir o painel **nova função** . A função que você está prestes a criar receberá eventos do Facebook.

    ![Azure Functions painel com o botão Adicionar função realçado.](images/new-function.png)

    1. Clique no bloco que diz **gatilho http**.
    1. Introduza o nome **FBListener**. O campo **Nível de Autorização** deve ser definido como **Função**.
    1. Clique em **Criar**.
    1. Substitua o conteúdo de **Run. CSX** pelo conteúdo de **FbListener/Run. CSX**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/FbListener/run.csx?range=1-154)]

1. Crie uma nova função de **gatilho http** chamada **CMListener**. Esta função recebe eventos do Content Moderator. Substitua o conteúdo de **Run. CSX** pelo conteúdo de **CMListener/Run. CSX**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/CmListener/run.csx?range=1-110)]

---

## <a name="configure-the-facebook-page-and-app"></a>Configurar a página e Aplicação do Facebook

1. Crie uma Aplicação do Facebook.

    ![página de desenvolvedor do Facebook](images/facebook-developer-app.png)

    1. Navegue até ao [site do Facebook para programadores](https://developers.facebook.com/)
    1. Clique em **My Apps** (As minhas Aplicações).
    1. Adicione uma Nova Aplicação.
    1. Nomeie-o como algo
    1. Selecionar **WebHooks-configuração de >**
    1. Selecione a **página** no menu suspenso e selecione **assinar este objeto**
    1. Forneça o **FBListener Url** como o URL de chamada de retorno e o **Token de Verificação** que configurou em **Definições da Aplicação de Funções**
    1. Depois de subscrito, desloque o ecrã para baixo até ao feed e selecione **subscribe** (subscrever).
    1. Clique no botão **testar** da linha de **feed** para enviar uma mensagem de teste para a função FBListener do Azure e, em seguida, pressione o botão **Enviar para meu servidor** . Você deve ver a solicitação sendo recebida em seu FBListener.

1. Crie uma Página do Facebook.

    > [!IMPORTANT]
    > Em 2018, o Facebook implementou um habilitação mais estrito de aplicativos do Facebook. Você não poderá executar as seções 2, 3 e 4 se seu aplicativo não tiver sido revisado e aprovado pela equipe de revisão do Facebook.

    1. Navegue até ao [Facebook](https://www.facebook.com/bookmarks/pages) e crie uma **nova Página do Facebook**.
    1. Permita que a Aplicação do Facebook aceda a esta página, seguindo estes passos:
        1. Navegue até ao [Graph API Explorer](https://developers.facebook.com/tools/explorer/).
        1. Selecione **Application** (Aplicação).
        1. Selecione **Page Access Token** (Token de Acesso de Página) e envie um pedido **Get**.
        1. Clique no **ID de Página** na resposta.
        1. Agora, acrescente **/subscribed_apps** ao URL e envie um pedido **Get** (resposta vazia).
        1. Submeta um pedido **Post**. Obtém a resposta como **success: true**.

3. Crie um token de acesso da Graph API que não expira.

    1. Navegue até ao [Graph API Explorer](https://developers.facebook.com/tools/explorer/).
    2. Selecione a opção **Application** (Aplicação).
    3. Selecione a opção **Get User Access Token** (Obter Token de Acesso de Utilizador).
    4. Em **Select Permissions** (Selecionar Permissões), selecione as opções **manage_pages** e **publish_pages**.
    5. Vamos utilizar o **token de acesso** (Token de Curta Duração) no próximo passo.

4. Vamos utilizar o Postman nos passos seguintes.

    1. Abra o **Postman** (ou obtenha-o [aqui](https://www.getpostman.com/)).
    2. Importe estes dois ficheiros:
        1. [Postman Collection](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/Facebook%20Permanant%20Page%20Access%20Token.postman_collection.json)
        2. [Postman Environment](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FB%20Page%20Access%20Token%20Environment.postman_environment.json)       
    3. Atualize estas variáveis de ambiente:
    
        | Chave | Valor   | 
        | -------------------- |-------------|
        | appId   | Insira aqui o seu Identificador da Aplicação do Facebook  | 
        | appSecret | Insira aqui o seu segredo da Aplicação do Facebook | 
        | short_lived_token | Insira o token de acesso de utilizador de curta duração que gerou no passo anterior |
    4. Agora, execute as três APIs listadas na coleção: 
        1. Selecione **Generate Long-Lived Access Token** (Gerar Token de Acesso de Curta Duração) e clique em **Send** (Enviar).
        2. Selecione **Get User ID** (Obter ID de Utilizador) e clique em **Send** (Enviar).
        3. Selecione **Get Permanent Page Access Token** (Obter Token de Acesso de Página Permanente) e clique em **Send** (Enviar).
    5. Copie o valor **access_token** da resposta e atribua-o à definição da Aplicação **fb:PageAccessToken**.

A solução envia todas as imagens e texto publicados na sua página do Facebook para o Content Moderator. Em seguida, os fluxos de trabalho que você configurou anteriormente são invocados. O conteúdo que não passa seus critérios definidos nos fluxos de trabalho é passado para revisões na ferramenta de revisão. O restante do conteúdo é publicado automaticamente.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você configura um programa para analisar imagens de produtos com a finalidade de programá-las por tipo de produto e permitir que uma equipe de revisão tome decisões informadas sobre a moderação do conteúdo. Em seguida, saiba mais sobre os detalhes da moderação da imagem.

> [!div class="nextstepaction"]
> [Moderação de imagens](./image-moderation-api.md)
