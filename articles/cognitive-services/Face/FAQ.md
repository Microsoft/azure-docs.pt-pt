---
title: Perguntas mais frequentes sobre - Face API
titlesuffix: Azure Cognitive Services
description: Seguem-se as respostas às perguntas mais populares sobre o serviço de API de rostos.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: sbowles
ms.openlocfilehash: 47ce80e1b0cefc01752d2445b751ebe1c2d65d08
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351342"
---
# <a name="face-api-frequently-asked-questions"></a>Face API perguntas mais frequentes

> [!TIP]
> Se não conseguir encontrar respostas para suas perguntas neste FAQ, experimente perguntar à Comunidade de API de rostos em [Stack Overflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) ou contacte ajuda e suporte no [UserVoice](https://cognitive.uservoice.com/).

-----
**Pergunta**: Que fatores podem reduzir a precisão da API de rostos para reconhecimento, verificação ou encontrar semelhante?

**resposta**: Em geral é os mesmo casos em que os humanos têm dificuldades em identificar alguém incluindo:
* Obstructions um ou ambos os olhos de bloqueio
* Iluminação harsh (por exemplo, grave backlighting)
* Alterações de cabelo de cabelo estilo ou facial
* Alterações à idade
* Extreme expressões faciais (por exemplo, alegam)

Face API, muitas vezes, é efetuada com êxito num desafio casos como o anterior, mas pode ser reduzida a precisão. Para tornar o reconhecimento mais robusto e enfrentar esses desafios, Treine as pessoas com fotos que incluem uma diversidade de ângulos e de iluminação.

-----
**Pergunta**:  Estou passando os dados de imagem binária na mas recebo um erro de "imagem da face inválido".

**resposta**:  Este erro indica que o algoritmo tinha um problema ao analisar a imagem. As causas incluem:
* Os formatos de entrada de imagem suportados inclui JPEG, PNG, GIF (o primeiro quadro), BMP.
* Tamanho do ficheiro de imagem deve ser não maior do que 4 MB
* O intervalo de tamanho de face detetável é 36 x 36 para 4096 x 4096 pixels. Não seja detectados rostos fora deste intervalo
* Alguns rostos poderão não ser detetados por causa de desafios técnicos, por exemplo, de grande de face de ângulos (head-representam), oclusão grandes. Rostos frontal e quase frontal tem os melhores resultados

-----
