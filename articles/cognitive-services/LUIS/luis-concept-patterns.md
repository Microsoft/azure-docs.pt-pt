---
title: Previsão de ajuda de padrões-LUIS
titleSuffix: Azure Cognitive Services
description: Um padrão permite-lhe obter maior exatidão para um objetivo sem fornecer expressões de com muitos mais.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 6234a13d07ac024849d1c890d82ef03e19c11af4
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012002"
---
# <a name="patterns-improve-prediction-accuracy"></a>Padrões de melhorar a exatidão da previsão
Padrões foram criados para melhorar a precisão quando várias expressões são muito semelhantes.  Um padrão permite-lhe obter maior exatidão para um objetivo sem fornecer expressões de com muitos mais. 

## <a name="patterns-solve-low-intent-confidence"></a>Padrões de resolver a confiança de intenção baixa
Considere uma aplicação de recursos humanos que os relatórios no organograma em relação a um funcionário. Dado o nome e a relação de um funcionário, LUIS retorne os funcionários envolvidos. Considere um funcionário, Tom, com um Gestor de Alice de nome e uma equipe de subordinados com o nome: Michael, Rebecca e Carl.

![Imagem de gráfico de organização](./media/luis-concept-patterns/org-chart.png)

|Expressões|Intenção prevista|Pontuação de intenção|
|--|--|--|
|Quem é subordinada de José?|GetOrgChart|.30|
|Quem é o subordinado de Tom?|GetOrgChart|.30|

Se tiver uma aplicação entre 10 e 20 expressões com diferentes comprimentos de sentença, ordem das palavras diferente e até mesmo palavras (sinónimos de "subordinado", "Gerir", "relatório"), o LUIS pode devolver uma pontuação de confiança baixa. Crie um padrão para ajudar a LUIS a entender a importância da ordem das palavras. 

Padrões de resolver as seguintes situações: 

* A pontuação da intenção é baixa
* A intenção correta não é a pontuação superior, mas muito próximo da pontuação superior. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Padrões não são uma garantia de intenção
Padrões de utilizam uma combinação de tecnologias de predição. A definição de um objetivo de uma expressão de modelo num padrão não é uma garantia de predição intenção, mas é um sinal forte. 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>Padrões não melhoram a detecção de entidade aprendida por máquina

Um padrão destina-se principalmente a ajudar a previsão de intenções e funções. O padrão. qualquer entidade é usada para extrair entidades de forma livre. Embora os padrões usem entidades, um padrão não ajuda a detectar uma entidade aprendida por computador.  

Não espere ver uma previsão de entidade aprimorada se você recolher várias declarações em um único padrão. Para que as entidades simples sejam acionadas, você precisa adicionar declarações ou usar entidades de lista caso contrário, o padrão não será acionado.

## <a name="patterns-use-entity-roles"></a>Padrões de utilizam as funções de entidade
Se dois ou mais entidades num padrão contextualmente estejam relacionadas, padrões de utilizam entidade [funções](luis-concept-roles.md) para extrair informações contextuais sobre entidades.  

## <a name="prediction-scores-with-and-without-patterns"></a>Pontuações de previsão com e sem padrões
Tendo em conta suficiente expressões de exemplo, LUIS seria capaz de aumentar a confiança da previsão sem padrões. Padrões de aumentam a pontuação de confiança, sem ter de fornecer expressões com tantos.  

## <a name="pattern-matching"></a>Correspondência de padrões
Um padrão é correspondido com base em detetar as entidades dentro o padrão em primeiro lugar, em seguida, validar o resto das palavras e a ordem das palavras do padrão. Entidades são necessárias no padrão para um padrão corresponder. O padrão é aplicado ao nível do token, não no nível de caractere. 

## <a name="pattern-only-apps"></a>Aplicativos somente de padrão
Você pode criar um aplicativo com tentativas que não têm nenhum exemplo de declarações, contanto que haja um padrão para cada tentativa. Para um aplicativo somente de padrão, o padrão não deve conter entidades aprendidas por computador, pois elas exigem um exemplo de declarações. 

## <a name="best-practices"></a>Melhores práticas
Saiba mais [melhores práticas](luis-concept-best-practices.md).

## <a name="pattern-syntax"></a>Sintaxe de padrão

Aprenda a sintaxe de padrão da [referência de sintaxe de padrão](reference-pattern-syntax.md). 

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre padrões:

* [Como adicionar padrões](luis-how-to-model-intent-pattern.md)
* [Como adicionar um padrão. qualquer entidade](luis-how-to-add-entities.md##add-a-patternany-entity)
* [Sintaxe de padrões](reference-pattern-syntax.md)

> [!div class="nextstepaction"]
> [Saiba como implementar padrões neste tutorial](luis-tutorial-pattern.md)
