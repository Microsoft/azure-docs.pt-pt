---
title: Reconhecer texto impresso, manuscritos - de imagem digitalizada
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados com o reconhecimento de manuscrito e impresso texto em imagens usando a API de imagem digitalizada.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ea5084128848db7d799b1791aef528305b4f6df4
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192372"
---
# <a name="recognize-printed-and-handwritten-text"></a>Reconhecer texto manuscrito e impresso

Imagem digitalizada oferece uma série de serviços que detete e extraia texto impresso ou manuscritos, que aparece nas imagens. Isto é útil numa variedade de cenários como notetaking, registros médicos, segurança e banca. O seguintes três seções detalhes três diferentes reconhecimento de texto APIs, cada um otimizado para diferentes casos de utilização.

## <a name="read-api"></a>API de leitura

A API de leitura Deteta o conteúdo de texto numa imagem usando nossos modelos mais recentes do reconhecimento e converte o texto identificado num fluxo de carateres legível por máquina. Está otimizado para imagens de muito texto (como documentos que foram verificados digitalmente) e para imagens com muito ruído visual. Ele executa forma assíncrona porque documentos maiores podem demorar vários minutos a devolver um resultado.

A operação de leitura mantém os agrupamentos de linha original de palavras reconhecidas na sua saída. Cada linha é acompanhado de delimitação de coordenadas de caixa, e cada palavra dentro da linha também possui suas própria coordenadas. Se uma palavra foi reconhecida com confiança baixa, essas informações sejam transmitidas também. Consulte a [documentos de referência da API de leitura](https://westus.api.cognitive.microosft.com/vision/v2.0/read/core/asyncBatchAnalyze) para saber mais.

> [!NOTE]
> Esta funcionalidade está atualmente em pré-visualização e só está disponível para texto em inglês.

### <a name="image-requirements"></a>Requisitos de imagem

A API de leitura funciona com as imagens que cumprem os requisitos seguintes:

- A imagem tem de ser apresentada no formato JPEG, PNG, BMP, PDF ou TIFF.
- As dimensões da imagem tem de ser entre 50 x 50 e 4200 x 4200 pixels. Páginas PDF tem de ser 17 x 17 polegadas ou menor.
- O tamanho do ficheiro da imagem tem de ser inferior a 20 megabytes (MB).

### <a name="limitations"></a>Limitações

Se estiver a utilizar uma subscrição do escalão gratuito, a API de leitura apenas irá processar as duas primeiras páginas de um documento PDF ou TIFF. Com uma subscrição paga, processe até 200 páginas. Observe também que a API irá detetar um máximo de 300 linhas por página.

## <a name="ocr-optical-character-recognition-api"></a>OCR (reconhecimento ótico de carateres) API

API de reconhecimento ótico de carateres (OCR) da visão do computador é semelhante à API de leitura, mas é executado de forma síncrona e não está otimizado para documentos grandes. Ele usa um modelo de reconhecimento anterior, mas funciona com mais idiomas.

OCR suporta 25 idiomas: Árabe, chinês simplificado, chinês tradicional, checo, dinamarquês, Holandês, inglês, finlandês, francês, alemão, grego, húngaro, italiano, japonês, coreano, norueguês, polaco, português, romeno, russo, Sérvio (Cirílico e Latim), eslovaco, espanhol, Sueco e turco. OCR Deteta automaticamente o idioma do texto detetado.

Se necessário, OCR corrige a rotação do texto reconhecido, retornando o deslocamento de rotação em graus sobre o eixo horizontal de imagem. OCR também fornece as coordenadas de quadro de cada palavra, como mostrado na ilustração seguinte.

![Diagrama a ilustrar uma imagem a ser girada e o texto a ser lido e delimitado](./Images/vision-overview-ocr.png)

Consulte a [documentos de referência de OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) para saber mais.

### <a name="image-requirements"></a>Requisitos de imagem

A API de OCR funciona nas imagens que cumprem os requisitos seguintes:

* A imagem tem de ser apresentada no formato JPEG, PNG, GIF ou BMP.
* O tamanho da imagem de entrada tem de ser entre 50 x 50 e 4200 x 4200 pixels.
* O texto na imagem pode ser girado pelo qualquer múltiplo de 90 graus, mais um pequeno ângulo de até 40 graus.

### <a name="limitations"></a>Limitações

Em fotografias em que o texto é dominante, os falsos positivos podem ser provenientes de palavras parcialmente reconhecidas. Em alguns fotografias, especialmente as fotos sem qualquer texto, a precisão pode variar dependendo do tipo de imagem.

## <a name="recognize-text-api"></a>Reconhecer a API de texto

> [!NOTE]
> A API de texto reconhecer está a ser preterida em favor da API de leitura. A API de leitura tem recursos semelhantes e é atualizada para processar ficheiros de várias páginas, TIFF e PDF.

A API de texto reconhecer é semelhante ao OCR, mas é executado de forma assíncrona e utiliza modelos de reconhecimento de atualizado. Consulte a [documentos de referência da API de texto de reconhecer](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) para saber mais.

### <a name="image-requirements"></a>Requisitos de imagem

A API de texto reconhecer funciona com as imagens que cumprem os requisitos seguintes:

- A imagem tem de ser apresentada no formato JPEG, PNG ou BMP.
- As dimensões da imagem tem de ser entre 50 x 50 e 4200 x 4200 pixels.
- O tamanho do ficheiro da imagem tem de ser inferior a 4 megabytes (MB).

## <a name="improve-results"></a>Melhorar resultados

A precisão das operações de reconhecimento de texto depende a qualidade das imagens. Os seguintes fatores podem fazer com que uma leitura incorreta:

* Imagens desfocadas.
* Texto manuscrito ou cursivo.
* Estilos de tipos de letra artísticos.
* Tamanho do texto pequeno.
* Planos de fundo complexos, sombras ou distorção de perspetiva ou por reflexos sobre o texto.
* Letras maiúsculas demasiado grandes ou em falta nos primórdios de palavras.
* Texto inferior à linha, superior à linha ou rasurado.

## <a name="next-steps"></a>Passos Seguintes

Siga os [extrair texto impresso (OCR)](./quickstarts/csharp-print-text.md) início rápido para implementar o reconhecimento de texto num simples C# aplicação.