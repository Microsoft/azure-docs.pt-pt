---
title: Tipos de entidades - LUIS
description: Uma entidade extrai dados de uma expressão do utilizador no tempo de execução da previsão. Um propósito _opcional_ e secundário é impulsionar a previsão da intenção ou de outras entidades utilizando a entidade como recurso.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 398d18642052726af4d4920443bad515ec0b5bef
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91316567"
---
# <a name="extract-data-with-entities"></a>Extrair dados com entidades

Uma entidade extrai dados de uma expressão do utilizador no tempo de execução da previsão. Um propósito _opcional_ e secundário é impulsionar a previsão da intenção ou de outras entidades utilizando a entidade como recurso.

Existem vários tipos de entidades:

* [entidade de aprendizagem automática](reference-entity-machine-learned-entity.md) - esta é a entidade primária. Deve desenhar o seu esquema com este tipo de entidade antes de utilizar outras entidades.
* Não aprendizagem automática utilizada como [recurso](luis-concept-feature.md) necessário - para correspondências de texto exatas, correspondências de padrões ou deteção por entidades pré-construídas
* [Pattern.any](#patternany-entity) - extrair texto de forma livre, como títulos de livro de um [Padrão](reference-entity-pattern-any.md)

as entidades de aprendizagem automática fornecem a maior gama de opções de extração de dados. As entidades que não aprendem máquinas trabalham por correspondência de texto e são usadas como [uma característica necessária](#design-entities-for-decomposition) para uma entidade de aprendizagem automática ou intenção.

## <a name="entities-represent-data"></a>As entidades representam dados

Entidades são dados que pretende retirar da expressão, tais como nomes, datas, nomes de produtos ou qualquer grupo significativo de palavras. Uma expressão pode incluir muitas entidades ou nenhuma. Uma aplicação do cliente _pode_ precisar dos dados para executar a sua tarefa.

As entidades devem ser rotuladas de forma consistente em todas as proclamações de formação para cada intenção de um modelo.

 Pode definir as suas próprias entidades ou utilizar entidades pré-construídas para economizar tempo para conceitos comuns como [dataV2](luis-reference-prebuilt-datetimev2.md), [ordinal,](luis-reference-prebuilt-ordinal.md) [e-mail](luis-reference-prebuilt-email.md)e [número de telefone.](luis-reference-prebuilt-phonenumber.md)

|Expressão|Entidade|Dados|
|--|--|--|
|Compre 3 bilhetes para Nova Iorque|Número pré-construído<br>Destino|3<br>Nova Iorque|


### <a name="entities-are-optional-but-recommended"></a>As entidades são opcionais, mas recomendadas

Enquanto as intenções são [necessárias,](luis-concept-intent.md) as entidades são opcionais. Não é necessário criar entidades para cada conceito na sua app, mas apenas para aqueles em que a aplicação do cliente precisa dos dados ou a entidade atua como uma dica ou sinal para outra entidade ou intenção.

À medida que a sua aplicação se desenvolve e uma nova necessidade de dados é identificada, pode adicionar entidades apropriadas ao seu modelo LUIS mais tarde.

<a name="entity-compared-to-intent"></a>

## <a name="entity-represents-data-extraction"></a>Entidade representa extração de dados

A entidade representa um conceito de dados _dentro da expressão._ Uma intenção classifica toda a _expressão._

Considere as seguintes quatro expressões:

|Expressão|Intenção prevista|Entidades extraídas|Explicação|
|--|--|--|--|
|Ajuda|Ajuda|-|Nada a extrair.|
|Enviar algo|enviar Alguma coisa|-|Nada a extrair. O modelo não tem uma característica necessária para extrair `something` neste contexto, e não existe nenhum destinatário indicado.|
|Envie um presente ao Bob|enviar Alguma coisa|`Bob`, `present`|O modelo extrai `Bob` adicionando uma característica necessária da entidade pré-construída. `personName` Uma entidade de aprendizagem automática foi utilizada para `present` extrair.|
|Envie ao Bob uma caixa de chocolates.|enviar Alguma coisa|`Bob`, `box of chocolates`|Os dois importantes `Bob` dados, e os `box of chocolates` , foram extraídos por entidades de aprendizagem automática.|

## <a name="label-entities-in-all-intents"></a>Entidades de etiquetagem em todas as intenções

As entidades extraem dados independentemente da intenção prevista. Certifique-se de rotular _todas as_ expressões de exemplo em todas as intenções. A `None` intenção de etiquetagem da entidade em falta causa confusão, mesmo que houvesse muito mais declarações de formação para as outras intenções.

## <a name="design-entities-for-decomposition"></a>Entidades de design para decomposição

as entidades de machine-learning permitem-lhe conceber o seu esquema de aplicações para decomposição, quebrando um grande conceito em subentidades.

A conceção para decomposição permite à LUIS devolver um profundo grau de resolução de entidade à aplicação do seu cliente. Isto permite que a sua aplicação ao cliente se concentre nas regras do negócio e deixe a resolução de dados para a LUIS.

Uma entidade de aprendizagem automática dispara com base no contexto aprendido através de palavras de exemplo.

[**as entidades de aprendizagem automática**](tutorial-machine-learned-entity.md) são os extratores de alto nível. As subentidades são entidades infantis de entidades de aprendizagem automática.

## <a name="effective-machine-learned-entities"></a>Entidades eficazes aprendidas com máquinas

Para construir as entidades aprendidas com a máquina de forma eficaz:

* A sua rotulagem deve ser consistente em todas as intenções. Isto inclui até expressões que fornece na intenção **de Nenhum** que inclua esta entidade. Caso contrário, o modelo não será capaz de determinar eficazmente as sequências.
* Se tiver uma entidade aprendida com subconsidades, certifique-se de que as diferentes encomendas e variantes da entidade e subentências são apresentadas nas expressões etiquetadas. As expressões de exemplo rotuladas devem incluir todos os formulários válidos, e incluir entidades que aparecem e estão ausentes e também reordenadas dentro da expressão.
* Deve evitar a adaptação excessiva das entidades a um conjunto muito fixo. **A adaptação excessiva** acontece quando o modelo não se generaliza bem, e é um problema comum em modelos de aprendizagem automática. Isto implica que a aplicação não funcionaria adequadamente em novos dados. Por sua vez, deve variar as expressões de exemplo rotuladas para que a aplicação seja capaz de generalizar para além dos exemplos limitados que fornece. Deve variar as diferentes subentidades com mudança suficiente para que o modelo pense mais no conceito em vez de apenas os exemplos mostrados.

## <a name="effective-prebuilt-entities"></a>Entidades pré-construídas eficazes

Para construir entidades eficazes que extraam dados comuns, como os fornecidos pelas [entidades pré-construídas,](luis-reference-prebuilt-entities.md)recomendamos o seguinte processo.

Melhore a extração de dados trazendo os seus próprios dados para uma entidade como recurso. Desta forma, todos os rótulos adicionais dos seus dados irão aprender o contexto de onde existem nomes de pessoas na sua aplicação.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Tipos de entidades

Uma sub-entidade para um progenitor deve ser uma entidade de aprendizagem automática. A sub-entidade pode utilizar uma entidade que não aprende máquinas como [recurso](luis-concept-feature.md).

Escolha a entidade com base na forma como os dados devem ser extraídos e como devem ser representados após a sua extração.

|Tipo de entidade|Objetivo|
|--|--|
|[**Aprendido com máquinas**](tutorial-machine-learned-entity.md)|Extrair dados aninhados e complexos aprendidos com exemplos rotulados. |
|[**Lista**](reference-entity-list.md)|Lista de itens e respetivos sinónimos extraídos com **correspondência de texto exata**.|
|[**Padrão.qualquer**](#patternany-entity)|Entidade onde encontrar o fim da entidade é difícil de determinar porque a entidade é de forma livre. Disponível apenas em [padrões.](luis-concept-patterns.md)|
|[**Pré-construído**](luis-reference-prebuilt-entities.md)|Já treinado para extrair dados específicos como URL ou e-mail. Algumas destas entidades pré-construídas são definidas no projeto De código aberto [Recognisers-Text.](https://github.com/Microsoft/Recognizers-Text) Se a sua cultura ou entidade específica não for suportada atualmente, contribua para o projeto.|
|[**Expressão Regular**](reference-entity-regular-expression.md)|Utiliza uma expressão regular para **correspondência de texto exata**.|


## <a name="extraction-versus-resolution"></a>Extração versus resolução

As entidades extraem dados à medida que os dados aparecem na expressão. As entidades não alteram nem resolvem os dados. A entidade não fornecerá qualquer resolução se o texto for um valor válido para a entidade ou não.

Existem formas de trazer a resolução para a extração, mas deve estar ciente de que isso limita a capacidade da app de ser imune a variações e erros.

As entidades de lista e a expressão regular (correspondência de textos) podem ser usadas como [características necessárias](luis-concept-feature.md#required-features) a uma sub-entidade e que funciona como um filtro para a extração. Deve usar isto cuidadosamente para não dificultar a capacidade da aplicação de prever.

## <a name="extracting-contextually-related-data"></a>Extrair dados contextualizaçãos relacionadas

Uma expressão pode conter duas ou mais ocorrências de uma entidade em que o significado dos dados se baseia no contexto dentro da expressão. Um exemplo é uma expressão para reservar um voo que tem duas localizações geográficas, origem e destino.

`Book a flight from Seattle to Cairo`

Os dois locais precisam de ser extraídos de forma a que a aplicação do cliente conheça o tipo de cada local para completar a compra do bilhete.

Para extrair a origem e o destino, crie duas subentâncias como parte da entidade de aprendizagem automática de máquinas de encomenda de bilhetes. Para cada uma das subentidades, crie uma característica necessária que utilize a geografiaV2.

<a name="using-component-constraints-to-help-define-entity"></a>
<a name="using-subentity-constraints-to-help-define-entity"></a>

### <a name="using-required-features-to-constrain-entities"></a>Usando as funcionalidades necessárias para conter as entidades

Saiba mais sobre [as funcionalidades necessárias](luis-concept-feature.md)

## <a name="patternany-entity"></a>Entidade Pattern.any

Um Padrão.qualquer um só está disponível num [Padrão](luis-concept-patterns.md).

<a name="if-you-need-more-than-the-maximum-number-of-entities"></a>
## <a name="exceeding-app-limits-for-entities"></a>Excedendo os limites de aplicações para entidades

Se precisar de mais do que o [limite,](luis-limits.md#model-limits)contacte. Para tal, recolha informações detalhadas sobre o seu sistema, vá ao site da [LUIS](luis-reference-regions.md#luis-website) e, em seguida, selecione **Support**. Se a sua subscrição Azure incluir serviços de suporte, contacte [o suporte técnico da Azure.](https://azure.microsoft.com/support/options/)

## <a name="entity-prediction-status-and-errors"></a>Estado de previsão da entidade e erros

O portal LUIS mostra quando a entidade tem uma previsão de entidade diferente da entidade que selecionou para um exemplo de expressão. Esta pontuação diferente baseia-se no modelo treinado atual. 

:::image type="content" source="./media/luis-concept-entities/portal-entity-prediction-error.png" alt-text="O portal LUIS mostra quando a entidade tem uma previsão de entidade diferente da entidade que selecionou para um exemplo de expressão.":::

O texto de erro é realçado dentro da expressão de exemplo, e a linha de expressão de exemplo tem um indicador de erro à direita, mostrado como um triângulo vermelho. 

Utilize estas informações para resolver os erros da entidade utilizando um ou mais dos seguintes:
* O texto realçado está mal rotulado. Para corrigir, rever, corrigir e retreinar. 
* Criar uma [funcionalidade](luis-concept-feature.md) para a entidade ajudar a identificar o conceito da entidade
* Adicione mais [palavras de exemplo](luis-concept-utterance.md) e etiqueta com a entidade
* [Reveja sugestões de aprendizagem ativa](luis-concept-review-endpoint-utterances.md) para quaisquer declarações recebidas no ponto final de previsão que possam ajudar a identificar o conceito da entidade.

## <a name="next-steps"></a>Passos seguintes

Aprenda conceitos sobre boas [expressões.](luis-concept-utterance.md)

Consulte [entidades Add](luis-how-to-add-entities.md) para saber mais sobre como adicionar entidades à sua app LUIS.

Ver [Tutorial: Extrair dados estruturados da expressão do utilizador com entidades de aprendizagem automática em Compreensão de Línguas (LUIS)](tutorial-machine-learned-entity.md) para aprender a extrair dados estruturados a partir de uma expressão utilizando a entidade de aprendizagem automática.

