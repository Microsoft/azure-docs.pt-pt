---
title: Criar uma função que se integra no Azure Logic Apps
description: Crie uma função de integração com Azure Logic Apps e Azure Cognitive Services. O fluxo de trabalho resultante categoriza os sentimentos de tweet envia notificações de e-mail.
author: craigshoemaker
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.topic: tutorial
ms.date: 04/10/2021
ms.author: cshoe
ms.custom: devx-track-csharp, mvc, cc996988-fb4f-47
ms.openlocfilehash: 3517835859de82117de07ad67cdf8027960ab777
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388690"
---
# <a name="tutorial-create-a-function-to-integrate-with-azure-logic-apps"></a>Tutorial: Criar uma função para integrar com Azure Logic Apps

As Funções do Azure integram-se com o Azure Logic Apps no Estruturador de Aplicações Lógicas. Esta integração permite-lhe utilizar o poder de computação de Funções em orquestrações com outros serviços Azure e terceiros.

Este tutorial mostra-lhe como criar um fluxo de trabalho para analisar a atividade do Twitter. À medida que os tweets são avaliados, o fluxo de trabalho envia notificações quando são detetados sentimentos positivos.

Neste tutorial, irá aprender a:

> [!div class="checklist"]
> * Criar um Recurso de API dos Serviços Cognitivos.
> * Criar uma função que categoriza sentimentos de tweet.
> * Criar uma aplicação lógica que se liga ao Twitter.
> * Adicionar a deteção de sentimento à aplicação lógica.
> * Ligar a aplicação lógica à função.
> * Enviar um e-mail com base na resposta da função.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do [Twitter](https://twitter.com/) ativa.
* Uma conta do [Outlook.com](https://outlook.com/) (para o envio de notificações).

> [!NOTE]
> Se quiser utilizar o conector do Gmail, apenas as contas de negócios da G-Suite podem utilizar este conector sem restrições em aplicações lógicas. Se tiver uma conta de consumo do Gmail, pode utilizar o conector do Gmail apenas com aplicações e serviços específicos aprovados pela Google, ou pode [criar uma aplicação de cliente da Google para utilizar para autenticação no seu conector Gmail.](/connectors/gmail/#authentication-and-bring-your-own-application) <br><br>Para obter mais informações, consulte [as políticas de segurança de dados e privacidade para conectores google em Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

## <a name="create-text-analytics-resource"></a>Criar recurso de Análise de Texto

As APIs dos Serviços Cognitivos estão disponíveis no Azure como recursos individuais. Utilize a API de Análise de Texto para detetar o sentimento dos tweets publicados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.

1. Em _Categorias_, selecione **IA + Machine Learning**

1. Em _Análise de Texto,_ selecione **Criar**.

1. Introduza os seguintes valores no ecrã _Criar Analítica de Texto._

    | Definição | Valor | Observações |
    | ------- | ----- | ------- |
    | Subscrição | O seu nome de subscrição Azure | |
    | Grupo de recursos | Criar um novo grupo de recursos chamado **tweet-sentiment-tutorial** | Mais tarde, elimina este grupo de recursos para remover todos os recursos criados durante este tutorial. |
    | Região | Selecione a região mais próxima de si | |
    | Name | **TweetSentimentApp** | |
    | Escalão de preço | Selecione **F0 grátis** | |

1. Selecione **Rever + criar**.

1. Selecione **Criar**.

1. Uma vez concluída a implementação, selecione **Ir para o Recurso**.

## <a name="get-text-analytics-settings"></a>Obtenha definições de Análise de Texto

Com o recurso Text Analytics criado, irá copiar algumas definições e reserve-as para utilização posterior.

1. Selecione **Chaves e Ponto final**.

1. Copiar **a tecla 1** clicando no ícone no final da caixa de entrada.

1. Cole o valor num editor de texto.

1. Copie o **Ponto Final** clicando no ícone no final da caixa de entrada.

1. Cole o valor num editor de texto.

## <a name="create-the-function-app"></a>Criar a aplicação de funções

1. A partir da caixa de pesquisa superior, procure e selecione **a aplicação Função**.

1. Selecione **Criar**.

1. Introduza os seguintes valores.

    | Definição | Valor sugerido | Observações |
    | ------- | ----- | ------- |
    | Subscrição | O seu nome de subscrição Azure | |
    | Grupo de recursos | **tweet-sentiment-tutorial** | Utilize o mesmo nome de grupo de recursos durante este tutorial. |
    | Nome da Aplicação de Funções | **TweetSentimentAPI** + um sufixo único | Os nomes das aplicações de funções são globalmente únicos. Os carateres válidos são `a-z` (não sensível a maiúsculas e minúsculas), `0-9` e `-`. |
    | Publicar | **Código** | |
    | Pilha de runtime | **.NET** | O código de função previsto para si está em C#. |
    | Versão | Selecione o número da versão mais recente | |
    | Região | Selecione a região mais próxima de si | |

1. Selecione **Rever + criar**.

1. Selecione **Criar**.

1. Uma vez concluída a implementação, selecione **Ir para o Recurso**.

## <a name="create-an-http-triggered-function"></a>Criar uma função acionada por HTTP  

1. A partir do menu esquerdo da janela _Funções,_ selecione **Funções**.

1. **Selecione Adicione** no menu superior e introduza os seguintes valores.

    | Definição | Valor | Observações |
    | ------- | ----- | ------- |
    | Ambiente de programação | **Desenvolver em portal** | |
    | Modelo | **HTTP Trigger** | |
    | Nova Função | **TweetSentimentFunction** | Este é o nome da sua função. |
    | Nível de autorização | **Function** | |

1. Selecione o botão **Adicionar.**

1. Selecione o botão **Código + Teste.**

1. Cole o seguinte código na janela do editor de código.

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
    
        string requestBody = String.Empty;
        using (StreamReader streamReader =  new  StreamReader(req.Body))
        {
            requestBody = await streamReader.ReadToEndAsync();
        }
    
        dynamic score = JsonConvert.DeserializeObject(requestBody);
        string value = "Positive";
    
        if(score < .3)
        {
            value = "Negative";
        }
        else if (score < .6) 
        {
            value = "Neutral";
        }
    
        return requestBody != null
            ? (ActionResult)new OkObjectResult(value)
           : new BadRequestObjectResult("Pass a sentiment score in the request body.");
    }
    ```

    Uma pontuação de sentimento é passada para a função, que devolve um nome de categoria para o valor.

1. Selecione o botão **Guardar** na barra de ferramentas para guardar as alterações.

    > [!NOTE]
    > Para testar a função, selecione **Teste/Executar** a partir do menu superior. No separador _Entrada,_ introduza um valor na caixa de `0.9` entrada do _Corpo_ e, em seguida, selecione **Executar**. Verifique se o valor _positivo_ é devolvido na caixa de conteúdo de _resposta HTTP_ na secção _Saída._

Em seguida, crie uma app lógica que se integre com Azure Functions, Twitter e a API de Serviços Cognitivos.

## <a name="create-a-logic-app"></a>Criar uma aplicação lógica

1. A partir da caixa de pesquisa superior, procure e selecione **Aplicações Lógicas.**

1. Selecione **Adicionar**.

1. Selecione **Consumo** e introduza os seguintes valores.

    | Definição | Valor sugerido |
    | ------- | --------------- |
    | Subscrição | O seu nome de subscrição Azure |
    | Grupo de recursos | **tweet-sentiment-tutorial** |
    | Nome de aplicativo lógico | **TweetSentimentApp** |
    | Region | Selecione a região mais próxima, de preferência a mesma região que selecionou em etapas anteriores. |

    Aceite valores predefinidos para todas as outras definições.

1. Selecione **Rever + criar**.

1. Selecione **Criar**.

1. Uma vez concluída a implementação, selecione **Ir para o Recurso**.

1. Selecione o **botão Blank Logic App.**

    :::image type="content" source="media/functions-twitter-email/blank-logic-app-button.png" alt-text="Botão de aplicativo de lógica em branco":::

1. Selecione o botão **Guardar** na barra de ferramentas para evitar o seu progresso.

Agora pode utilizar o Design de Aplicações Lógicas para adicionar serviços e gatilhos à sua aplicação.

## <a name="connect-to-twitter"></a>Ligar ao Twitter

Crie uma ligação ao Twitter para que a sua aplicação possa fazer sondagens para novos tweets.

1. Procure no **Twitter** na caixa de pesquisa superior.

1. Selecione o ícone do **Twitter.**

1. Selecione o acionador **Quando um novo tweet é publicado**.

1. Introduza os seguintes valores para configurar a ligação.

    | Definição |  Valor |
    | ------- | ---------------- |
    | Nome da ligação | **MyTwitterConnection** |
    | Tipo de Autenticação | **Utilize a aplicação partilhada padrão** |

1. Selecione **Iniciar sessão**.

1. Siga as instruções na janela pop-up para concluir a sessão no Twitter.

1. Em seguida, insira os seguintes valores na caixa _de tweet quando um novo tweet é publicado._

    | Definição | Valor |
    | ------- | ----- |
    | Texto da pesquisa | **#my-twitter-tutorial** |
    | Como o forno quer verificar se há itens? | **15** na caixa de texto, e <br> **Minuto** no dropdown |

1. Selecione o botão **Guardar** na barra de ferramentas para evitar o seu progresso.

Em seguida, conecte-se à análise de texto para detetar o sentimento dos tweets recolhidos.

## <a name="add-text-analytics-sentiment-detection"></a>Adicionar deteção de sentimento de Análise de Texto

1. Selecione **Novo passo**.

1. Procure **por Text Analytics** na caixa de pesquisa.

1. Selecione o ícone **Text Analytics.**

1. Selecione **Detetar Sentimento** e introduza os seguintes valores.

    | Definição | Valor |
    | ------- | ----- |
    | Nome da ligação | **TextAnalyticsConnection** |
    | Chave de conta | Cole na chave da conta Text Analytics que tenha reservado anteriormente. |
    | Site URL | Cole no ponto final text Analytics que tenha reservado anteriormente. |

1. Selecione **Criar**.

1. Clique dentro da _caixa de parâmetros Novo_ Add e verifique a caixa ao lado de **documentos** que aparecem no pop-up.

1. Clique no Id dos _documentos - 1_ caixa de texto para abrir o pop-up de conteúdo dinâmico.

1. Na caixa de pesquisa dinâmica de _conteúdos,_ procure **o id** e clique no **id do Tweet.**

1. Clique dentro dos _documentos Texto - 1_ caixa de texto para abrir o pop-up de conteúdo dinâmico.

1. Na caixa de pesquisa dinâmica de _conteúdos,_ procure **por texto** e clique no **texto do Tweet**.

1. Em **Escolher uma ação**, escreva **Análise de Texto** e clique na ação **Detetar sentimento**.

1. Selecione o botão **Guardar** na barra de ferramentas para evitar o seu progresso.

A caixa _deteto sentimento_ deve parecer a seguinte imagem.

:::image type="content" source="media/functions-twitter-email/detect-sentiment.png" alt-text="Detetar definições de sentimento":::

## <a name="connect-sentiment-output-to-function-endpoint"></a>Conecte a saída do sentimento ao ponto final da função

1. Selecione **Novo passo**.

1. Procure **funções Azure** na caixa de pesquisa.

1. Selecione o ícone **Azure Functions.**

1. Procure o nome da sua função na caixa de pesquisa. Se seguiu a orientação acima, o nome da sua função começa com **TweetSentimentAPI**.

1. Selecione o ícone da função.

1. Selecione o **item TweetSentimentFunction.**

1. Clique dentro da caixa _''Escolha's Feeling_ e selecione o item _deteção_ de **sentimento** a partir da janela pop-up.

1. Selecione o botão **Guardar** na barra de ferramentas para evitar o seu progresso.

## <a name="add-conditional-step"></a>Adicionar passo condicional

1. Selecione o **botão Adicionar um** botão de ação.

1. Clique dentro da caixa _de Controlo_ e procure e selecione **Controlo** na janela pop-up.

1. Selecione **Condição**.

1. Clique dentro da caixa _de valor Escolha_ e selecione o item _TweetSentimentFunction_ **Body** a partir da janela pop-up.

1. Introduza **Positivo** na _Caixa de Valor_ Escolher.

1. Selecione o botão **Guardar** na barra de ferramentas para evitar o seu progresso.

## <a name="add-email-notifications"></a>Adicionar notificações por e-mail

1. Sob a caixa _True,_ selecione o **botão de ação Adicionar.**

1. Procure e selecione **Office 365 Outlook** na caixa de texto.

1. Procurar **por enviar** e selecionar Enviar por email o artigo Texto( S.A. ' Pesse) para enviar e selecionar **Enviar por email** o artigo Texto( S.A. ' Pes

1. Selecione o botão **Iniciar** sção.

1. Siga as indicações na janela pop-up para concluir a sessão no Office 365 Outlook.

1. Insira o seu endereço de e-mail na caixa _'Para'_

1. Clique dentro da caixa _'Assunto'_ e clique no item **'Corpo'** no _TweetSentimentFunction_. Se o item _'Corpo'_ não estiver na lista, clique no link **Ver mais** para expandir a lista de opções.

1. Depois do artigo _do Corpo_ no _Sujeito,_ introduza o texto **Tweet a partir de:**.

1. Depois do _Tweet a partir de:_ texto, clique novamente na caixa e selecione o nome de **utilizador** da lista de opções quando um novo tweet _é publicado._

1. Clique dentro da caixa _Body_ e selecione **o texto tweet** sob a lista de opções quando um novo tweet é _publicado_ lista de opções. Se o item _de texto do Tweet_ não estiver na lista, clique no link Ver **mais** para expandir a lista de opções.

1. Selecione o botão **Guardar** na barra de ferramentas para evitar o seu progresso.

A caixa de e-mail deve agora parecer com esta imagem.

:::image type="content" source="media/functions-twitter-email/email-notification.png" alt-text="Notificação por e-mail":::

## <a name="run-the-workflow"></a>Executar o fluxo de trabalho

1. Na sua conta de Twitter, tweet o seguinte texto: **Estou a gostar de #my-twitter-tutorial**.

1. Volte ao Designer de Aplicações Lógicas e selecione o botão **Executar.**

1. Consulte o seu e-mail para obter uma mensagem do fluxo de trabalho.

## <a name="clean-up-resources"></a>Limpar os recursos

Para limpar todos os serviços e contas da Azure criados durante este tutorial, elimine o grupo de recursos.

1. Procure **grupos de recursos** na caixa de pesquisa superior.

1. Selecione o **tweet-sentiment-tutorial**.

1. Selecione **Eliminar grupo de recursos**

1. Insira **tweet-sentiment-tutorial** na caixa de texto.

1. Selecione o botão **Eliminar**.

Opcionalmente, pode querer voltar à sua conta de Twitter e eliminar quaisquer tweets de teste do seu feed.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma API sem servidor com as Funções do Azure](functions-create-serverless-api.md)
