---
title: Padrões ajudam a previsão - LUIS
titleSuffix: Azure Cognitive Services
description: Um padrão permite-lhe ganhar mais precisão para uma intenção sem fornecer muitas mais expressões.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 6c1b548de25369c162b4a08dfa20fce62c17f99f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75890278"
---
# <a name="patterns-improve-prediction-accuracy"></a>Os padrões melhoram a precisão da previsão
Os padrões são projetados para melhorar a precisão quando várias expressões são muito semelhantes.  Um padrão permite-lhe ganhar mais precisão para uma intenção sem fornecer muitas mais expressões. 

## <a name="patterns-solve-low-intent-confidence"></a>Padrões resolvem baixa confiança de intenção
Considere uma aplicação de Recursos Humanos que reporte sobre o gráfico organizacional em relação a um funcionário. Dado o nome e a relação de um empregado, a LUIS devolve os colaboradores envolvidos. Considere um empregado, Tom, com um empresário chamado Alice, e uma equipa de subordinados chamados: Michael, Rebecca e Carl.

![Imagem do gráfico da Organização](./media/luis-concept-patterns/org-chart.png)

|Expressões|Intenção prevista|Pontuação de intenção|
|--|--|--|
|Quem é subordinado do Tom?|Getorgchart|.30|
|Quem é o subordinado de Tom?|Getorgchart|.30|

Se uma aplicação tiver entre 10 a 20 frases com diferentes comprimentos de frase, ordem de palavras diferentes e até palavras diferentes (sinónimos de "subordinado", "gerir", "reportar"), LUIS pode devolver uma pontuação de baixa confiança. Crie um padrão para ajudar luis a entender a importância da palavra ordem. 

Os padrões resolvem as seguintes situações: 

* A pontuação da intenção é baixa
* A intenção correta não é a pontuação máxima, mas muito perto do resultado máximo. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Padrões não são uma garantia de intenção
Os padrões usam uma mistura de tecnologias de previsão. Definir uma intenção de uma expressão de modelo num padrão não é uma garantia da previsão de intenção, mas é um sinal forte. 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>Os padrões não melhoram a deteção de entidades aprendidas por máquinas

Um padrão destina-se principalmente a ajudar na previsão de intenções e papéis. O _padrão.qualquer_ entidade é usada para extrair entidades de forma livre. Embora os padrões utilizem entidades, um padrão não ajuda a detetar uma entidade aprendida por máquinas.  

Não espere ver uma previsão melhorada da entidade se colapsar várias expressões num único padrão. Para que entidades simples disparem, é necessário adicionar declarações ou utilizar entidades da lista que o seu padrão não irá disparar.

## <a name="patterns-use-entity-roles"></a>Padrões usam funções de entidade
Se duas ou mais entidades num padrão estiverem contextualizaçãomente relacionadas, os padrões utilizam [funções](luis-concept-roles.md) de entidade para extrair informações contextuais sobre entidades.  

## <a name="prediction-scores-with-and-without-patterns"></a>Pontuações de previsão com e sem padrões
Dado o suficiente exemplo de declarações, luis seria capaz de aumentar a confiança da previsão sem padrões. Os padrões aumentam a pontuação de confiança sem ter que fornecer tantas expressões.  

## <a name="pattern-matching"></a>Correspondência de padrão
Um padrão é combinado com base na deteção das entidades dentro do padrão primeiro, em seguida, validando o resto das palavras e ordem de palavrado do padrão. As entidades são necessárias no padrão para um padrão que combine. O padrão é aplicado no nível do símbolo, não no nível de carácter. 

## <a name="pattern-only-apps"></a>Aplicativos só para padrões
Você pode construir uma app com intenções que não têm pronunciamentos de exemplo, desde que haja um padrão para cada intenção. Para uma aplicação só para padrões, o padrão não deve conter entidades aprendidas por máquinas porque estas requerem declarações exemplo. 

## <a name="best-practices"></a>Melhores práticas
Aprenda [as melhores práticas.](luis-concept-best-practices.md)

## <a name="pattern-syntax"></a>Sintaxe de padrão

Aprenda a sintaxe padrão a partir da [referência sintaxe](reference-pattern-syntax.md)padrão . 

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre padrões:

* [Como adicionar padrões](luis-how-to-model-intent-pattern.md)
* [Como adicionar padrão.qualquer entidade](luis-how-to-add-entities.md#add-a-patternany-entity)
* [Padrões Sintaxe](reference-pattern-syntax.md)

> [!div class="nextstepaction"]
> [Saiba implementar padrões neste tutorial](luis-tutorial-pattern.md)
