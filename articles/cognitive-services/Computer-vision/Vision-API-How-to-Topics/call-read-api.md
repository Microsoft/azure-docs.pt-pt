---
title: Como chamar a API de Leitura
titleSuffix: Azure Cognitive Services
description: Saiba como ligar para a API de leitura e configurar o seu comportamento em detalhe.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: pafarley
ms.openlocfilehash: c1f610a5fb3db41091ddc4d0d921bf1a0cd2cf34
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012233"
---
# <a name="call-the-read-api"></a>Ligue para a API de leitura

Neste guia, você vai aprender a chamar a API de leitura para extrair texto de imagens. Você vai aprender as diferentes maneiras de configurar o comportamento desta API para atender às suas necessidades.

Este guia pressupõe que já <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" criou um recurso de Visão De Computador para criar um recurso de "  target="_blank"> Visão De Computador e obteve uma chave </a> de subscrição e URL de ponto final. Se não o fez, siga um [rápido arranque](../quickstarts-sdk/client-library.md) para começar.

## <a name="submit-data-to-the-service"></a>Enviar dados para o serviço

A chamada de [Leitura](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005) da API retira uma imagem ou documento PDF à medida que a entrada e extrai texto assíncrono.

`https://{endpoint}/vision/v3.2-preview.3/read/analyze[?language][&pages][&readingOrder]`

A chamada retorna com um campo de cabeçalho de resposta chamado `Operation-Location` . O `Operation-Location` valor é um URL que contém o ID da operação para ser usado no passo seguinte.

|Cabeçalho de resposta| Valor de exemplo |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.1/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Faturação** 
>
> A página [de preços da Visão de Computador](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) inclui o nível de preços para Ler. Cada imagem ou página analisada é uma transação. Se ligar para a operação com um documento PDF ou TIFF contendo 100 páginas, a operação Ler contará como 100 transações e será faturado por 100 transações. Se efec se efec se efec se submeter 50 x 100 = 5000 transações.

## <a name="determine-how-to-process-the-data"></a>Determinar como processar os dados

### <a name="language-specification"></a>Especificação linguística

A chamada [de Leitura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) tem um parâmetro de pedido opcional para a linguagem. A leitura suporta a identificação de linguagem automática e documentos multilingues, pelo que só fornece um código linguístico se quiser forçar o documento a ser processado como essa língua específica.

### <a name="natural-reading-order-output-latin-languages-only"></a>Produção de ordem de leitura natural (apenas línguas latinas)
Com a [API de pré-visualização Leitura 3.2,](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)pode especificar a ordem em que as linhas de texto são saídas com o `readingOrder` parâmetro de consulta. Utilize `natural` para uma saída de ordem de leitura mais amiga do homem, como mostra o exemplo seguinte. Esta funcionalidade é suportada apenas para línguas latinas.

:::image border type="content" source="../Images/ocr-reading-order-example.png" alt-text="Exemplo de ordem de leitura de OCR":::



### <a name="select-pages-or-page-ranges-for-text-extraction"></a>Selecione gamas de páginas ou páginas para extração de texto
Com a [API de pré-visualização Leitura 3.2](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005), para documentos de várias páginas, utilize o `pages` parâmetro de consulta para especificar os números de página ou os intervalos de página para extrair texto apenas dessas páginas. O exemplo a seguir mostra um documento com 10 páginas, com texto extraído para ambos os casos - todas as páginas (1-10) e páginas selecionadas (3-6).

:::image border type="content" source="../Images/ocr-select-pages.png" alt-text="Saída de páginas selecionadas":::

## <a name="get-results-from-the-service"></a>Obtenha resultados do serviço

O segundo passo é ligar para a operação [Get Read Results.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d9869604be85dee480c8750) Esta operação toma como entrada o ID de operação que foi criado pela operação Ler. 

`https://{endpoint}/vision/v3.2-preview.3/read/analyzeResults/{operationId}`

Devolve uma resposta JSON que contém um campo **de estado** com os seguintes valores possíveis. 

|Valor | Significado |
|:-----|:----|
| `notStarted`| A operação ainda não começou. |
| `running`| A operação está a ser processada. |
| `failed`| A operação falhou. |
| `succeeded`| A operação foi bem sucedida. |

Você chama a esta operação iterativamente até que ela retorne com o valor **bem sucedido.** Utilize um intervalo de 1 a 2 segundos para evitar exceder os pedidos por segundo (RPS).

> [!NOTE]
> O nível livre limita a taxa de pedido a 20 chamadas por minuto. O nível pago permite 10 pedidos por segundo (RPS) que podem ser aumentados mediante pedido. Utilize o canal de suporte Azure ou a sua equipa de conta para solicitar um pedido mais elevado por segundo (RPS).

Quando o campo **de estado** tem o `succeeded` valor, a resposta JSON contém o conteúdo de texto extraído da sua imagem ou documento. A resposta JSON mantém os agrupamentos de linha originais de palavras reconhecidas. Inclui as linhas de texto extraídas e as suas coordenadas de caixa de delimitação. Cada linha de texto inclui todas as palavras extraídas com as suas coordenadas e pontuações de confiança.

> [!NOTE]
> Os dados submetidos à `Read` operação são temporariamente encriptados e armazenados em repouso por um curto período de tempo e, em seguida, eliminados. Isto permite que as suas aplicações recuperem o texto extraído como parte da resposta do serviço.

### <a name="sample-json-output"></a>Amostra de saída JSON

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

### <a name="handwritten-classification-for-text-lines-latin-languages-only"></a>Classificação manuscrita para linhas de texto (apenas línguas latinas)
A resposta [da API de pré-visualização Leia 3.2](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005) inclui classificar se cada linha de texto é ou não de estilo de caligrafia, juntamente com uma pontuação de confiança. Esta funcionalidade é suportada apenas para línguas latinas. O exemplo a seguir mostra a classificação manuscrita para o texto na imagem.

:::image border type="content" source="../Images/ocr-handwriting-classification.png" alt-text="Exemplo de classificação da caligrafia OCR":::

## <a name="next-steps"></a>Passos seguintes

Para experimentar a API REST, vá à [Referência API de Leitura.](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)