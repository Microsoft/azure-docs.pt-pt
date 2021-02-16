---
title: Modelos de exemplo
description: Lista fontes para modelos de amostra.
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 6817601659c841ca98031f4e3e1590743bbed171
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530540"
---
# <a name="sample-models"></a>Modelos de exemplo

Este artigo lista alguns recursos para dados de amostra que podem ser usados para testar o serviço de renderização remota Azure.

## <a name="built-in-sample-model"></a>Modelo de amostra incorporado

Fornecemos um modelo de amostra incorporado que pode sempre ser carregado usando o URL **builtin://Engine**

![Modelo de exemplo](./media/sample-model.png "Modelo de exemplo")

Estatísticas de modelos:

| Name | Valor |
|-----------|:-----------|
| [Tamanho do servidor necessário](../reference/vm-sizes.md) | padrão |
| Número de triângulos | 18,7 Milhões |
| Número de peças móveis | 2073 |
| Número de materiais | 94 |

## <a name="third-party-data"></a>Dados de terceiros

O Grupo Khronos mantém um conjunto de modelos de amostras glTF para testes. O ARR suporta o formato glTF tanto em texto *(.gltf*) como na forma binária *(.glb).* Sugerimos a utilização dos modelos PBR para obter os melhores resultados visuais:

* [Modelos de amostra de glTF](https://github.com/KhronosGroup/glTF-Sample-Models)

## <a name="next-steps"></a>Passos seguintes

* [Quickstart: Renderiza um modelo com Unidade](../quickstarts/render-model.md)
* [Início Rápido: Converter um modelo para composição](../quickstarts/convert-model.md)
