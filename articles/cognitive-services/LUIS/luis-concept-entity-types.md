---
title: Tipos de entidade
titleSuffix: Language Understanding - Azure Cognitive Services
description: 'Entidades extrair dados da expressão. Tipos de entidade dão-lhe previsível extração de dados. Existem dois tipos de entidades: aprendeu de máquina e não aprendidas máquina. É importante saber que tipo de entidade, está trabalhando com expressões com.'
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: diberry
ms.openlocfilehash: d12ea20f9f510b0e2d3d3512d8d8c71a3fb96eec
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372527"
---
# <a name="entity-types-and-their-purposes-in-luis"></a>Tipos de entidade e suas finalidades no LUIS

Entidades extrair dados da expressão. Tipos de entidade dão-lhe previsível extração de dados. Existem dois tipos de entidades: aprendeu de máquina e não aprendidas máquina. É importante saber que tipo de entidade, está trabalhando com expressões com. 

## <a name="entity-compared-to-intent"></a>Em comparação comparada a intenção de entidade

A entidade representa uma palavra ou frase dentro da expressão que pretende que sejam extraídos. Uma expressão pode incluir muitas entidades ou nenhum de todo. Uma entidade representa uma classe, incluindo uma coleção de objetos semelhantes (lugares, coisas, pessoas, eventos ou conceitos). Entidades descrevem informações relevantes para a intenção e, às vezes, são essenciais para a sua aplicação realizar suas tarefas. Por exemplo, uma aplicação de pesquisa de notícias pode incluir entidades como "tópico", "origem", "palavra-chave" e "data de publicação", que são dados de chave para pesquisar notícias. Num aplicativo de reservas de viagens, o "localização", "data", "companhia aérea", "viagens class" e "pedidos" são informações da chave de reservas de voo (relevantes para a intenção de "Reservar quarto de voo").

Por comparação, a intenção representa a predição da expressão inteira. 

## <a name="entities-help-with-data-extraction-only"></a>Entidades de grande valia apenas a extração de dados

Coloca uma etiqueta ou marca de entidades com o objetivo de it apenas, de extração de entidade não ajuda com a intenção de predição.

## <a name="entities-represent-data"></a>Entidades representam dados

As entidades são dados que pretende solicitar a partir da expressão. Isso pode ser um nome, data, nome do produto ou qualquer grupo de palavras. 

|Expressão|Entidade|Dados|
|--|--|--|
|Comprar 3 pedidos de Nova Iorque|Número pré-criados<br>Location.Destination|3<br>Nova Iorque|
|Comprar um pedido de nova York para Londres em 5 de Março|Location.Origin<br>Location.Destination<br>DatetimeV2 pré-criados|Nova Iorque<br>Londres<br>5 de março de 2018|

## <a name="entities-are-optional-but-highly-recommended"></a>As entidades são opcionais, mas altamente recomendada

Enquanto os objetivos são necessários, as entidades são opcionais. Não é necessário criar entidades para todos os conceitos na sua aplicação, mas apenas para as necessárias para a aplicação de cliente tomar medidas. 

Se as expressões não têm detalhes que seu bot tem de continuar, não é necessário para adicioná-los. Como seu aplicativo amadurece, pode adicioná-los mais tarde. 

Se não tiver a certeza de como usaria as informações, adicionar algumas entidades pré-concebidas comuns, tais como [datetimeV2](luis-reference-prebuilt-datetimev2.md), [ordinal](luis-reference-prebuilt-ordinal.md), [e-mail](luis-reference-prebuilt-email.md), e [número de telefone ](luis-reference-prebuilt-phonenumber.md).

## <a name="label-for-word-meaning"></a>Etiqueta para o significado da palavra

Se a opção do word ou disposição do word é o mesmo, mas não significa que a mesma coisa, não classifique-o com a entidade. 

As seguintes expressões, o word `fair` é um homograph. Ele é têm a mesma, mas tem um significado diferente:

|Expressão|
|--|
|Que tipo de fairs condado de estão acontecendo na área de Seattle neste Verão?|
|É a classificação atual para a revisão de Seattle justo?|

Se quisesse uma entidade de eventos para localizar todos os dados de eventos, a palavra da etiqueta `fair` a primeira expressão, mas não na segunda.

## <a name="entities-are-shared-across-intents"></a>Entidades são partilhadas entre intenções

Entidades são partilhadas entre os objetivos. Elas não pertencem a qualquer objetivo único. Objetivos e entidades podem ser associadas a semanticamente mas não é uma relação exclusiva.

Na expressão "reservar quarto-me um pedido para Paris", "Paris" é uma entidade que faça referência a localização. Ao reconhecer as entidades que são mencionadas na expressão do usuário, LUIS, ajuda a sua aplicação de cliente escolha as ações de específicas para satisfazer o pedido do utilizador.

## <a name="mark-entities-in-none-intent"></a>Marcar entidades na intenção None

Todos os objetivos, incluindo o **None** intenção, deve ter marcado entidades, sempre que possível. Isto ajuda a saber mais sobre em que as entidades são nas expressões e quais são palavras em torno das entidades de LUIS. 

## <a name="entity-status-for-predictions"></a>Estado de entidade para predições

O portal de LUIS indica quando a entidade numa expressão de exemplo é diferente da entidade marcada ou é demasiado feche a outra entidade e, portanto, claro. Isso é indicado por um sublinhado em vermelho na expressão de exemplo. 

Para obter mais informações, consulte [previsões de estado de entidade](luis-how-to-add-example-utterances.md#entity-status-predictions). 

## <a name="types-of-entities"></a>Tipos de entidades

LUIS oferece muitos tipos de entidades. Escolha a entidade com base na forma como os dados devem ser extraídos e como ele deve ser representado depois é extraído.

Entidades podem ser extraídas com machine-learning, que permite o LUIS saber mais sobre a forma como a entidade é apresentado na expressão. Entidades podem ser extraídas sem machine-learning, o texto exato ou uma expressão regular de correspondência. Entidades em padrões podem ser extraídas com uma implementação mista. 

Assim que a entidade é extraída, os dados da entidade podem ser representados como uma única unidade de informações ou combinados com outras entidades para formar uma unidade de informações que pode utilizar a aplicação de cliente.

|Ficou a saber de máquina|Pode marcar|Tutorial|Exemplo<br>Resposta|Tipo de entidade|Objetivo|
|--|--|--|--|--|--|
|✔|✔|[✔](luis-tutorial-composite-entity.md)|[✔](luis-concept-data-extraction.md#composite-entity-data)|[**Composição**](#composite-entity)|Agrupamento de entidades, independentemente do tipo de entidade.|
|✔|✔|[✔](luis-quickstart-intent-and-hier-entity.md)|[✔](luis-concept-data-extraction.md#hierarchical-entity-data)|[**Hierárquica**](#hierarchical-entity)|Agrupamento de entidades simples.|
|||[✔](luis-quickstart-intent-and-list-entity.md)|[✔](luis-concept-data-extraction.md#list-entity-data)|[**List**](#list-entity)|Lista de itens e seus sinónimos extraídos com correspondência exata de texto.|
|Mista||[✔](luis-tutorial-pattern.md)|[✔](luis-concept-data-extraction.md#patternany-entity-data)|[**Pattern.any**](#patternany-entity)|Entidade em que é difícil determinar o fim da entidade.|
|||[✔](luis-tutorial-prebuilt-intents-entities.md)|[✔](luis-concept-data-extraction.md#prebuilt-entity-data)|[**Prebuilt**](#prebuilt-entity)|Já preparado extrair os vários tipos de dados.|
|||[✔](luis-quickstart-intents-regex-entity.md)|[✔](luis-concept-data-extraction.md#regular-expression-entity-data)|[**Expressão regular**](#regular-expression-entity)|Usa a expressão regular para corresponder ao texto.|
|✔|✔|[✔](luis-quickstart-primary-and-secondary-data.md)|[✔](luis-concept-data-extraction.md#simple-entity-data)|[**Simple**](#simple-entity)|Contém um único conceito na palavra ou frase.|

Apenas as entidades aprendidas por máquina tem de ser marcado como nas expressões de exemplo para cada intenção. Aprendeu máquina entidades funcionam melhor quando testado por meio [consultas de ponto final](luis-concept-test.md#endpoint-testing) e [expressões de ponto final de revisão](luis-how-to-review-endoint-utt.md). 

Pattern.any entidades precisam ser marcadas no [padrão](luis-how-to-model-intent-pattern.md) exemplos de modelo, não os exemplos da intenção do utilizador. 

Entidades mistas usar uma combinação de métodos de deteção de entidade.

## <a name="composite-entity"></a>entidade composta

Uma entidade composta é constituída por outras entidades, como entidades previamente concebidas, simples, expressão regular, lista e entidades hierárquicas. As entidades separadas formam uma entidade inteira. 

Esta entidade é uma boa ajustar quando os dados:

* Estão relacionados entre si. 
* Estão relacionados entre si no contexto da expressão.
* Utilize uma variedade de tipos de entidade.
* Tem de ser agrupados e processados pela aplicação de cliente como uma unidade de informações.
* Ter uma variedade de expressões de utilizador que necessitam de machine learning.

![Entidade composta](./media/luis-concept-entities/composite-entity.png)

[Tutorial](luis-tutorial-composite-entity.md)<br>
[Resposta JSON de exemplo para a entidade](luis-concept-data-extraction.md#composite-entity-data)<br>

## <a name="hierarchical-entity"></a>Entidade hierárquica

Uma entidade hierárquica é uma categoria de contextualmente aprendidas entidades simples chamada children.

Esta entidade é uma boa ajustar quando os dados:

* São entidades simples.
* Estão relacionados entre si no contexto da expressão.
* Utilize a opção de palavra específica para indicar cada entidade subordinada. Exemplos destas palavras: de/para, deixar/em direção a, sair de/para.
* Crianças com frequência estão a ser a mesma expressão. 
* Têm de ser agrupadas e processadas pela aplicação cliente como uma unidade de informações.

Não utilize se:

* Precisa de uma entidade que tem as correspondências de texto exato para crianças, independentemente do contexto. Utilize um [listar entidade](#list-entity) em vez disso. 
* Precisa de uma entidade para uma relação principal-subordinado com outros tipos de entidade. Utilize o [entidade composta](#composite-entity).

![entidades hierárquicas](./media/luis-concept-entities/hierarchical-entity.png)

[Tutorial](luis-quickstart-intent-and-hier-entity.md)<br>
[Resposta JSON de exemplo para a entidade](luis-concept-data-extraction.md#hierarchical-entity-data)<br>

### <a name="roles-versus-hierarchical-entities"></a>Funções versus entidades hierárquicas

[Funções](luis-concept-roles.md#roles-versus-hierarchical-entities) de um padrão de resolver o mesmo problema como entidades hierárquicas, mas aplicam a todos os tipos de entidade. Funções só estão atualmente disponíveis em padrões. Funções não estão disponíveis em expressões de exemplo dos objetivos.  

## <a name="list-entity"></a>Entidade de lista

Lista de entidades representam um conjunto de palavras relacionadas, juntamente com os seus sinónimos fixo e fechado. LUIS não Deteta valores adicionais para entidades de lista. Utilize o **Recomendamos** funcionalidade para ver sugestões para novas palavras com base na lista atual. Se houver mais de uma entidade de lista com o mesmo valor, cada entidade é devolvida na consulta de ponto final. 

A entidade é uma boa ajustar quando os dados de texto:

* São um conjunto conhecido.
* O conjunto não excede os [limites](luis-boundaries.md) máximos do LUIS para este tipo de entidade.
* O texto na expressão é uma correspondência exata com um sinónimo ou o nome canónico. LUIS não usa a lista para além de correspondências de texto exato. Lematização plurais e outras variações não estão resolvidas com uma entidade de lista. Para gerir variações, considere a utilização de um [padrão](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance) com a sintaxe de texto opcional.

![entidade de lista](./media/luis-concept-entities/list-entity.png)

[Tutorial](luis-quickstart-intent-and-list-entity.md)<br>
[Resposta JSON de exemplo para a entidade](luis-concept-data-extraction.md#list-entity-data)

## <a name="patternany-entity"></a>Entidade de Pattern.any

Pattern.any é um marcador de posição de comprimento variável utilizado apenas na expressão de modelo de um padrão para marcar onde a entidade começa e termina.  

A entidade é uma boa ajustar quando:

* O fim da entidade pode ser confundido com o texto restante da expressão. 
[Tutorial](luis-tutorial-pattern.md)<br>
[Resposta JSON de exemplo para a entidade](luis-concept-data-extraction.md#patternany-entity-data)

**Exemplo**  
Devido uma aplicação de cliente que procura para livros com base no título, a pattern.any extrai o título completo. Uma expressão de modelo pattern.any a utilizar para esta pesquisa de livro é `Was {BookTitle} written by an American this year[?]`. 

Na tabela a seguir, cada linha tem duas versões da expressão. A expressão superior é como LUIS inicialmente a ver a expressão, em que não está claro com o título do livro começa e termina. A expressão na parte inferior é como LUIS saberão o título do livro que um padrão esteja in-loco para extração. 

|Expressão|
|--|
|O homem quem identificou seu esposa um Hat e outras histórias Clinical foi escrita por um American deste ano?<br>Foi **o homem que identificou seu esposa um Hat e outras histórias Clinical** escrito por um American deste ano?|
|Foi metade em modo de suspensão de Pijama Frog escrito por um American deste ano?<br>Foi **metade em modo de suspensão de Pijama Frog** escrito por um American deste ano?|
|Foi a tristeza específica de bolo de Lemon: Uma nova escrita por um American deste ano?<br>Foi **a tristeza específica de bolo de Lemon: Uma nova** escrito por um American deste ano?|
|Foi que há um Wocket no meu bolso! escrito por um American deste ano?<br>Foi **há um Wocket no meu bolso!** escrito por um American deste ano?|

## <a name="prebuilt-entity"></a>Entidade pré-criados

Entidades pré-concebidas são tipos internos que representam conceitos comuns, como e-mail, o URL e o número de telefone. Os nomes de entidades pré-criados estão reservados. [Todas as entidades pré-concebidas](luis-prebuilt-entities.md) que são adicionados à aplicação são devolvidos na consulta de predição de ponto final, se forem encontradas na expressão. 

A entidade é uma boa ajustar quando:

* Os dados corresponde a um caso de utilização comuns suportado pelo entidades previamente concebidas para a cultura do idioma. 

Entidades pré-concebidas podem ser adicionadas e removidas em qualquer altura.

![Entidade pré-criados numérica](./media/luis-concept-entities/number-entity.png)

[Tutorial](luis-tutorial-prebuilt-intents-entities.md)<br>
[Resposta JSON de exemplo para a entidade](luis-concept-data-extraction.md#prebuilt-entity-data)

Algumas destas entidades criados previamente definidas no código aberto [reconhecedores texto](https://github.com/Microsoft/Recognizers-Text) projeto. Se sua cultura específica ou a entidade não é atualmente suportada, contribuem para o projeto. 

### <a name="troubleshooting-prebuilt-entities"></a>Resolução de problemas de entidades pré-concebidas

No portal do LUIS, se uma entidade pré-criados é marcada em vez de sua entidade personalizada, tem algumas opções de como corrigir este problema.

As entidades criados previamente adicionadas à aplicação serão _sempre_ retornado, mesmo que a expressão deve extrair entidades personalizadas para o mesmo texto. 

#### <a name="change-tagged-entity-in-example-utterance"></a>Alterar a entidade marcada na expressão de exemplo

Se a entidade pré-criados é o mesmo texto ou tokens como a entidade personalizada, selecione o texto na expressão de exemplo e alterar a expressão marcada. 

Se a entidade pré-criados é marcada com mais de texto ou tokens que a entidade personalizada, tem algumas opções de como corrigir este problema:

* [Remover a expressão de exemplo](#remove-example-utterance-to-fix-tagging) método
* [Remover entidade pré-criados](#remove-prebuilt-entity-to-fix-tagging) método

#### <a name="remove-example-utterance-to-fix-tagging"></a>Remover a expressão de exemplo para corrigir a etiquetagem 

Sua primeira opção consiste em eliminar a expressão de exemplo e voltar a preparar a aplicação. Adicionar novamente apenas a palavra ou frase, ou seja, a entidade como uma expressão de exemplo e, em seguida, marcar a entidade e train. Agora, adicione novamente a entidade criados previamente e a expressão de exemplo original. A entidade personalizada deve continuar a ser marcado em vez da entidade pré-criados. 

#### <a name="remove-prebuilt-entity-to-fix-tagging"></a>Remover entidade criados previamente para corrigir a etiquetagem

A segunda opção consiste em remover a entidade pré-concebidos a partir da aplicação, em seguida, marcar a entidade personalizada na expressão de exemplo e depois adicione a entidade pré-criados novamente para a aplicação. Esta correção pressupõe que a entidade pré-criados não faz parte de uma entidade composta. 

## <a name="regular-expression-entity"></a>Entidade de expressão regular 

Uma expressão regular é melhor para texto de expressão não processados. Ele ignora caso e ignora a variante cultural.  Correspondência de expressões regulares é aplicada após a verificação ortográfica alterações ao nível do caráter, não no nível de token. Se a expressão regular é demasiado complexa, como o uso de muitos Retos, não é possível adicionar a expressão para o modelo. Utiliza a parte, mas nem todos os [Regex de .NET](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions) biblioteca. 

A entidade é uma boa ajustar quando:

* Os dados são formatados consistentemente com qualquer variação que também é consistente.
* A expressão regular não precisa de mais de 2 níveis de aninhamento. 

![Entidade de expressão regular](./media/luis-concept-entities/regex-entity.png)

[Tutorial](luis-quickstart-intents-regex-entity.md)<br>
[Resposta JSON de exemplo para a entidade](luis-concept-data-extraction.md#regular-expression-entity-data)<br>

## <a name="simple-entity"></a>Entidade simples 

Uma entidade é uma entidade genérica que descreve um único conceito e será adquirida do contexto aprendidas por máquina. Uma vez que as entidades simples geralmente são nomes, como nomes de empresa, nomes de produtos ou outras categorias de nomes, adicione uma [lista de frase](luis-concept-feature.md) ao utilizar uma entidade para impulsionar o sinal dos nomes utilizados. 

A entidade é uma boa ajustar quando:

* Os dados não estão formatados de forma consistente, mas indicam a mesma coisa. 

![entidade Simple](./media/luis-concept-entities/simple-entity.png)

[Tutorial](luis-quickstart-primary-and-secondary-data.md)<br/>
[Resposta de exemplo para a entidade](luis-concept-data-extraction.md#simple-entity-data)<br/>

## <a name="entity-limits"></a>Limites de entidade

Revisão [limites](luis-boundaries.md#model-boundaries) para compreender quantos de cada tipo de entidade é possível adicionar a um modelo.

## <a name="composite-vs-hierarchical-entities"></a>Entidades hierárquica do vs compostos

Entidades compostas e entidades hierárquicas tanto têm relações pai-filho e são máquina aprendida. O machine learning permite que o LUIS compreender as entidades com base em diferentes contextos (disposição de palavras). Entidades compostas são mais flexíveis porque permitem que os tipos de entidade diferentes como filhos. Subordinados de uma entidade hierárquica são apenas as entidades simples. 

|Tipo|Objetivo|Exemplo|
|--|--|--|
|Hierárquica|Principal-subordinado de entidades simples|Location.Origin=New York<br>Location.Destination=London|
|Composição|Entidades de principal-subordinado: pré-criados, lista, simples, hierárquica| número = 3<br>lista = de primeira classe<br>prebuilt.datetimeV2=March 5|

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Se precisar de mais do que o número máximo de entidades 

Poderá ter de utilizar entidades hierárquicas e compostas. Entidades hierárquicas refletem a relação entre entidades que partilham características ou que são membros de uma categoria. As entidades subordinadas são todos os membros da categoria de seu pai. Por exemplo, uma entidade hierárquica PlaneTicketClass com o nome pode ter as entidades subordinadas EconomyClass e FirstClass. A hierarquia abrange apenas um nível de profundidade.  

Entidades compostas representam partes de um todo. Por exemplo, uma entidade composta com o nome PlaneTicketOrder pode ter entidades subordinadas companhia aérea, destino, DepartureCity, DepartureDate e PlaneTicketClass. Criar uma entidade composta de entidades simples já existentes, filhos de entidades hierárquicas ou entidades pré-concebidas.  

LUIS também fornece o tipo de entidade de lista que não é aprendidas por máquina, mas permite que a sua aplicação LUIS especificar uma lista fixa de valores. Ver [limites de LUIS](luis-boundaries.md) referência para rever os limites do tipo de entidade de lista. 

Se já considerada hierárquico e compostos e lista entidades e ainda precisa de mais do que o limite, contacte o suporte. Para fazê-lo, coletar informações detalhadas sobre o seu sistema, vá para o [LUIS](luis-reference-regions.md#luis-website) Web site e, em seguida, selecione **suporte**. Se a sua subscrição do Azure inclui os serviços de suporte, contacte [suporte técnico do Azure](https://azure.microsoft.com/support/options/). 

## <a name="next-steps"></a>Passos Seguintes

Aprenda conceitos sobre o good [expressões com](luis-concept-utterance.md). 

Ver [adicionar entidades](luis-how-to-add-entities.md) para saber mais sobre como adicionar entidades à sua aplicação LUIS.
