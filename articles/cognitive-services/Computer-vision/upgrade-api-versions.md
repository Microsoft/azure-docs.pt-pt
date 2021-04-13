---
title: Upgrade para Ler v3.0 da API de Visão Computacional
titleSuffix: Azure Cognitive Services
description: Saiba como fazer upgrade para Visão De Computador v3.0 Leia a API a partir de v2.0/v2.1.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 08/11/2020
ms.author: pafarley
ROBOTS: NOINDEX
ms.openlocfilehash: 7a05b04872b4f957e879d93972edc45e2932d059
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364095"
---
# <a name="upgrade-from-read-v2x-to-read-v3x"></a>Upgrade de Ler v2.x para Ler v3.x

Este guia mostra como atualizar o seu código API de recipiente ou nuvem existente de Ler v2.x para Ler v3.x.

## <a name="determine-your-api-path"></a>Determine o seu caminho API
Utilize a tabela seguinte para determinar a **cadeia de versão** no caminho API com base na versão Ler 3.x para a qual está a migrar.

|Tipo de produto| Versão | Cadeia de versão em caminho API 3.x |
|:-----|:----|:----|
|Serviço | Ler 3.0 ou 3.1 | **v3.0** ou **v3.1** respectivamente |
|Serviço | Ler 3.2 pré-visualização | **v3.2-pré-visualização.1** |
|Contentor | Ler 3.0 pré-visualização ou Ler 3.1 pré-visualização | **v3.0** ou **v3.1-preview.2** respectivamente |


Em seguida, utilize as seguintes secções para reduzir as suas operações e substitua a **cadeia de versão** no seu percurso API pelo valor da tabela. Por exemplo, para ler as versões de nuvem de **pré-visualização v3.2** e recipiente, atualize o caminho da API para **https://{endpoint}/visão/v3.2-preview.1/read/analyze[?language]**.

## <a name="servicecontainer"></a>Serviço/Contentor

### `Batch Read File`

|Ler 2.x |Ler 3.x  |
|----------|-----------|
|https://{endpoint}/visão/**v2.0/read/core/asyncBatchAnalyze**     |https://{endpoint}/vision/<**versão>** /ler/analisar[?language]|
    
Um novo parâmetro _linguístico_ opcional está disponível. Se não conhece a linguagem do seu documento, ou pode ser multilíngue, não o inclua. 

### `Get Read Results`

|Ler 2.x |Ler 3.x  |
|----------|-----------|
|https://{endpoint}/visão/**v2.0/read/operations**/{operationId}     |https://{endpoint}/vision/<**versão>** /read/analyzeResults/{operationId}|

### <a name="get-read-operation-result-status-flag"></a>`Get Read Operation Result` bandeira de estado

Quando a chamada `Get Read Operation Result` for bem sucedida, devolve um campo de cordas de estado no corpo JSON.
 
|Ler 2.x |Ler 3.x  |
|----------|-----------|
|`"NotStarted"` |    `"notStarted"`|
|`"Running"` | `"running"`|
|`"Failed"` | `"failed"`|
|`"Succeeded"` | `"succeeded"`|
    
### <a name="api-response-json"></a>Resposta da API (JSON) 

Note as seguintes alterações ao json:
* Em v2.x, `Get Read Operation Result` devolverá o json de reconhecimento de OCR quando o estado estiver `Succeeded"` . Em v3.0, este campo é `succeeded` .
* Para obter a raiz para a matriz de página, mude a hierarquia json de `recognitionResults` `analyzeResult` / `readResults` . A linha por página e as palavras json hierarquia permanecem inalteradas, pelo que não são necessárias alterações de código.
* O ângulo da página `clockwiseOrientation` foi renomeado `angle` para e o intervalo foi alterado de 0 - 360 graus para -180 a 180 graus. Dependendo do seu código, pode ou não ter de fazer alterações, uma vez que a maioria das funções matemáticas podem lidar com qualquer um dos intervalos.

A API v3.0 também introduz as seguintes melhorias que pode opcionalmente alavancar:
* `createdDateTime` e `lastUpdatedDateTime` são adicionados para que possa acompanhar a duração do processamento. Veja a documentação para obter mais informações. 
* `version` diz-lhe a versão da API usada para gerar resultados
* Uma palavra por `confidence` palavra foi adicionada. Este valor é calibrado de modo que um valor de 0,95 significa que há uma probabilidade de 95% de o reconhecimento estar correto. A pontuação de confiança pode ser usada para selecionar que texto enviar para revisão humana. 
    
    
Em 2.X, o formato de saída é o seguinte: 
    
```json
    {
        {
                "status": "Succeeded",
                "recognitionResults": [
                    {
                    "page": 1,
                    "language": "en",
                    "clockwiseOrientation": 349.59,
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
                            },
            // The rest of result is omitted for brevity 
            
}
```
    
Em v3.0, foi ajustado:
    
```json
    {
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
                        },
        // The rest of result is omitted for brevity 
        
    }
```

## <a name="service-only"></a>Apenas serviço

### `Recognize Text`
`Recognize Text` é uma operação *de pré-visualização* que está a ser *depreciada em todas as versões da API de Visão Computacional*. Deve migrar de `Recognize Text` `Read` para (v3.0) ou `Batch Read File` (v2.0, v2.1). v3.0 `Read` inclui modelos mais recentes e melhores para reconhecimento de texto e funcionalidades adicionais, pelo que é recomendado. Para atualizar a partir `Recognize Text` `Read` de:

|Reconhecer Texto 2.x |Ler 3.x  |
|----------|-----------|
|https://{endpoint}/visão/**v2.0/reconhecerText[?mode]**|https://{endpoint}/vision/<**versão>** /ler/analisar[?language]|
    
O parâmetro _do modo_ não é suportado em `Read` . O texto manuscrito e impresso será automaticamente suportado.
    
Um novo parâmetro _linguístico_ opcional está disponível em v3.0. Se não conhece a linguagem do seu documento, ou pode ser multilíngue, não o inclua. 

### `Get Recognize Text Operation Result`

|Reconhecer Texto 2.x |Ler 3.x  |
|----------|-----------|
|https://{endpoint}/visão/**v2.0/textOperations/**{operationId}|https://{endpoint}/vision/<**versão>** /read/analyzeResults/{operationId}|

### <a name="get-recognize-text-operation-result-status-flags"></a>`Get Recognize Text Operation Result` bandeiras de estado
Quando a chamada `Get Recognize Text Operation Result` for bem sucedida, devolve um campo de cordas de estado no corpo JSON. 
 
|Reconhecer Texto 2.x |Ler 3.x  |
|----------|-----------|
|`"NotStarted"` |    `"notStarted"`|
|`"Running"` | `"running"`|
|`"Failed"` | `"failed"`|
|`"Succeeded"` | `"succeeded"`|

### <a name="api-response-json"></a>Resposta da API (JSON)

Note as seguintes alterações ao json:    
* Em v2.x, `Get Read Operation Result` devolverá o json de reconhecimento de OCR quando o estado estiver `Succeeded` . Em v3.x, este campo é `succeeded` .
* Para obter a raiz para a matriz de página, mude a hierarquia json de `recognitionResult` `analyzeResult` / `readResults` . A linha por página e as palavras json hierarquia permanecem inalteradas, pelo que não são necessárias alterações de código.

A API v3.0 também introduz as seguintes melhorias que pode opcionalmente alavancar. Consulte a referência API para mais detalhes:
* `createdDateTime` e `lastUpdatedDateTime` são adicionados para que possa acompanhar a duração do processamento. Veja a documentação para obter mais informações. 
* `version` diz-lhe a versão da API usada para gerar resultados
* Uma palavra por `confidence` palavra foi adicionada. Este valor é calibrado de modo que um valor de 0,95 significa que há uma probabilidade de 95% de o reconhecimento estar correto. A pontuação de confiança pode ser usada para selecionar que texto enviar para revisão humana. 
* `angle` orientação geral do texto no sentido dos ponteiros do relógio, medido em graus entre (-180, 180].
* `width` e `"height"` dar-lhe as dimensões do seu documento, e `"unit"` fornece a unidade dessas dimensões (pixels ou polegadas, dependendo do tipo de documento.)
* `page` documentos multi-páginas são suportados
* `language` a linguagem de entrada do documento (a partir do parâmetro _linguístico_ opcional.)


Em 2.X, o formato de saída é o seguinte: 
    
```json
    {
        {
                "status": "Succeeded",
                "recognitionResult": [
                    {
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
                            },
            // The rest of result is omitted for brevity 
            
    }
```
    
Em v3.x, foi ajustado:
    
```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
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
                        },
        // The rest of result is omitted for brevity 
        
    }
```

## <a name="container-only"></a>Apenas contentor

### `Synchronous Read`

|Ler 2.0 |Ler 3.x  |
|----------|-----------|
|https://{endpoint}/visão/**v2.0/read/core/Analyze**     |https://{endpoint}/vision/<**versão>** /read/syncAnalyze[?language]|
