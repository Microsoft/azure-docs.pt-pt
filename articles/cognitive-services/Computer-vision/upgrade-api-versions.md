---
title: Upgrade para v3.0 da API de Visão Computacional
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
ms.openlocfilehash: 6e695fcfacac19ca82273d84d049bdb2afe14b54
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214181"
---
# <a name="upgrade-to-computer-vision-v30-read-api-from-v20v21"></a>Upgrade para Visão Computacional v3.0 Ler API a partir de v2.0/v2.1

Este guia mostra como atualizar o código API da Visão de Computador v2.0 ou v2.1 REST para as operações de leitura v3.0. 

## <a name="upgrade-batch-read-file-to-read"></a>Upgrade `Batch Read File` para `Read`


1. Altere o caminho da API para `Batch Read File` 2.x da seguinte forma:


    |Ler 2.x |Ler 3.0  |
    |----------|-----------|
    |https://{endpoint}/visão/**v2.0/read/core/asyncBatchAnalyze**     |https://{endpoint}/vision/**v3.0/read/analyze**[?language]|
    
    Um novo parâmetro _linguístico_ opcional está disponível. Se não conhece a linguagem do seu documento, ou pode ser multilíngue, não o inclua. 

2. Altere o caminho da API para `Get Read Results` 2.x da seguinte forma:

    |Ler 2.x |Ler 3.0  |
    |----------|-----------|
    |https://{endpoint}/vision/**v2.0**/read/**operations**/{operationId}     |https://{endpoint}/vision/**v3.0**/read/**analyzeResults**/{operationId}|

3. Altere o código para verificar os resultados do json de `Get Read Operation Result` . Quando a chamada `Get Read Operation Result` for bem sucedida, devolve um campo de cordas de estado no corpo JSON. Os seguintes valores de v2.0 foram alterados para melhor alinhar com as outras APIs de descanso do serviço cognitivo. 
 
    |Ler 2.x |Ler 3.0  |
    |----------|-----------|
    |`"NotStarted"` |   `"notStarted"`|
    |`"Running"` | `"running"`|
    |`"Failed"` | `"failed"`|
    |`"Succeeded"` | `"succeeded"`|
    
4. Altere o seu código para interpretar o resultado final do reconhecimento JSON de `Get Read Operation Result` . 

    Note as seguintes alterações ao json:
    
    - Em v2.x, `"Get Read Operation Result"` devolverá o json de reconhecimento de OCR quando o estado estiver `"Succeeded"` . Em v3.0, este campo é `"succeeded"` .
    - Para obter a raiz para a matriz de página, mude a hierarquia json de `"recognitionResults"` `"analyzeResult"` / `"readResults"` . A linha por página e as palavras json hierarquia permanecem inalteradas, pelo que não são necessárias alterações de código.
    -   O ângulo da página `"clockwiseOrientation"` foi renomeado `"angle"` para e o intervalo foi alterado de 0 - 360 graus para -180 a 180 graus. Dependendo do seu código, pode ou não ter de fazer alterações, uma vez que a maioria das funções matemáticas podem lidar com qualquer um dos intervalos.
    -   A API v3.0 também introduz as seguintes melhorias que pode opcionalmente alavancar: - `"createdDateTime"` e `"lastUpdatedDateTime"` são adicionadas para que possa acompanhar a duração do processamento. Consulte a documentação para mais detalhes. 
        - `"version"` diz-lhe a versão da API usada para gerar resultados
        - Uma palavra por `"confidence"` palavra foi adicionada. Este valor é calibrado de modo que um valor de 0,95 significa que há uma probabilidade de 95% de o reconhecimento estar correto. A pontuação de confiança pode ser usada para selecionar que texto enviar para revisão humana. 
    
    
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

## <a name="upgrade-from-recognize-text-to-read"></a>Upgrade de `Recognize Text` para `Read`
`Recognize Text` é uma operação *de pré-visualização* que está a ser *depreciada em todas as versões da API de Visão Computacional*. Deve migrar de `Recognize Text` `Read` para (v3.0) ou `Batch Read File` (v2.0, v2.1). v3.0 `Read` inclui modelos mais recentes e melhores para reconhecimento de texto e funcionalidades adicionais, pelo que é recomendado. Para atualizar a partir `Recognize Text` `Read` de:

1. Altere o caminho da API para `Recognize Text` v2.x da seguinte forma:


    |Reconhecer Texto 2.x |Ler 3.0  |
    |----------|-----------|
    |https://{endpoint}/vision/**v2.0/recogniseText[?mode]**|https://{endpoint}/vision/**v3.0/read/analyze**[?language]|
    
    O parâmetro _do modo_ não é suportado em `Read` . O texto manuscrito e impresso será automaticamente suportado.
    
    Um novo parâmetro _linguístico_ opcional está disponível em v3.0. Se não conhece a linguagem do seu documento, ou pode ser multilíngue, não o inclua. 

2. Altere o caminho da API para `Get Recognize Text Operation Result` v2.x da seguinte forma:

    |Reconhecer Texto 2.x |Ler 3.0  |
    |----------|-----------|
    |https://{endpoint}/vision/**v2.0/textOperations/**{operationId}|https://{endpoint}/vision/**v3.0/read/analyzeResults**/{operationId}|

3. Altere o código para verificar os resultados do json de `Get Recognize Text Operation Result` . Quando a chamada `Get Read Operation Result` for bem sucedida, devolve um campo de cordas de estado no corpo JSON. 
 
    |Reconhecer Texto 2.x |Ler 3.0  |
    |----------|-----------|
    |`"NotStarted"` |   `"notStarted"`|
    |`"Running"` | `"running"`|
    |`"Failed"` | `"failed"`|
    |`"Succeeded"` | `"succeeded"`|


4. Altere o seu código para interpretar o resultado final de reconhecimento JSON `Get Recognize Text Operation Result` de suporte `Get Read Operation Result` .

    Note as seguintes alterações ao json:    

    - Em v2.x, `"Get Read Operation Result"` devolverá o json de reconhecimento de OCR quando o estado estiver `"Succeeded"` . Em v3.0, este campo é `"succeeded"` .
    - Para obter a raiz para a matriz de página, mude a hierarquia json de `"recognitionResult"` `"analyzeResult"` / `"readResults"` . A linha por página e as palavras json hierarquia permanecem inalteradas, pelo que não são necessárias alterações de código.
    -   A API v3.0 também introduz as seguintes melhorias que pode opcionalmente alavancar. Consulte a referência API para obter mais detalhes: - `"createdDateTime"` e `"lastUpdatedDateTime"` são adicionados para que possa acompanhar a duração do processamento. Consulte a documentação para mais detalhes. 
        - `"version"` diz-lhe a versão da API usada para gerar resultados
        - Uma palavra por `"confidence"` palavra foi adicionada. Este valor é calibrado de modo que um valor de 0,95 significa que há uma probabilidade de 95% de o reconhecimento estar correto. A pontuação de confiança pode ser usada para selecionar que texto enviar para revisão humana. 
        - `"angle"` orientação geral do texto no sentido dos ponteiros do relógio, medido em graus entre (-180, 180].
        -  `"width"` e `"height"` dar-lhe as dimensões do seu documento, e `"unit"` fornece a unidade dessas dimensões (pixels ou polegadas, dependendo do tipo de documento.)
        - `"page"` documentos multi-páginas são suportados
        - `"language"` a linguagem de entrada do documento (a partir do parâmetro _linguístico_ opcional.)


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
    
## <a name="all-other-operations"></a>Todas as outras operações

Não há outras mudanças entre v2. X e v3.0 da API de Visão Computacional. Pode simplesmente modificar o caminho da API para substituir `v2.0` `v3.0` .
