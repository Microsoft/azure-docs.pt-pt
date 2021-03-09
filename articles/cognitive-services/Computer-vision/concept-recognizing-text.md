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
ms.openlocfilehash: e0247560afa8229f4fa5c25ec7dfbbca4f7defb2
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102486103"
---
# <a name="optical-character-recognition-ocr"></a>Reconhecimento ótico de carateres (OCR)

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

> [!NOTE]
> **Entrada linguística** 
>
> A [chamada de Leitura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) tem um parâmetro de pedido opcional para a linguagem. A leitura suporta a identificação de linguagem automática e documentos multilingues, pelo que só fornece um código linguístico se quiser forçar o documento a ser processado como essa língua específica.

## <a name="ocr-demo-examples"></a>Demonstração de OCR (exemplos)

![Demonstrações de OCR](./Images/ocr-demo.gif)

## <a name="step-1-the-read-operation"></a>Passo 1: A operação de leitura

A chamada de [Leitura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) da API retira uma imagem ou documento PDF à medida que a entrada e extrai texto assíncrono. A chamada retorna com um campo de cabeçalho de resposta chamado `Operation-Location` . O `Operation-Location` valor é um URL que contém o ID da operação para ser usado no passo seguinte.

|Cabeçalho de resposta| URL de resultados |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.1/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Faturação** 
>
> A página [de preços da Visão de Computador](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) inclui o nível de preços para Ler. Cada imagem ou página analisada é uma transação. Se ligar para a operação com um documento PDF ou TIFF contendo 100 páginas, a operação Ler contará como 100 transações e será faturado por 100 transações. Se efec se efec se efec se submeter 50 x 100 = 5000 transações.

## <a name="step-2-the-get-read-results-operation"></a>Passo 2: A operação Get Read Results

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

> [!NOTE]
> Os dados submetidos à `Read` operação são temporariamente encriptados e armazenados em repouso por um curto período de tempo e, em seguida, eliminados. Isto permite que as suas aplicações recuperem o texto extraído como parte da resposta do serviço.

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

## <a name="natural-reading-order-output-latin-only"></a>Saída da ordem de leitura natural (apenas em latim)
Com a [API de pré-visualização Leitura 3.2,](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)especifique a ordem em que as linhas de texto são saídas com o `readingOrder` parâmetro de consulta. Utilize `natural` para uma saída de ordem de leitura mais amiga do homem, como mostra o exemplo seguinte. Esta funcionalidade é suportada apenas para línguas latinas.

:::image border type="content" source="./Images/ocr-reading-order-example.png" alt-text="Exemplo de ordem de leitura de OCR":::

## <a name="handwritten-classification-for-text-lines-latin-only"></a>Classificação manuscrita para linhas de texto (apenas em latim)
A resposta [da API de pré-visualização Leia 3.2](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005) inclui classificar se cada linha de texto é ou não de estilo de caligrafia, juntamente com uma pontuação de confiança. Esta funcionalidade é suportada apenas para línguas latinas. O exemplo a seguir mostra a classificação manuscrita para o texto na imagem.

:::image border type="content" source="./Images/ocr-handwriting-classification.png" alt-text="Exemplo de classificação da caligrafia OCR":::

## <a name="select-pages-or-page-ranges-for-text-extraction"></a>Selecione gamas de páginas ou páginas para extração de texto
Com a [API de pré-visualização Leitura 3.2](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005), para documentos de várias páginas, utilize o `pages` parâmetro de consulta para especificar os números de página ou os intervalos de página para extrair texto apenas dessas páginas. O exemplo a seguir mostra um documento com 10 páginas, com texto extraído para ambos os casos - todas as páginas (1-10) e páginas selecionadas (3-6).

:::image border type="content" source="./Images/ocr-select-pages.png" alt-text="Saída de páginas selecionadas":::

## <a name="supported-languages"></a>Linguagens suportadas
As APIs de leitura suportam um total de 73 idiomas para texto de estilo de impressão. Consulte a lista completa de [línguas apoiadas pelo OCR](./language-support.md#optical-character-recognition-ocr). Ocr de estilo manuscrito é suportado exclusivamente para inglês.

## <a name="use-the-cloud-api-or-deploy-on-premise"></a>Use a API de nuvem ou implemente no local
As APIs de nuvem de Leitura 3.x são a opção preferida para a maioria dos clientes devido à facilidade de integração e à rápida produtividade fora da caixa. O Azure e o serviço de Visão Computacional lidam com escala, desempenho, segurança de dados e necessidades de conformidade enquanto se concentra em satisfazer as necessidades dos seus clientes.

Para a colocação no local, o [recipiente Read Docker (pré-visualização)](./computer-vision-how-to-install-containers.md) permite-lhe implantar as novas capacidades de OCR no seu próprio ambiente local. Os contentores são ótimos para requisitos específicos de governação de dados e segurança.

## <a name="ocr-api"></a>OCR API

A [API OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20d) utiliza um modelo de reconhecimento mais antigo, suporta apenas imagens e executa de forma sincronizada, regressando imediatamente com o texto detetado. Consulte as [línguas suportadas pelo OCR](./language-support.md#optical-character-recognition-ocr) e leia a API.

> [!NOTE]
> As operações Do Computador Vison 2.0 RecogniseText estão em vias de ser depreciadas a favor da nova API de leitura abrangida por este artigo. Os clientes existentes devem [transitar para a utilização de operações de Leitura](upgrade-api-versions.md).

## <a name="next-steps"></a>Passos seguintes

- Começa com a API do [Computação Vision REST ou com as rápidas partidas da biblioteca do cliente.](./quickstarts-sdk/client-library.md)
- Saiba mais sobre a [API De Leitura 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005)REST .
- Saiba mais sobre a [API de pré-visualização pública Read 3.2](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005) com suporte para um total de 73 idiomas.
