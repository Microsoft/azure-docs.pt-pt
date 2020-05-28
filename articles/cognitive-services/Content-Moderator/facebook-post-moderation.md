---
title: 'Tutorial: Conteúdo moderado do Facebook - Moderador de Conteúdo'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você aprenderá a usar o Moderador de Conteúdo baseado em machine-learning para ajudar a moderar publicações e comentários do Facebook.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: pafarley
ms.openlocfilehash: a9a5fe2eed0c0e6cf990183d321a71a7fabfbbee
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84118642"
---
# <a name="tutorial-moderate-facebook-posts-and-commands-with-azure-content-moderator"></a>Tutorial: Publicações e comandos moderados do Facebook com Moderador de Conteúdo Azure

Neste tutorial, você aprenderá a usar o Moderador de Conteúdo Azure para ajudar a moderar as publicações e comentários numa página do Facebook. O Facebook enviará o conteúdo publicado pelos visitantes para o serviço Moderador de Conteúdos. Em seguida, os seus fluxos de trabalho moderadores de conteúdo publicarão o conteúdo ou criarão avaliações dentro da ferramenta Review, dependendo das pontuações e limiares de conteúdo. Veja o [vídeo de demonstração build 2017](https://channel9.msdn.com/Events/Build/2017/T6033) para um exemplo de trabalho deste cenário.

Este tutorial mostrar-lhe como:

> [!div class="checklist"]
> * Criar uma equipa do Content Moderator.
> * Criar Funções do Azure que escutam eventos HTTP do Content Moderator e do Facebook.
> * Ligue uma página do Facebook ao Moderador de Conteúdo usando uma aplicação do Facebook.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Este diagrama ilustra cada componente deste cenário:

![Diagrama de Moderador de Conteúdo recebendo informações do Facebook através de "FBListener" e enviando informações através de "CMListener"](images/tutorial-facebook-moderation.png)

> [!IMPORTANT]
> Em 2018, o Facebook implementou uma política de verificação mais rigorosa para apps do Facebook. Não poderá completar os passos deste tutorial se a sua aplicação não tiver sido revista e aprovada pela equipa de revisão do Facebook.

## <a name="prerequisites"></a>Pré-requisitos

- A chave de uma subscrição do Content Moderator. Siga as instruções na [conta Create a Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para subscrever o serviço moderador de conteúdo e obter a sua chave.
- Uma [conta no Facebook.](https://www.facebook.com/)

## <a name="create-a-review-team"></a>Criar uma equipa de revisão

Consulte o Moderador de [Conteúdo experimente na web](quick-start.md) quickstart para obter instruções sobre como se inscrever na ferramenta De revisão de moderador de [conteúdo](https://contentmoderator.cognitive.microsoft.com/) e criar uma equipa de revisão. Tome nota do valor de ID da **equipa** na página **credenciais.**

## <a name="configure-image-moderation-workflow"></a>Configure fluxo de trabalho de moderação de imagem

Consulte o guia [Dedefinição, teste e utilize fluxos de trabalho](review-tool-user-guide/workflows.md) para criar um fluxo de trabalho de imagem personalizado. O Moderador de Conteúdo utilizará este fluxo de trabalho para verificar automaticamente as imagens no Facebook e enviar algumas para a ferramenta Review. Tome nota do **nome**do fluxo de trabalho .

## <a name="configure-text-moderation-workflow"></a>Configure fluxo de trabalho de moderação de texto

Mais uma vez, consulte o guia [Definir, teste e utilização de fluxos de trabalho;](review-tool-user-guide/workflows.md) desta vez, crie um fluxo de trabalho de texto personalizado. O Moderador de Conteúdo utilizará este fluxo de trabalho para verificar automaticamente o conteúdo do texto. Tome nota do **nome**do fluxo de trabalho .

![Configurar o Fluxo de Trabalho de Texto](images/text-workflow-configure.PNG)

Teste o seu fluxo de trabalho utilizando o botão **executar workflow.**

![Testar o Fluxo de Trabalho de Texto](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Criar Funções do Azure

Inscreva-se no [portal Azure](https://portal.azure.com/) e siga estes passos:

1. Crie uma Function App do Azure, tal como mostra a página [Funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal).
1. Vá à recém-criada App de Funções.
1. Dentro da App, vá ao separador **de funcionalidades** da Plataforma e selecione **Configuração**. Na secção de **definições** de Aplicação da página seguinte, selecione **nova definição** de aplicação para adicionar os seguintes pares chave/valor:
    
    | Nome de definição de aplicativo | valor   | 
    | -------------------- |-------------|
    | `cm:TeamId`   | O TeamId do Content Moderator  | 
    | `cm:SubscriptionKey` | A chave de subscrição do Content Moderator. Veja [Credenciais](review-tool-user-guide/credentials.md) |
    | `cm:Region` | O nome da região do Content Moderator, sem os espaços. Pode encontrar este nome no campo **de localização** do separador **Overview** do seu recurso Azure.|
    | `cm:ImageWorkflow` | Nome do fluxo de trabalho a executar em Imagens |
    | `cm:TextWorkflow` | Nome do fluxo de trabalho a executar em Texto |
    | `cm:CallbackEndpoint` | Url para a App de Função CMListener que irá criar mais tarde neste guia |
    | `fb:VerificationToken` | Um símbolo secreto que cria, usado para subscrever os eventos de feed do Facebook |
    | `fb:PageAccessToken` | O token de acesso da Graph API do Facebook não expira e permite que a função Ocultar/Eliminar mensagens em seu nome. Receberá este símbolo mais tarde. |

    Clique no botão **Guardar** na parte superior da página.

1. Volte para o separador de recursos da **Plataforma.** Utilize o botão no painel esquerdo para levantar o painel de **+** **funções New.** A função que está prestes a criar receberá eventos do Facebook.

    ![As funções azure painel com o botão Função Adicionar realçado.](images/new-function.png)

    1. Clique no azulejo que diz **http trigger**.
    1. Introduza o nome **FBListener**. O campo **Nível de Autorização** deve ser definido como **Função**.
    1. Clique em **Criar**.
    1. Substitua o conteúdo do **run.csx** pelo conteúdo de **FbListener/run.csx**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/FbListener/run.csx?range=1-154)]

1. Crie uma nova função **de gatilho http** chamada **CMListener**. Esta função recebe eventos do Content Moderator. Substitua o conteúdo do **run.csx** pelo conteúdo da **CMListener/run.csx**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/CmListener/run.csx?range=1-110)]

---

## <a name="configure-the-facebook-page-and-app"></a>Configurar a página e Aplicação do Facebook

1. Crie uma Aplicação do Facebook.

    ![página de desenvolvedor de facebook](images/facebook-developer-app.png)

    1. Navegue até ao [site do Facebook para programadores](https://developers.facebook.com/)
    1. Clique em **My Apps** (As minhas Aplicações).
    1. Adicione uma Nova Aplicação.
    1. nomeá-lo algo
    1. Selecione **Webhooks -> Configuração**
    1. Selecione **Página** no menu dropdown e selecione **Subscrever este objeto**
    1. Forneça o **FBListener Url** como o URL de chamada de retorno e o **Token de Verificação** que configurou em **Definições da Aplicação de Funções**
    1. Depois de subscrito, desloque o ecrã para baixo até ao feed e selecione **subscribe** (subscrever).
    1. Clique no botão **Teste** da linha de **alimentação** para enviar uma mensagem de teste para a sua função FBListener Azure e, em seguida, clique no botão **Enviar para o Meu Servidor.** Deve ver o pedido recebido no seu FBListener.

1. Crie uma Página do Facebook.

    > [!IMPORTANT]
    > Em 2018, o Facebook implementou uma verificação mais rigorosa das aplicações do Facebook. Não poderá executar as secções 2, 3 e 4 se a sua aplicação não tiver sido revista e aprovada pela equipa de revisão do Facebook.

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

A solução envia todas as imagens e texto publicados na sua página do Facebook para o Content Moderator. Em seguida, os fluxos de trabalho que configuraste anteriormente são invocados. O conteúdo que não passa os seus critérios definidos nos fluxos de trabalho é passado para avaliações dentro da ferramenta de revisão. O resto do conteúdo é publicado automaticamente.

## <a name="next-steps"></a>Próximos passos

Neste tutorial, você criou um programa para analisar imagens do produto com o propósito de marcá-las por tipo de produto e permitir que uma equipa de revisão tome decisões informadas sobre moderação de conteúdo. Em seguida, saiba mais sobre os detalhes da moderação da imagem.

> [!div class="nextstepaction"]
> [Moderação de imagens](./image-moderation-api.md)
