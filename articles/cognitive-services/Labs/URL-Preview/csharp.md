---
title: 'Início rápido: Versão prévia da URL do projeto,C#'
titlesuffix: Azure Cognitive Services
description: Comece a utilizar a Pré-visualização do URL do Projeto com C#.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: quickstart
ms.date: 03/16/2018
ms.author: rosh
ms.openlocfilehash: ec0c8ad06480cd596365f43f13ca57fb242d364c
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698039"
---
# <a name="quickstart-url-preview-query-in-c"></a>Início rápido: Consulta de visualização de URL emC#

O exemplo de C# seguinte cria uma Pré-visualização do URL do site da SwiftKey: https://swiftkey.com/en.

## <a name="prerequisites"></a>Pré-requisitos

Você precisará do [Visual Studio 2017 ou posterior](https://www.visualstudio.com/downloads/) para executar esse código no Windows. (A Edição de Comunidade gratuita irá funcionar.)

Obter uma chave de acesso da avaliação gratuita dos [Laboratórios dos Serviços Cognitivos](https://labs.cognitive.microsoft.com/en-us/project-answer-search)

## <a name="code-scenario"></a>Cenário de código

O exemplo de C# seguinte cria uma Pré-visualização do URL do site da SwiftKey: https://swiftkey.com/en. 

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
using System.IO;
using System.Net;
using System.Text;

namespace UrlPrevCshp
{
    class Program
    {
        static void Main(string[] args)
        {
            String uriBase = "https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search";
            String searchQuery = "https://swiftkey.com/en"; 
            var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery);

            // Do the Web request and get response
            WebRequest request = HttpWebRequest.Create(uriQuery);
            request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR-SUBSCRIPTION-KEY";
            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            Console.WriteLine("\nHTTP Headers:\n");
            foreach (String header in response.Headers)
            {
                if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
                    Console.WriteLine(header + ": " + response.Headers[header]);
            }

            Console.WriteLine(JsonPrettyPrint(json));
            

            Console.ReadKey();
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
            char last = ' ';
            int offset = 0;
            int indentLength = 2;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\\':
                        if (quote && last != '\\') ignore = true;
                        break;
                }

                if (quote)
                {
                    sb.Append(ch);
                    if (last == '\\' && ignore) ignore = false;
                }
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
                            if (quote || ch != ' ') sb.Append(ch);
                            break;
                    }
                }
                last = ch;
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
- [Início Rápido do Java](java-quickstart.md)
- [Início Rápido do JavaScript](javascript.md)
- [Início Rápido do Node](node-quickstart.md)
- [Início Rápido do Python](python-quickstart.md)
