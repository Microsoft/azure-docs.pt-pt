---
title: IDs - Reconhecimento de Formulários
titleSuffix: Azure Cognitive Services
description: Aprenda conceitos relacionados com a extração de dados a partir de documentos de identidade com a API de IDs pré-construído do Reconhecimento de Formulários.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 01a73e7940f88a3eb6e040f26d255448294cab18
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467843"
---
# <a name="form-recognizer-prebuilt-identification-card-id-model"></a>Modelo de cartão de identificação pré-construído (ID) do Reconhecimento de Formulários

O Azure Form Recogniser pode analisar e extrair informações de cartões de identificação do governo (IDs) utilizando o seu modelo de IDs pré-construído. Combina as nossas poderosas capacidades [de reconhecimento de caracteres óticos (OCR)](../computer-vision/concept-recognizing-text.md) com capacidades de reconhecimento de ID para extrair informações chave dos passaportes mundiais e das licenças de condução dos EUA (todos os 50 estados e D.C).). A IDs API extrai informações-chave destes documentos de identidade, tais como nome próprio, apelido, data de nascimento, número de documento, entre outros. Esta API está disponível na pré-visualização do Formulário Recogniser v2.1 como serviço de nuvem e como um recipiente no local.

## <a name="what-does-the-id-service-do"></a>O que faz o serviço de ID? 

O serviço de IDs pré-construído extrai os valores-chave dos passaportes mundiais e das cartas de condução dos EUA e devolve-os numa resposta JSON estruturada organizada. 

![Carta de Condução de Amostra](./media/id-example-drivers-license.JPG)

![Passaporte de amostra](./media/id-example-passport-result.JPG)

### <a name="fields-extracted"></a>Campos extraídos

|Nome| Tipo | Description | Valor | 
|:-----|:----|:----|:----|
|  País | país | Código de país em conformidade com a norma ISO 3166 | "EUA" | 
|  DataOfBirth | data | DOB em formato YYYY-MM-DD | "1980-01-01" | 
|  Datas DaExpiração | data | Data de validade no formato YYYY-MM-DD | "2019-05-05" |  
|  Número de documentos | string | Número relevante do passaporte, número da carta de condução, etc. | "340020013" |  
|  FirstName | string | Extraído nome dado e inicial do meio, se aplicável | "JENNIFER" | 
|  LastName | string | Sobrenome extraído | "BROOKS" |   
|  Nacionalidade | país | Código de país em conformidade com a norma ISO 3166 | "EUA" |
|  Sexo | género | Os possíveis valores extraídos incluem "M", "F" e "X" | "F" | 
|  MachineReadableZone | objeto | Passaporte extraído MRZ incluindo duas linhas de 44 caracteres cada | "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F19050547100007<<<<<<<<<<<<<<<<<<<<<<< 6 715816" |
|  DocumentoType | string | Tipo de documento, por exemplo, Passaporte, Carta de Condução | "passaporte" |  
|  Endereço | string | Endereço extraído (apenas carta de condução) | "123 STREET ADDRESS YOUR CITY WA 99999-1234"|
|  Region | string | Região extraída, estado, província, etc. (Apenas carta de condução) | "Washington" | 

### <a name="additional-features"></a>Características adicionais

A IDS API também devolve as seguintes informações:

* Nível de confiança no campo (cada campo devolve um valor de confiança associado)
* Texto em bruto OCR (saída de texto extraída por OCR para a totalidade do recibo)
* Caixa de limites de cada campo extraído nas cartas de condução dos EUA
* Caixa de limites para zona legível de máquina (MRZ) em passaportes

  > [!NOTE]
  > IDs pré-construídos não detetam autenticidade de ID
  >
  > Os IDs pré-construídos do Reconhecimento de Formulários extrai dados-chave a partir de dados de identificação. No entanto, não deteta a validade ou autenticidade do documento de identidade original. 

## <a name="try-it-out"></a>Experimente

Para experimentar o serviço de IDs do Reconhecimento de Formulários, aceda à ferramenta online Sample UI:

> [!div class="nextstepaction"]
> [Experimente modelos pré-construídos](https://fott-preview.azurewebsites.net/)

## <a name="input-requirements"></a>Requisitos de entrada

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-id-types"></a>Tipos de ID suportados  

* **IDs pré-construídos v2.1-pré-visualização.3** Extrai valores-chave dos passaportes mundiais e das cartas de condução dos EUA. 

  > [!NOTE]
  > Suporte do tipo ID 
  >
  > Os tipos de ID suportados atualmente incluem passaporte mundial e cartas de condução dos EUA. Estamos ativamente a tentar expandir o nosso suporte de identificação a outros documentos de identidade em todo o mundo.

## <a name="post-analyze-id-document"></a>Documento de Id de análise de post

A operação [de ID de análise](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5f74a7daad1f2612c46f5822) retira uma imagem ou PDF de um ID como entrada e extrai os valores de interesse. A chamada devolve um campo de cabeçalho de resposta chamado `Operation-Location` . O `Operation-Location` valor é um URL que contém o Resultado ID para ser usado no passo seguinte.

|Cabeçalho de resposta| URL de resultados |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/idDocument/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="get-analyze-id-document-result"></a>OBTER Analisar Resultado do Documento de ID

<!---
Need to update this with updated APIM links when available
-->

O segundo passo é chamar a operação [**Get Analyze idDocument Result.**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeFormResult) Esta operação toma como entrada o Resultado ID que foi criado pela operação de ID de análise. Devolve uma resposta JSON que contém um campo **de estado** com os seguintes valores possíveis. Você chama a esta operação iterativamente até que ela retorne com o valor **bem sucedido.** Utilize um intervalo de 3 a 5 segundos para evitar exceder os pedidos por segundo (RPS).

|Campo| Tipo | Valores possíveis |
|:-----|:----:|:----|
|status | string | notStarted: A operação de análise ainda não começou. |
| |  | funcionamento: A operação de análise está em curso. |
| |  | falhou: A operação de análise falhou. |
| |  | conseguiu: A operação de análise foi bem sucedida. |

Quando o campo **de status** tiver o valor **bem sucedido,** a resposta JSON incluirá os resultados de compreensão de recibo e reconhecimento de texto. O resultado dos IDs é organizado como um dicionário de valores de campo nomeados, onde cada valor contém o texto extraído, valor normalizado, caixa de delimitação, confiança e elementos de palavra correspondentes. O resultado do reconhecimento de texto é organizado como uma hierarquia de linhas e palavras, com texto, caixa de delimitação e informação de confiança.

![resultados da receção da amostra](./media/id-example-passport-result.JPG)

### <a name="sample-json-output"></a>Amostra de saída JSON

Veja o seguinte exemplo de uma resposta JSON bem sucedida: O `readResults` nó contém todo o texto reconhecido. O texto é organizado por página, depois por linha, depois por palavras individuais. O `documentResults` nó contém os valores de ID que o modelo descobriu. Este nó é também onde você encontrará pares de chave/valor úteis como o primeiro nome, apelido, número de documento, e muito mais.

```json
{ 
   "status": "succeeded",
  "createdDateTime": "2021-03-04T22:29:33Z",
  "lastUpdatedDateTime": "2021-03-04T22:29:36Z",
  "analyzeResult": {
    "version": "2.1.0",
    "readResults": [
     {
        "page": 1,
        "angle": 0.3183,
        "width": 549,
        "height": 387,
        "unit": "pixel",
        "lines": [
          {
            "text": "PASSPORT",
            "boundingBox": [
              57,
              10,
              120,
              11,
              119,
              22,
              57,
              22
            ],
            "words": [
              {
                "text": "PASSPORT",
                "boundingBox": [
                  57,
                  11,
                  119,
                  11,
                  118,
                  23,
                  57,
                  22
                ],
                "confidence": 0.994
              }
            ],
          ...
      }
    ],
    
     "documentResults": [
      {
        "docType": "prebuilt:idDocument:passport",
        "docTypeConfidence": 0.995,
        "pageRange": [
          1,
          1
        ],
        "fields": {
          "Country": {
            "type": "country",
            "valueCountry": "USA",
            "text": "USA"
          },
          "DateOfBirth": {
            "type": "date",
            "valueDate": "1980-01-01",
            "text": "800101"
          },
          "DateOfExpiration": {
            "type": "date",
            "valueDate": "2019-05-05",
            "text": "190505"
          },
          "DocumentNumber": {
            "type": "string",
            "valueString": "340020013",
            "text": "340020013"
          },
          "FirstName": {
            "type": "string",
            "valueString": "JENNIFER",
            "text": "JENNIFER"
          },
          "LastName": {
            "type": "string",
            "valueString": "BROOKS",
            "text": "BROOKS"
          },
          "Nationality": {
            "type": "country",
            "valueCountry": "USA",
            "text": "USA"
          },
          "Sex": {
            "type": "gender",
            "valueGender": "F",
            "text": "F"
          },
          "MachineReadableZone": {
            "type": "object",
            "text": "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816",
            "boundingBox": [
              16,
              314.1,
              504.2,
              317,
              503.9,
              363,
              15.7,
              360.1
            ],
            "page": 1,
            "confidence": 0.384,
            "elements": [
              "#/readResults/0/lines/33/words/0",
              "#/readResults/0/lines/33/words/1",
              "#/readResults/0/lines/33/words/2",
              "#/readResults/0/lines/33/words/3",
              "#/readResults/0/lines/33/words/4",
              "#/readResults/0/lines/34/words/0"
            ]
          },
          "DocumentType": {
            "type": "string",
            "text": "passport",
            "confidence": 0.995
          }
        }
      }
    ]
  }
}
```


## <a name="next-steps"></a>Passos seguintes

- Experimente as suas próprias identificações e amostras na UI da [amostra do reconhecimento de formulários.](https://fott-preview.azurewebsites.net/)
- Complete um [quickstart do Form Recogniser](quickstarts/client-library.md) para começar a escrever uma aplicação de processamento de ID com o Form Recogniser no idioma de desenvolvimento da sua escolha.

## <a name="see-also"></a>Ver também

* [**O que é o Reconhecedor de Formato?**](./overview.md)
* [**REST API referenciar docs**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)
