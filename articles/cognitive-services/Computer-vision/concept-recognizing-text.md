---
title: Reconhecimento de Caracteres Óticos (OCR) - Visão computacional
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados com o reconhecimento de caracteres óticos (OCR) a partir de imagens e documentos com texto impresso e manuscrito utilizando a API de Visão Computacional.
services: cognitive-services
author: msbbonsu
manager: netahw
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: t-bebon
ms.custom: seodec18
ms.openlocfilehash: 2b3f9b0a4bec76f1f5f9b1f42ec33fdf5e2678bf
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/04/2020
ms.locfileid: "87760163"
---
# <a name="optical-character-recognition-ocr"></a>Reconhecimento Ótico de Carateres (OCR)

A API de Visão Computacional da Microsoft inclui capacidades de reconhecimento de caracteres óticos (OCR) que extraem texto impresso ou manuscrito a partir de imagens e documentos PDF. As APIs OCR extraem texto de documentos analógicos (imagens, documentos digitalizados) e documentos digitalizados. Pode extrair texto de imagens na natureza, como fotos de placas ou contentores com números de série, bem como de documentos - faturas, contas, relatórios financeiros, artigos e muito mais. O novo API de Leitura OCR está disponível como parte do serviço gerido na nuvem ou nas instalações (contentores). Além disso, suporta redes virtuais e pontos finais privados para atender às necessidades de conformidade e privacidade da sua empresa.

## <a name="read-api"></a>Ler API 

[A API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) de Leitura da Visão de Computador é a mais recente tecnologia OCR da Microsoft que extrai texto impresso em vários idiomas, texto manuscrito (apenas inglês), dígitos e símbolos de moeda a partir de imagens e documentos PDF de várias páginas. É otimizado para extrair texto de imagens pesadas de texto silvestre e documentos PDF de várias páginas com línguas mistas. Suporta a deteção de texto impresso e manuscrito (apenas em inglês) na mesma imagem ou documento. Consulte a página completa da página de [idiomas suportados por OCR.](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr)

### <a name="how-ocr-works"></a>Como funciona o OCR

A [API de leitura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) suporta documentos pesados de texto até 2000 páginas e, portanto, executa assíncrono. O primeiro passo é chamar a operação Ler. A operação Ler requer uma imagem ou documento PDF à medida que a entrada e devolve um ID de operação. 

O segundo passo é convocar a operação [Get Results.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750) Esta operação requer a operação ID que foi criada pela operação Ler. Em seguida, devolve o conteúdo de texto extraído da sua imagem ou documento sob a forma de JSON. A resposta JSON mantém os agrupamentos de linha originais de palavras reconhecidas. Inclui as linhas de texto extraídas e as suas coordenadas de caixa de delimitação. Cada linha de texto inclui todas as palavras extraídas com as suas coordenadas e pontuações de confiança.

Se necessário, leia a rotação da página reconhecida devolvendo o offset rotativo em graus sobre o eixo de imagem horizontal, como se pode ver na seguinte ilustração.

![Como o OCR converte imagens e documentos em saída estruturada com texto extraído](./Images/how-ocr-works.svg)

### <a name="sample-ocr-output"></a>Amostra de saída de OCR

Uma resposta bem sucedida é devolvida no formato JSON, como mostra o seguinte exemplo:

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-05-28T05:13:21Z",
  "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
  "analyzeResult": {
    "version": "3.0.0",
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

Siga o [extrato impresso e manuscrito de texto](./QuickStarts/CSharp-hand-text.md) rápido para implementar OCR usando C# e a API REST.

### <a name="input-requirements"></a>Requisitos de entrada

A API de Leitura tem as seguintes entradas:
* Formatos de ficheiros suportados: JPEG, PNG, BMP, PDF e TIFF
* Para PDF e TIFF, até 2000 páginas são processadas. Para assinantes de nível gratuito, apenas as duas primeiras páginas são processadas.
* O tamanho do ficheiro deve ser inferior a 50 MB e dimensões de pelo menos 50 x 50 pixels e, no máximo, 10000 x 10000 pixels.
* As dimensões PDF devem ser no máximo 17 x 17 polegadas, correspondentes a tamanhos legais ou de papel A3 e menores.

### <a name="text-from-images"></a>Texto a partir de imagens

A seguinte saída da API de leitura mostra as linhas de texto e palavras extraídas de uma imagem com texto em diferentes ângulos, cores e fontes

![Uma imagem sendo girada e seu texto sendo lido e delineado](./Images/text-from-images-example.png)

### <a name="text-from-documents"></a>Texto a partir de documentos

Além das imagens, a API de Leitura toma como entrada um documento PDF.

![Uma imagem sendo girada e seu texto sendo lido e delineado](./Images/text-from-documents-example.png)


### <a name="handwritten-text-in-english"></a>Texto manuscrito em inglês

Neste momento, a operação Read suporta a extração de texto manuscrito exclusivamente em inglês.

![Uma imagem sendo girada e seu texto sendo lido e delineado](./Images/handwritten-example.png)

### <a name="printed-text-in-supported-languages"></a>Texto impresso em línguas apoiadas

A API de Leitura 3.0 suporta a extração de texto impresso em línguas inglesa, espanhola, alemã, francesa, italiana, portuguesa e neerlandesa. [Leia 3.1 A pré-visualização pública da API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005) adiciona suporte para chinês simplificado. Se o seu cenário necessitar de apoiar mais línguas, consulte a visão geral da API do OCR neste documento. Consulte a lista de todas as [línguas apoiadas pelo OCR](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr)

![Uma imagem sendo girada e seu texto sendo lido e delineado](./Images/supported-languages-example.png)

### <a name="mixed-languages-support"></a>Suporte de línguas mistas

A API de Leitura suporta imagens e documentos com múltiplas línguas neles, vulgarmente conhecidas como documentos de linguagem mista. Fá-lo classificando cada linha de texto do documento para a língua detetada antes de extrair o conteúdo do texto.

![Uma imagem sendo girada e seu texto sendo lido e delineado](./Images/mixed-language-example.png)

### <a name="data-privacy-and-security"></a>Privacidade e segurança dos dados

Tal como acontece com todos os serviços cognitivos, os desenvolvedores que usam o serviço Read devem estar cientes das políticas da Microsoft sobre os dados dos clientes. Consulte a página de Serviços Cognitivos no [Microsoft Trust Center](https://www.microsoft.com/en-us/trust-center/product-overview) para saber mais.

### <a name="containers-for-on-premise-deployment"></a>Contentores para implantação no local

A leitura também está disponível como um recipiente Docker (pré-visualização) para permitir a implementação das novas capacidades de OCR no seu próprio ambiente. Os contentores são ótimos para requisitos específicos de segurança e governação de dados. Veja [como instalar e executar Recipientes de leitura.](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)


## <a name="ocr-api"></a>OCR API

A [API OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) utiliza um modelo de reconhecimento mais antigo, suporta apenas imagens e executa de forma sincronizada, regressando imediatamente com o texto detetado. Consulte as [línguas suportadas por OCR](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) do que a API lida.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [API De Leitura 3.0](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005)REST .
- Saiba mais sobre a [API de pré-visualização pública Read 3.1](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005) com suporte adicional para chinês simplificado.
- Siga o [quickstart de texto extrato](./QuickStarts/CSharp-hand-text.md) para implementar OCR usando C#, Java, JavaScript ou Python juntamente com a REST API.
