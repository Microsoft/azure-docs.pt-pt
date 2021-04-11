---
title: Layouts - Reconhecimento de Formulários
titleSuffix: Azure Cognitive Services
description: Aprenda conceitos relacionados com a análise de layout com a API do Reconhecimento de Formulários - utilização e limites.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 73bef21f430bde1c6c2c95d7c3f685cccbbd9179
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467022"
---
# <a name="form-recognizer-layout-service"></a>Serviço de Layout do Reconhecimento de Formulários

O Azure Form Recogniser pode extrair texto, tabelas, marcas de seleção e informação de estrutura a partir de documentos que utilizem o seu serviço Layout. A API layout permite que os clientes levem documentos em diversos formatos e devolvam as representações estruturadas dos documentos. Combina as nossas poderosas capacidades [de Reconhecimento de Caracteres Óticos (OCR)](../computer-vision/concept-recognizing-text.md) com modelos de aprendizagem profunda para extrair texto, tabelas, marcas de seleção e estrutura documental. 

## <a name="what-does-the-layout-service-do"></a>O que faz o serviço Layout?

O Layout API extrai texto, tabelas, marcas de seleção e informação de estrutura de documentos com excecional precisão e devolve uma resposta JSON organizada, estruturada. Os documentos podem ser de uma variedade de formatos e qualidade, incluindo imagens capturadas por telefone, documentos digitalizados e PDFs digitais. A API layout extrairá com precisão a saída estruturada de todos estes documentos.

![Exemplo de layout](./media/layout-tool-example.JPG)

## <a name="try-it-out"></a>Experimente

Para experimentar o Serviço de Layout do Reconhecimento de Formulários, aceda à ferramenta UI de amostra online:

> [!div class="nextstepaction"]
> [Ferramenta de teste de OCR (FOTT)](https://fott-preview.azurewebsites.net)

Você precisará de uma subscrição Azure[(crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)) e um ponto final de [recurso do Reconhecimento de Formulário](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) e chave para experimentar a API do Layout do Reconhecimento de Formulários. 

![Screenshot de UI de amostra; são analisadas as marcas de texto, tabelas e seleção de um documento](./media/analyze-layout.png)

### <a name="input-requirements"></a>Requisitos de entrada 

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-layout-operation"></a>A operação de layout de análise

Primeiro, ligue para a operação [de Layout de Análise.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeLayoutAsync) O Layout de análise requer um documento (imagem, TIFF ou ficheiro PDF) como entrada e extrai o texto, tabelas, marcas de seleção e estrutura do documento. A chamada devolve um campo de cabeçalho de resposta chamado `Operation-Location` . O `Operation-Location` valor é um URL que contém o Resultado ID para ser usado no passo seguinte.

|Cabeçalho de resposta| URL de resultados |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/layout/analyzeResults/44a436324-fc4b-4387-aa06-090cfbf0064f` |

### <a name="natural-reading-order-output-latin-only"></a>Saída da ordem de leitura natural (apenas em latim)

Pode especificar a ordem em que as linhas de texto são saídas com o `readingOrder` parâmetro de consulta. Utilize `natural` para uma saída de ordem de leitura mais amiga do homem, como mostra o exemplo seguinte. Esta funcionalidade é suportada apenas para línguas latinas.

:::image type="content" source="../Computer-vision/Images/ocr-reading-order-example.png" alt-text="Exemplo de ordem de leitura de layout" lightbox="../Computer-vision/Images/ocr-reading-order-example.png":::

### <a name="select-page-numbers-or-ranges-for-text-extraction"></a>Selecione números de página ou intervalos para extração de texto

Para documentos de várias páginas, utilize o `pages` parâmetro de consulta para indicar números de página específicos ou intervalos de página para a extração de texto. O exemplo a seguir mostra um documento com 10 páginas, com texto extraído para ambos os casos - todas as páginas (1-10) e páginas selecionadas (3-6).

:::image type="content" source="../Computer-vision/Images/ocr-select-pages.png" alt-text="Saída de páginas selecionadas de layout":::

## <a name="the-get-analyze-layout-result-operation"></a>A operação Get Analyze Layout Result

O segundo passo é chamar a operação [Get Layout Result.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeLayoutResult) Esta operação toma como entrada o Resultado ID que foi criado pela operação Layout de Análise. Devolve uma resposta JSON que contém um campo **de estado** com os seguintes valores possíveis. 

|Campo| Tipo | Valores possíveis |
|:-----|:----:|:----|
|status | string | `notStarted`: A operação de análise ainda não começou.<br /><br />`running`: A operação de análise está em curso.<br /><br />`failed`: A operação de análise falhou.<br /><br />`succeeded`: A operação de análise foi bem sucedida.|

Chame esta operação iterativamente até que devolva o `succeeded` valor. Utilize um intervalo de 3 a 5 segundos para evitar exceder os pedidos por segundo (RPS).

Quando o campo **de status** tiver o `succeeded` valor, a resposta JSON incluirá o layout extraído, texto, tabelas e marcas de seleção. Os dados extraídos incluem linhas de texto e palavras extraídas, caixas de delimitação, aparição de texto com indicação manuscrita, tabelas e marcas de seleção com indicação selecionada/não selecionada. 

### <a name="handwritten-classification-for-text-lines-latin-only"></a>Classificação manuscrita para linhas de texto (apenas em latim)

A resposta inclui classificar se cada linha de texto é ou não de estilo de caligrafia, juntamente com uma pontuação de confiança. Esta funcionalidade é suportada apenas para línguas latinas. O exemplo a seguir mostra a classificação manuscrita para o texto na imagem.

:::image type="content" source="../Computer-vision/Images/ocr-handwriting-classification.png" alt-text="exemplo de classificação da caligrafia":::

### <a name="sample-json-output"></a>Amostra de saída JSON

A resposta à operação *Get Analyze Layout Result* é uma representação estruturada do documento com toda a informação extraída. Consulte aqui um [ficheiro de documento de amostra](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout.pdf) e a sua saída de layout de [amostra](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout-output.json)de saída estruturada .

A saída JSON tem duas partes:

* `readResults` O nó contém todas as marcas de texto e seleção reconhecidas. O texto é organizado por página, depois por linha, depois por palavras individuais. 
* `pageResults` O nó contém as tabelas e células extraídas com as suas caixas de delimitação, confiança, e uma referência às linhas e palavras em "readResults".

## <a name="example-output"></a>Saída exemplo

### <a name="text"></a>Texto

Layout API extrai texto de documentos (PDF, TIFF) e imagens (JPG, PNG, BMP) com múltiplos ângulos de texto e cores. Aceita fotos de documentos, faxes, texto impresso e/ou manuscrito (apenas inglês) e modos mistos. O texto é extraído com informações fornecidas sobre linhas, palavras, caixas de delimitação, pontuações de confiança e estilo (manuscrito ou outro). Todas as informações de texto estão incluídas na `readResults` secção da saída JSON. 

### <a name="tables"></a>Tables

Layout API extrai tabelas de documentos (PDF, TIFF) e imagens (JPG, PNG, BMP). Os documentos podem ser digitalizados, fotografados ou digitalizados. As tabelas podem ser complexas com células ou colunas fundidas, com ou sem fronteiras, e com ângulos estranhos. As informações de tabela extraídas incluem o número de colunas e linhas, o vão da linha e o período de colunas. Cada célula é extraída com a sua caixa de delimitação e referência ao texto extraído na `readResults` secção. As informações sobre a tabela estão localizadas na `pageResults` secção da saída JSON. 

![Tabelas exemplo](./media/tables-example.jpg)

### <a name="selection-marks"></a>Marcas de seleção

Layout API também extrai marcas de seleção de documentos. As marcas de seleção extraídas incluem a caixa de delimitação, a confiança e o estado (selecionado/não selecionado). As informações sobre a marca de seleção são extraídas na `readResults` secção da saída JSON. 

## <a name="next-steps"></a>Passos seguintes

* Experimente a sua própria extração de layout usando a [ferramenta UI da amostra de reconhecimento](https://fott-preview.azurewebsites.net/) de formulários
* Complete um [quickstart de Reconhecimento de Formulário](quickstarts/client-library.md) para começar a extrair layouts na linguagem de desenvolvimento da sua escolha.

## <a name="see-also"></a>Ver também

* [O que é o Reconhecedor de Formato?](./overview.md)
* [REST API referenciar docs](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeLayoutAsync)
