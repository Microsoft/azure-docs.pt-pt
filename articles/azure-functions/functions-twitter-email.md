---
title: Criar uma função que se integra no Azure Logic Apps
description: Crie uma função que se integra no Azure Logic Apps e nos Serviços Cognitivos do Azure para categorizar sentimentos de tweets e enviar notificações se esses sentimentos forem negativos.
author: craigshoemaker
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.topic: tutorial
ms.date: 04/27/2020
ms.author: cshoe
ms.custom: devx-track-csharp, mvc, cc996988-fb4f-47
ms.openlocfilehash: 5750597d7d4d372be975aa64ce8db11859791da2
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98674322"
---
# <a name="create-a-function-that-integrates-with-azure-logic-apps"></a>Criar uma função que se integra no Azure Logic Apps

As Funções do Azure integram-se com o Azure Logic Apps no Estruturador de Aplicações Lógicas. Esta integração permite-lhe utilizar o poder de computação das Funções em orquestrações com outros serviços do Azure e de terceiros. 

Este tutorial mostra-lhe como usar funções Azure com aplicações lógicas e serviços cognitivos em Azure para executar análise de sentimento a partir de posts do Twitter. Uma função de gatilho HTTP classifica os tweets como verdes, amarelos ou vermelhos com base na pontuação de sentimento. É enviado um e-mail quando é detetado um sentimento negativo. 

![imagem dos dois primeiros passos da aplicação no Estruturador de Aplicações Lógicas](media/functions-twitter-email/00-logic-app-overview.png)

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Criar um Recurso de API dos Serviços Cognitivos.
> * Criar uma função que categoriza sentimentos de tweet.
> * Criar uma aplicação lógica que se liga ao Twitter.
> * Adicionar a deteção de sentimento à aplicação lógica. 
> * Ligar a aplicação lógica à função.
> * Enviar um e-mail com base na resposta da função.

## <a name="prerequisites"></a>Pré-requisitos

+ Uma conta do [Twitter](https://twitter.com/) ativa. 
+ Uma conta do [Outlook.com](https://outlook.com/) (para o envio de notificações).

> [!NOTE]
> Se quiser utilizar o conector do Gmail, apenas as contas de negócios da G-Suite podem utilizar este conector sem restrições em aplicações lógicas. Se tiver uma conta de consumo do Gmail, pode utilizar o conector do Gmail apenas com aplicações e serviços específicos aprovados pela Google, ou pode [criar uma aplicação de cliente da Google para utilizar para autenticação no seu conector Gmail.](/connectors/gmail/#authentication-and-bring-your-own-application) Para obter mais informações, consulte [as políticas de segurança de dados e privacidade para conectores google em Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

+ Este artigo utiliza como ponto de partida os recursos criados na função [Criar a primeira função a partir do portal do Azure](./functions-get-started.md).
Se ainda não o fez, conclua estes passos agora para criar a sua aplicação de funções.

## <a name="create-a-cognitive-services-resource"></a>Criar um recurso dos Serviços Cognitivos

As APIs dos Serviços Cognitivos estão disponíveis no Azure como recursos individuais. Utilize a API de Análise de Texto para detetar o sentimento dos tweets que estão a ser monitorizados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Clique em **Criar um recurso** no canto superior esquerdo do portal Azure.

3. Clique **em AI + Machine Learning** Text  >  **Analytics**. Em seguida, utilize as definições conforme especificado na tabela para criar o recurso.

    ![Página Criar recurso dos Serviços Cognitivos](media/functions-twitter-email/01-create-text-analytics.png)

    | Definição      |  Valor sugerido   | Descrição                                        |
    | --- | --- | --- |
    | **Nome** | MyCognitiveServicesAccnt | Escolha um nome de conta exclusivo. |
    | **Localização** | E.U.A. Oeste | Utilize a localização mais próxima de si. |
    | **Escalão de preço** | F0 | Comece com o escalão mais baixo. Se ficar sem chamadas, aumente para um escalão superior.|
    | **Grupo de recursos** | myResourceGroup | Utilize o mesmo grupo de recursos para todos os serviços neste tutorial.|

4. Clique em **Criar** para criar o recurso. 

5. Clique **em visão geral** e copie o valor do Ponto **Final** para um editor de texto. Este valor serve para criar uma ligação para a API dos Serviços Cognitivos.

    ![Definições dos Serviços Cognitivos](media/functions-twitter-email/02-cognitive-services.png)

6. Na coluna de navegação esquerda, clique em **Chaves** e, em seguida, copie o valor da **Chave 1** e defina-o à parte num editor de texto. A chave serve para ligar a aplicação lógica à API dos Serviços Cognitivos. 
 
    ![Chaves de Serviços Cognitivos](media/functions-twitter-email/03-cognitive-serviecs-keys.png)

## <a name="create-the-function-app"></a>Criar a aplicação de funções

O Azure Functions fornece uma ótima maneira de descarregar tarefas de processamento num fluxo de trabalho de aplicações lógicas. Este tutorial usa uma função de gatilho HTTP para processar pontuações de sentimento de tweet dos Serviços Cognitivos e devolver um valor de categoria.  

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-an-http-trigger-function"></a>Criar uma função de gatilho HTTP  

1. A partir do menu esquerdo da janela **Funções,** selecione **Funções** e selecione **Adicione** no menu superior.

2. A partir da janela **Nova Função,** selecione **HTTP trigger**.

    ![Escolha a função de gatilho HTTP](./media/functions-twitter-email/06-function-http-trigger.png)

3. A partir da página **Nova Função,** selecione **Criar Função**.

4. Na sua nova função de gatilho HTTP, selecione **Código + Teste** a partir do menu esquerdo, substitua o conteúdo do ficheiro pelo seguinte código `run.csx` e, em seguida, selecione **Guardar**:

    ```csharp
    #r "Newtonsoft.Json"
    
    using System;
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        string category = "GREEN";
    
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        log.LogInformation(string.Format("The sentiment score received is '{0}'.", requestBody));
    
        double score = Convert.ToDouble(requestBody);
    
        if(score < .3)
        {
            category = "RED";
        }
        else if (score < .6) 
        {
            category = "YELLOW";
        }
    
        return requestBody != null
            ? (ActionResult)new OkObjectResult(category)
            : new BadRequestObjectResult("Please pass a value on the query string or in the request body");
    }
    ```

    Este código de função devolve uma categoria de cor com base na classificação de sentimento recebida no pedido. 

5. Para testar a função, selecione **Teste** a partir do menu superior. No separador **Entrada,** introduza um valor `0.2` de **corpo** e, em seguida, selecione **Executar**. Um valor de **RED** é devolvido no **conteúdo de resposta HTTP** no **separador Saída.** 

    :::image type="content" source="./media/functions-twitter-email/07-function-test.png" alt-text="Definir as definições de procuração":::

Agora, tem uma função que categoriza classificações de sentimentos. Em seguida, vai criar uma aplicação lógica que integra a sua função com o Twitter e a API dos Serviços Cognitivos. 

## <a name="create-a-logic-app"></a>Criar uma aplicação lógica   

1. No portal Azure, clique no botão **Criar um** botão de recurso encontrado no canto superior esquerdo do portal Azure.

2. Clique **na Web** Logic  >  **App**.
 
3. Em seguida, escreva um valor para **Nome**, como `TweetSentiment`, e utilize as definições conforme especificado na tabela.

    ![Criar aplicação lógica no portal do Azure](./media/functions-twitter-email/08-logic-app-create.png)

    | Definição      |  Valor sugerido   | Descrição                                        |
    | ----------------- | ------------ | ------------- |
    | **Nome** | TweetSentiment | Escolha um nome adequado para a sua aplicação. |
    | **Grupo de recursos** | myResourceGroup | Escolha o mesmo grupo de recursos que utilizou anteriormente. |
    | **Localização** | E.U.A. Leste | Escolha uma localização perto de si. |    

4. Depois de introduzir os valores de definições apropriadas, clique em **Criar** para criar a sua aplicação lógica. 

5. Depois de a aplicação ter sido criada, clique na aplicação lógica nova que está afixada ao dashboard. Em seguida, no Designer de Aplicações Lógicas, desloque-se para baixo e clique no modelo **Aplicação Lógica em Branco**. 

    ![Modelo Aplicação Lógica em Branco](media/functions-twitter-email/09-logic-app-create-blank.png)

Agora, pode utilizar o Estruturador de Aplicações Lógicas para adicionar serviços e acionadores à aplicação.

## <a name="connect-to-twitter"></a>Ligar ao Twitter

Em primeiro lugar, crie uma ligação para a sua conta do Twitter. A aplicação lógica consulta tweets, que acionam a execução da aplicação.

1. No estruturador, clique no serviço **Twitter** e clique no acionador **When a new tweet is posted** (Quando é publicado um tweet novo). Inicie sessão na sua conta do Twitter e autorize a Aplicação Lógica a utilizá-la.

2. Utilize as definições de acionador do Twitter, conforme especificado na tabela. 

    ![Definições do conector do Twitter](media/functions-twitter-email/10-tweet-settings.png)

    | Definição      |  Valor sugerido   | Descrição                                        |
    | ----------------- | ------------ | ------------- |
    | **Texto da pesquisa** | #Azure | Utilize um hashtag que seja suficientemente popular para gerar novos tweets no intervalo escolhido. Se estiver a utilizar o escalão Gratuito e a hashtag for demasiado popular, pode esgotar rapidamente a totalidade da quota de transações da sua API dos Serviços Cognitivos. |
    | **Intervalo** | 15 | O tempo decorrido entre pedidos do Twitter, em unidades de frequência. |
    | **Frequência** | Minuto | A unidade de frequência utilizada para consultar o Twitter.  |

3.  Clique em **Guardar** para ligar à sua conta do Twitter. 

A sua aplicação está agora ligada ao Twitter. Em seguida, vai ligar à análise de texto para detetar o sentimento dos tweets recolhidos.

## <a name="add-sentiment-detection"></a>Adicionar a deteção de sentimentos

1. Clique em **New step** (Novo passo) e então, **Add an action** (Adicionar uma ação).

2. Em **Escolher uma ação**, escreva **Análise de Texto** e clique na ação **Detetar sentimento**.
    
    ![Screenshot que mostra a secção "Escolha uma ação" com "Text Analytics" na caixa de pesquisa e a ação "Detetar sentimento" selecionada. ](media/functions-twitter-email/11-detect-sentiment.png)

3. Escreva um nome de ligação, como `MyCognitiveServicesConnection`, cole a chave da API dos Serviços Cognitivos e o ponto final dos Serviços Cognitivos que definir à parte num editor de texto, e clique em **Criar**.

    ![Novo passo e Adicionar uma ação](media/functions-twitter-email/12-connection-settings.png)

4. Em seguida, insira o **texto do Tweet** na caixa de texto e, em seguida, clique em Novo **Passo**.

    ![Defina o texto a analisar](media/functions-twitter-email/13-analyze-tweet-text.png)

Agora que a deteção de sentimentos está configurada, pode adicionar uma ligação à função que consome a saída da classificação do sentimento.

## <a name="connect-sentiment-output-to-your-function"></a>Ligar a saída do sentimento à função

1. No Design de Aplicações Lógicas, clique em **Novo passo**  >  **Adicione uma ação,** filtre nas **funções Azure** e clique em **Escolher uma função Azure**.

    ![Detetar Sentimento](media/functions-twitter-email/14-azure-functions.png)
  
4. Selecione a aplicação de funções que criou anteriormente.

    ![Screenshot que mostra a secção "Escolha uma ação" com uma aplicação de função selecionada.](media/functions-twitter-email/15-select-function.png)

5. Selecione a função que criou para este tutorial.

    ![Selecionar função](media/functions-twitter-email/16-select-function.png)

4. No **corpo do Pedido**, clique em **Classificação** e em **Guardar**.

    ![Resultado](media/functions-twitter-email/17-function-input-score.png)

Agora, a função é acionada quando é enviada uma classificação de sentimento a partir da aplicação lógica. A função devolve uma categoria codificada com cores à aplicação lógica. Em seguida, adicione uma notificação de e-mail que é enviada quando a função devolver o valor de sentimento **RED** (vermelho). 

## <a name="add-email-notifications"></a>Adicionar notificações por e-mail

A última parte do fluxo de trabalho é acionar um e-mail quando o sentimento é classificado como _RED_. Este artigo utiliza um conector Outlook.com. Pode seguir passos semelhantes para utilizar um conector do Gmail ou do Outlook do Office 365.   

1. No Designer de Aplicações Lógicas, clique em **Novo passo**  >  **Adicione uma condição**. 

    ![Adicione uma condição à aplicação lógica.](media/functions-twitter-email/18-add-condition.png)

2. Clique em **Escolher um valor** e clique em **Corpo**. Selecione **é igual ao**, clique em **Escolher um valor**, escreva `RED` e clique em **Guardar**. 

    ![Escolha uma ação para a condição.](media/functions-twitter-email/19-condition-settings.png)    

3. Em **IF TRUE**, clique em **Adicionar uma ação**, procure por `outlook.com`, clique em **Send an email** (Enviar e-mail) e inicie sessão na sua conta do Outlook.com.

    ![Screenshot que mostra a secção "IF TRUE" com "outlook.com" inserida na caixa de pesquisa e a ação "Enviar um e-mail" selecionada.](media/functions-twitter-email/20-add-outlook.png)

    > [!NOTE]
    > Se não tiver uma conta do Outlook.com, pode escolher outro conector, como o Gmail ou Outlook do Office 365

4. Na ação **Send an email**, utilize as definições de e-mail, conforme especificado na tabela. 

    ![Configure o e-mail para a ação “send an email”.](media/functions-twitter-email/21-configure-email.png)
    
| Definição      |  Valor sugerido   | Descrição  |
| ----------------- | ------------ | ------------- |
| **Para** | Escreva o seu endereço de e-mail | O endereço de e-mail que recebe a notificação. |
| **Assunto** | Sentimento negativo de tweet detetado  | A linha de assunto da notificação de e-mail.  |
| **Corpo** | Texto do tweet, Localização | Clique nos parâmetros **Tweet text** (Texto do tweet) e **Location** (Localização). |

1. Clique em **Guardar**.

Agora que o fluxo de trabalho está concluído, pode ativar a aplicação lógica e ver a função em funcionamento.

## <a name="test-the-workflow"></a>Testar o fluxo de trabalho

1. No Estruturador de Aplicações Lógicas, clique em **Executar** para iniciar a aplicação.

2. Na coluna esquerda, clique em **Descrição Geral** para ver o estado da aplicação lógica. 
 
    ![Estado de execução da aplicação lógica](media/functions-twitter-email/22-execution-history.png)

3. (Opcional) Clique numa das execuções para ver detalhes da mesma.

4. Aceda à sua função, veja os registos e confirme que os valores de sentimentos foram recebidos e processados.
 
    ![Ver registos de funções](media/functions-twitter-email/sent.png)

5. Se for detetado um sentimento potencialmente negativo, receberá um e-mail. Se não tiver recebido uma mensagem de e-mail, pode alterar o código da função para devolver sempre RED:

    ```csharp
    return (ActionResult)new OkObjectResult("RED");
    ```

    Depois de ter verificado as notificações por e-mail, reverta para o código original:

    ```csharp
    return requestBody != null
        ? (ActionResult)new OkObjectResult(category)
        : new BadRequestObjectResult("Please pass a value on the query string or in the request body");
    ```

    > [!IMPORTANT]
    > Depois de concluir este tutorial, deve desativar a aplicação lógica. Ao desativar a aplicação, evita ser cobrado por execuções e esgotar as transações na sua API dos Serviços Cognitivos.

Agora já viu como é fácil integrar funções num fluxo de trabalho de Apps Lógicas.

## <a name="disable-the-logic-app"></a>Desativar a aplicação lógica

Para desativar a aplicação lógica, clique em **Descrição Geral** e clique em **Desativar**, na parte superior do ecrã. Desativar a aplicação impede que esta seja executada e incorra em custos, sem ser eliminada.

![Registo de funções](media/functions-twitter-email/disable-logic-app.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um Recurso de API dos Serviços Cognitivos.
> * Criar uma função que categoriza sentimentos de tweet.
> * Criar uma aplicação lógica que se liga ao Twitter.
> * Adicionar a deteção de sentimento à aplicação lógica. 
> * Ligar a aplicação lógica à função.
> * Enviar um e-mail com base na resposta da função.

Avance para o tutorial seguinte para aprender a criar uma API sem servidor para a função.

> [!div class="nextstepaction"] 
> [Criar uma API sem servidor com as Funções do Azure](functions-create-serverless-api.md)

Para saber mais sobre as aplicações lógicas, veja [Azure Logic Apps](../logic-apps/logic-apps-overview.md).