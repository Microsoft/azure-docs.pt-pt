---
title: 'Início rápido: Pesquisa de resposta do projeto,C#'
titlesuffix: Azure Cognitive Services
description: Exemplo de código para começar a utilizar a Pesquisa de Respostas do Projeto com C#.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: quickstart
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: fe6758cb6b8d120862cbbe593574360c1bc7b17b
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698113"
---
# <a name="quickstart-project-answer-search-query-in-c"></a>Início rápido: Consulta de pesquisa de resposta do projeto emC#

O seguinte exemplo de C# cria e envia uma consulta para obter informações sobre a terceira lei de cálculo.

## <a name="prerequisites"></a>Pré-requisitos

Você precisará do [Visual Studio 2017 ou posterior](https://www.visualstudio.com/downloads/) para executar esse código no Windows. (A Edição de Comunidade gratuita irá funcionar.)

Obter uma chave de acesso da avaliação gratuita dos [Laboratórios dos Serviços Cognitivos](https://labs.cognitive.microsoft.com/en-us/project-answer-search)

## <a name="code-scenario"></a>Cenário de código

O seguinte código em C# cria e envia a consulta. 

É implementado nos passos seguintes:
1. Declare variáveis para especificar o ponto final e um URL de consulta para pré-visualização.  
2. Crie o pedido.
3. Adicione o cabeçalho *Ocp-Apim-Subscription-Key*. 
4. Execute o pedido Web de forma assíncrona. 
5. Leia a resposta.
6. Imprima os cabeçalhos e os resultados JSON para a consola.

**Código fonte**

```
using System;
using System.Collections.Generic;
using System.Text;
using System.Net;
using System.IO;

namespace Answers_csharp
{
    class Program
    {
        // Replace the accessKey string value with your valid access key.
        const string accessKey = "YOUR-SUBSCRIPTION-KEY";

        const string uriBase = "https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search"; 

        const string searchTerm = "third law of calculus"; 

        // Used to return news search results including relevant headers
        struct SearchResult
        {
            public String jsonResult;
            public Dictionary<String, String> relevantHeaders;
        }

        static void Main()
        {
            Console.OutputEncoding = System.Text.Encoding.UTF8;
            Console.WriteLine("Searching locally for: " + searchTerm);

            SearchResult result = BingLocalSearch(searchTerm);

            Console.WriteLine("\nRelevant HTTP Headers:\n");
            foreach (var header in result.relevantHeaders)
                Console.WriteLine(header.Key + ": " + header.Value);

            Console.WriteLine("\nJSON Response:\n");
            Console.WriteLine(JsonPrettyPrint(result.jsonResult));

            Console.Write("\nPress Enter to exit ");
            Console.ReadLine();
        }

        /// <summary>
        /// Does Bing knowledge search and returns the results as a SearchResult.
        /// </summary>
        static SearchResult BingLocalSearch(string searchQuery)
        {
            // Construct the URI of the search request
            var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery) + "&mkt=en-us";

            // Send the Web request and get the response.
            WebRequest request = HttpWebRequest.Create(uriQuery);
            request.Headers["Ocp-Apim-Subscription-Key"] = accessKey; 

            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            // Create result object for return
            var searchResult = new SearchResult();
            searchResult.jsonResult = json;
            searchResult.relevantHeaders = new Dictionary<String, String>();

            // Extract Bing HTTP headers
            foreach (String header in response.Headers)
            {
                if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
                    searchResult.relevantHeaders[header] = response.Headers[header];
            }

            return searchResult;
        }

        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            int offset = 0;
            int indentLength = 3;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\'':
                        if (quote) ignore = !ignore;
                        break;
                }

                if (quote)
                    sb.Append(ch);
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (ch != ' ') sb.Append(ch);
                            break;
                    }
                }
            }

            return sb.ToString().Trim();
        }

    }
}

```
## <a name="running-the-application"></a>Executar a aplicação

Para executar a aplicação:

1. Crie uma solução de Consola nova no Visual Studio.
2. Substitua `Program.cs` pelo código fornecido.
3. Substitua o valor `YOUR-ACCESS-KEY` por uma chave de acesso válida para a sua subscrição.
4. Execute o programa.

## <a name="next-steps"></a>Passos Seguintes
[Início Rápido do Java](java-quickstart.md)
