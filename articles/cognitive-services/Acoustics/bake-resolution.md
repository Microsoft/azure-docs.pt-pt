---
title: Resolução de criar Acoustics de projeto
titlesuffix: Azure Cognitive Services
description: Esta descrição geral conceptual descreve a diferença entre as resoluções de genérico e tudo bem, ao mesmo tempo implantando acoustics.
services: cognitive-services
author: KyleStorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 04/05/2019
ms.author: KyleStorck
ms.openlocfilehash: 7dbf63ba39c5dcdebb363cfc37a45f0216a07497
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471835"
---
# <a name="project-acoustics-bake-resolution"></a>Resolução de criar Acoustics de projeto
Esta descrição geral conceptual descreve a diferença entre as resoluções de genérico e tudo bem, ao mesmo tempo implantando acoustics. Escolha esta definição durante o passo de sondas do fluxo de trabalho baking.

## <a name="Coarse-vs-Fine-Resolution"></a>Resolução vs genérico

A única diferença entre as definições de resolução de genérico e tudo bem, é a frequência com que a simulação é executada. Bem utiliza uma frequência de duas vezes tão elevada como genérico. Isso tem várias implicações na simulação acústica:

* O wavelength para genérico se duas vezes, desde que tudo bem, sendo, portanto, os voxels duas vezes tão grandes.
* O tempo de simulação está diretamente relacionada com o tamanho de voxel, tornando um genérico criar aproximadamente 16 vezes mais rápido do que um criar tudo bem.
* Portais (por exemplo, as portas ou windows) inferior ao tamanho de voxel não podem ser simulados. A definição genérico pode fazer com que alguns destes portais menores, a não ser simulada; Por conseguinte, não passar som por meio em tempo de execução. Pode ver se isso está acontecendo, visualizando o voxels.
* A frequência de simulação inferior resulta em menos diffraction em torno de cantos e margens.
* Origens de som não podem estar localizadas dentro de "preenchido" voxels (ou seja, voxels que contêm a geometria). Isso resulta em nenhum som. É mais difícil colocar as origens de som para que não estejam dentro o voxels maiores de genérico que é quando utiliza a definição adequada.
* O maior voxels será atrapalham a mais em portais, conforme mostrado abaixo. A primeira imagem foi criada com genérico, enquanto o segundo é a mesma porta com resolução fina. Conforme indicado pelas marcas de vermelhas, há muito menos intrusões a porta através da definição tudo bem. A linha azul é a porta, conforme definido por geometry, enquanto a linha vermelha é o portal de acústico eficaz definido pelo tamanho voxel. Como esta invasão papel em determinada situação depende completamente como os voxels alinhar com a geometria do portal, que é determinado pelo tamanho e localizações de seus objetos na cena.

![Captura de ecrã do voxels genérico preenchendo uma porta no Unreal](media/unreal-coarse-bake.png)

![Captura de ecrã do voxels bem numa porta no Unreal](media/unreal-fine-bake.png)

## <a name="next-steps"></a>Passos Seguintes

Experimente as definições de resolução de genérico e bem usando nosso [Unreal](unreal-baking.md) ou [Unity](unity-baking.md) plug-ins.