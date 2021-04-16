---
title: Tipos de entidades - LUIS
description: Uma entidade extrai dados de uma expressão do utilizador no tempo de execução da previsão. Um propósito _opcional_ e secundário é impulsionar a previsão da intenção ou de outras entidades utilizando a entidade como recurso.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: 44cffecd653ec2ec748e73d01dc86a87cfcd7de9
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500333"
---
# <a name="entities-in-luis"></a>Entidades em LUIS

Uma entidade é um item ou um elemento que é relevante para a intenção do utilizador. As entidades definem dados que podem ser extraídos da expressão e são essenciais para completar a ação exigida pelo utilizador. Por exemplo:

|Expressão|Intenção prevista|Entidades extraídas|Explicação|
|--|--|--|--|
|Olá, tudo bem?|Saudação|-|Nada a extrair.|
|Quero pedir uma pequena pizza.|orderPizza| "pequeno" | A entidade "tamanho" é extraída como "pequena".|
|Desligue a luz do quarto|turnOff| "Quarto" | A entidade "Quarto" é extraída como "quarto".|
|Verifique o saldo na minha conta poupança terminando em 4406|checkBalance| "poupança", "4406" | A entidade "accountType" é extraída como entidade "poupança" e "accountNumber" é extraída como "4406".|
|Compre 3 bilhetes para Nova Iorque|buyTickets| "3", "Nova Iorque" | A entidade "ticketsCount" é extraída como entidade "3" e "Destino" é extraída como "Nova Iorque".|

As entidades são opcionais, mas recomendadas. Não precisa de criar entidades para todos os conceitos da sua app, apenas para aqueles em que:

* A aplicação do cliente precisa dos dados, ou 
* A entidade atua como uma sugestão ou sinal para outra entidade ou intenção. Para saber mais sobre entidades como Funcionalidades vá para [Entidades como funcionalidades.](#entities-as-features)

## <a name="entity-types"></a>Tipos de entidade

Para criar uma entidade, tem que dar-lhe um nome e um tipo. Existem vários tipos de entidades no LUIS. 

### <a name="list-entity"></a>Entidade de Lista

Uma entidade de lista representa um conjunto fixo e fechado de palavras relacionadas juntamente com os seus sinónimos. Pode utilizar as entidades da lista para reconhecer múltiplos sinónimos ou variações e extrair uma saída normalizada para eles. Utilize a opção *de recomendação* para ver sugestões de novas palavras com base na lista atual. 

Uma entidade de lista não é aprendida com máquinas, o que significa que a LUIS não descobre valores adicionais para as entidades de lista. Luis marca qualquer correspondência com um item em qualquer lista como entidade na resposta.

A correspondência nas entidades da lista é sensível ao caso e tem de ser uma correspondência exata a extrair. Os valores normalizados também são utilizados ao combinar a entidade de lista. Por exemplo:

|Valor normalizado|Sinónimos|
|--|--|
|Pequeno|sm, sml, minúsculo, menor|
|Médio|md, mdm, regular, média, meio|
|Grande|lg, lrg, grande|

Consulte o artigo de referência das [entidades da lista](reference-entity-list.md) para obter mais informações.

### <a name="regex-entity"></a>Entidade Regex

Uma entidade de expressão regular extrai uma entidade com base num padrão de expressão regular que fornece. Ignora o caso e ignora a variante cultural. A expressão regular é melhor para textos estruturados ou uma sequência predefinida de valores alfanuméricos que são esperados num determinado formato. Por exemplo:

|Entidade|Expressão regular|Exemplo|
|--|--|--|
|Número de voo|voo [A-Z] {2} [0-9]{4}| voo AS 1234|
|Número do Cartão de Crédito|[0-9]{16}|5478789865437632|

Consulte o artigo de referência das [entidades regex](reference-entity-regular-expression.md) para obter mais informações.

### <a name="prebuilt-entity"></a>Entidade Pré-criada

A LUIS oferece um conjunto de entidades pré-construídas para reconhecer tipos comuns de dados como nome, data, número e moeda.  O comportamento das entidades pré-construídas é fixo. O suporte à entidade pré-construída varia de acordo com a cultura da app LUIS. Por exemplo:

|Entidade pré-construída|Valor de exemplo|
|--|--|
|Nome da pessoa|James, Bill, Tom|
|DatatimeV2|2019-05-02, 2 de maio, 8h no dia 2 de maio de 2019|

Consulte o artigo de referência das [entidades pré-construídas](./luis-reference-prebuilt-entities.md) para obter mais informações.

### <a name="patternany-entity"></a>Padrão.Qualquer Entidade

Um padrão. Qualquer entidade é um espaço reservado de comprimento variável usado apenas na expressão do modelo de um padrão para marcar onde a entidade começa e termina. Segue uma regra ou padrão específico e é melhor utilizado para frases com estrutura lexical fixa. Por exemplo:

|Expressão de exemplo|Padrão|Entidade|
|--|--|--|
|Posso comer um hambúrguer, por favor?|Posso ter uma {refeição} [por favor][?]| hambúrguer
|Posso comer uma pizza?|Posso ter uma {refeição} [por favor][?]| pizza
|Onde posso encontrar o Grande Gatsby?|Onde posso encontrar {bookName}?| O Grande Gatsby|

Consulte o [Artigo de referência de Entidades.Qualquer entidade](./reference-entity-pattern-any.md) para obter mais informações.

### <a name="machine-learned-ml-entity"></a>Entidade aprendida com máquina (ML)

A entidade aprendida em máquinas usa o contexto para extrair entidades com base em exemplos rotulados. É a entidade preferida para a construção de aplicações LUIS. Baseia-se em algoritmos de aprendizagem automática e requer que a rotulagem seja adaptada à sua aplicação com sucesso. Utilize uma entidade ML para identificar dados que nem sempre estão bem formatados, mas têm o mesmo significado. 

|Expressão de exemplo|Entidade *de produto* extraída|
|--|--|
|Quero comprar um livro.|"livro"|
|Pode dar-me estes sapatos, por favor?|"Sapatos"|
|Adicione os calções ao meu cesto.|"Calções"|

Você pode saber mais sobre as entidades aprendidas com a Máquina [aqui.](./reference-entity-machine-learned-entity.md)

Consulte o [artigo de referência das entidades aprendidas para](./reference-entity-pattern-any.md) obter mais informações.

#### <a name="ml-entity-with-structure"></a>Entidade ML com Estrutura

Uma entidade ML pode ser composta por sub-entidades mais pequenas, cada uma das quais pode ter propriedades próprias. Por exemplo, *o Endereço* pode ter a seguinte estrutura:

* Endereço: 4567 Main Street, NY, 98052, EUA
    * Número do edifício: 4567
    * Nome da rua: Rua Principal
    * Estado: NY
    * Código Postal: 98052
    * País: EUA


### <a name="building-effective-ml-entities"></a>Construção de entidades ml eficazes

Para construir entidades aprendidas com máquinas de forma eficaz, siga estas melhores práticas:

* Se tiver uma entidade aprendida com sub-entidades, certifique-se de que as diferentes encomendas e variantes da entidade e sub-entidades são apresentadas nas expressões rotuladas. As expressões de exemplo rotuladas devem incluir todos os formulários válidos, e incluir entidades que aparecem e estão ausentes e também reordenadas dentro da expressão.

* Evite a adaptação excessiva das entidades a um conjunto muito fixo. A adaptação excessiva acontece quando o modelo não se generaliza bem, e é um problema comum em modelos de aprendizagem automática. Isto implica que a aplicação não funcionaria adequadamente em novos tipos de exemplos. Por sua vez, deve variar as expressões de exemplo rotuladas para que a aplicação possa generalizar para além dos exemplos limitados que fornece.

* A sua rotulagem deve ser consistente em todas as intenções. Isto inclui até expressões que fornece na intenção *de None* que inclui esta entidade. Caso contrário, o modelo não será capaz de determinar eficazmente as sequências.

## <a name="entities-as-features"></a>Entidades como características

Outra função importante das entidades é usá-las como características ou características distintivas para outras intenções ou entidades para que o seu sistema observe e aprenda através delas.

### <a name="entities-as-features-for-intents"></a>Entidades como características para intenções

Pode usar as entidades como sinal para uma intenção. Por exemplo, a presença de uma determinada entidade na expressão pode distinguir a intenção a que se enquadra.

|Expressão de exemplo|Entidade|Intenção|
|--|--|--|
|Marque-me uma *luta para Nova Iorque.*|City|Voo do Livro|
|Reserve-me a *sala de conferências principal.*|Quarto|Sala de Reserva|

### <a name="entities-as-feature-for-entities"></a>Entidades como Recurso para Entidades

Pode ainda utilizar as entidades como indicador da presença de outras entidades. Um exemplo comum disso é usar uma entidade pré-construída como uma característica para outra entidade ML.
Se você está construindo um sistema de reserva de voo e sua expressão parece "Reserve-me um voo do Cairo para Seattle", você terá *Origin City* e *Destination City* como entidades ML. Uma boa prática seria utilizar a entidade pré-construída `GeographyV2` como recurso para ambas as entidades.

Consulte o [artigo de referência das entidades GeografiaV2](./luis-reference-prebuilt-geographyv2.md) para mais informações.

Também pode utilizar as entidades como funcionalidades necessárias para outras entidades. Isto ajuda na resolução de entidades extraídas. Por exemplo, se estiver a criar uma aplicação de encomenda de pizza e tiver uma `Size` entidade ML, pode criar `SizeList` uma entidade de lista e usá-la como uma funcionalidade necessária para a `Size` entidade. A sua aplicação devolverá o valor normalizado como entidade extraída da expressão. 

Consulte [as funcionalidades](luis-concept-feature.md) para mais informações e [entidades pré-construídas](./luis-reference-prebuilt-entities.md) para saber mais sobre a resolução de entidades pré-construídas disponíveis na sua cultura. 


## <a name="entity-prediction-status-and-errors"></a>Estado de previsão da entidade e erros

O portal LUIS mostra o seguinte quando a entidade tem uma previsão de entidade diferente da entidade que rotulou para uma palavra de exemplo. Esta pontuação diferente baseia-se no modelo treinado atual. 

:::image type="content" source="./media/luis-concept-entities/portal-entity-prediction-error.png" alt-text="O portal LUIS mostra quando a entidade tem uma previsão de entidade diferente da entidade que selecionou para um exemplo de expressão":::

O texto que causa o erro é realçado dentro da expressão de exemplo, e a linha de expressão de exemplo tem um indicador de erro à direita, mostrado como um triângulo vermelho. 

Para resolver erros de entidade, tente um ou mais dos seguintes erros:

* O texto realçado está mal rotulado. Para corrigir, reveja a etiqueta, corrija-a e retreine a aplicação. 
* Crie uma [funcionalidade](luis-concept-feature.md) para a entidade ajudar a identificar o conceito da entidade.
* Adicione mais [palavras de exemplo](luis-concept-utterance.md) e etiquetar com a entidade.
* [Reveja sugestões de aprendizagem ativa](luis-concept-review-endpoint-utterances.md) para quaisquer declarações recebidas no ponto final de previsão que possam ajudar a identificar o conceito da entidade.


## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre boas [declarações de](luis-concept-utterance.md)exemplo.
* Consulte [entidades Add](luis-how-to-add-entities.md) para saber mais sobre como adicionar entidades à sua app LUIS.
* Saiba mais sobre [os limites de candidatura da](./luis-limits.md)LUIS. 
* Use um [tutorial](tutorial-machine-learned-entity.md) para aprender a extrair dados estruturados de uma expressão usando a entidade de aprendizagem automática.
