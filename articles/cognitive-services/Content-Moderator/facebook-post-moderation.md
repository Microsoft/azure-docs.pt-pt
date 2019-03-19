---
title: 'Tutorial: Moderar conteúdo do Facebook - Content Moderator'
titlesuffix: Azure Cognitive Services
description: Neste tutorial, irá aprender a utilizar baseados em machine-learning Content Moderator para ajudar a moderado publicações do Facebook e comentários.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/18/2019
ms.author: pafarley
ms.openlocfilehash: 662eca2a727f3112f169ab8d669bf18c81700275
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57871033"
---
# <a name="tutorial-moderate-facebook-posts-and-commands-with-azure-content-moderator"></a>Tutorial: Moderado publicações do Facebook e comandos do Azure Content moderator

Neste tutorial, irá aprender a utilizar o Azure Content Moderator para ajudar a moderar as postagens e comentários numa página de Facebook. Facebook enviará o conteúdo publicado por visitantes para o serviço do Content Moderator. Em seguida, os fluxos de trabalho do Content Moderator serão publicar o conteúdo ou criar revisões dentro da ferramenta de revisão, consoante as classificações de conteúdo e os limiares. Consulte a [vídeo de demonstração do Build 2017](https://channel9.msdn.com/Events/Build/2017/T6033) para obter um exemplo de trabalho deste cenário.

Este tutorial mostrar-lhe como:

> [!div class="checklist"]
> * Criar uma equipa do Content Moderator.
> * Criar Funções do Azure que escutam eventos HTTP do Content Moderator e do Facebook.
> * Ligar a uma página do Facebook para Content Moderator através de uma aplicação do Facebook.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Este diagrama ilustra cada componente deste cenário:

![Diagrama do Content Moderator recebe informações do Facebook por meio de "FBListener" e enviar informações por "CMListener"](images/tutorial-facebook-moderation.png)

## <a name="prerequisites"></a>Pré-requisitos

- A chave de uma subscrição do Content Moderator. Siga as instruções em [criar uma conta dos serviços cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para subscrever o serviço do Content Moderator e obtenha a chave.
- R [conta do Facebook](https://www.facebook.com/).

## <a name="create-a-review-team"></a>Criar uma equipa de revisão

Consulte a [Experimente o Content Moderator na web](quick-start.md) início rápido para obter instruções sobre como inscrever-se a [ferramenta rever conteúdo do moderador](https://contentmoderator.cognitive.microsoft.com/) e criar uma equipa de revisão. Anote o **ID da equipa** valor na **credenciais** página.

## <a name="configure-image-moderation-workflow"></a>Configurar fluxo de trabalho de moderação de imagem

Consulte a [definir, teste e utilize fluxos de trabalho](review-tool-user-guide/workflows.md) guia para criar um fluxo de trabalho de imagem personalizada. Isso permitirá que o Content Moderator verificar as imagens no Facebook e enviar algumas para a ferramenta de revisão automaticamente. Tome nota do fluxo de trabalho **nome**.

## <a name="configure-text-moderation-workflow"></a>Configurar fluxo de trabalho de moderação de texto

Novamente, consulte a [definir, teste e utilize fluxos de trabalho](review-tool-user-guide/workflows.md) guia; neste momento, criar um fluxo de trabalho de texto personalizado. Isso permitirá que o moderador de conteúdo verificar automaticamente o conteúdo de texto. Tome nota do fluxo de trabalho **nome**.

![Configurar o Fluxo de Trabalho de Texto](images/text-workflow-configure.PNG)

Testar o seu fluxo de trabalho com o **executar o fluxo de trabalho** botão.

![Testar o Fluxo de Trabalho de Texto](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Criar Funções do Azure

Inicie sessão para o [Portal do Azure](https://portal.azure.com/) e siga estes passos:

1. Crie uma Function App do Azure, tal como mostra a página [Funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal).
2. Vá para a aplicação de funções recentemente criado.
3. Na aplicação, vá para o **funcionalidades de plataforma** separador e selecione **configurações de aplicativo**. Na **as configurações do aplicativo** secção da página seguinte, desloque para baixo da lista e clique em **Adicionar nova definição**. Adicionar os seguintes pares de chave/valor
    
    | Nome da definição de aplicação | valor   | 
    | -------------------- |-------------|
    | cm:TeamId   | O TeamId do Content Moderator  | 
    | cm:SubscriptionKey | A chave de subscrição do Content Moderator. Veja [Credenciais](review-tool-user-guide/credentials.md) | 
    | cm:Region | O nome da região do Content Moderator, sem os espaços. Veja a nota anterior. |
    | cm:ImageWorkflow | Nome do fluxo de trabalho a executar em Imagens |
    | cm:TextWorkflow | Nome do fluxo de trabalho a executar em Texto |
    | cm:CallbackEndpoint | URL da Function App CMListener que irá criar mais à frente neste guia |
    | fb:VerificationToken | O token secreto, também utilizado para subscrever os eventos do feed do Facebook |
    | fb:PageAccessToken | O token de acesso da Graph API do Facebook não expira e permite que a função Ocultar/Eliminar mensagens em seu nome. |

    Clique nas **guardar** botão na parte superior da página.

1. Utilizar o **+** botão no painel esquerdo, para abrir o novo painel de função.

    ![Painel de funções do Azure com o botão Adicionar função realçado.](images/new-function.png)

    Em seguida, clique em **+ nova função** na parte superior da página. Esta função recebe eventos do Facebook. Crie esta função, seguindo estes passos:

    1. Clique no mosaico que diz **acionador Http**.
    1. Introduza o nome **FBListener**. O campo **Nível de Autorização** deve ser definido como **Função**.
    1. Clique em **Criar**.
    1. Substitua os conteúdos do **csx** com o conteúdo de **FbListener/run.csx**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/FbListener/run.csx?range=1-160)]

1. Criar uma nova **acionador de Http** função chamada **CMListener**. Esta função recebe eventos do Content Moderator. Substitua os conteúdos do **csx** com o conteúdo de **CMListener/run.csx**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/CmListener/run.csx?range=1-106)]

---

## <a name="configure-the-facebook-page-and-app"></a>Configurar a página e Aplicação do Facebook
1. Crie uma Aplicação do Facebook.

    ![página de desenvolvedor do Facebook](images/facebook-developer-app.png)

    1. Navegue até ao [site do Facebook para programadores](https://developers.facebook.com/)
    2. Clique em **My Apps** (As minhas Aplicações).
    3. Adicione uma Nova Aplicação.
    1. der um nome
    1. Selecione **Webhooks -> o conjunto de cópia de segurança**
    1. Selecione **página** no menu pendente e selecione **subscrever este objeto**
    1. Forneça o **FBListener Url** como o URL de chamada de retorno e o **Token de Verificação** que configurou em **Definições da Aplicação de Funções**
    1. Depois de subscrito, desloque o ecrã para baixo até ao feed e selecione **subscribe** (subscrever).

2. Crie uma Página do Facebook.

    1. Navegue até ao [Facebook](https://www.facebook.com/bookmarks/pages) e crie uma **nova Página do Facebook**.
    2. Permita que a Aplicação do Facebook aceda a esta página, seguindo estes passos:
        1. Navegue até ao [Graph API Explorer](https://developers.facebook.com/tools/explorer/).
        2. Selecione **Application** (Aplicação).
        3. Selecione **Page Access Token** (Token de Acesso de Página) e envie um pedido **Get**.
        4. Clique no **ID de Página** na resposta.
        5. Agora, acrescente **/subscribed_apps** ao URL e envie um pedido **Get** (resposta vazia).
        6. Submeta um pedido **Post**. Obtém a resposta como **success: true**.

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
    
        | Chave | Value   | 
        | -------------------- |-------------|
        | appId   | Insira aqui o seu Identificador da Aplicação do Facebook  | 
        | appSecret | Insira aqui o seu segredo da Aplicação do Facebook | 
        | short_lived_token | Insira o token de acesso de utilizador de curta duração que gerou no passo anterior |
    4. Agora, execute as três APIs listadas na coleção: 
        1. Selecione **Generate Long-Lived Access Token** (Gerar Token de Acesso de Curta Duração) e clique em **Send** (Enviar).
        2. Selecione **Get User ID** (Obter ID de Utilizador) e clique em **Send** (Enviar).
        3. Selecione **Get Permanent Page Access Token** (Obter Token de Acesso de Página Permanente) e clique em **Send** (Enviar).
    5. Copie o valor **access_token** da resposta e atribua-o à definição da Aplicação **fb:PageAccessToken**.

A solução envia todas as imagens e texto publicados na sua página do Facebook para o Content Moderator. Em seguida, são chamados os fluxos de trabalho que configurou anteriormente. O conteúdo que não é transmitido aos critérios definidos nos fluxos de trabalho é passado para revisões dentro da ferramenta de revisão. O restante do conteúdo é publicado automaticamente.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, configurou um programa para analisar imagens dos produtos com o objetivo de marcá-los por tipo de produto e permitindo que uma equipa de revisão tomar decisões informadas sobre a moderação de conteúdos. Em seguida, saiba mais sobre os detalhes de moderação de imagens.

> [!div class="nextstepaction"]
> [Moderação de imagens](./image-moderation-api.md)
