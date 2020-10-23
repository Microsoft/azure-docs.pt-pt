---
title: Reconhecimento de Caracteres Óticos (OCR) - Visão computacional
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados com o reconhecimento de caracteres óticos (OCR) de imagens e documentos com texto impresso e manuscrito usando a API de Visão Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 34a7cd8669c1545361bc7cd9579cfb6140c0c946
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331707"
---
# <a name="optical-character-recognition-ocr"></a>Reconhecimento Ótico de Carateres (OCR)

A A API de Visão Computacional do Azure inclui capacidades de reconhecimento de caracteres óticos (OCR) que extraem texto impresso ou manuscrito a partir de imagens. Pode extrair texto de imagens, como fotos de placas ou contentores com números de série, bem como de documentos - faturas, contas, relatórios financeiros, artigos e muito mais.

## <a name="read-api"></a>Ler API 

A [API de Leitura de](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) Visão Computacional é a mais recente tecnologia OCR da Azure ([saiba quais as novidades](./whats-new.md)) que extrai texto impresso (em vários idiomas), texto manuscrito (apenas inglês), dígitos e símbolos de moeda a partir de imagens e documentos PDF de várias páginas. É otimizado para extrair texto de imagens pesadas de texto e documentos PDF de várias páginas com línguas mistas. Suporta a deteção de texto impresso e manuscrito na mesma imagem ou documento.

![Como o OCR converte imagens e documentos em saída estruturada com texto extraído](./Images/how-ocr-works.svg)

## <a name="input-requirements"></a>Requisitos de entrada
A chamada **de Leitura** requer imagens e documentos como entrada. Têm os seguintes requisitos:

* Formatos de ficheiros suportados: JPEG, PNG, BMP, PDF e TIFF
* Para ficheiros PDF e TIFF, são processadas até 2000 páginas (apenas duas primeiras páginas para o nível livre).
* O tamanho do ficheiro deve ser inferior a 50 MB (4 MB para o nível livre) e dimensões de pelo menos 50 x 50 pixels e, no máximo, 10000 x 10000 pixels. 
* As dimensões PDF devem ser no máximo 17 x 17 polegadas, correspondentes a tamanhos legais ou de papel A3 e menores.

### <a name="read-32-preview-allows-selecting-pages"></a>Ler 3.2 pré-visualização permite selecionar página(s)
Com a [API de pré-visualização Leitura 3.2](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-1/operations/5d986960601faab4bf452005), para grandes documentos multi-páginas, pode fornecer números de página específicos ou intervalos de página como parâmetro de entrada para extrair texto apenas dessas páginas. Este é um novo parâmetro de entrada para além do parâmetro linguístico opcional.

> [!NOTE]
> **Entrada linguística** 
>
> A [chamada de Leitura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) tem um parâmetro de pedido opcional para a linguagem. Este é o código linguístico BCP-47 do texto no documento. A leitura suporta a identificação de linguagem automática e documentos multilingues, pelo que só fornece um código linguístico se quiser forçar o documento a ser processado como essa língua específica.

## <a name="the-read-call"></a>A chamada de Leitura

A chamada de [Leitura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) da API retira uma imagem ou documento PDF à medida que a entrada e extrai texto assíncrono. A chamada retorna com um campo de cabeçalho de resposta chamado `Operation-Location` . O `Operation-Location` valor é um URL que contém o ID da operação para ser usado no passo seguinte.

|Cabeçalho de resposta| URL de resultados |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.1/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Faturação** 
>
> A página [de preços da Visão de Computador](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) inclui o nível de preços para Ler. Cada imagem ou página analisada é uma transação. Se ligar para a operação com um documento PDF ou TIFF contendo 100 páginas, a operação Ler contará como 100 transações e será faturado por 100 transações. Se efec se efec se efec se efec se efec se submeter 50 x 100 = 5000 transações.

## <a name="the-get-read-results-call"></a>A chamada de Resultados da Leitura

O segundo passo é ligar para a operação [Get Read Results.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d9869604be85dee480c8750) Esta operação toma como entrada o ID de operação que foi criado pela operação Ler. Devolve uma resposta JSON que contém um campo **de estado** com os seguintes valores possíveis. Você chama a esta operação iterativamente até que ela retorne com o valor **bem sucedido.** Utilize um intervalo de 1 a 2 segundos para evitar exceder os pedidos por segundo (RPS).

|Campo| Tipo | Valores possíveis |
|:-----|:----:|:----|
|status | string | notStarted: A operação ainda não começou. |
| |  | funcionamento: A operação está a ser processada. |
| |  | falhou: A operação falhou. |
| |  | conseguiu: A operação foi bem sucedida. |

> [!NOTE]
> O nível livre limita a taxa de pedido a 20 chamadas por minuto. O nível pago permite 10 pedidos por segundo (RPS) que podem ser aumentados mediante pedido. Utilize o canal de suporte Azure ou a sua equipa de conta para solicitar um pedido mais elevado por segundo (RPS).

Quando o campo **de estado** tem o valor **bem sucedido,** a resposta JSON contém o conteúdo de texto extraído da sua imagem ou documento. A resposta JSON mantém os agrupamentos de linha originais de palavras reconhecidas. Inclui as linhas de texto extraídas e as suas coordenadas de caixa de delimitação. Cada linha de texto inclui todas as palavras extraídas com as suas coordenadas e pontuações de confiança.

## <a name="sample-json-output"></a>Amostra de saída JSON

Veja o seguinte exemplo de uma resposta JSON bem sucedida:

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-05-28T05:13:21Z",
  "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
  "analyzeResult": {
    "version": "3.1.0",
    "readResults": [
      {
        "page": 1,
        "language": "en",
        "angle": 0.8551,
        "width": 2661,
        "height": 1901,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              67,
              646,
              2582,
              713,
              2580,
              876,
              67,
              821
            ],
            "text": "The quick brown fox jumps",
            "words": [
              {
                "boundingBox": [
                  143,
                  650,
                  435,
                  661,
                  436,
                  823,
                  144,
                  824
                ],
                "text": "The",
                "confidence": 0.958
              }
            ]
          }
        ]
      }
    ]
  }
}
```
### <a name="read-32-preview-adds-text-line-style-latin-languages-only"></a>Ler 3.2 pré-visualização adiciona estilo de linha de texto (apenas línguas latinas)
A [API de pré-visualização Leitura 3.2](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-1/operations/5d986960601faab4bf452005) produz um objeto **de aparência** classificando se cada linha de texto é estilo de impressão ou caligrafia, juntamente com uma pontuação de confiança. Esta funcionalidade é suportada apenas para línguas latinas.

Comece com os [quickstarts OCR SDK de Leitura de Visão de Computador](./quickstarts-sdk/client-library.md) e os [quickstarts da API de Read REST](./QuickStarts/CSharp-hand-text.md) para começar a integrar as capacidades de OCR nas suas aplicações.

## <a name="supported-languages-for-print-text"></a>Línguas suportadas para texto de impressão
A [API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) de leitura suporta a extração de texto impresso em línguas inglesa, espanhola, alemã, francesa, italiana, portuguesa e neerlandesa.

Consulte as [línguas apoiadas](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) para obter a lista completa de línguas apoiadas pelo OCR.

### <a name="read-32-preview-adds-simplified-chinese-and-japanese"></a>Ler 3.2 pré-visualização adiciona chinês e japonês simplificado
A [pré-visualização pública da API read 3.2](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-1/operations/5d986960601faab4bf452005) adiciona suporte para chinês e japonês simplificado. Se o seu cenário necessitar de suportar mais línguas, consulte a secção API do [OCR.](#ocr-api) 

## <a name="supported-languages-for-handwritten-text"></a>Línguas apoiadas para texto manuscrito
A operação Read suporta atualmente a extração de texto manuscrito exclusivamente em inglês.

## <a name="use-the-rest-api-and-sdk"></a>Utilize a REST API e a SDK
A [API Read 3.x REST](./QuickStarts/CSharp-hand-text.md) é a opção preferida para a maioria dos clientes devido à facilidade de integração e à rápida produtividade fora da caixa. O Azure e o serviço de Visão Computacional lidam com escala, desempenho, segurança de dados e necessidades de conformidade enquanto se concentra em satisfazer as necessidades dos seus clientes.

## <a name="deploy-on-premise-with-docker-containers"></a>Implantar no local com contentores Docker
O [recipiente Read Docker (pré-visualização)](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers) permite-lhe implantar as novas capacidades de OCR no seu próprio ambiente local. Os contentores são ótimos para requisitos específicos de governação de dados e segurança.

## <a name="example-outputs"></a>Saídas de exemplo

### <a name="text-from-images"></a>Texto a partir de imagens

A seguinte saída da API de leitura mostra o texto extraído de uma imagem com diferentes ângulos de texto, cores e fontes.

![Uma imagem de várias palavras em diferentes cores e ângulos, com texto extraído listado](./Images/text-from-images-example.png)

### <a name="text-from-documents"></a>Texto a partir de documentos

A API de leitura também pode tomar documentos PDF como entrada.

![Um documento de fatura, com texto extraído listado](./Images/text-from-pdf-example.png)

### <a name="handwritten-text"></a>Texto manuscrito

A operação Ler extrai texto manuscrito a partir de imagens (atualmente apenas em inglês).

![Uma imagem de uma nota manuscrita, com texto extraído listado](./Images/handwritten-example.png)

### <a name="printed-text"></a>Texto impresso

A operação Ler pode extrair texto impresso em várias línguas diferentes.

![Uma imagem de um livro espanhol, com texto extraído listado](./Images/supported-languages-example.png)

### <a name="mixed-language-documents"></a>Documentos linguísticos mistos

A API de Leitura suporta imagens e documentos que contêm várias línguas diferentes, vulgarmente conhecidas como documentos de linguagem mista. Funciona classificando cada linha de texto no documento para a língua detetada antes de extrair o seu conteúdo de texto.

![Uma imagem de frases em várias línguas, com texto extraído listado](./Images/mixed-language-example.png)

## <a name="ocr-api"></a>OCR API

A [API OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) utiliza um modelo de reconhecimento mais antigo, suporta apenas imagens e executa de forma sincronizada, regressando imediatamente com o texto detetado. Consulte as [línguas suportadas pelo OCR](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) e leia a API.

## <a name="data-privacy-and-security"></a>Privacidade e segurança dos dados

Tal como acontece com todos os serviços cognitivos, os desenvolvedores que utilizam os serviços Read/OCR devem estar cientes das políticas da Microsoft sobre os dados dos clientes. Consulte a página de Serviços Cognitivos no [Microsoft Trust Center](https://www.microsoft.com/trust-center/product-overview) para saber mais.

> [!NOTE]
> As operações Do Computador Vison 2.0 RecogniseText estão em vias de ser depreciadas a favor da nova API de leitura abrangida por este artigo. Os clientes existentes devem [transitar para a utilização de operações de Leitura](upgrade-api-versions.md).

## <a name="next-steps"></a>Passos seguintes

- Começa com os [quickstarts SDK de leitura de visão de computador](./quickstarts-sdk/client-library.md) em C#, Java, JavaScript ou Python.
- Utilize os [quickstarts API de Read REST](./QuickStarts/CSharp-hand-text.md) em C#, Java, JavaScript ou Python para aprender a usar as APIs REST REST.
- Saiba mais sobre a [API Read REST](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005).
- Saiba mais sobre a [API de pré-visualização pública Read 3.2](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-1/operations/5d986960601faab4bf452005) com suporte adicional para chinês e japonês simplificado.
