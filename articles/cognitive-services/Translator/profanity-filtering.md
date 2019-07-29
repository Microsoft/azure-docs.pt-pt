---
title: Filtragem de profanação-API de Tradução de Texto
titleSuffix: Azure Cognitive Services
description: Use a filtragem de profanação no API de Tradução de Texto.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 114dea098db5c824a1235ba1635f547383bf6743
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595189"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Adicionar filtragem de profanação com o API de Tradução de Texto

Normalmente, o serviço do tradutor retém a profanação que está presente na origem na tradução. O grau de profanação e o contexto que torna palavras impróprias diferem entre culturas. Como resultado, o grau de profanação no idioma de destino pode ser amplificado ou reduzido.

Se você quiser evitar a presença de profanação na tradução, mesmo se a profanação estiver presente no texto de origem, use a opção de filtragem de profanação disponível no método translate (). Essa opção permite que você escolha se deseja ver a profanação excluída, marcada com as marcas apropriadas ou não tomar nenhuma ação tomada.

O método translate () usa o parâmetro "Options", que contém o novo elemento "ProfanityAction". Os valores aceitos de ProfanityAction são "NoAction", "Marked" e "Deleted".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Valores aceitos de ProfanityAction e exemplos
|Valor de ProfanityAction | Action | Exemplo: Origem-japonês | Exemplo: Destino-inglês|
| :---|:---|:---|:---|
| NoAction | Predefinição. O mesmo que não definir a opção. A profanação passa da origem para o destino. | 彼は変態です。 | Ele é um automáticas. |
| Marca | Palavras obscenas são circundadas por profanação de marcas \<XML >... \<>/profanity. | 彼は変態です。 | Ele é uma \<profanação > automáticas\</profanity >. |
| Eliminado | Palavras obscenas são removidas da saída sem substituição. | 彼は Marketplace. | Ele é um. |

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Aplicar a filtragem de profanação à sua chamada à API do Tradutor](reference/v3-0-translate.md)
