---
title: Design com modelos - LUIS
description: A compreensão da linguagem fornece vários tipos de modelos. Alguns modelos podem ser usados de uma forma.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: bbb1f0d43b2a3fd2e8a2dff2201a09622ecaf977
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683929"
---
# <a name="design-with-intent-and-entity-models"></a>Design com modelos de intenção e entidade

A compreensão linguística fornece dois tipos de modelos para que possa definir o seu esquema de aplicação. O esquema da sua aplicação determina que informações recebe da previsão de uma nova expressão do utilizador.

O esquema da aplicação é construído a partir de modelos que cria usando o ensino de [máquinas:](#authoring-uses-machine-teaching)
* [Intenções](#intents-classify-utterances) classificam as palavras dos utilizadores
* [Entidades](#entities-extract-data) extraem dados da expressão

## <a name="authoring-uses-machine-teaching"></a>A autoria utiliza o ensino de máquinas

A metodologia de ensino de máquinas da LUIS permite-lhe facilmente ensinar conceitos a uma máquina. Compreender a _aprendizagem automática_ não é necessário para usar o LUIS. Em vez disso, você, como professor, comunica um conceito ao LUIS, fornecendo exemplos do conceito e explicando como um conceito deve ser modelado usando outros conceitos relacionados. Você, como professor, também pode melhorar o modelo do LUIS interativamente identificando e corrigindo erros de previsão.

<a name="v3-authoring-model-decomposition"></a>

## <a name="intents-classify-utterances"></a>Intenções classificam as palavras

Uma intenção classifica as declarações de exemplo para ensinar LUIS sobre a intenção. As declarações de exemplo dentro de uma intenção são usadas como exemplos positivos da expressão. Estas mesmas expressões são usadas como exemplos negativos em todas as outras intenções.

Considere uma aplicação que precisa determinar a intenção de um utilizador de encomendar um livro e uma app que precisa do endereço de envio para o cliente. Esta aplicação tem duas intenções: `OrderBook` e `ShippingLocation` .

A seguinte expressão é um **exemplo positivo** para a `OrderBook` intenção e um exemplo **negativo** para as `ShippingLocation` intenções e `None` intenções:

`Buy the top-rated book on bot architecture.`

## <a name="entities-extract-data"></a>Entidades extraem dados

Uma entidade representa uma unidade de dados que pretende extrair da expressão. Uma entidade de machine-learning é uma entidade de alto nível que contém subentidades, que também são entidades de aprendizagem automática.

Um exemplo de uma entidade de aprendizagem automática é uma encomenda de um bilhete de avião. Conceptualmente esta é uma única transação com muitas unidades menores de dados, tais como data, hora, quantidade de assentos, tipo de assento, como primeira classe ou autocarro, localização de origem, localização de destino e escolha de refeições.

## <a name="intents-versus-entities"></a>Intenções versus entidades

Uma intenção é o resultado desejado de _toda_ a expressão, enquanto as entidades são pedaços de dados extraídos da expressão. Normalmente, as intenções estão ligadas a ações, que a aplicação do cliente deve tomar. As entidades são informações necessárias para a realização desta ação. Do ponto de vista da programação, uma intenção desencadearia uma chamada de método e as entidades seriam usadas como parâmetros para essa chamada de método.

Esta expressão _deve_ ter uma intenção e _pode_ ter entidades:

`Buy an airline ticket from Seattle to Cairo`

Esta expressão tem uma única intenção:

* Comprar um bilhete de avião

Esta expressão _pode_ ter várias entidades:

* Localizações de Seattle (origem) e Cairo (destino)
* A quantidade de um único bilhete

## <a name="entity-model-decomposition"></a>Decomposição do modelo de entidade

O LUIS suporta a _decomposição_ do modelo com as APIs autoras, decompondo um conceito em partes mais pequenas. Isto permite-lhe construir os seus modelos com confiança na forma como as várias peças são construídas e previstas.

A decomposição do modelo tem as seguintes peças:

* [intençãos](#intents-classify-utterances)
    * [características](#features)
* [entidades de aprendizagem automática](reference-entity-machine-learned-entity.md)
    * subentidades (também entidades de aprendizagem automática)
        * [características](#features)
            * [lista de frases](luis-concept-feature.md)
            * [entidades não-máquina-aprendizagem,](luis-concept-feature.md) tais como [expressões regulares,](reference-entity-regular-expression.md) [listas](reference-entity-list.md)e [entidades pré-construídas](luis-reference-prebuilt-entities.md)

<a name="entities-extract-data"></a>
<a name="machine-learned-entities"></a>

## <a name="features"></a>Funcionalidades

Uma [característica](luis-concept-feature.md) é um traço distintivo ou atributo de dados que o seu sistema observa. As funcionalidades de machine learning dão pistas importantes para o LUIS procurar coisas que distingam um conceito. São indícios de que o LUIS pode usar, mas não regras rígidas. Estas dicas são utilizadas em conjunto com as etiquetas para encontrar os dados.

## <a name="patterns"></a>Padrões

[Os padrões](luis-concept-patterns.md) são projetados para melhorar a precisão quando várias expressões são muito semelhantes. Um padrão permite-lhe ganhar mais precisão para uma intenção sem fornecer muitas mais expressões.

## <a name="extending-the-app-at-runtime"></a>Alargar a aplicação em tempo de execução

O esquema da aplicação (modelos e funcionalidades) é treinado e publicado para o ponto final de previsão. Pode [passar novas informações](schema-change-prediction-runtime.md), juntamente com a expressão do utilizador, para o ponto final da previsão para aumentar a previsão.

## <a name="next-steps"></a>Passos seguintes

* Compreender [as intenções](luis-concept-intent.md) e [as entidades.](luis-concept-entity-types.md)
* Saiba mais sobre [funcionalidades](luis-concept-feature.md)