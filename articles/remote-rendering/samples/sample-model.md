---
title: Modelos de amostra
description: Lista fontes para modelos de amostra.
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 0c28d36c7934fbbac0ddd11562c8dc237b0360ee
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679486"
---
# <a name="sample-models"></a>Modelos de amostra

Este artigo lista alguns recursos para dados de amostra que podem ser usados para testar o serviço de renderização remota Azure.

## <a name="built-in-sample-model"></a>Modelo de amostra incorporada

Fornecemos um modelo de amostra incorporado que pode sempre ser carregado usando o URL **builtin://Engine**

![Modelo de amostra](./media/sample-model.png "Modelo de amostra")

Estatísticas do modelo:

| Nome | Valor |
|-----------|:-----------|
| [Tamanho VM necessário](../how-tos/session-rest-api.md#create-a-session) | padrão |
| Número de triângulos | 18,7 Milhões |
| Número de peças móveis | 2073 |
| Número de materiais | 94 |

## <a name="third-party-data"></a>Dados de terceiros

O Grupo Khronos mantém um conjunto de modelos de amostras GLTF para testes. O ARR suporta o formato GLTF tanto em texto (*.gltf*) como em forma binária (*.glb*). Sugerimos a utilização dos modelos PBR para obter os melhores resultados visuais:

* [modelos de amostra glTF](https://github.com/KhronosGroup/glTF-Sample-Models)

## <a name="next-steps"></a>Passos seguintes

* [Quickstart: Render um modelo com Unidade](../quickstarts/render-model.md)
* [Quickstart: Converter um modelo para renderização](../quickstarts/convert-model.md)
