---
title: Tipos de entidades - LUIS
description: Uma entidade extrai dados de uma expressão do utilizador no tempo de execução da previsão. Um propósito _opcional,_ secundário é impulsionar a previsão da intenção ou de outras entidades, utilizando a entidade como recurso.
ms.topic: conceptual
ms.date: 05/17/2020
ms.openlocfilehash: a5e4812eab84650401dd19b0f8d7b361a5135dd3
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682183"
---
# <a name="extract-data-with-entities"></a>Extrair dados com entidades

Uma entidade extrai dados de uma expressão do utilizador no tempo de execução da previsão. Um propósito _opcional,_ secundário é impulsionar a previsão da intenção ou de outras entidades, utilizando a entidade como recurso.

Existem vários tipos de entidades:

* [entidade de machine-learning](reference-entity-machine-learned-entity.md) - esta é a entidade principal. Deve desenhar o seu esquema com este tipo de entidade antes de utilizar outras entidades.
* Não-aprendizagem automática utilizada como [característica](luis-concept-feature.md) necessária - para correspondências de texto exatas, correspondências de padrões ou deteção por entidades pré-construídas
* [Pattern.any](#patternany-entity) - para extrair texto de forma livre, como títulos de livro de um [Padrão](reference-entity-pattern-any.md)

As entidades de machine-learning fornecem a mais ampla gama de escolhas de extração de dados. As entidades que não aprendem máquinas funcionam por correspondência de texto e são usadas como [uma característica necessária](#design-entities-for-decomposition) para uma entidade ou intenção de aprendizagem automática.

## <a name="entities-represent-data"></a>Entidades representam dados

As entidades são dados que pretende retirar da expressão, tais como nomes, datas, nomes de produtos ou qualquer grupo significativo de palavras. Uma expressão pode incluir muitas entidades ou nenhuma. Uma aplicação do cliente _pode_ precisar dos dados para executar a sua tarefa.

As entidades precisam de ser rotuladas de forma consistente em todas as declarações de formação para cada intenção de um modelo.

 Pode definir as suas próprias entidades ou utilizar entidades pré-construídas para economizar tempo para conceitos comuns como [dataV2](luis-reference-prebuilt-datetimev2.md), [ordinal,](luis-reference-prebuilt-ordinal.md) [e-mail](luis-reference-prebuilt-email.md)e número de [telefone.](luis-reference-prebuilt-phonenumber.md)

|Expressão|Entidade|Dados|
|--|--|--|
|Compre 3 bilhetes para Nova Iorque|Número pré-construído<br>Destino|3<br>Nova Iorque|


### <a name="entities-are-optional-but-recommended"></a>As entidades são opcionais, mas recomendadas

Embora sejam [necessárias intenções,](luis-concept-intent.md) as entidades são opcionais. Não precisa de criar entidades para cada conceito da sua app, mas apenas para aqueles em que a aplicação do cliente precisa dos dados ou a entidade funciona como uma dica ou sinal para outra entidade ou intenção.

À medida que a sua aplicação se desenvolve e uma nova necessidade de dados é identificada, pode adicionar as entidades apropriadas ao seu modelo LUIS mais tarde.

## <a name="entity-compared-to-intent"></a>Entidade comparada com a intenção

A entidade representa um conceito de dados _dentro da expressão._ Uma intenção classifica toda a _expressão._

Considere as seguintes quatro expressões:

|Expressão|Intenção prevista|Entidades extraídas|Explicação|
|--|--|--|--|
|Ajuda|Ajuda|-|Nada para extrair.|
|Enviar algo|enviar Algo|-|Nada para extrair. O modelo não tem uma característica necessária para extrair `something` neste contexto, e não há nenhum destinatário indicado.|
|Envie um presente ao Bob.|enviar Algo|`Bob`, `present`|O modelo extrai `Bob` adicionando uma característica necessária da entidade pré-construída. `personName` Uma entidade de aprendizagem automática foi usada para `present` extrair.|
|Mande ao Bob uma caixa de chocolates.|enviar Algo|`Bob`, `box of chocolates`|As duas peças importantes de dados, `Bob` e `box of chocolates` as, foram extraídas por entidades de aprendizagem automática.|

## <a name="design-entities-for-decomposition"></a>Entidades de design para decomposição

As entidades de machine-learning permitem-lhe conceber o seu esquema de aplicação para decomposição, quebrando um grande conceito em subentidades.

A conceção para decomposição permite à LUIS devolver um profundo grau de resolução de entidades à sua aplicação de cliente. Isto permite que a sua aplicação de cliente se concentre nas regras do negócio e deixe a resolução de dados para a LUIS.

Uma entidade de aprendizagem automática dispara com base no contexto aprendido através de declarações de exemplo.

[**as entidades de machine-learning**](tutorial-machine-learned-entity.md) são os extratores de alto nível. As subentidades são entidades infantis de entidades de machine-learning.

## <a name="effective-machine-learned-entities"></a>Entidades eficazes aprendidas por máquinas

Para construir a máquina aprendeu entidades eficazmente:

* A sua rotulagem deve ser consistente em todas as intenções. Isto inclui até declarações que fornece na intenção **None** que incluem esta entidade. Caso contrário, o modelo não será capaz de determinar eficazmente as sequências.
* Se tiver uma entidade aprendida com subentidades, certifique-se de que as diferentes encomendas e variantes da entidade e subentidades são apresentadas nas expressões rotuladas. As expressões de exemplo rotuladas devem incluir todos os formulários válidos, e incluir entidades que aparecem e estão ausentes e também reordenadas dentro da expressão.
* Deve evitar a sobremontagem das entidades num conjunto muito fixo. O excesso de **adaptação** acontece quando o modelo não se generaliza bem, e é um problema comum nos modelos de aprendizagem automática. Isto implica que a aplicação não funcionaria adequadamente em novos dados. Por sua vez, deve variar as expressões de exemplo rotulados para que a aplicação seja capaz de generalizar para além dos exemplos limitados que fornece. Deve variar as diferentes subentidades com alterações suficientes para que o modelo pense mais no conceito em vez de apenas os exemplos mostrados.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Tipos de entidades

Uma subentidade para um pai deve ser uma entidade de aprendizagem automática. A subentidade pode utilizar uma entidade não-machine-learning como [recurso](luis-concept-feature.md).

Escolha a entidade com base na forma como os dados devem ser extraídos e como devem ser representados após a sua extração.

|Tipo de entidade|Objetivo|
|--|--|
|[**Aprendido em máquina**](tutorial-machine-learned-entity.md)|Extrair dados aninhados e complexos aprendidos a partir de exemplos rotulados. |
|[**Lista**](reference-entity-list.md)|Lista de itens e seus sinónimos extraídos com **correspondência exata**de texto .|
|[**Padrão.qualquer**](#patternany-entity)|Entidade onde encontrar o fim da entidade é difícil de determinar porque a entidade é de forma livre. Disponível apenas em [padrões.](luis-concept-patterns.md)|
|[**Pré-construído**](luis-reference-prebuilt-entities.md)|Já treinado para extrair dados específicos, como URL ou e-mail. Algumas destas entidades pré-construídas são definidas no projeto [Recognisers-Text](https://github.com/Microsoft/Recognizers-Text) de código aberto. Se a sua cultura ou entidade específica não for atualmente apoiada, contribua para o projeto.|
|[**Expressão Regular**](reference-entity-regular-expression.md)|Utiliza expressão regular para **uma correspondência de texto exata**.|


## <a name="extraction-versus-resolution"></a>Extração versus resolução

As entidades extraem dados à medida que os dados aparecem na expressão. As entidades não alteram nem resolvem os dados. A entidade não fornecerá qualquer resolução se o texto for ou não um valor válido para a entidade.

Existem formas de introduzir a resolução na extração, mas deve estar ciente de que isso limita a capacidade da app de ser imune a variações e erros.

As entidades de lista e as entidades de expressão regular (correspondência de texto) podem ser utilizadas como [características necessárias](luis-concept-feature.md#required-features) a uma subentidade e que atuam como um filtro para a extração. Deve usar isto cuidadosamente para não dificultar a capacidade da aplicação de prever.

## <a name="extracting-contextually-related-data"></a>Extração de dados contexicamente relacionados

Uma expressão pode conter duas ou mais ocorrências de uma entidade onde o significado dos dados se baseie no contexto dentro da expressão. Um exemplo é uma expressão para reservar um voo que tem duas localizações geográficas, origem e destino.

`Book a flight from Seattle to Cairo`

Os dois locais precisam de ser extraídos de forma a que a aplicação do cliente conheça o tipo de cada local para concluir a compra do bilhete.

Para extrair a origem e o destino, crie duas subentidades como parte da entidade de machine-learning da ticket order. Para cada uma das subentidades, crie uma característica necessária que utilize geografiaV2.

<a name="using-component-constraints-to-help-define-entity"></a>
<a name="using-subentity-constraints-to-help-define-entity"></a>

### <a name="using-required-features-to-constrain-entities"></a>Utilização de funcionalidades necessárias para condicionar entidades

Saiba mais sobre [as funcionalidades necessárias](luis-concept-feature.md)

## <a name="patternany-entity"></a>Entidade Pattern.any

Um Padrão.qualquer está disponível apenas num [Padrão](luis-concept-patterns.md).

<a name="if-you-need-more-than-the-maximum-number-of-entities"></a>
## <a name="exceeding-app-limits-for-entities"></a>Exceder limites de aplicações para entidades

Se precisar de mais do que o [limite,](luis-limits.md#model-limits)suporte de contato. Para tal, recolha informações detalhadas sobre o seu sistema, vá ao site da [LUIS](luis-reference-regions.md#luis-website) e, em seguida, selecione **Suporte**. Se a sua subscrição Azure incluir serviços de suporte, contacte o [suporte técnico do Azure.](https://azure.microsoft.com/support/options/)

## <a name="entity-prediction-status"></a>Estado de previsão da entidade

O portal LUIS mostra quando a entidade tem uma previsão de entidade diferente da entidade que selecionou por exemplo. Esta pontuação diferente baseia-se no modelo treinado atualmente. Utilize estas informações para resolver erros de treino utilizando um ou mais dos seguintes:
* Criar uma [funcionalidade](luis-concept-feature.md) para a entidade ajudar a identificar o conceito da entidade
* Adicione mais [declarações](luis-concept-utterance.md) de exemplo e rótulo com a entidade
* [Reveja sugestões](luis-concept-review-endpoint-utterances.md) de aprendizagem ativa para quaisquer declarações recebidas no ponto final da previsão que possam ajudar a identificar o conceito da entidade.

## <a name="next-steps"></a>Passos seguintes

Aprenda conceitos sobre boas [expressões.](luis-concept-utterance.md)

Consulte [adicionar entidades](luis-how-to-add-entities.md) para saber mais sobre como adicionar entidades à sua app LUIS.

Ver [Tutorial: Extrair dados estruturados da expressão do utilizador com entidades de machine-learning em Compreensão linguística (LUIS)](tutorial-machine-learned-entity.md) para aprender a extrair dados estruturados de uma expressão utilizando a entidade de machine-learning.

