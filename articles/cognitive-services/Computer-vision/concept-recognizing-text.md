---
title: Reconhecimento de texto impresso e manuscrito - Visão computacional
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados com o reconhecimento de texto impresso e manuscrito em imagens usando a API da Visão Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: a4c90ed12c8023e0b9ebc509b20d8d9224b49f1b
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78365483"
---
# <a name="recognize-printed-and-handwritten-text"></a>Reconhecer texto impresso e manuscrito

A Computer Vision fornece uma série de serviços que detetam e extraem texto impresso ou manuscrito que aparece nas imagens. Isto é útil em uma variedade de cenários como a tomada de notas, registos médicos, segurança e banca. As três secções seguintes detalham três APIs de reconhecimento de texto diferentes, cada uma otimizada para diferentes casos de utilização.

## <a name="read-api"></a>Ler API

A API read deteta conteúdo de texto numa imagem utilizando os nossos modelos de reconhecimento mais recentes e converte o texto identificado num fluxo de caracteres legível por máquina. É otimizado para imagens pesadas de texto (como documentos que foram digitalizados digitalmente) e para imagens com muito ruído visual. Determinará qual o modelo de reconhecimento a utilizar para cada linha de texto, suportando imagens com texto impresso e manuscrito. A API de leitura executa assincronicamente porque documentos maiores podem demorar vários minutos a devolver um resultado.

A operação Read mantém os agrupamentos originais de linhas de palavras reconhecidas na sua saída. Cada linha vem com coordenadas de caixa de limitadores, e cada palavra dentro da linha também tem as suas próprias coordenadas. Se uma palavra foi reconhecida com pouca confiança, essa informação também é transmitida. Consulte os pontos de referência da [API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) e leia os pontos de referência da [API](https://go.microsoft.com/fwlink/?linkid=2118322) Para saber mais.

> [!NOTE]
> Esta funcionalidade só está disponível para texto inglês e espanhol (pré-visualização).

### <a name="image-requirements"></a>Requisitos de imagem

A API read trabalha com imagens que cumprem os seguintes requisitos:

- A imagem deve ser apresentada no formato JPEG, PNG, BMP, PDF ou TIFF.
- As dimensões da imagem devem estar entre 50 x 50 e 10000 x 10000 pixels. As páginas PDF devem ter 17 x 17 polegadas ou menores.
- O tamanho do ficheiro da imagem deve ser inferior a 20 megabytes (MB).

### <a name="limitations"></a>Limitações

Se estiver a utilizar uma subscrição de nível livre, o Read API só processará as duas primeiras páginas de um documento PDF ou TIFF. Com uma subscrição paga, processará até 200 páginas. Note também que a API irá detetar um máximo de 300 linhas por página.

## <a name="ocr-optical-character-recognition-api"></a>OCR (reconhecimento ótico de caracteres) API

O reconhecimento ótico de caracteres da Computer Vision (OCR) API é semelhante ao API read, mas executa sincronizadamente e não está otimizado para grandes documentos. Usa um modelo de reconhecimento anterior, mas funciona com mais línguas; ver [suporte linguístico](language-support.md#text-recognition) para uma lista completa das línguas apoiadas.

Se necessário, o OCR corrige a rotação do texto reconhecido devolvendo a compensação rotativa em graus sobre o eixo de imagem horizontal. O CR também fornece as coordenadas de quadro de cada palavra, como visto na ilustração seguinte.

![Uma imagem a ser girada e o seu texto a ser lido e delineado](./Images/vision-overview-ocr.png)

Consulte os médicos de [referência da OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) para saber mais.

### <a name="image-requirements"></a>Requisitos de imagem

A OCR API trabalha em imagens que satisfaçam os seguintes requisitos:

* A imagem deve ser apresentada em formato JPEG, PNG, GIF ou BMP.
* O tamanho da imagem de entrada deve ser entre 50 x 50 e 4200 x 4200 pixels.
* O texto na imagem pode ser rodado por qualquer múltiplo de 90 graus mais um pequeno ângulo de até 40 graus.

### <a name="limitations"></a>Limitações

Nas fotografias onde o texto é dominante, os falsos positivos podem vir de palavras parcialmente reconhecidas. Em algumas fotografias, especialmente fotografias sem texto, a precisão pode variar dependendo do tipo de imagem.

## <a name="recognize-text-api"></a>Reconhecer a API de Texto

> [!NOTE]
> A API de texto reconhecido está a ser depreciada a favor da API de leitura. O Read API tem capacidades semelhantes e é atualizado para lidar com ficheiros PDF, TIFF e várias páginas.

A API de Texto Reconhecido é semelhante à OCR, mas executa assincronicamente e utiliza modelos de reconhecimento atualizados. Consulte os doutoros de referência da [API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) de texto reconhecer para saber mais.

### <a name="image-requirements"></a>Requisitos de imagem

A API de Texto Reconhecido trabalha com imagens que cumprem os seguintes requisitos:

- A imagem deve ser apresentada em formato JPEG, PNG ou BMP.
- As dimensões da imagem devem estar entre 50 x 50 e 4200 x 4200 pixels.
- O tamanho do ficheiro da imagem deve ser inferior a 4 megabytes (MB).

## <a name="limitations"></a>Limitações

A precisão das operações de reconhecimento de texto depende da qualidade das imagens. Os seguintes fatores podem provocar uma leitura imprecisa:

* Imagens desfocadas.
* Texto manuscrito ou cursivo.
* Estilos de tipos de letra artísticos.
* Tamanho do texto pequeno.
* Planos de fundo complexos, sombras ou distorção de perspetiva ou por reflexos sobre o texto.
* Letras maiúsculas ou em falta no início das palavras.
* Texto inferior à linha, superior à linha ou rasurado.

## <a name="next-steps"></a>Passos seguintes

Siga o [texto extrato (Ler)](./QuickStarts/CSharp-hand-text.md) rapidamente C# para implementar o reconhecimento de texto numa aplicação simples.
