---
title: Use aplicativo web - Personalizer
description: Personalize uma aplicação web C# .NET com um loop Personalizer para fornecer o conteúdo correto a um utilizador com base em ações (com funcionalidades) e funcionalidades de contexto.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: tutorial
ms.date: 06/10/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: e06d191573219df44631f6ffaee86f895166de57
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777263"
---
# <a name="tutorial-add-personalizer-to-a-net-web-app"></a>Tutorial: Adicione Personalizer a uma aplicação web .NET

Personalize uma aplicação web C# .NET com um loop Personalizer para fornecer o conteúdo correto a um utilizador com base em ações (com funcionalidades) e funcionalidades de contexto.

**Neste tutorial, ficará a saber como:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Configurar a chave personalização e o ponto final
> * Coletar funcionalidades
> * Ligue para APIs de classificação e recompensa
> * Exibir ação superior, designada como _rewardActionId_



## <a name="select-the-best-content-for-a-web-app"></a>Selecione o melhor conteúdo para uma aplicação web

Uma aplicação web deve usar o Personalizer quando há uma lista de _ações_ (algum tipo de conteúdo) na página web que precisa de ser personalizada para um único item superior (rewardActionId) para exibir. Exemplos de listas de ação incluem artigos de notícias, locais de colocação de botões e escolhas de palavras para nomes de produtos.

Envia a lista de ações, juntamente com funcionalidades de contexto, para o ciclo Personalizer. O personalização seleciona a melhor ação e, em seguida, a sua aplicação web exibe essa ação.

Neste tutorial, as ações são tipos de comida:

* massa
* Gelado
* suco
* salada
* pipocas
* café
* sopa

Para ajudar o Personaler a aprender sobre as suas ações, envie ambas _as ações com funcionalidades_ e _funcionalidades de contexto_ com cada pedido de API do Rank.

Uma **característica** do modelo é a informação sobre a ação ou contexto que pode ser agregado (agrupado) entre membros da sua base de utilizadores de aplicações web. Uma funcionalidade _não é_ individualmente específica (como um ID do utilizador) ou altamente específica (por exemplo, uma hora exata do dia).

### <a name="actions-with-features"></a>Ações com características

Cada ação (item de conteúdo) tem características para ajudar a distinguir o item alimentar.

As funcionalidades não são configuradas como parte da configuração do loop no portal Azure. Em vez disso, são enviados como um objeto JSON com cada chamada API rank. Isto permite flexibilidade para que as ações e as suas características cresçam, mudem e encolhem ao longo do tempo, o que permite ao Personaler seguir tendências.

```csharp
 /// <summary>
  /// Creates personalizer actions feature list.
  /// </summary>
  /// <returns>List of actions for personalizer.</returns>
  private IList<RankableAction> GetActions()
  {
      IList<RankableAction> actions = new List<RankableAction>
      {
          new RankableAction
          {
              Id = "pasta",
              Features =
              new List<object>() { new { taste = "savory", spiceLevel = "medium" }, new { nutritionLevel = 5, cuisine = "italian" } }
          },

          new RankableAction
          {
              Id = "ice cream",
              Features =
              new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionalLevel = 2 } }
          },

          new RankableAction
          {
              Id = "juice",
              Features =
              new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionLevel = 5 }, new { drink = true } }
          },

          new RankableAction
          {
              Id = "salad",
              Features =
              new List<object>() { new { taste = "sour", spiceLevel = "low" }, new { nutritionLevel = 8 } }
          },

          new RankableAction
          {
              Id = "popcorn",
              Features =
              new List<object>() { new { taste = "salty", spiceLevel = "none" }, new { nutritionLevel = 3 } }
          },

          new RankableAction
          {
              Id = "coffee",
              Features =
              new List<object>() { new { taste = "bitter", spiceLevel = "none" }, new { nutritionLevel = 3 }, new { drink = true } }
          },

          new RankableAction
          {
              Id = "soup",
              Features =
              new List<object>() { new { taste = "sour", spiceLevel = "high" }, new { nutritionLevel =  7} }
          }
      };

      return actions;
  }
```


## <a name="context-features"></a>Características de contexto

As funcionalidades de contexto ajudam o Personalizador a compreender o contexto das ações. O contexto desta aplicação da amostra inclui:

* hora do dia - manhã, tarde, noite, noite
* preferência do utilizador pelo gosto - salgado, doce, amargo, azedo ou salgado
* contexto do navegador - agente utilizador, localização geográfica, remetente

```csharp
/// <summary>
/// Get users time of the day context.
/// </summary>
/// <returns>Time of day feature selected by the user.</returns>
private string GetUsersTimeOfDay()
{
    Random rnd = new Random();
    string[] timeOfDayFeatures = new string[] { "morning", "noon", "afternoon", "evening", "night", "midnight" };
    int timeIndex = rnd.Next(timeOfDayFeatures.Length);
    return timeOfDayFeatures[timeIndex];
}

/// <summary>
/// Gets user food preference.
/// </summary>
/// <returns>Food taste feature selected by the user.</returns>
private string GetUsersTastePreference()
{
    Random rnd = new Random();
    string[] tasteFeatures = new string[] { "salty", "bitter", "sour", "savory", "sweet" };
    int tasteIndex = rnd.Next(tasteFeatures.Length);
    return tasteFeatures[tasteIndex];
}
```

## <a name="how-does-the-web-app-use-personalizer"></a>Como é que a aplicação web usa o Personalizer?

A aplicação web usa Personalizer para selecionar a melhor ação da lista de escolhas de alimentos. Fá-lo enviando as seguintes informações com cada chamada da API do Rank:
* **ações** com as suas características, tais como `taste` e `spiceLevel`
* funcionalidades de **contexto** como `time` o dia, a preferência do utilizador `taste` e as informações do agente de utilizador do navegador, e as funcionalidades de contexto
* **ações para excluir,** como o sumo
* **eventId**, que é diferente para cada chamada para Rank API.

## <a name="personalizer-model-features-in-a-web-app"></a>Funcionalidades de modelo personalizado em uma aplicação web

Personalizar necessita de funcionalidades para as ações (conteúdo) e o contexto atual (utilizador e ambiente). As funcionalidades são usadas para alinhar ações com o contexto atual no modelo. O modelo é uma representação do conhecimento passado do Personalizador sobre ações, contexto e suas características que lhe permitem tomar decisões educadas.

O modelo, incluindo funcionalidades, é atualizado num horário baseado na definição **de frequência de atualização** do seu Modelo no portal Azure.

> [!CAUTION]
> As funcionalidades desta aplicação destinam-se a ilustrar funcionalidades e valores de funcionalidades, mas não necessariamente às melhores funcionalidades para usar numa aplicação web.

### <a name="plan-for-features-and-their-values"></a>Plano para funcionalidades e seus valores

As funcionalidades devem ser selecionadas com o mesmo planeamento e design que se aplicaria a qualquer esquema ou modelo na sua arquitetura técnica. Os valores de funcionalidade podem ser definidos com lógica de negócio ou sistemas de terceiros. Os valores de características não devem ser tão específicos que não se apliquem em um grupo ou classe de funcionalidades.

### <a name="generalize-feature-values"></a>Generalizar valores de características

#### <a name="generalize-into-categories"></a>Generalizar em categorias

Esta aplicação usa `time` como recurso, mas agruca o tempo em categorias como `morning` , e `afternoon` `evening` `night` . Este é um exemplo de utilização da informação do tempo, mas não de uma forma muito específica, `10:05:01 UTC+2` como.

#### <a name="generalize-into-parts"></a>Generalizar em partes

Esta aplicação utiliza as funcionalidades HTTP Request do navegador. Isto começa com uma cadeia muito específica com todos os dados, por exemplo:

```http
Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/530.99 (KHTML, like Gecko) Chrome/80.0.3900.140 Safari/537.36
```

A biblioteca de classes **HttpRequestFeatures** generaliza esta cadeia num objeto **userAgentInfo** com valores individuais. Quaisquer valores demasiado específicos são definidos para uma corda vazia. Quando as características de contexto do pedido são enviadas, tem o seguinte formato JSON:

```JSON
{
  "httpRequestFeatures": {
    "_synthetic": false,
    "OUserAgent": {
      "_ua": "",
      "_DeviceBrand": "",
      "_DeviceFamily": "Other",
      "_DeviceIsSpider": false,
      "_DeviceModel": "",
      "_OSFamily": "Windows",
      "_OSMajor": "10",
      "DeviceType": "Desktop"
    }
  }
}
```


## <a name="using-sample-web-app"></a>Usando a aplicação web de amostra

A aplicação web baseada no navegador da amostra (todo o código é fornecido) precisa das seguintes aplicações instaladas para executar a aplicação.

Instale o seguinte software:

* [.NET Core 2.1](https://dotnet.microsoft.com/download/dotnet-core/2.1) - o servidor back-end da amostra utiliza .NET core
* [Node.js](https://nodejs.org/) - o cliente/front end depende desta aplicação
* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)- ou [.NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/) - use o ambiente de desenvolvimento do Visual Studio 2019 ou o .NET Core CLI para construir e executar a app

### <a name="set-up-the-sample"></a>Configurar a amostra
1. Clone o Azure Personalizer Samples repo.

    ```bash
    git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
    ```

1. Navegue em _amostras/HttpRequestFeatures_ para abrir a solução, `HttpRequestFeaturesExample.sln` .

    Se solicitado, permita que o Visual Studio atualize o pacote .NET para Personalizar.

### <a name="set-up-azure-personalizer-service"></a>Configurar o Serviço personalizadoR Azure

1. [Crie um recurso Personalizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer) no portal Azure.

1. No portal Azure, encontre o `Endpoint` e `Key1` `Key2` (ou funcionará) no **separador Chaves e Pontos Finais.** Estes são os seus `PersonalizerServiceEndpoint` e os `PersonalizerApiKey` seus.
1. Preencha o `PersonalizerServiceEndpoint` ** inappsettings.jsem**.
1. Configure os `PersonalizerApiKey` segredos como uma [aplicação](https://docs.microsoft.com/aspnet/core/security/app-secrets) de uma das seguintes formas:

    * Se estiver a utilizar o CLI .NET Core, pode utilizar o `dotnet user-secrets set "PersonalizerApiKey" "<API Key>"` comando.
    * Se estiver a utilizar o Visual Studio, pode clicar no projeto e selecionar a opção do menu **Gerir os Segredos do Utilizador** para configurar as teclas Personalizer. Ao fazê-lo, o Visual Studio abrirá um `secrets.json` ficheiro onde pode adicionar as chaves da seguinte forma:

    ```JSON
    {
      "PersonalizerApiKey": "<your personalizer key here>",
    }
    ```

## <a name="run-the-sample"></a>Executar o exemplo

Construa e executar HttpRequestFeaturesExample com um dos seguintes métodos:

* Visual Studio 2019: Press **F5**
* .NET Core CLI: `dotnet build` então `dotnet run`

Através de um navegador web, pode enviar um pedido de Rank e um pedido de Recompensa e ver as suas respostas, bem como as funcionalidades de pedido de http extraídas do seu ambiente.

> [!div class="mx-imgBorder"]
> ![A screenshot mostra um exemplo da Funcionalidade de Pedido de Http num navegador web.](./media/tutorial-web-app/web-app-single-page.png)

## <a name="demonstrate-the-personalizer-loop"></a>Demonstrar o ciclo personalizer

1. Selecione o novo botão **'Procurar'** para criar um novo objeto JSON para a chamada API do Rank. Isto cria as ações (com características) e características de contexto e exibe os valores para que possa ver como é o JSON.

    Para a sua própria aplicação futura, a geração de ações e funcionalidades pode acontecer no cliente, no servidor, uma mistura dos dois, ou com chamadas para outros serviços.

1. Selecione **Enviar Pedido** de Envio para enviar o objeto JSON para o servidor. O servidor chama a API do Ranking Personalizado. O servidor recebe a resposta e devolve a ação mais bem classificada ao cliente para exibir.

1. Desacione o valor da recompensa e, em seguida, selecione o botão **Enviar Pedido de Recompensa.** Se não alterar o valor da recompensa, a aplicação do cliente envia sempre o valor de `1` Personalizar.

    > [!div class="mx-imgBorder"]
    > ![A screenshot mostra a secção Pedido de Recompensa.](./media/tutorial-web-app/reward-score-api-call.png)

    Para a sua própria aplicação futura, a geração da pontuação de recompensa pode acontecer depois de recolher informações do comportamento do utilizador no cliente, juntamente com a lógica de negócio no servidor.

## <a name="understand-the-sample-web-app"></a>Compreenda a aplicação web de amostra

A aplicação web de amostra tem um servidor **C# .NET,** que gere a recolha de funcionalidades e envia e recebe chamadas HTTP para o seu ponto final personalizado.

A aplicação web de amostra utiliza uma **aplicação de cliente frontal knockout** para capturar funcionalidades e processar ações de interface de utilizador, tais como clicar em botões e enviar dados para o servidor .NET.

As seguintes secções explicam as partes do servidor e do cliente que um desenvolvedor precisa entender para usar o Personalizer.

## <a name="rank-api-client-application-sends-context-to-server"></a>API de classificação: A aplicação do cliente envia contexto para servidor

A aplicação do cliente recolhe o _agente_utilizador do navegador do utilizador.

> [!div class="mx-imgBorder"]
> ![Construa e executará o projeto HTTPRequestFeaturesExample. Uma janela do navegador abre para exibir a aplicação de uma página única.](./media/tutorial-web-app/user-agent.png)

## <a name="rank-api-server-application-calls-personalizer"></a>Rank API: Aplicação do servidor chama Personalizer

Esta é uma aplicação web típica .NET com uma aplicação de cliente, grande parte do código da placa da caldeira é fornecido para si. Qualquer código não específico do Personaler é removido dos seguintes cortes de código para que possa focar-se no código específico do Personaler.

### <a name="create-personalizer-client"></a>Criar cliente personalizador

Nas **Startup.cs**do servidor, o ponto final e a tecla Personalizer são utilizados para criar o cliente Personalizer. A aplicação do cliente não precisa de comunicar com o Personaler nesta aplicação, em vez de depender do servidor para efec se efecer aquelas chamadas SDK.

O código de arranque .NET do servidor web é:

```csharp
using Microsoft.Azure.CognitiveServices.Personalizer;
// ... other using statements removed for brevity

namespace HttpRequestFeaturesExample
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            string personalizerApiKey = Configuration.GetSection("PersonalizerApiKey").Value;
            string personalizerEndpoint = Configuration.GetSection("PersonalizerConfiguration:ServiceEndpoint").Value;
            if (string.IsNullOrEmpty(personalizerEndpoint) || string.IsNullOrEmpty(personalizerApiKey))
            {
                throw new ArgumentException("Missing Azure Personalizer endpoint and/or api key.");
            }
            services.AddSingleton(client =>
            {
                return new PersonalizerClient(new ApiKeyServiceClientCredentials(personalizerApiKey))
                {
                    Endpoint = personalizerEndpoint
                };
            });

            services.AddMvc();
        }

        // ... code removed for brevity
    }
}
```

### <a name="select-best-action"></a>Selecione a melhor ação

No **PersonalizerController.cs**do servidor, a API do servidor **GenerateRank** resume a preparação para chamar a API de classificação

* Criar novo `eventId` para a chamada Rank
* Obtenha a lista de ações
* Obtenha a lista de funcionalidades do utilizador e crie funcionalidades de contexto
* Opcionalmente, desa um conjunto de ações excluídas
* Ligue para a API do Rank, devolva os resultados ao cliente

```csharp
/// <summary>
/// Creates a RankRequest with user time of day, HTTP request features,
/// and taste as the context and several different foods as the actions
/// </summary>
/// <returns>RankRequest with user info</returns>
[HttpGet("GenerateRank")]
public RankRequest GenerateRank()
{
    string eventId = Guid.NewGuid().ToString();

    // Get the actions list to choose from personalizer with their features.
    IList<RankableAction> actions = GetActions();

    // Get context information from the user.
    HttpRequestFeatures httpRequestFeatures = GetHttpRequestFeaturesFromRequest(Request);
    string timeOfDayFeature = GetUsersTimeOfDay();
    string tasteFeature = GetUsersTastePreference();

    // Create current context from user specified data.
    IList<object> currentContext = new List<object>() {
            new { time = timeOfDayFeature },
            new { taste = tasteFeature },
            new { httpRequestFeatures }
    };

    // Exclude an action for personalizer ranking. This action will be held at its current position.
    IList<string> excludeActions = new List<string> { "juice" };

    // Rank the actions
    return new RankRequest(actions, currentContext, excludeActions, eventId);
}
```

O JSON enviado ao Personaler, contendo ambas as ações (com características) e as características de contexto atual, parece:

```json
{
    "contextFeatures": [
        {
            "time": "morning"
        },
        {
            "taste": "savory"
        },
        {
            "httpRequestFeatures": {
                "_synthetic": false,
                "MRefer": {
                    "referer": "http://localhost:51840/"
                },
                "OUserAgent": {
                    "_ua": "",
                    "_DeviceBrand": "",
                    "_DeviceFamily": "Other",
                    "_DeviceIsSpider": false,
                    "_DeviceModel": "",
                    "_OSFamily": "Windows",
                    "_OSMajor": "10",
                    "DeviceType": "Desktop"
                }
            }
        }
    ],
    "actions": [
        {
            "id": "pasta",
            "features": [
                {
                    "taste": "savory",
                    "spiceLevel": "medium"
                },
                {
                    "nutritionLevel": 5,
                    "cuisine": "italian"
                }
            ]
        },
        {
            "id": "ice cream",
            "features": [
                {
                    "taste": "sweet",
                    "spiceLevel": "none"
                },
                {
                    "nutritionalLevel": 2
                }
            ]
        },
        {
            "id": "juice",
            "features": [
                {
                    "taste": "sweet",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 5
                },
                {
                    "drink": true
                }
            ]
        },
        {
            "id": "salad",
            "features": [
                {
                    "taste": "sour",
                    "spiceLevel": "low"
                },
                {
                    "nutritionLevel": 8
                }
            ]
        },
        {
            "id": "popcorn",
            "features": [
                {
                    "taste": "salty",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 3
                }
            ]
        },
        {
            "id": "coffee",
            "features": [
                {
                    "taste": "bitter",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 3
                },
                {
                    "drink": true
                }
            ]
        },
        {
            "id": "soup",
            "features": [
                {
                    "taste": "sour",
                    "spiceLevel": "high"
                },
                {
                    "nutritionLevel": 7
                }
            ]
        }
    ],
    "excludedActions": [
        "juice"
    ],
    "eventId": "82ac52da-4077-4c7d-b14e-190530578e75",
    "deferActivation": null
}
```

### <a name="return-personalizer-rewardactionid-to-client"></a>Devolução de recompensas PersonalizerActionId ao cliente

A Rank API devolve ao servidor a melhor recompensa de ação **selecionada.**

Mostrar a ação devolvida em **RewardActionId**.

```json
{
    "ranking": [
        {
            "id": "popcorn",
            "probability": 0.833333254
        },
        {
            "id": "salad",
            "probability": 0.03333333
        },
        {
            "id": "juice",
            "probability": 0
        },
        {
            "id": "soup",
            "probability": 0.03333333
        },
        {
            "id": "coffee",
            "probability": 0.03333333
        },
        {
            "id": "pasta",
            "probability": 0.03333333
        },
        {
            "id": "ice cream",
            "probability": 0.03333333
        }
    ],
    "eventId": "82ac52da-4077-4c7d-b14e-190530578e75",
    "rewardActionId": "popcorn"
}
```

### <a name="client-displays-the-rewardactionid-action"></a>O cliente exibe a ação rewardActionId

Neste tutorial, o `rewardActionId` valor é exibido.

Na sua própria aplicação futura, pode ser algum texto exato, um botão ou uma secção da página web realçada. A lista é devolvida para qualquer pós-análise de pontuações, não para uma encomenda do conteúdo. Apenas o `rewardActionId` conteúdo deve ser exibido.

## <a name="reward-api-collect-information-for-reward"></a>Recompensa API: recolher informações para recompensa

A [pontuação da recompensa](concept-rewards.md) deve ser cuidadosamente planeada, tal como as características estão planeadas. A pontuação da recompensa normalmente deve ser um valor de 0 a 1. O valor _pode_ ser calculado parcialmente na aplicação do cliente, com base em comportamentos do utilizador, e parcialmente no servidor, com base na lógica de negócio e objetivos.

Se o servidor não chamar a API reward dentro do **tempo de espera Reward** configurado no portal Azure para o seu recurso Personalizer, então a recompensa **Padrão** (também configurada no portal Azure) é utilizada para esse evento.

Nesta aplicação de amostra, pode selecionar um valor para ver como a recompensa impacta as seleções.

## <a name="additional-ways-to-learn-from-this-sample"></a>Formas adicionais de aprender com esta amostra

A amostra utiliza vários eventos baseados no tempo configurados no portal Azure para o seu recurso Personalizer. Jogue com esses valores e volte a esta aplicação web de amostra para ver como as mudanças impactam as chamadas Rank e Reward:

* Recompensar o tempo de espera
* Frequência de atualização de modelos

As definições adicionais para jogar incluem:
* Recompensa por defeito
* Percentagem de exploração


## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar este tutorial, limpe os seguintes recursos:

* Apague o seu diretório de projeto de amostra.
* Elimine o seu recurso Personalizer - pense num recurso Personalizer como dedicado às ações e contexto - só reutilizar o recurso se ainda estiver a utilizar os alimentos como domínio de ações sujeitas.


## <a name="next-steps"></a>Passos seguintes
* [Como funciona o Personalizador](how-personalizer-works.md)
* [Características](concepts-features.md): aprenda conceitos sobre funcionalidades usando com ações e contexto
* [Recompensas](concept-rewards.md): conheça o cálculo das recompensas
