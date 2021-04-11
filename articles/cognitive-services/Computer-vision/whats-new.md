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
ms.openlocfilehash: f102bbe45fd5eea853ba63def4f1c1a92888ca46
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285948"
---
# <a name="whats-new-in-computer-vision"></a>Novidades na Visão Computacional

Saiba o que há de novo no serviço. Estes itens podem ser notas de lançamento, vídeos, posts de blog e outros tipos de informação. Marque esta página para ficar a par do serviço.

## <a name="march-2021"></a>março de 2021

### <a name="computer-vision-32-public-preview-update"></a>Visão Computacional 3.2 Atualização de Pré-visualização Pública

A pré-visualização pública da API v3.2 da Visão Computacional foi atualizada. O lançamento de pré-visualização tem todas as funcionalidades de Visão de Computador, juntamente com APIs de leitura e análise atualizadas.

> [!div class="nextstepaction"]
> [Ver Visualização computorizada v3.2 visualização pública 3](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)

## <a name="february-2021"></a>Fevereiro de 2021

### <a name="read-api-v32-public-preview-with-ocr-support-for-73-languages"></a>Leia API v3.2 Visualização pública com suporte OCR para 73 línguas
A pré-visualização pública da API v3.2 da Computer Vision, disponível como serviço de nuvem e contentor Docker, inclui estas atualizações:
* [OCR para 73 línguas,](./language-support.md#optical-character-recognition-ocr) incluindo línguas chinesas simplificadas e tradicionais, japonesas, coreanas e latinas.
* Ordem de leitura natural para a produção da linha de texto (apenas línguas latinas)
* Classificação do estilo de caligrafia para linhas de texto juntamente com uma pontuação de confiança (apenas línguas latinas).
* Extrair texto apenas para páginas selecionadas para um documento de várias páginas.
* Disponível como [recipiente distrosiável](./computer-vision-how-to-install-containers.md?tabs=version-3-2) para implantação no local.

Consulte o guia de como ler [a API para](Vision-API-How-to-Topics/call-read-api.md) saber mais.

> [!div class="nextstepaction"]
> [Utilize a API de Leitura v3.2 Visualização pública](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)


## <a name="january-2021"></a>Janeiro de 2021

### <a name="spatial-analysis-container-update"></a>Atualização do contentor de análise espacial

Uma nova versão do [recipiente de Análise Espacial](spatial-analysis-container.md) foi lançada com um novo conjunto de funcionalidades. Este recipiente Docker permite-lhe analisar o streaming de vídeo em tempo real para entender as relações espaciais entre as pessoas e o seu movimento através de ambientes físicos. 

* [As operações de análise espacial](spatial-analysis-operations.md) podem agora ser configuradas para detetar se uma pessoa está a usar uma cobertura facial protetora, como uma máscara. 
    * Um classificador de máscara pode ser ativado para o `personcount` , `personcrossingline` e `personcrossingpolygon` operações configurando o `ENABLE_FACE_MASK_CLASSIFIER` parâmetro.
    * Os atributos `face_mask` e `face_noMask` serão devolvidos como metadados com pontuação de confiança para cada pessoa detetada no fluxo de vídeo
* A *operação personcrossingpolygon* foi alargada para permitir o cálculo do tempo de vida que uma pessoa passa numa zona. Pode definir o `type` parâmetro na configuração de Zona para a operação `zonedwelltime` e um novo evento de tipo *personZoneDwellTimeEvent* incluirá o `durationMs` campo povoado com o número de milissegundos que a pessoa passou na zona.
* **Breaking change**: O evento *personZoneEvent* foi renomeado para *personZoneEnterExitEvent*. Este evento é levantado pela operação *personcrossingpolygon* quando uma pessoa entra ou sai da zona e fornece informações direcionais com o lado numerado da zona que foi atravessada.
* O URL de vídeo pode ser fornecido como "Parâmetro Privado/obfuscado" em todas as operações. A obfuscção é opcional agora e só funcionará se `KEY` e for fornecida como `IV` variáveis ambientais.
* A calibração é ativada por padrão para todas as operações. `do_calibration: false`Desative-o.
* Suporte adicional para recalibração automática (por defeito desativado) através do `enable_recalibration` parâmetro, consulte [as operações de Análise Espacial](./spatial-analysis-operations.md) para mais detalhes
* Parâmetros de calibração da câmara para o `DETECTOR_NODE_CONFIG` . Consulte as [operações de Análise Espacial](./spatial-analysis-operations.md) para obter mais detalhes.


## <a name="october-2020"></a>Outubro de 2020

### <a name="computer-vision-api-v31-ga"></a>API de Visão Computacional v3.1 GA

A API de Visão Computacional em Disponibilidade Geral foi atualizada para v3.1.

## <a name="september-2020"></a>Setembro de 2020

### <a name="spatial-analysis-container-preview"></a>Pré-visualização do recipiente de análise espacial

O [recipiente de Análise Espacial](spatial-analysis-container.md) está agora em pré-visualização. A funcionalidade de Análise Espacial da Visão Computacional permite-lhe analisar vídeos de streaming em tempo real para compreender as relações espaciais entre as pessoas e o seu movimento através de ambientes físicos. Análise Espacial é um recipiente Docker que pode usar no local. 

### <a name="read-api-v31-public-preview-adds-ocr-for-japanese"></a>Ler API v3.1 Visualização Pública adiciona OCR para japonês
A pré-visualização pública da API v3.1 da Computer Vision adiciona estas capacidades:
* OCR para língua japonesa
* Para cada linha de texto, indique se a aparência é estilo de caligrafia ou impressão, juntamente com uma pontuação de confiança (apenas línguas latinas).
* Para um texto de extrato de documento de várias páginas apenas para páginas selecionadas ou intervalo de página.

* Esta versão de pré-visualização da API de leitura suporta línguas inglesa, holandesa, francesa, alemã, italiana, japonesa, portuguesa, chinesa simplificada e espanhola.

Consulte o guia de como ler [a API para](Vision-API-How-to-Topics/call-read-api.md) saber mais.

> [!div class="nextstepaction"]
> [Saiba mais sobre a API v3.1 Visualização Pública 2](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005)

## <a name="july-2020"></a>Julho de 2020

### <a name="read-api-v31-public-preview-with-ocr-for-simplified-chinese"></a>Leia API v3.1 Pré-visualização pública com OCR para chinês simplificado
A pré-visualização pública da API v3.1 da Computer Vision adiciona suporte para chinês simplificado.

* Esta versão de pré-visualização da API de leitura suporta línguas inglesa, holandesa, francesa, alemã, italiana, portuguesa, chinesa simplificada e espanhola.

Consulte o guia de como ler [a API para](Vision-API-How-to-Topics/call-read-api.md) saber mais.

> [!div class="nextstepaction"]
> [Saiba mais sobre a API v3.1 Visualização Pública 1](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005)

## <a name="may-2020"></a>Maio de 2020
A API de Visão Computacional v3.0 entrou na Disponibilidade Geral, com atualizações para a API de Leitura:

* Apoio ao inglês, holandês, francês, alemão, italiano, português e espanhol
* Maior precisão
* Pontuação de confiança para cada palavra extraída
* Novo formato de saída

Consulte a [visão geral](overview-ocr.md) do OCR para saber mais.

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