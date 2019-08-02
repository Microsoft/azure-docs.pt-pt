---
title: Resolução de sobrefestivals de projetos acústicos
titlesuffix: Azure Cognitive Services
description: Esta visão geral conceitual descreve a diferença entre resoluções de grande e refinas enquanto trazendo acústica.
services: cognitive-services
author: KyleStorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: kylsto
ROBOTS: NOINDEX
ms.openlocfilehash: e237a010bfecc038816c1eedf94e2f94b1e12472
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704879"
---
# <a name="project-acoustics-bake-resolution"></a>Resolução de sobrefestivals de projetos acústicos
Esta visão geral conceitual descreve a diferença entre resoluções de grande e refinas enquanto trazendo acústica. Você escolhe essa configuração durante a etapa investigações do fluxo de trabalho do trazendo.

## <a name="Coarse-vs-Fine-Resolution"></a>Alta resolução vs. fina

A única diferença entre as configurações de resolução grande e refina é a frequência com a qual a simulação é executada. A multa usa uma frequência duas vezes mais alta. Isso tem várias implicações na simulação acústica:

* O comprimento de onda para alta é duas vezes maior do que o suficiente e, portanto, o voxels é duas vezes maior.
* O tempo de simulação está diretamente relacionado ao tamanho do VOXEL, tornando-se uma grande folga cerca de 16 vezes mais rápido do que uma distorta.
* Portais (por exemplo, portas ou janelas) menores que o tamanho de VOXEL não podem ser simulados. A configuração grosseira pode fazer com que alguns desses portais menores não sejam simulados; Portanto, eles não passarão a soar no tempo de execução. Você pode ver se isso está acontecendo exibindo o voxels.
* A frequência de simulação inferior resulta em menos Diffraction em relação a cantos e bordas.
* Fontes de som não podem ser localizadas dentro de voxels "preenchidas" (ou seja, voxels que contêm geometria). Isso resulta em sem som. É mais difícil inserir fontes de som para que elas não fiquem dentro do voxels maior do que é ao usar a configuração correta.
* O voxels maior invasorá mais em portais, como mostrado abaixo. A primeira imagem foi criada usando uma grande, enquanto a segunda é a mesma porta usando a resolução refinada. Conforme indicado pelas marcas vermelhas, há muito menos intrusão na porta usando a configuração fina. A linha azul é a porta, conforme definido pela geometria, enquanto a linha vermelha é o portal acústico efetivo definido pelo tamanho de VOXEL. A forma como essa invasão é reproduzida em uma determinada situação depende completamente de como a voxels linha com a geometria do portal, que é determinada pelo tamanho e pelas localizações dos objetos na cena.

![Captura de tela de voxels grosso preenchendo um porta em um não real](media/unreal-coarse-bake.png)

![Captura de tela de voxels fino em uma porta de maneira não real](media/unreal-fine-bake.png)

## <a name="next-steps"></a>Passos Seguintes

Experimente as configurações de resolução grande e refinada usando [](unreal-baking.md) nossos plug-ins inreais ou do [Unity](unity-baking.md) .
