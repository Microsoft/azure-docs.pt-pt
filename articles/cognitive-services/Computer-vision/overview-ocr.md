---
title: O que é o reconhecimento de caracteres óticos?
titleSuffix: Azure Cognitive Services
description: O serviço de reconhecimento de caracteres óticos (OCR) extrai texto visível numa imagem e devolve-o como cordas estruturadas.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/29/2021
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: da4ada8b505c747d24738e175a1701b5ea73b4e4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536738"
---
# <a name="what-is-optical-character-recognition"></a>O que é o reconhecimento de caracteres óticos?

O serviço de reconhecimento de caracteres óticos (OCR) permite extrair texto impresso ou manuscrito a partir de imagens, tais como fotos de sinais de rua e produtos, bem como de &mdash; faturas de documentos, contas, relatórios financeiros, artigos e muito mais. Utiliza modelos baseados em aprendizagem profunda e trabalha com texto em uma variedade de superfícies e fundos.

As APIs do OCR suportam a extração de texto impresso em [várias línguas.](./language-support.md) Siga um [arranque rápido](./quickstarts-sdk/client-library.md) para começar.

![Demonstrações de OCR](./Images/ocr-demo.gif)

Esta documentação contém os seguintes tipos de artigos:
* Os [arranques rápidos](./quickstarts-sdk/client-library.md) são instruções passo a passo que permitem fazer chamadas para o serviço e obter resultados num curto espaço de tempo. 
* Os [guias de como fazer](./Vision-API-How-to-Topics/call-read-api.md) contêm instruções para utilizar o serviço de forma mais específica ou personalizada.
<!--* The [conceptual articles](Vision-API-How-to-Topics/call-read-api.md) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions. -->

## <a name="supported-languages"></a>Linguagens suportadas
As APIs do OCR suportam um total de 73 idiomas para texto de estilo de impressão. Consulte a lista completa de [línguas apoiadas pelo OCR](./language-support.md#optical-character-recognition-ocr). Ocr de estilo manuscrito é suportado exclusivamente para inglês.

## <a name="input-requirements"></a>Requisitos de entrada

A chamada **de Leitura** requer imagens e documentos como entrada. Têm os seguintes requisitos:

* Formatos de ficheiros suportados: JPEG, PNG, BMP, PDF e TIFF
* Para ficheiros PDF e TIFF, são processadas até 2000 páginas (apenas duas primeiras páginas para o nível livre).
* O tamanho do ficheiro deve ser inferior a 50 MB (4 MB para o nível livre) e dimensões de pelo menos 50 x 50 pixels e, no máximo, 10000 x 10000 pixels. 

## <a name="read-api"></a>Ler API 

A [API de Leitura de](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) Visão Computacional é a mais recente tecnologia OCR da Azure ([saiba quais as novidades](./whats-new.md)) que extrai texto impresso (em vários idiomas), texto manuscrito (apenas inglês), dígitos e símbolos de moeda a partir de imagens e documentos PDF de várias páginas. É otimizado para extrair texto de imagens pesadas de texto e documentos PDF de várias páginas com línguas mistas. Suporta a deteção de texto impresso e manuscrito na mesma imagem ou documento.

![Como o OCR converte imagens e documentos em saída estruturada com texto extraído](./Images/how-ocr-works.svg)

### <a name="key-features"></a>Principais funcionalidades

A API de Leitura inclui as seguintes funcionalidades. 

* Impressão de extração de texto em 73 línguas
* Extração de texto manuscrito em inglês
* Linhas de texto e palavras com pontuações de localização e confiança
* Não é necessária identificação linguística
* Suporte para línguas mistas, modo misto (impressão e manuscrita)
* Selecione páginas e páginas variam de documentos grandes e multi-páginas
* Ordem de leitura natural para linhas de texto
* Classificação da caligrafia para linhas de texto
* Disponível como recipiente Distroless Docker para implantação no local

Saiba [como utilizar as funcionalidades OCR](./vision-api-how-to-topics/call-read-api.md).

## <a name="use-the-cloud-api-or-deploy-on-premise"></a>Use a API de nuvem ou implemente no local
As APIs de nuvem de Leitura 3.x são a opção preferida para a maioria dos clientes devido à facilidade de integração e à rápida produtividade fora da caixa. O Azure e o serviço de Visão Computacional lidam com escala, desempenho, segurança de dados e necessidades de conformidade enquanto se concentra em satisfazer as necessidades dos seus clientes.

Para a colocação no local, o [recipiente Read Docker (pré-visualização)](./computer-vision-how-to-install-containers.md) permite-lhe implantar as novas capacidades de OCR no seu próprio ambiente local. Os contentores são ótimos para requisitos específicos de governação de dados e segurança.

## <a name="ocr-api"></a>OCR API

O legado [OCR API](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f20d) usa um modelo de reconhecimento mais antigo, suporta apenas imagens e executa sincronizadamente, regressando imediatamente com o texto detetado. Consulte a coluna OCR de [línguas apoiadas](./language-support.md#optical-character-recognition-ocr) para obter uma lista de línguas apoiadas.

> [!WARNING]
> As operações do Computador Visão 2.0 RecogniseText estão em vias de ser depreciadas a favor da nova [API](#read-api) de leitura abrangida por este artigo. Os clientes existentes devem [transitar para a utilização de operações de Leitura](upgrade-api-versions.md).

## <a name="data-privacy-and-security"></a>Privacidade e segurança dos dados

Tal como acontece com todos os Serviços Cognitivos, os desenvolvedores que usam o serviço de Visão de Computador devem estar cientes das políticas da Microsoft sobre os dados dos clientes. Consulte a [página de Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) no Microsoft Trust Center para saber mais.

## <a name="next-steps"></a>Passos seguintes

- Começa com o [OCR (Ler) REST API ou a biblioteca de clientes quickstarts](./quickstarts-sdk/client-library.md).
- Saiba mais sobre a [API De Leitura 3.2 REST](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005).
