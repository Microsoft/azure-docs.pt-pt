---
title: Resolução do Projeto Acoustics Bake
titlesuffix: Azure Cognitive Services
description: Esta visão geral conceptual descreve a diferença entre resoluções grossas e finas enquanto cozia acústica.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: d8eb3b2cbaf7b4e842d8338eefde756f6d381111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854354"
---
# <a name="project-acoustics-bake-resolution"></a>Resolução do Projeto Acoustics Bake
Esta visão geral conceptual descreve a diferença entre resoluções grossas e finas enquanto cozia acústica. Escolha esta definição durante o passo das sondas do fluxo de trabalho de cozedura.

## <a name="coarse-vs-fine-resolution"></a><a name="Coarse-vs-Fine-Resolution"></a>Resolução grosseira vs fina

A única diferença entre as definições grossas e finas de resolução é a frequência em que a simulação é realizada. O fine usa uma frequência duas vezes mais alta que grossa. Isto tem uma série de implicações na simulação acústica:

* O comprimento de onda para grosso é duas vezes maior que o comprimento, e portanto os voxels são duas vezes maiores.
* O tempo de simulação está diretamente relacionado com o tamanho do voxel, fazendo uma cozedura grossa cerca de 16 vezes mais rápido do que um cozer fino.
* Os portais (por exemplo, portas ou janelas) menores do que o tamanho do voxel não podem ser simulados. A definição grosseira pode fazer com que alguns destes portais menores não sejam simulados; portanto, eles não vão passar o som através no tempo de execução. Pode ver se isto está a acontecer vendo os voxels.
* A frequência de simulação inferior resulta em menos difração em cantos e bordas.
* Fontes sonoras não podem ser localizadas dentro de voxels "cheios" (isto é, voxels que contêm geometria). Isto não resulta em som. É mais difícil colocar fontes sonoras para que não estejam dentro dos voxels maiores do que quando se usa o ajuste fino.
* Os voxels maiores intrometem-se mais em portais, como mostrado abaixo. A primeira imagem foi criada usando grosso, enquanto a segunda é a mesma porta usando uma resolução fina. Como indicado pelas marcas vermelhas, há muito menos intrusão na porta usando o ajuste fino. A linha azul é a porta como definida pela geometria, enquanto a linha vermelha é o portal acústico eficaz definido pelo tamanho voxel. Como esta intrusão se desenrola numa determinada situação depende completamente de como os voxels alinham-se com a geometria do portal, que é determinada pelo tamanho e localização dos seus objetos na cena.

![Screenshot de voxels grosseiros enchendo uma porta em Unreal](media/unreal-coarse-bake.png)

![Screenshot de voxels finos em uma porta em Unreal](media/unreal-fine-bake.png)

## <a name="next-steps"></a>Passos seguintes

Experimente as configurações grossas e finas de resolução usando os nossos plugins [Unreal](unreal-baking.md) ou [Unity.](unity-baking.md)
