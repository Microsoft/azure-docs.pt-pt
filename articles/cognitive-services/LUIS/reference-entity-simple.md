---
title: Tipo de entidade simples-LUIS
titleSuffix: Azure Cognitive Services
description: Uma entidade simples é uma entidade genérica que descreve um único conceito e é aprendida do contexto aprendido pelo computador. Como as entidades simples são geralmente nomes como nomes de empresa, nomes de produtos ou outras categorias de nomes, adicione uma lista de frases ao usar uma entidade simples para aumentar o sinal dos nomes usados.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: c1514b6cd512924a162a524d11e888055fa06514
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563210"
---
# <a name="simple-entity"></a>Entidade simples 

Uma entidade simples é uma entidade genérica que descreve um único conceito e é aprendida do contexto aprendido pelo computador. Como as entidades simples são geralmente nomes como nomes de empresa, nomes de produtos ou outras categorias de nomes, adicione uma [lista de frases](luis-concept-feature.md) ao usar uma entidade simples para aumentar o sinal dos nomes usados. 

**A entidade é uma boa opção quando:**

* Os dados não são formatados consistentemente, mas indicam a mesma coisa. 

![entidade Simple](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>JSON de exemplo

`Bob Jones wants 3 meatball pho`

Na expressão anterior, `Bob Jones` é identificado como um simples `Customer` entidade.

Os dados devolvidos a partir do ponto final incluem o nome da entidade, o texto detetado da expressão, a localização do texto detetado e a pontuação:

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

|Objeto de dados|Nome da entidade|Valor|
|--|--|--|
|Entidade Simple|`Customer`|`bob jones`|

## <a name="next-steps"></a>Passos Seguintes

Neste [tutorial](luis-quickstart-primary-and-secondary-data.md), extraia os dados aprendidos por máquina de nome do trabalho de emprego de um expressão usando a **entidade simples**. Para aumentar a precisão de extração, adicione uma [lista de frases](luis-concept-feature.md) de termos específicos à entidade simples.