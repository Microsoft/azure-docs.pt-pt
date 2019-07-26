---
title: Tipos de entidade
titleSuffix: Language Understanding - Azure Cognitive Services
description: 'As entidades extraem dados do expressão. Os tipos de entidade oferecem extração previsível de dados. Há dois tipos de entidades: aprendidas por máquina e não aprendidas por computador. É importante saber para qual tipo de entidade você está trabalhando no declarações.'
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: a5a3ba8c25107317e7c47ee358f9a6ebe7d4556f
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479120"
---
# <a name="entity-types-and-their-purposes-in-luis"></a>Tipos de entidade e suas finalidades no LUIS

As entidades extraem dados do expressão. Os tipos de entidade oferecem extração previsível de dados. Há dois tipos de entidades: aprendidas por máquina e não aprendidas por computador. É importante saber para qual tipo de entidade você está trabalhando no declarações. 

## <a name="entity-compared-to-intent"></a>Em comparação comparada a intenção de entidade

A entidade representa uma palavra ou frase dentro da expressão que pretende que sejam extraídos. Uma expressão pode incluir muitas entidades ou nenhum de todo. Um aplicativo cliente pode precisar que a entidade execute sua tarefa ou use-a como um guia de várias opções para apresentar ao usuário. 

Uma entidade:

* Representa uma classe que inclui uma coleção de objetos semelhantes (locais, coisas, pessoas, eventos ou conceitos). 
* Descreve as informações relevantes para a intenção


Por exemplo, uma aplicação de pesquisa de notícias pode incluir entidades como "tópico", "origem", "palavra-chave" e "data de publicação", que são dados de chave para pesquisar notícias. Em um aplicativo de reservas de viagem, o "local", "data", "companhia aérea", "classe de viagem" e "tíquetes" são informações importantes para a reserva de voo (relevante para a intenção de "vôo de livro").

Por comparação, a intenção representa a predição da expressão inteira. 

## <a name="entities-help-with-data-extraction-only"></a>As entidades ajudam apenas com extração de dados

Você rotula ou marca entidades para fins de extração de entidade somente, não ajuda com previsão de intenção.

## <a name="entities-represent-data"></a>Entidades representam dados

As entidades são dados que pretende solicitar a partir da expressão. Isso pode ser um nome, data, nome do produto ou qualquer grupo de palavras. 

|Expressão|Entidade|Dados|
|--|--|--|
|Comprar 3 pedidos de Nova Iorque|Número pré-criados<br>Location.Destination|3<br>Nova Iorque|
|Comprar um pedido de nova York para Londres em 5 de Março|Location.Origin<br>Location.Destination<br>DatetimeV2 pré-criados|Nova Iorque<br>Londres<br>5 de março de 2018|

## <a name="entities-are-optional-but-highly-recommended"></a>As entidades são opcionais, mas altamente recomendada

Enquanto os objetivos são necessários, as entidades são opcionais. Você não precisa criar entidades para todos os conceitos em seu aplicativo, mas apenas para aqueles necessários para que o aplicativo cliente execute uma ação. 

Se as expressões não têm detalhes que seu bot tem de continuar, não é necessário para adicioná-los. Como seu aplicativo amadurece, pode adicioná-los mais tarde. 

Se você não tiver certeza de como usaria as informações, adicione algumas entidades predefinidas comuns, como [datetimeV2](luis-reference-prebuilt-datetimev2.md), [ordinal](luis-reference-prebuilt-ordinal.md), [email](luis-reference-prebuilt-email.md)e número de [telefone](luis-reference-prebuilt-phonenumber.md).

## <a name="label-for-word-meaning"></a>Etiqueta para o significado da palavra

Se a opção do word ou disposição do word é o mesmo, mas não significa que a mesma coisa, não classifique-o com a entidade. 

As seguintes expressões, o word `fair` é um homograph. Ele é têm a mesma, mas tem um significado diferente:

|Expressão|
|--|
|Que tipo de fairs condado de estão acontecendo na área de Seattle neste Verão?|
|É a classificação atual para a revisão de Seattle justo?|

Se quisesse uma entidade de eventos para localizar todos os dados de eventos, a palavra da etiqueta `fair` a primeira expressão, mas não na segunda.

## <a name="entities-are-shared-across-intents"></a>Entidades são partilhadas entre intenções

Entidades são partilhadas entre os objetivos. Elas não pertencem a qualquer objetivo único. As intenções e as entidades podem ser associadas semanticamente, mas não são uma relação exclusiva.

No expressão "reservar um tíquete para Paris", "Paris" é uma entidade que se refere ao local. Ao reconhecer as entidades mencionadas no expressão do usuário, o LUIS ajuda o aplicativo cliente a escolher as ações específicas a serem executadas para atender à solicitação do usuário.

## <a name="mark-entities-in-none-intent"></a>Marcar entidades em nenhuma intenção

Todas as intenções, incluindo a intenção **None** , devem ter entidades marcadas, quando possível. Isto ajuda a saber mais sobre em que as entidades são nas expressões e quais são palavras em torno das entidades de LUIS. 

## <a name="entity-status-for-predictions"></a>Estado de entidade para predições

O portal do LUIS informa quando a entidade em um exemplo expressão é diferente da entidade marcada ou está muito perto de outra entidade e, portanto, não clara. Isso é indicado por um sublinhado vermelho no exemplo expressão. 

Para obter mais informações, consulte [previsões de status de entidade](luis-how-to-add-example-utterances.md#entity-status-predictions). 

## <a name="types-of-entities"></a>Tipos de entidades

O LUIS oferece muitos tipos de entidades. Escolha a entidade com base em como os dados devem ser extraídos e como eles devem ser representados após serem extraídos.

As entidades podem ser extraídas com o aprendizado de máquina, o que permite que o LUIS Continue aprendendo sobre como a entidade aparece no expressão. As entidades podem ser extraídas sem aprendizado de máquina, correspondendo a um texto exato ou a uma expressão regular. As entidades em padrões podem ser extraídas com uma implementação mista. 

Depois que a entidade é extraída, os dados da entidade podem ser representados como uma única unidade de informações ou combinados com outras entidades para formar uma unidade de informações que o aplicativo cliente pode usar.

|Aprendido com o computador|Pode marcar|Tutorial|Exemplo<br>Resposta|Tipo de entidade|Objetivo|
|--|--|--|--|--|--|
|✔|✔|[✔](luis-tutorial-composite-entity.md)|[✔](luis-concept-data-extraction.md#composite-entity-data)|[**Spot**](#composite-entity)|Agrupamento de entidades, independentemente do tipo de entidade.|
|||[✔](luis-quickstart-intent-and-list-entity.md)|[✔](luis-concept-data-extraction.md#list-entity-data)|[**Lista**](#list-entity)|Lista de itens e seus sinônimos extraídos com correspondência exata de texto.|
|Mista||[✔](luis-tutorial-pattern.md)|[✔](luis-concept-data-extraction.md#patternany-entity-data)|[**Padrão. any**](#patternany-entity)|Entidade na qual o fim da entidade é difícil de determinar.|
|||[✔](luis-tutorial-prebuilt-intents-entities.md)|[✔](luis-concept-data-extraction.md#prebuilt-entity-data)|[**Prebuilt**](#prebuilt-entity)|Já treinado para extrair vários tipos de dados.|
|||[✔](luis-quickstart-intents-regex-entity.md)|[✔](luis-concept-data-extraction.md#regular-expression-entity-data)|[**Expressão regular**](#regular-expression-entity)|Usa a expressão regular para corresponder o texto.|
|✔|✔|[✔](luis-quickstart-primary-and-secondary-data.md)|[✔](luis-concept-data-extraction.md#simple-entity-data)|[**Único**](#simple-entity)|Contém um único conceito em palavra ou frase.|

Somente entidades aprendidas por máquina precisam ser marcadas no exemplo declarações. As entidades aprendidas por computador funcionam melhor quando testadas por meio de [consultas de ponto de extremidade](luis-concept-test.md#endpoint-testing) e revisão do ponto de [extremidade declarações](luis-how-to-review-endoint-utt.md) 

Padrão. todas as entidades precisam ser marcadas nos exemplos de modelo de [padrão](luis-how-to-model-intent-pattern.md) , não nos exemplos de usuário de intenção. 

Entidades mistas usam uma combinação de métodos de detecção de entidade.

## <a name="machine-learned-entities-use-context"></a>Contexto de uso de entidades aprendidas por computador

As entidades aprendidas por computador aprendem com o contexto no expressão. Isso torna a variação do posicionamento no exemplo declarações significativa. 

## <a name="non-machine-learned-entities-dont-use-context"></a>Entidades não aprendidas por computador não usam contexto

As seguintes entidades não conhecidas por máquina não levam o contexto de expressão em conta ao corresponder entidades: 

* [Entidades pré-concebidas](#prebuilt-entity)
* [Entidades Regex](#regular-expression-entity)
* [Lista de entidades](#list-entity) 

Essas entidades não exigem rotulagem ou treinamento do modelo. Depois de adicionar ou configurar a entidade, as entidades são extraídas. A desvantagem é que essas entidades podem ser sobrecombinadas, em que, se o contexto tiver sido levado em conta, a correspondência não teria sido feita. 

Isso acontece com as entidades de lista em novos modelos com frequência. Você cria e testa seu modelo com uma entidade de lista, mas quando publica seu modelo e recebe consultas do ponto de extremidade, você percebe que seu modelo está se sobrecorrespondência devido à falta de contexto. 

Se você quiser corresponder palavras ou frases e assumir o contexto, terá duas opções. A primeira é usar uma entidade simples emparelhada com uma lista de frases. A lista de frases não será usada para correspondência, mas, em vez disso, ajudará a sinalizar palavras relativamente semelhantes (lista intercambiável). Se você precisar ter uma correspondência exata em vez das variações de uma lista de frases, use uma entidade de lista com uma função, descrita abaixo.

### <a name="context-with-non-machine-learned-entities"></a>Contexto com entidades não aprendidas por computador

Se você quiser o contexto do expressão para entidades aprendidas que não sejam de máquina, use [funções](luis-concept-roles.md).

Se você tiver uma entidade não aprendida por máquina, como [entidades](#prebuilt-entity)predefinidas, entidades [Regex](#regular-expression-entity) ou entidades de [lista](#list-entity) , que corresponde além da instância desejada, considere a criação de uma entidade com duas funções. Uma função capturará o que você está procurando e uma função irá capturar o que você não está procurando. Ambas as versões precisarão ser rotuladas no exemplo declarações.  

## <a name="composite-entity"></a>entidade composta

Uma [entidade composta](reference-entity-composite.md) é composta por outras entidades, como entidades predefinidas, expressão simples, regular e entidades de lista. As entidades separadas formam uma entidade inteira. 

## <a name="list-entity"></a>Entidade de lista

As [entidades de lista](reference-entity-list.md) representam um conjunto fixo e fechado de palavras relacionadas junto com seus sinônimos. LUIS não Deteta valores adicionais para entidades de lista. Utilize o **Recomendamos** funcionalidade para ver sugestões para novas palavras com base na lista atual. Se houver mais de uma entidade de lista com o mesmo valor, cada entidade é devolvida na consulta de ponto final. 

## <a name="patternany-entity"></a>Entidade de Pattern.any

[Padrão. any](reference-entity-pattern-any.md) é um espaço reservado de comprimento variável usado somente no modelo de um padrão expressão para marcar onde a entidade começa e termina.  

## <a name="prebuilt-entity"></a>Entidade pré-criados

As entidades predefinidas são tipos internos que representam conceitos comuns, como email, URL e número de telefone. Os nomes de entidades pré-criados estão reservados. [Todas as entidades](luis-prebuilt-entities.md) predefinidas que são adicionadas ao aplicativo são retornadas na consulta de previsão de ponto de extremidade se elas forem encontradas no expressão. 

A entidade é uma boa opção quando:

* Os dados correspondem a um caso de uso comum com suporte de entidades predefinidas para sua cultura de idioma. 

As entidades predefinidas podem ser adicionadas e removidas a qualquer momento.

![Entidade pré-criados numérica](./media/luis-concept-entities/number-entity.png)

[Tutorial](luis-tutorial-prebuilt-intents-entities.md)<br>
[Exemplo de resposta JSON para a entidade](luis-concept-data-extraction.md#prebuilt-entity-data)

Algumas dessas entidades predefinidas são definidas no projeto de [texto](https://github.com/Microsoft/Recognizers-Text) de reconhecedores de código aberto. Se sua cultura específica ou a entidade não é atualmente suportada, contribuem para o projeto. 

### <a name="troubleshooting-prebuilt-entities"></a>Solucionando problemas de entidades predefinidas

No portal do LUIS, se uma entidade predefinida estiver marcada em vez de sua entidade personalizada, você terá algumas opções de como corrigir isso.

As entidades predefinidas adicionadas ao aplicativo serão _sempre_ retornadas, mesmo que o expressão deva extrair entidades personalizadas para o mesmo texto. 

#### <a name="change-tagged-entity-in-example-utterance"></a>Alterar a entidade marcada no exemplo expressão

Se a entidade predefinida for o mesmo texto ou tokens que a entidade personalizada, selecione o texto no exemplo expressão e altere o expressão marcado. 

Se a entidade predefinida estiver marcada com mais texto ou tokens do que sua entidade personalizada, você terá algumas opções de como corrigir isso:

* [Remover exemplo](#remove-example-utterance-to-fix-tagging) de método expressão
* [Remover](#remove-prebuilt-entity-to-fix-tagging) método de entidade predefinida

#### <a name="remove-example-utterance-to-fix-tagging"></a>Remover o exemplo expressão para corrigir a marcação 

Sua primeira opção é remover o exemplo expressão. 

1. Exclua o exemplo expressão.
1. Readaptação do aplicativo. 
1. Adicione de volta apenas a palavra ou frase que é a entidade, que é marcada como uma entidade predefinida, como um exemplo completo de expressão. A palavra ou frase ainda terá a entidade predefinida marcada. 
1. Selecione a entidade no exemplo expressão na página **intenção** e altere para sua entidade personalizada e treine novamente. Isso deve impedir LUIS de marcar esse texto exato como a entidade predefinida em qualquer declarações de exemplo que use esse texto. 
1. Adicione o exemplo original inteiro expressão de volta à intenção. A entidade personalizada deve continuar sendo marcada em vez da entidade predefinida. Se a entidade personalizada não estiver marcada, você precisará adicionar mais exemplos desse texto em declarações.

#### <a name="remove-prebuilt-entity-to-fix-tagging"></a>Remover a entidade predefinida para corrigir a marcação

1. Remova a entidade predefinida do aplicativo. 
1. Na página **intenção** , marque a entidade personalizada no exemplo expressão.
1. Prepare a aplicação.
1. Adicione a entidade predefinida de volta ao aplicativo e treine o aplicativo. Essa correção pressupõe que a entidade predefinida não faz parte de uma entidade composta.

## <a name="regular-expression-entity"></a>Entidade de expressão regular 

Uma [entidade de expressão regular](reference-entity-regular-expression.md) extrai uma entidade com base em um padrão de expressão regular fornecido por você.

## <a name="simple-entity"></a>Entidade simples

R [entidade simple](reference-entity-simple.md) é um valor aprendidas por máquina. Pode ser uma palavra ou frase.
## <a name="entity-limits"></a>Limites de entidade

Revisão [limites](luis-boundaries.md#model-boundaries) para compreender quantos de cada tipo de entidade é possível adicionar a um modelo.

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Se precisar de mais do que o número máximo de entidades 

Talvez seja necessário usar entidades compostas em combinação com funções de entidade.

Entidades compostas representam partes de um todo. Por exemplo, uma entidade composta com o nome PlaneTicketOrder pode ter entidades subordinadas companhia aérea, destino, DepartureCity, DepartureDate e PlaneTicketClass.

O LUIS também fornece o tipo de entidade de lista que não é aprendido pelo computador, mas permite que seu aplicativo LUIS especifique uma lista fixa de valores. Ver [limites de LUIS](luis-boundaries.md) referência para rever os limites do tipo de entidade de lista. 

Se você considerou essas entidades e ainda precisa de mais do que o limite, entre em contato com o suporte. Para fazê-lo, coletar informações detalhadas sobre o seu sistema, vá para o [LUIS](luis-reference-regions.md#luis-website) Web site e, em seguida, selecione **suporte**. Se a sua subscrição do Azure inclui os serviços de suporte, contacte [suporte técnico do Azure](https://azure.microsoft.com/support/options/). 

## <a name="next-steps"></a>Passos Seguintes

Aprenda conceitos sobre o good [expressões com](luis-concept-utterance.md). 

Ver [adicionar entidades](luis-how-to-add-entities.md) para saber mais sobre como adicionar entidades à sua aplicação LUIS.
