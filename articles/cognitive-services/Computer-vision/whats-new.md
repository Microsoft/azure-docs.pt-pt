---
title: Quais as novidades na Visão de Computador?
titleSuffix: Azure Cognitive Services
description: Este artigo contém notícias sobre a Visão de Computador.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 01/13/2021
ms.author: pafarley
ms.openlocfilehash: f10319de67a105b4b5e4641c4171ccd0a6e63440
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99490882"
---
# <a name="whats-new-in-computer-vision"></a>Novidades na Visão Computacional

Saiba o que há de novo no serviço. Estes itens podem ser notas de lançamento, vídeos, posts de blog e outros tipos de informação. Marque esta página para ficar a par do serviço.

## <a name="january-2021"></a>Janeiro de 2021

### <a name="spatial-analysis-container-update"></a>Atualização do contentor de análise espacial

Uma nova versão do recipiente de [análise espacial](spatial-analysis-container.md) foi lançada com um novo conjunto de funcionalidades. Este recipiente Docker permite-lhe analisar o streaming de vídeo em tempo real para entender as relações espaciais entre as pessoas e o seu movimento através de ambientes físicos. 

* [As operações de análise espacial](spatial-analysis-operations.md) podem agora ser configuradas para detetar se uma pessoa está a usar uma cobertura facial protetora, como uma máscara. 
    * Um classificador de máscara pode ser ativado para o `personcount` , `personcrossingline` e `personcrossingpolygon` operações configurando o `ENABLE_FACE_MASK_CLASSIFIER` parâmetro.
    * Os atributos `face_mask` e `face_noMask` serão devolvidos como metadados com pontuação de confiança para cada pessoa detetada no fluxo de vídeo
* A *operação personcrossingpolygon* foi alargada para permitir o cálculo do tempo de vida que uma pessoa passa numa zona. Pode definir o `type` parâmetro na configuração de Zona para a operação `zonedwelltime` e um novo evento de tipo *personZoneDwellTimeEvent* incluirá o `durationMs` campo povoado com o número de milissegundos que a pessoa passou na zona.
* **Breaking change**: O evento *personZoneEvent* foi renomeado para *personZoneEnterExitEvent*. Este evento é levantado pela operação *personcrossingpolygon* quando uma pessoa entra ou sai da zona e fornece informações direcionais com o lado numerado da zona que foi atravessada.
* O URL de vídeo pode ser fornecido como "Parâmetro Privado/obfuscado" em todas as operações. A obfuscção é opcional agora e só funcionará se `KEY` e for fornecida como `IV` variáveis ambientais.
* A calibração é ativada por padrão para todas as operações. `do_calibration: false`Desative-o.
* Suporte adicional para recalibração automática (por defeito desativado) através do `enable_recalibration` parâmetro, consulte [as operações de análise espacial](https://docs.microsoft.com/azure/cognitive-services/computer-vision/spatial-analysis-operations) para mais detalhes
* Parâmetros de calibração da câmara para o `DETECTOR_NODE_CONFIG` . Consulte [as operações de análise espacial](https://docs.microsoft.com/azure/cognitive-services/computer-vision/spatial-analysis-operations) para obter mais detalhes.


## <a name="october-2020"></a>Outubro de 2020

### <a name="computer-vision-api-v31-ga"></a>API de Visão Computacional v3.1 GA

A API de Visão Computacional em Disponibilidade Geral foi atualizada para v3.1.

## <a name="september-2020"></a>Setembro de 2020

### <a name="spatial-analysis-container-preview"></a>Pré-visualização do recipiente de análise espacial

O [recipiente de análise espacial](spatial-analysis-container.md) está agora em pré-visualização. A funcionalidade de análise espacial da Visão Computacional permite-lhe analisar vídeos de streaming em tempo real para entender as relações espaciais entre as pessoas e o seu movimento através de ambientes físicos. A análise espacial é um recipiente Docker que podes usar no local. 

### <a name="read-api-v31-public-preview-adds-ocr-for-japanese"></a>Ler API v3.1 Visualização Pública adiciona OCR para japonês
A pré-visualização pública da API v3.1 da Computer Vision adiciona estas capacidades:
* OCR para língua japonesa
* Para cada linha de texto, indique se a aparência é estilo de caligrafia ou impressão, juntamente com uma pontuação de confiança (apenas línguas latinas).
* Para um texto de extrato de documento de várias páginas apenas para páginas selecionadas ou intervalo de página.

* Esta versão de pré-visualização da API de leitura suporta línguas inglesa, holandesa, francesa, alemã, italiana, japonesa, portuguesa, chinesa simplificada e espanhola.

Consulte a [visão geral](concept-recognizing-text.md) da API para saber mais.

> [!div class="nextstepaction"]
> [Saiba mais sobre a API v3.1 Visualização Pública 2](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005)

## <a name="july-2020"></a>Julho de 2020

### <a name="read-api-v31-public-preview-with-ocr-for-simplified-chinese"></a>Leia API v3.1 Pré-visualização pública com OCR para chinês simplificado
A pré-visualização pública da API v3.1 da Computer Vision adiciona suporte para chinês simplificado.

* Esta versão de pré-visualização da API de leitura suporta línguas inglesa, holandesa, francesa, alemã, italiana, portuguesa, chinesa simplificada e espanhola.

Consulte a [visão geral](concept-recognizing-text.md) da API para saber mais.

> [!div class="nextstepaction"]
> [Saiba mais sobre a API v3.1 Visualização Pública 1](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005)

## <a name="may-2020"></a>Maio de 2020
A API de Visão Computacional v3.0 entrou na Disponibilidade Geral, com atualizações para [ler a API:](concept-recognizing-text.md)

* Apoio ao inglês, holandês, francês, alemão, italiano, português e espanhol
* Maior precisão
* Pontuação de confiança para cada palavra extraída
* Novo formato de saída

## <a name="march-2020"></a>Março de 2020

* O TLS 1.2 passa a ser aplicado para todos os pedidos HTTP a este serviço. Para mais informações, consulte [a segurança dos Serviços Cognitivos Azure.](../cognitive-services-security.md)

## <a name="january-2020"></a>Janeiro de 2020

### <a name="read-api-30-public-preview"></a>Leia API 3.0 Antevisão pública

Tem agora a opção de utilizar a versão 3.0 da API de leitura para extrair texto impresso ou manuscrito a partir de imagens. Em comparação com versões anteriores, 3.0 fornece:
* Maior precisão
* Novo formato de saída
* Pontuação de confiança para cada palavra extraída
* Apoio às línguas espanhola e inglesa com o parâmetro linguístico adicional

Siga um [quickstart de texto extrato](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/REST/CSharp-hand-text.md?tabs=version-3) para começar a utilizar a API 3.0.

## <a name="cognitive-service-updates"></a>Atualizações do Serviço Cognitivo

[Anúncios de atualização da Azure para Serviços Cognitivos](https://azure.microsoft.com/updates/?product=cognitive-services)
