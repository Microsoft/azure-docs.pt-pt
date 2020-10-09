---
title: Modelos de exemplo
description: Lista fontes para modelos de amostra.
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 8e5dcb6c9dfa08efc0889fcab779d6cb333d2330
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88507540"
---
# <a name="sample-models"></a>Modelos de exemplo

Este artigo lista alguns recursos para dados de amostra que podem ser usados para testar o serviço de renderização remota Azure.

## <a name="built-in-sample-model"></a>Modelo de amostra incorporado

Fornecemos um modelo de amostra incorporado que pode sempre ser carregado usando o URL **builtin://Engine**

![Modelo de exemplo](./media/sample-model.png "Modelo de exemplo")

Estatísticas de modelos:

| Nome | Valor |
|-----------|:-----------|
| [Tamanho do servidor necessário](../how-tos/session-rest-api.md#create-a-session) | padrão |
| Número de triângulos | 18,7 Milhões |
| Número de peças móveis | 2073 |
| Número de materiais | 94 |

## <a name="third-party-data"></a>Dados de terceiros

O Grupo Khronos mantém um conjunto de modelos de amostras glTF para testes. O ARR suporta o formato glTF tanto em texto *(.gltf*) como na forma binária *(.glb).* Sugerimos a utilização dos modelos PBR para obter os melhores resultados visuais:

* [Modelos de amostra de glTF](https://github.com/KhronosGroup/glTF-Sample-Models)

## <a name="next-steps"></a>Passos seguintes

* [Quickstart: Renderiza um modelo com Unidade](../quickstarts/render-model.md)
* [Quickstart: Converter um modelo para renderização](../quickstarts/convert-model.md)
