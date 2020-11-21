---
title: Padrões ajudam a previsão - LUIS
titleSuffix: Azure Cognitive Services
description: Um padrão permite-lhe ganhar mais precisão para uma intenção sem fornecer muitos mais pronunciamentos.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: cda71c6e825ff27ba9b03e1306ccb287663e8613
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025964"
---
# <a name="patterns-improve-prediction-accuracy"></a>Os padrões melhoram a precisão da previsão
Os padrões são projetados para melhorar a precisão quando várias expressões são muito semelhantes.  Um padrão permite-lhe ganhar mais precisão para uma intenção sem fornecer muitos mais pronunciamentos.

## <a name="patterns-solve-low-intent-confidence"></a>Padrões resolvem baixa confiança intenção
Considere uma aplicação de Recursos Humanos que reporte no gráfico organizacional em relação a um funcionário. Dado o nome e a relação de um empregado, a LUIS devolve os colaboradores envolvidos. Considere um empregado, Tom, com um nome de gerente Alice, e uma equipa de subordinados chamado: Michael, Rebecca e Carl.

![Gráfico de imagem da Organização](./media/luis-concept-patterns/org-chart.png)

|Expressões|Intenção prevista|Pontuação de intenção|
|--|--|--|
|Quem é o subordinado do Tom?|GetOrgChart|.30|
|Quem é o subordinado de Tom?|GetOrgChart|.30|

Se uma app tiver entre 10 e 20 expressões com diferentes comprimentos de frase, ordem de palavras diferentes e até palavras diferentes (sinónimos de "subordinado", "gerir", "reportar"), o LUIS pode devolver uma pontuação de baixa confiança. Crie um padrão para ajudar o LUIS a compreender a importância da ordem da palavra.

Os padrões resolvem as seguintes situações:

* A pontuação de intenção é baixa
* A intenção correta não é a pontuação máxima, mas muito perto da pontuação máxima.

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Padrões não são uma garantia de intenção
Os padrões usam uma mistura de tecnologias de previsão. Definir uma intenção para uma expressão de modelo em um padrão não é uma garantia da previsão de intenção, mas é um sinal forte.

<a name="patterns-do-not-improve-entity-detection"></a>

## <a name="patterns-do-not-improve-machine-learning-entity-detection"></a>Os padrões não melhoram a deteção de entidades de aprendizagem automática

Um padrão destina-se principalmente a ajudar a previsão de intenções e papéis. O _padrão.qualquer_ entidade é usada para extrair entidades de forma livre. Enquanto os padrões usam entidades, um padrão não ajuda a detetar uma entidade de aprendizagem automática.

Não espere ver uma previsão melhorada da entidade se colapsar várias expressões num único padrão. Para entidades simples dispararem, é necessário adicionar expressões ou utilizar entidades de listas que o seu padrão não dispare.

## <a name="patterns-use-entity-roles"></a>Os padrões usam funções de entidade
Se duas ou mais entidades num padrão estiverem contextualização, os padrões utilizam [as funções](./luis-concept-entity-types.md) da entidade para extrair informação contextual sobre entidades.

## <a name="prediction-scores-with-and-without-patterns"></a>Pontuações de previsão com e sem padrões
Dado o exemplo suficiente, LUIS seria capaz de aumentar a confiança da previsão sem padrões. Os padrões aumentam a pontuação de confiança sem ter que fornecer tantas expressões.

## <a name="pattern-matching"></a>Correspondência de padrões
Um padrão é combinado com base na deteção das entidades dentro do padrão primeiro, em seguida, validando o resto das palavras e ordem de palavras do padrão. As entidades são necessárias no padrão para que um padrão corresponda. O padrão é aplicado ao nível do símbolo, não ao nível do carácter.

## <a name="pattern-only-apps"></a>Aplicativos apenas para padrões
Pode construir uma app com intenções que não têm palavras de exemplo, desde que haja um padrão para cada intenção. Para uma aplicação apenas por padrões, o padrão não deve conter entidades de aprendizagem automática porque estas requerem palavras de exemplo.

## <a name="patternany-entity"></a>Entidade Pattern.any

[!INCLUDE [Pattern.any entity - concepts](./includes/pattern-any-entity.md)]

## <a name="best-practices"></a>Melhores práticas
Aprenda as [melhores práticas.](luis-concept-best-practices.md)

## <a name="pattern-syntax"></a>Sintaxe de padrão

Aprenda a sintaxe padrão a partir da referência de [sintaxe de padrão](reference-pattern-syntax.md).

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre padrões:

* [Como adicionar padrões](luis-how-to-model-intent-pattern.md)
* [Como adicionar padrão.qualquer entidade](luis-how-to-add-entities.md#add-a-patternany-entity)
* [Sintaxe de padrões](reference-pattern-syntax.md)

> [!div class="nextstepaction"]
> [Saiba como implementar padrões neste tutorial](luis-tutorial-pattern.md)
