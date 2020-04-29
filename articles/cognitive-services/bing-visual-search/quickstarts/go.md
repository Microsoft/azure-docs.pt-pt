---
title: 'Quickstart: Obtenha insights de imagem usando o REST API e Go - Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Aprenda a enviar uma imagem para a API de Pesquisa Visual bing e obtenha informações sobre isso.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: 836012c11d16810172c27fb948e1185f99f7de83
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75446649"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-go"></a>Quickstart: Obtenha insights de imagem usando o Bing Visual Search REST API e Go

Este quickstart utiliza a linguagem de programação Go para ligar para a API de Pesquisa Visual bing e exibir resultados. Um pedido post envia uma imagem para o ponto final da API. Os resultados incluem URLs e informações descritivas sobre imagens semelhantes à imagem carregada.

## <a name="prerequisites"></a>Pré-requisitos

* Instale os [binários Go](https://golang.org/dl/).
* A impressora bonita e profunda é usada para apresentar resultados. Pode instalar go-spew `$ go get -u https://github.com/davecgh/go-spew` com o comando.

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="project-and-libraries"></a>Projeto e bibliotecas

Crie um projeto Go no seu IDE ou editor. Em `net/http` seguida, importar `ioutil` para pedidos, `encoding/json` ler a resposta, e para lidar com o texto json de resultados. A `go-spew` biblioteca é usada para analisar os resultados da JSON.

```go
package main

import (
    "bytes"
    "io"
    "fmt"
    "io/ioutil"
    "mime/multipart"
    "net/http"
    "os"
    "path/filepath"
    "encoding/json"
    "github.com/davecgh/go-spew/spew"
)

```

## <a name="struct-to-format-results"></a>Struct para os resultados do formato

Os `BingAnswer` formatos estruturais são devolvidos na resposta JSON, que é multinível e complexa. A seguinte implementação abrange alguns dos elementos essenciais:

```go
type BingAnswer struct {
    Type         string `json:"_type"`
    Instrumentation struct {
        Type string   `json:"_type"`
        PingUrlBase   string  `json:"_pingUrlBase"`
        PageLoadPingUrl  string `json:"_pageLoadPingUrl"`
    } `json:"instrumentation"`
    Tags []struct  {
        DisplayName       string    `json:"displayName"`
        Actions                 []struct {
            Type      string `json:"_type"`
            ActionType    string    `json:"actionType"`
            Data             struct {
                Value     []struct {
                    WebSearchUrl          string `json:"webSearchUrl"`
                    Name        string  `json:"name"`
                }`json:"value"`
                ImageInsightsToken string `json:"imageInsightsToken"`
                InsightsMetadata struct {
                    PagesIncludingCount int `json:"pagesIncludingCount"`
                    AvailableSizesCount  int  `json:"availableSizesCount"`
                }
                ImageId string  `json:"imageId"`
                AccentColor  string `json:"accentColor"`
            } `json:"data"`
        } `json:"actions"`
    } `json:"tags"`
    Image struct {
        WebSearchUrl   string  `json:"webSearchUrl"`
        WebSearchUrlPingSuffix  string `json:"webSearchUrlPingSuffix"`
        Name   string   `json:"name"`
        IsFamilyFriendly   bool  `json:"isFamilyFriendly"`
        ContentSize   string   `json:"contentSize"`
        EncodingFormat    string   `json:"encodingFormat"`
        HostPageDisplayUrl   string    `json:"hostPageDisplayUrl"`
        Width     int   `json:"width"`
        Height    int    `json:"height"`
        Thumbnail    struct  {
            Width   int   `json:"width"`
            Height  int   `json:"height"`
        }
        InsightsMetadata  struct {
            PagesIncludingCount   int   `json:"pagesIncludingCount"`
            AvailableSizesCount    int    `json:"availableSizesCount"`
        }
        AccentColor   string     `json:"accentColor"`
        VisualWords   string   `json:"visualWords"`
    } `json:"image"`
}

```

## <a name="main-function-and-variables"></a>Função principal e variáveis  

O código seguinte declara a função principal e atribui variáveis necessárias. Confirme que o ponto final está correto e substitua o valor `token` por uma chave de subscrição válida da sua conta do Azure. O `batchNumber` é um GUID necessário para liderar e seguir os limites dos dados do POST. A `fileName` variável identifica o ficheiro de imagem para o POST. `endpoint`pode ser o ponto final global abaixo, ou o ponto final personalizado do [subdomínio](../../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal Azure para o seu recurso:

```go
func main() {
    // Verify the endpoint URI and replace the token string with a valid subscription key.se
    endpoint := "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch"
    token := "YOUR-ACCESS-KEY"
    client := &http.Client{}
    batchNumber := "d7ecc447-912f-413e-961d-a83021f1775f"
    fileName := "ElectricBike.JPG"

    body, contentType := createRequestBody(fileName, batchNumber)
    
    req, err := http.NewRequest("POST", endpoint, body)
    req.Header.Add("Content-Type", contentType)
    req.Header.Set("Ocp-Apim-Subscription-Key", token)
    
    resp, err := client.Do(req)
    if err != nil {
        panic(err)
    }
    
       defer resp.Body.Close()
    
    resbody, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        panic(err)
    }

    //fmt.Print(string(resbody))
    
    // Create a new answer.  
    ans := new(BingAnswer)
    err = json.Unmarshal(resbody, &ans)
    if err != nil {
        fmt.Println(err)
    }
    
    fmt.Print("Output of BingAnswer: \r\n\r\n")
    
    // Iterate over search results and print the result name and URL.
    for _, result := range ans.Tags {
        spew.Dump(result)
    }
}

```

## <a name="boundaries-of-post-body"></a>Limites do corpo post

Um pedido de POST para o ponto final de Pesquisa Visual requer limites de liderança e de trilha que encerram os dados do POST. O limite principal inclui um número de `Content-Disposition: form-data; name="image"; filename=`lote, o identificador do tipo de conteúdo, mais o nome de ficheiro da imagem para POST. O limite de fuga é simplesmente o número do lote. Estas funções não estão `main` incluídas no bloco:

```go
func BuildFormDataStart(batNum string, fileName string) string{

    startBoundary := "--batch_" + batNum + "\r\n"
    requestBoundary := startBoundary + "\r\n" + "Content-Disposition: form-data; name=\"image\"; filename=" + fileName + "\r\n\r\n"
    return requestBoundary
}

func BuildFormDataEnd(batNum string) string{

    return "\r\n" + "\r\n" + "--batch_" + batNum + "\r\n"

}

```
## <a name="add-image-bytes-to-post-body"></a>Adicione bytes de imagem ao corpo post

Este segmento de código cria o pedido POST que contém dados de imagem:

```go
func createRequestBody(fileName string, batchNumber string) (*bytes.Buffer, string) {
    file, err := os.Open(fileName)
    if err != nil {
        panic(err)
    }
    defer file.Close()

    body := &bytes.Buffer{}
    writer := multipart.NewWriter(body)
    
    writer.SetBoundary(BuildFormDataStart(batchNumber, fileName))
    
    part, err := writer.CreateFormFile("image", filepath.Base(file.Name()))
    if err != nil {
        panic(err)
    }
    io.Copy(part, file)
    writer.WriteField("lastpart", BuildFormDataEnd(batchNumber))    
    writer.Close()
    return body, writer.FormDataContentType()
}

```

## <a name="send-the-request"></a>Enviar o pedido

O seguinte código envia o pedido e lê os resultados:

```go
resp, err := client.Do(req)
    if err != nil {
        panic(err)
    }
    
       defer resp.Body.Close()
    
    resbody, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        panic(err)
    }

```

## <a name="handle-the-response"></a>Processar a resposta

A `Unmarshall` função extrai informações do texto JSON devolvido pela API de Pesquisa Visual. A `go-spew` bonita impressora exibe os resultados:

```go
    // Create a new answer.  
    ans := new(BingAnswer)
    err = json.Unmarshal(resbody, &ans)
    if err != nil {
        fmt.Println(err)
    }
    
    fmt.Print("Output of BingAnswer: \r\n\r\n")
    
    // Iterate over search results and print the result name and URL.
    for _, result := range ans.Tags {
        spew.Dump(result)
    }

```
> [!NOTE]
> Francesco Giordano contribuiu com o código para este exemplo.

## <a name="results"></a>Resultados

Os resultados identificam imagens semelhantes à imagem contida no corpo do POST. Os campos `WebSearchUrl` úteis são e: `Name`

```go
    Value: ([]struct { WebSearchUrl string "json:\"webSearchUrl\""; Name string "json:\"name\"" }) (len=66 cap=94) {
     (struct { WebSearchUrl string "json:\"webSearchUrl\""; Name string "json:\"name\"" }) {
      WebSearchUrl: (string) (len=129) "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&id=B9E6621161769D578A9E4DD9FD742128DE65225A&simid=608046863828453626",
      Name: (string) (len=87) "26\"Foldable Electric Mountain Bike Bicycle Ebike W/Lithium Battery 250W 36V MY8L | eBay"
     },
     (struct { WebSearchUrl string "json:\"webSearchUrl\""; Name string "json:\"name\"" }) {
      WebSearchUrl: (string) (len=129) "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&id=5554757348A9E88E9EEAB74217E228689E716B61&simid=607988237543409883",
      Name: (string) (len=96) "Best 25+ Electric mountain bike ideas on Pinterest | Mountain bicycle, Mtb mountain bike and ..."
     },
     (struct { WebSearchUrl string "json:\"webSearchUrl\""; Name string "json:\"name\"" }) {
      WebSearchUrl: (string) (len=129) "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&id=B56F626A4803D829FE326842E2B97CE5B87F17F2&simid=607991699288949513",
      Name: (string) (len=100) "Electric Fat Bike Mountain Bicycle Snow Bike Cruiser Ebike Motor Lithium Battery Dual Oil Brakes ..."
     },
     (struct { WebSearchUrl string "json:\"webSearchUrl\""; Name string "json:\"name\"" }) {
      WebSearchUrl: (string) (len=129) "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&id=CCAE400EE510DCA6F5740ABAF08A5310B4EF0698&simid=608003854048890458",
      Name: (string) (len=81) "Best Mountain Bikes Under 1500 (Outstanding Performance) | Mountain Bicycle World"
     },
     (struct { WebSearchUrl string "json:\"webSearchUrl\""; Name string "json:\"name\"" }) {
      WebSearchUrl: (string) (len=129) "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&id=1244730E2E3F2D5DFBED9A9CFE1DBE27B09F08BC&simid=608005181199745622",
      Name: (string) (len=98) "ANCHEER Folding Electric Mountain Bike with 26\" Super Lightweight Magnesium (36V 250W) - GearScoot"
     },
     (struct { WebSearchUrl string "json:\"webSearchUrl\""; Name string "json:\"name\"" }) {
      WebSearchUrl: (string) (len=129) "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&id=38AAD876E57BB0D502FBDD5B1CB29EB7EB8DD9E2&simid=608041654062220328",
      Name: (string) (len=97) "29 best CB Bikes Gadgets & Accessories images on Pinterest | Bicycles, Bicycling and Bike gadgets"
     },
     (struct { WebSearchUrl string "json:\"webSearchUrl\""; Name string "json:\"name\"" }) {
      WebSearchUrl: (string) (len=129) "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&id=6892C8FD22D0E42911C99AFD8FE1FD37BD82E02C&simid=608052803759703185",
      Name: (string) (len=98) "ANCHEER Folding Electric Mountain Bike with 26\" Super Lightweight Magnesium (36V 250W) - GearScoot"
     },

```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [O que é a API](../overview.md) de Pesquisa Visual Bing? 
>  [Bing Web Search quickstart in Go](../../Bing-Web-Search/quickstarts/go.md)
