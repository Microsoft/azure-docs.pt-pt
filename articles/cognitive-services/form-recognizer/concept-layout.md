---
title: Layouts - Reconhecimento de Formulários
titleSuffix: Azure Cognitive Services
description: Aprenda conceitos relacionados com a análise de layout com a API do Reconhecimento de Formulários - utilização e limites.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: pafarley
ms.openlocfilehash: 60ef490bbba4dca4f01b82967ad6ea8636e7bd4e
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353702"
---
# <a name="form-recognizer-layout-service"></a>Serviço de Layout do Reconhecimento de Formulários

O Azure Form Recogniser pode extrair texto, tabelas, marcas de seleção e informação de estrutura a partir de documentos que utilizem o seu serviço Layout. A API layout permite que os clientes levem documentos em diversos formatos e devolvam dados estruturados e representação do documento. Combina as nossas poderosas capacidades [de Reconhecimento de Caracteres Óticos (OCR)](../computer-vision/concept-recognizing-text.md) com a compreensão documental de modelos de aprendizagem profunda para extrair texto, tabelas, marcas de seleção e estrutura de documentos. 

## <a name="what-does-the-layout-service-do"></a>O que faz o serviço Layout?

O Layout API extrai texto, tabelas, marcas de seleção e informação de estrutura de documentos com excecional precisão e devolve-os numa resposta JSON estruturada organizada. Os documentos podem ser de uma variedade de formatos e qualidade, incluindo imagens capturadas por telefone, documentos digitalizados e PDFs digitais. A API layout extrairá a saída estruturada de todos estes documentos.

![Exemplo de layout](./media/layout-tool-example.JPG)

## <a name="try-it-out"></a>Experimente

Para experimentar o Serviço de Layout do Reconhecimento de Formulários, aceda à ferramenta UI de amostra online:

> [!div class="nextstepaction"]
> [IU de Exemplo](https://fott-preview.azurewebsites.net/)

Você precisará de uma subscrição Azure[(crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)) e um ponto final de [recurso do Reconhecimento de Formulário](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) e chave para experimentar a API do Layout do Reconhecimento de Formulários. 

![Screenshot de UI de amostra; são analisadas as marcas de texto, tabelas e seleção de um documento](./media/analyze-layout.png)

### <a name="input-requirements"></a>Requisitos de entrada 

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-layout-operation"></a>A operação de layout de análise

A operação ['Layout' de análise](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync) requer um documento (ficheiro de imagem, TIFF ou PDF) à medida que a entrada e extrai o texto, tabelas, marcas de seleção e estrutura do documento. A chamada devolve um campo de cabeçalho de resposta chamado `Operation-Location` . O `Operation-Location` valor é um URL que contém o Resultado ID para ser usado no passo seguinte.

|Cabeçalho de resposta| URL de resultados |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/layout/analyzeResults/44a436324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-layout-result-operation"></a>A operação Get Analyze Layout Result

O segundo passo é chamar a operação [Get Layout Result.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeLayoutResult) Esta operação toma como entrada o Resultado ID que foi criado pela operação Layout de Análise. Devolve uma resposta JSON que contém um campo **de estado** com os seguintes valores possíveis. 

|Campo| Tipo | Valores possíveis |
|:-----|:----:|:----|
|status | string | `notStarted`: A operação de análise ainda não começou.<br /><br />`running`: A operação de análise está em curso.<br /><br />`failed`: A operação de análise falhou.<br /><br />`succeeded`: A operação de análise foi bem sucedida.|

Chama a esta operação iterativamente até que volte com o `succeeded` valor. Utilize um intervalo de 3 a 5 segundos para evitar exceder os pedidos por segundo (RPS).

Quando o campo **de status** tiver o `succeeded` valor, a resposta JSON incluirá os resultados de extração de layout, texto, tabelas e marcas de seleção extraídas. Os dados extraídos contêm as linhas e palavras de texto extraídas, caixa de limitação, indicação manuscrita de aparência de texto, tabelas e marcas de seleção com indicação de selecionado/não selecionado. 

### <a name="sample-json-output"></a>Amostra de saída JSON

A resposta à operação Get Analyze Layout Result será a representação estruturada do documento com toda a informação extraída. Consulte aqui um [ficheiro de documento de amostra](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout.pdf) e a sua saída de layout de [amostra](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout-output.json)de saída estruturada .

A saída JSON tem duas partes: 
* `"readResults"` O nó contém todas as marcas de texto e seleção reconhecidas. O texto é organizado por página, depois por linha, depois por palavras individuais. 
* `"pageResults"` O nó contém as tabelas e células extraídas com as suas caixas de delimitação, confiança, e uma referência às linhas e palavras em "readResults".

## <a name="example-output"></a>Saída exemplo

### <a name="text"></a>Texto

Layout extrai texto de documentos (PDF, TIFF) e imagens (jpg, png, bmp) com diferentes ângulos de texto, cores, ângulos, fotos de documentos, faxes, impressos, manuscritos (apenas em inglês) e modos mistos. O texto é extraído com informações sobre linhas, palavras, caixas de delimitação, pontuações de confiança e estilo (manuscrito ou outro). Todas as informações de texto estão incluídas na `"readResults"` secção da saída JSON. 

### <a name="tables"></a>Tabelas

Layout extrai tabelas de documentos (PDF, TIFF) e imagens (jpg, png, bmp). Os documentos podem ser digitalizados, fotografados ou digitalizados. As tabelas podem ser tabelas complexas com células ou colunas fundidas, com ou sem fronteiras, e com ângulos estranhos. As tabelas extraídas incluem o número de colunas e linhas, o vão da linha e o vão das colunas. Cada célula é extraída com a sua caixa de delimitação e referência ao texto extraído na `"readResults"` secção. As informações sobre a tabela estão localizadas na `"pageResults"` secção da saída JSON. 

![Tabelas exemplo](./media/tables-example.jpg)

### <a name="selection-marks"></a>Marcas de seleção

Layout também extrai marcas de seleção de documentos. As marcas de seleção extraídas incluem a caixa de delimitação, confiança e estado (selecionado/não selecionado). As informações sobre a marca de seleção são extraídas na `"readResults"` secção da saída JSON. 

## <a name="next-steps"></a>Passos seguintes

- Experimente a sua própria extração de layout usando o [Formulário Recogniser Sample UI](https://fott-preview.azurewebsites.net/)
- Complete um [início rápido da biblioteca do cliente Do Reconhecimento de Formulários](quickstarts/client-library.md) para começar a extrair layouts no idioma à sua escolha.
- Ou, siga os [dados do Layout de Extrato](./QuickStarts/python-layout.md) rápido para implementar a extração de dados de layout usando Python e a API REST.

## <a name="see-also"></a>Ver também

* [O que é o Reconhecedor de Formato?](./overview.md)
* [REST API referenciar docs](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync)