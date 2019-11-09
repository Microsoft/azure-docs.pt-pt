---
title: Filtragem de profanação-API de Tradução de Texto
titleSuffix: Azure Cognitive Services
description: Use a filtragem de profanação para determinar o nível de profanação traduzido em seu texto no API de Tradução de Texto de serviços cognitivas do Azure.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: e7e2097e0d3daf360f1fa0f30bf3fd2c62c07163
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73836233"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Adicionar filtragem de profanação com o API de Tradução de Texto

Normalmente, o serviço do tradutor retém a profanação que está presente na origem na tradução. O grau de profanação e o contexto que torna palavras impróprias diferem entre culturas. Como resultado, o grau de profanação no idioma de destino pode ser amplificado ou reduzido.

Se você quiser evitar a presença de profanação na tradução, mesmo se a profanação estiver presente no texto de origem, use a opção de filtragem de profanação disponível no método translate (). Essa opção permite que você escolha se deseja ver a profanação excluída, marcada com as marcas apropriadas ou não tomar nenhuma ação tomada.

O método translate () usa o parâmetro "Options", que contém o novo elemento "ProfanityAction". Os valores aceitos de ProfanityAction são "NoAction", "Marked" e "Deleted".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Valores aceitos de ProfanityAction e exemplos
|Valor de ProfanityAction | Ação | Exemplo: fonte-japonês | Exemplo: destino-inglês|
| :---|:---|:---|:---|
| NoAction | Predefinição. O mesmo que não definir a opção. A profanação passa da origem para o destino. | 彼は変態です Marketplace. | Ele é um automáticas. |
| Marca | Palavras obscenas são cercadas por marcas XML \<profanação >... \</profanity >. | 彼は変態です Marketplace. | Ele é uma profanação \<> automáticas\</profanity >. |
| Eliminado | Palavras obscenas são removidas da saída sem substituição. | 彼は Marketplace. | Ele é um. |

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Aplicar a filtragem de profanação à sua chamada à API do Tradutor](reference/v3-0-translate.md)
