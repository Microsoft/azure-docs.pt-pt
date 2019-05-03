---
title: Ciclo de comentários - Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizar o conteúdo desta C# início rápido com o serviço de Personalizer.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: c566d1fd4b151efc0d28b7059504e60a1451c034
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027146"
---
# <a name="quickstart-personalize-content-using-c"></a>Início rápido: Personalizar o conteúdo usandoC# 

Apresentar conteúdo personalizado neste C# início rápido com o serviço de Personalizer.

Este exemplo demonstra como utilizar a biblioteca de cliente de personalização para C# para efetuar as seguintes ações: 

 * Classificar uma lista de ações para personalização.
 * Relatório recompensa alocar à parte superior com a classificação de ações com base na seleção do usuário para o evento especificado.

Introdução à personalização envolve os seguintes passos:

1. Referenciar o SDK 
1. Escrever código para classificar as ações que pretende mostrar aos seus utilizadores
1. Escrever código para enviar as remunerações para preparar o loop.

## <a name="prerequisites"></a>Pré-requisitos

* Terá de uma chave de subscrição e o url do serviço de emissão de token.
* [Visual Studio 2015 ou 2017](https://visualstudio.microsoft.com/downloads/).
* O pacote NuGet do SDK Microsoft.Azure.CognitiveServices.Personalization. Abaixo, são fornecidas as instruções de instalação.

## <a name="creating-a-new-console-app-and-referencing-the-personalizer-sdk"></a>Criar uma nova aplicação de consola e referenciar o SDK de Personalizer 

<!--
Get the latest code as a Visual Studio solution from [GitHub] (add link).
-->

1. Crie uma nova Aplicação da Consola Visual C# no Visual Studio.
1. Instale o pacote de NuGet da biblioteca de cliente de personalização. No menu, selecione **ferramentas**, selecione **Gestor de pacote de Nuget**, em seguida, **gerir pacotes NuGet para solução**.
1. Selecione o **navegue** separador e, no **pesquisa** caixa tipo `Microsoft.Azure.CognitiveServices.Personalization`.
1. Selecione **Microsoft.Azure.CognitiveServices.Personalization** quando for apresentada.
1. Selecione a caixa de verificação junto ao nome do seu projeto e selecione **instalar**.

## <a name="add-the-code-and-put-in-your-personalizer-and-azure-keys"></a>Adicione o código e colocar as suas chaves Personalizer e o Azure

1. Substitua Program.cs pelo seguinte código. 
1. Substitua `serviceKey` valor com a sua chave de subscrição Personalizer válido.
1. Substitua `serviceEndpoint` com o ponto final de serviço. Um exemplo é `https://westus2.api.cognitive.microsoft.com/`.
1. Execute o programa.

## <a name="add-code-to-rank-the-actions-you-want-to-show-to-your-users"></a>Adicionar código para classificar as ações que pretende mostrar aos seus utilizadores

O seguinte C# código é uma listagem completa para transmitir informações de utilizador e obter informações sobre o seu conteúdo, _features _ações_, para Personalizer utilizando o SDK. Personalizer devolve a parte superior com a classificação de ação para mostrar o utilizador.  

```csharp
using Microsoft.Azure.CognitiveServices.Personalization;
using Microsoft.Azure.CognitiveServices.Personalization.Models;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;

namespace PersonalizationExample
{
    class Program
    {
        // The key specific to your personalization service instance; e.g. "0123456789abcdef0123456789ABCDEF"
        private const string serviceKey = "";

        // The endpoint specific to your personalization service instance; e.g. https://westus2.api.cognitive.microsoft.com/
        private const string serviceEndpoint = "";

        static void Main(string[] args)
        {
            int iteration = 1;
            bool runLoop = true;

            Uri url = new Uri(serviceEndpoint);

            // Get the actions list to choose from personalization with their features.
            IList<RankableAction> actions = GetActions();

            // Initialize Personalization client.
            PersonalizationClient client = InitializePersonalizationClient(url);

            do
            {
                Console.WriteLine("\nIteration: " + iteration++);

                // Get context information from the user.
                string timeOfDayFeature = GetUsersTimeOfDay();
                string tasteFeature = GetUsersTastePreference();

                // Create current context from user specified data.
                IList<object> currentContext = new List<object>() {
                    new { time = timeOfDayFeature },
                    new { taste = tasteFeature }
                };

                // Exclude an action for personalization ranking. This action will be held at its current position.
                IList<string> excludeActions = new List<string> { "juice" };

                // Generate an ID to associate with the request.
                string eventId = Guid.NewGuid().ToString();

                // Rank the actions
                var request = new RankRequest(actions, currentContext, excludeActions, eventId);
                RankResponse response = client.Rank(request);

                Console.WriteLine("\nPersonalization service thinks you would like to have: " + response.RewardActionId + ". Is this correct? (y/n)");

                float reward = 0.0f;
                string answer = GetKey();

                if (answer == "Y")
                {
                    reward = 1;
                    Console.WriteLine("\nGreat! Enjoy your food.");
                }
                else if (answer == "N")
                {
                    reward = 0;
                    Console.WriteLine("\nYou didn't like the recommended food choice.");
                }
                else
                {
                    Console.WriteLine("\nEntered choice is invalid. Service assumes that you didn't like the recommended food choice.");
                }

                Console.WriteLine("\nPersonalization service ranked the actions with the probabilities as below:");
                foreach (var rankedResponse in response.Ranking)
                {
                    Console.WriteLine(rankedResponse.Id + " " + rankedResponse.Probability);
                }

                // Send the reward for the action based on user response.
                client.Reward(response.EventId, new RewardRequest(reward));

                Console.WriteLine("\nPress q to break, any other key to continue:");
                runLoop = !(GetKey() == "Q");

            } while (runLoop);
        }

        /// <summary>
        /// Initializes the personalization client.
        /// </summary>
        /// <param name="url">Azure endpoint</param>
        /// <returns>Personalization client instance</returns>
        static PersonalizationClient InitializePersonalizationClient(Uri url)
        {
            PersonalizationClient client = new PersonalizationClient(url,
            new ApiKeyServiceClientCredentials(serviceKey),
            new DelegatingHandler[] { });

            return client;
        }

        /// <summary>
        /// Get users time of the day context.
        /// </summary>
        /// <returns>Time of day feature selected by the user.</returns>
        static string GetUsersTimeOfDay()
        {
            string[] timeOfDayFeatures = new string[] { "morning", "afternoon", "evening", "night" };

            Console.WriteLine("\nWhat time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night");
            if (!int.TryParse(GetKey(), out int timeIndex) || timeIndex < 1 || timeIndex > timeOfDayFeatures.Length)
            {
                Console.WriteLine("\nEntered value is invalid. Setting feature value to " + timeOfDayFeatures[0] + ".");
                timeIndex = 1;
            }

            return timeOfDayFeatures[timeIndex - 1];
        }

        /// <summary>
        /// Gets user food preference.
        /// </summary>
        /// <returns>Food taste feature selected by the user.</returns>
        static string GetUsersTastePreference()
        {
            string[] tasteFeatures = new string[] { "salty", "sweet" };

            Console.WriteLine("\nWhat type of food would you prefer (enter number)? 1. salty 2. sweet");
            if (!int.TryParse(GetKey(), out int tasteIndex) || tasteIndex < 1 || tasteIndex > tasteFeatures.Length)
            {
                Console.WriteLine("\nEntered value is invalid. Setting feature value to " + tasteFeatures[0] + ".");
                tasteIndex = 1;
            }

            return tasteFeatures[tasteIndex - 1];
        }

        /// <summary>
        /// Creates personalization actions feature list.
        /// </summary>
        /// <returns>List of actions for personalization.</returns>
        static IList<RankableAction> GetActions()
        {
            IList<RankableAction> actions = new List<RankableAction>
            {
                new RankableAction
                {
                    Id = "pasta",
                    Features =
                    new List<object>() { new { taste = "salty", spiceLevel = "medium" }, new { nutritionLevel = 5, cuisine = "italian" } }
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
                    new List<object>() { new { taste = "salty", spiceLevel = "low" }, new { nutritionLevel = 8 } }
                }
            };

            return actions;
        }

        private static string GetKey()
        {
            return Console.ReadKey().Key.ToString().Last().ToString().ToUpper();
        }
    }
}
```

## <a name="run-the-program"></a>Execute o programa

Compile e execute o programa. O programa de início rápido pede-lhe algumas perguntas para reunir as preferências do usuário, conhecidas como recursos, em seguida, fornecem a ação superior.

![O programa de início rápido pede-lhe algumas perguntas para reunir as preferências do usuário, conhecidas como recursos, em seguida, fornecem a ação superior.](media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

## <a name="clean-up-resources"></a>Limpar recursos
Quando tiver terminado o início rápido, remova todos os ficheiros criados neste início rápido. 

## <a name="next-steps"></a>Passos Seguintes

[Como funciona a Personalizer](how-personalizer-works.md)


