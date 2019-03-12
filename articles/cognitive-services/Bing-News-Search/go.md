---
title: 'Início rápido: Obtenha notícias através de API de REST de pesquisa de notícias do Bing e Go'
titleSuffix: Azure Cognitive Services
description: Saiba como obter os resultados de notícias da API de pesquisa de notícias do Bing.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 2/21/2019
ms.author: rosh
ms.openlocfilehash: 295c32c1e14dc6a69a37040f92d27a6862359228
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57545059"
---
# <a name="quickstart-get-news-results-using-the-bing-news-search-rest-api-and-go"></a>Início rápido: Obter resultados de notícias, usando a API de REST de pesquisa de notícias do Bing e Go

Este início rápido utiliza a linguagem Go para chamar a API de pesquisa de notícias do Bing. Os resultados incluem os nomes e os URLs de fontes noticiosas identificados pela cadeia de consulta.

## <a name="prerequisites"></a>Pré-requisitos
* Instalar o [ir binários](https://golang.org/dl/)
* Instalar a biblioteca de go-spew para a mesma impressora muito para apresentar resultados
    * Instale esta libarary: `$ go get -u https://github.com/davecgh/go-spew`

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="create-a-project-and-import-libraries"></a>Crie um projeto e bibliotecas de importação

Crie um novo projeto de Go no seu IDE ou editor. Em seguida, importe `net/http` para pedidos, `ioutil` ler a resposta, e `encoding/json` para lidar com o texto JSON de resultados. A biblioteca de go-spew é necessário para analisar o JSON. 

```
package main

import (
    "fmt"
    "net/http"
    "io/ioutil"
    "encoding/json"
    "github.com/davecgh/go-spew/spew"
)

```

## <a name="create-a-struct-to-format-the-news-search-results"></a>Criar uma estrutura para formatar os resultados da pesquisa de notícias

A estrutura `NewsAnswer` formata os dados apresentados na resposta. A resposta JSON é muito complexo e vários níveis.  A seguinte implementação aborda os essentials.

```
// This struct formats the answer provided by the Bing News Search API.
type NewsAnswer struct {
    ReadLink       string `json: "readLink"` 
    QueryContext   struct {
        OriginalQuery   string   `json: "originalQuery"`
        AdultIntent     bool        `json: "adultIntent"`
    } `json: "queryContext"`
    TotalEstimatedMatches   int  `json: totalEstimatedMatches"` 
    Sort  []struct {
        Name    string  `json: "name"`
        ID       string    `json: "id"`
        IsSelected       bool  `json: "isSelected"`
        URL      string   `json: "url"`
    }  `json: "sort"` 
    Value   []struct   {
        Name     string   `json: "name"`
        URL   string    `json: "url"`
        Image   struct   {
            Thumbnail   struct  {
                ContentUrl  string  `json: "thumbnail"`
                Width   int  `json: "width"`
                Height  int   `json: "height"`
            } `json: "thumbnail"` 
            Description  string  `json: "description"`
            Provider  []struct   {
                Type   string    `json: "_type"`
                Name  string     `json: "name"`
            } `json: "provider"` 
            DatePublished   string   `json: "datePublished"`
        } `json: "image"` 
    } `json: "value"` 
}

```

## <a name="declare-the-main-function-and-define-variables"></a>Declarar a função principal e definir variáveis  

O seguinte código declara a função principal e atribui a variáveis necessárias. Confirme que o ponto final está correto e substitua o valor `token` por uma chave de subscrição válida da sua conta do Azure.

```
func main() {
    // Verify the endpoint URI and replace the token string with a valid subscription key.  
    const endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
    token := "YOUR-ACCESS-KEY"
    searchTerm := "Microsoft Cognitive Services"

    // Declare a new GET request.
    req, err := http.NewRequest("GET", endpoint, nil)
    if err != nil {
        panic(err)
    }

    // The rest of the code in this example goes here in the main function.
}
```

## <a name="query-and-header"></a>Consulta e cabeçalho

Adicione o cabeçalho do chave como cadeia de caracteres e acesso da consulta

```
// Add the query to the request.  
param := req.URL.Query()
param.Add("q", searchTerm)
req.URL.RawQuery = param.Encode()

// Insert the subscription-key header.  
req.Header.Add("Ocp-Apim-Subscription-Key", token)

```

## <a name="get-request"></a>O pedido

Criar o cliente e enviar o pedido Get. 

```
// Instantiate a client.  
client := new(http.Client)

// Send the request to Bing.  
resp, err := client.Do(req)
if err != nil {
    panic(err)
}

```

## <a name="send-the-request"></a>Enviar o pedido

Enviar o pedido e ler os resultados usando `ioutil`.

```
resp, err := client.Do(req)
    if err != nil {
        panic(err)
}

// Close the connection.    
defer resp.Body.Close()

// Read the results
resbody, err := ioutil.ReadAll(resp.Body)
if err != nil {
    panic(err)
}

```

## <a name="handle-the-response"></a>Processar a resposta

O `Unmarshall` função extrai informações a partir do texto JSON devolvido pela API de pesquisa de notícias.  Em seguida, pode apresentar os nós dos resultados usando o `go-spew` impressora bonita.

```
// Create a new answer object 
ans := new(NewsAnswer)
err = json.Unmarshal(body, &ans)
if err != nil {
    fmt.Println(err)
}
    
fmt.Print("Output of BingAnswer: \r\n\r\n")
    
// Iterate over search results and print the result name and URL.
for _, result := range ans.Value{
spew.Dump(result.Name, result.URL)
}

```

## <a name="results"></a>Resultados

Os resultados contêm o nome e um URL de cada resultado.

```
(string) (len=91) "Cognitive Services Market: Global Industry Analysis and Opportunity Assessment, 2019 - 2025"
(string) (len=142) "https://www.marketwatch.com/press-release/cognitive-services-market-global-industry-analysis-and-opportunity-assessment-2019---2025-2019-02-21"
(string) (len=104) "Microsoft calls for greater collaboration to harness the power of AI to empower people with disabilities"
(string) (len=115) "https://indiaeducationdiary.in/microsoft-calls-greater-collaboration-harness-power-ai-empower-people-disabilities-2/"
(string) (len=70) "Microsoft 'Intelligent Cloud Hub' to build AI-ready workforce in India"
(string) (len=139) "https://cio.economictimes.indiatimes.com/news/cloud-computing/microsoft-intelligent-cloud-hub-to-build-ai-ready-workforce-in-india/67187807"
(string) (len=81) "Skills shortage is stopping many Asian companies from embracing A.I., study shows"
(string) (len=106) "https://www.cnbc.com/2019/02/20/microsoft-idc-study-skills-shortages-stopping-companies-from-using-ai.html"
(string) (len=143) "Cognitive Computing in Healthcare Market Emerging Top Key Vendors- Apixio, MedWhat, Healthcare X.0, Apple, Google, Microsoft, and IBM 2017-2025"
(string) (len=40) "http://www.digitaljournal.com/pr/4163064"
(string) (len=49) "Microsoft launches AI skills initiative in Brazil"
(string) (len=80) "https://www.zdnet.com/article/microsoft-launches-ai-skills-initiative-in-brazil/"
(string) (len=45) "Kuwait's CITRA and Microsoft host AI OpenHack"
(string) (len=70) "http://www.itp.net/618639-kuwaits-citra-and-microsoft-host-ai-openhack"
(string) (len=51) "CITRA and Microsoft collaborate to host AI workshop"
(string) (len=123) "https://www.zawya.com/mena/en/press-releases/story/CITRA_and_Microsoft_collaborate_to_host_AI_workshop-ZAWYA20190212105751/"

```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [O que é a pesquisa de notícias do Bing](search-the-web.md)
