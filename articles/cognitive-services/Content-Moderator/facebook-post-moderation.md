---
title: 'Tutorial: Moderação de conteúdos do Facebook - Azure Content Moderator'
titlesuffix: Azure Cognitive Services
description: Neste tutorial, irá aprender a utilizar baseados em machine-learning Content Moderator para ajudar a moderado publicações do Facebook e comentários.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 751878f668ff24b1656ab504d6c120714269fab7
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57534878"
---
# <a name="tutorial-facebook-content-moderation-with-content-moderator"></a>Tutorial: Moderação de conteúdos do Facebook com o Content Moderator

Neste tutorial, irá aprender a utilizar baseados em machine-learning Content Moderator para ajudar a moderado publicações do Facebook e comentários.

Este tutorial explica como seguir estes passos:

1. Criar uma equipa do Content Moderator.
2. Criar Funções do Azure que escutam eventos HTTP do Content Moderator e do Facebook.
3. Criar uma Página e Aplicação do Facebook e ligá-la ao Content Moderator.

Quando terminarmos, o Facebook irá enviar o conteúdo publicado pelos visitantes para o Content Moderator. Com base nos limiares de correspondência, os fluxos de trabalho do Content Moderator publicam o conteúdo ou criam revisões na ferramenta de revisão. 

A figura seguinte mostra os blocos modulares da solução.

![Moderação de publicações do Facebook](images/tutorial-facebook-moderation.png)

## <a name="create-a-content-moderator-team"></a>Criar uma equipa do Content Moderator

Consulte a [Experimente o Content Moderator na web](quick-start.md) início rápido para inscrever-se o Content Moderator e criar uma equipa.

## <a name="configure-image-moderation-workflow-threshold"></a>Configurar o fluxo de trabalho de moderação de imagens (limiar)

Consulte a página [Fluxos de trabalho](review-tool-user-guide/workflows.md) para configurar um fluxo de trabalho de imagem personalizada (limiar). Tome nota do **nome** do fluxo de trabalho.

## <a name="3-configure-text-moderation-workflow-threshold"></a>3. Configurar o fluxo de trabalho de moderação de texto (limiar)

Utilize passos semelhantes aos da página [Fluxos de trabalho](review-tool-user-guide/workflows.md) para configurar um fluxo de trabalho e limiar de texto personalizado. Tome nota do **nome** do fluxo de trabalho.

![Configurar o Fluxo de Trabalho de Texto](images/text-workflow-configure.PNG)

Teste o fluxo de trabalho através do botão "Executar Fluxo de Trabalho".

![Testar o Fluxo de Trabalho de Texto](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Criar Funções do Azure

Inicie sessão no [Portal de Gestão do Azure](https://portal.azure.com/) para criar as suas Funções do Azure. Siga estes passos.

1. Crie uma Function App do Azure, tal como mostra a página [Funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal).
2. Abra a Function App que acabou de criar.
3. Na Aplicação, navegue até **Funcionalidades de plataforma -> Definições da aplicação**
4. Especifique as seguintes [definições da aplicação](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#settings):

> [!NOTE]
> O **cm: Região** deve ser o nome da região (sem espaços).
> Por exemplo, **westeurope**, não Europa Ocidental, **westcentralus**, não E.U.A. Centro-Oeste, etc.
>

| Definição da Aplicação | Descrição   | 
| -------------------- |-------------|
| cm:TeamId   | O TeamId do Content Moderator  | 
| cm:SubscriptionKey | A chave de subscrição do Content Moderator. Veja [Credenciais](review-tool-user-guide/credentials.md) | 
| cm:Region | O nome da região do Content Moderator, sem os espaços. Veja a nota anterior. |
| cm:ImageWorkflow | Nome do fluxo de trabalho a executar em Imagens |
| cm:TextWorkflow | Nome do fluxo de trabalho a executar em Texto |
| cm:CallbackEndpoint | URL da Function App CMListener que irá criar mais à frente neste guia |
| fb:VerificationToken | O token secreto, também utilizado para subscrever os eventos do feed do Facebook |
| fb:PageAccessToken | O token de acesso da Graph API do Facebook não expira e permite que a função Ocultar/Eliminar mensagens em seu nome. |

5. Crie uma nova função **HttpTrigger-CSharp** com o nome **FBListener**. Esta função recebe eventos do Facebook. Crie esta função, seguindo estes passos:

    1. Mantenha a página [Criação de Funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) aberta para referência.
    2. Clique no sinal de adição **+** para criar a nova função.
    3. Em vez dos modelos incorporados, escolha a opção **Criar a sua própria função/função personalizada**.
    4. Clique no mosaico que diz **HttpTrigger-CSharp**.
    5. Introduza o nome **FBListener**. O campo **Nível de Autorização** deve ser definido como **Função**.
    6. Clique em **Criar**.
    7. Substitua o conteúdo do ficheiro **run.csx** pelo conteúdo de [**FbListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FbListener/run.csx).

6. Crie uma nova função **HttpTrigger-CSharp** com o nome **CMListener**. Esta função recebe eventos do Content Moderator. Siga estes passos para criar esta função.

    1. Mantenha a página [Criação de Funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) aberta para referência.
    2. Clique no sinal de adição **+** para criar a nova função.
    3. Em vez dos modelos incorporados, escolha a opção **Criar a sua própria função/função personalizada**.
    4. Clique no mosaico que diz **HttpTrigger-CSharp**
    5. Introduza o nome **CMListener**. O campo **Nível de Autorização** deve ser definido como **Função**.
    6. Clique em **Criar**.
    7. Substitua o conteúdo do ficheiro **run.csx** pelo conteúdo de [**CMListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/CmListener/run.csx).

## <a name="configure-the-facebook-page-and-app"></a>Configurar a página e Aplicação do Facebook
1. Crie uma Aplicação do Facebook.

    1. Navegue até ao [site do Facebook para programadores](https://developers.facebook.com/)
    2. Clique em **My Apps** (As minhas Aplicações).
    3. Adicione uma Nova Aplicação.
    4. Selecione **Webhooks -> Get Started** (Introdução)
    5. Selecione **Page (Página) -> Subscribe to this topic (Subscrever este tópico)**
    6. Forneça o **FBListener Url** como o URL de chamada de retorno e o **Token de Verificação** que configurou em **Definições da Aplicação de Funções**
    7. Depois de subscrito, desloque o ecrã para baixo até ao feed e selecione **subscribe** (subscrever).

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

Já está!

A solução envia todas as imagens e texto publicados na sua página do Facebook para o Content Moderator. Os fluxos de trabalho que configurou anteriormente são invocados. O conteúdo que não passar nos critérios que definiu nos fluxos de trabalho resulta em revisões na ferramenta de revisão. O resto do conteúdo é publicado.

## <a name="license"></a>Licença

Todos os exemplos e SDKs dos Serviços Cognitivos da Microsoft estão licenciados com a Licença do MIT. Para obter mais detalhes, veja [LICENSE](https://microsoft.mit-license.org/) (LICENÇA).

## <a name="next-steps"></a>Passos Seguintes

1. [Veja uma demonstração (vídeo)](https://channel9.msdn.com/Events/Build/2017/T6033) desta solução no Microsoft Build 2017.
1. [O exemplo do Facebook no GitHub](https://github.com/MicrosoftContentModerator/samples-fbPageModeration)
1. https://docs.microsoft.com/azure/azure-functions/functions-create-github-webhook-triggered-function
2. https://ukimiawz.github.io/facebook/2015/08/12/webhook-facebook-subscriptions/
3. https://stackoverflow.com/questions/17197970/facebook-permanent-page-access-token
