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
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 7fd9ae3ab1f50dc91118ba11bc357a0f6dc0e771
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141048"
---
# <a name="entity-types-and-their-purposes-in-luis"></a>Tipos de entidade e suas finalidades no LUIS

Entidades extrair dados da expressão. Tipos de entidade dão-lhe previsível extração de dados. Existem dois tipos de entidades: aprendeu de máquina e não aprendidas máquina. É importante saber que tipo de entidade, está trabalhando com expressões com. 

## <a name="entity-compared-to-intent"></a>Em comparação comparada a intenção de entidade

A entidade representa uma palavra ou frase dentro da expressão que pretende que sejam extraídos. Uma expressão pode incluir muitas entidades ou nenhum de todo. Uma aplicação cliente poderá ter a entidade para executar a sua tarefa ou utilizá-lo como um guia de várias opções para apresentar ao usuário. 

Uma entidade:

* Representa uma classe, incluindo uma coleção de objetos semelhantes (lugares, coisas, pessoas, eventos ou conceitos). 
* Descreve as informações relevantes para a intenção


Por exemplo, uma aplicação de pesquisa de notícias pode incluir entidades como "tópico", "origem", "palavra-chave" e "data de publicação", que são dados de chave para pesquisar notícias. Num aplicativo de reservas de viagens, o "localização", "data", "companhia aérea", "viagens class" e "pedidos" são informações da chave de reservas de voo (relevantes para a intenção de "Reservar quarto de voo").

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
|||[✔](luis-quickstart-intent-and-list-entity.md)|[✔](luis-concept-data-extraction.md#list-entity-data)|[**List**](#list-entity)|Lista de itens e seus sinónimos extraídos com correspondência exata de texto.|
|Mista||[✔](luis-tutorial-pattern.md)|[✔](luis-concept-data-extraction.md#patternany-entity-data)|[**Pattern.any**](#patternany-entity)|Entidade em que é difícil determinar o fim da entidade.|
|||[✔](luis-tutorial-prebuilt-intents-entities.md)|[✔](luis-concept-data-extraction.md#prebuilt-entity-data)|[**Prebuilt**](#prebuilt-entity)|Já preparado extrair os vários tipos de dados.|
|||[✔](luis-quickstart-intents-regex-entity.md)|[✔](luis-concept-data-extraction.md#regular-expression-entity-data)|[**Expressão regular**](#regular-expression-entity)|Usa a expressão regular para corresponder ao texto.|
|✔|✔|[✔](luis-quickstart-primary-and-secondary-data.md)|[✔](luis-concept-data-extraction.md#simple-entity-data)|[**Simple**](#simple-entity)|Contém um único conceito na palavra ou frase.|

Apenas as entidades aprendidas por máquina tem de ser marcado como nas expressões de exemplo. Aprendeu máquina entidades funcionam melhor quando testado por meio [consultas de ponto final](luis-concept-test.md#endpoint-testing) e [expressões de ponto final de revisão](luis-how-to-review-endoint-utt.md). 

Pattern.any entidades precisam ser marcadas no [padrão](luis-how-to-model-intent-pattern.md) exemplos de modelo, não os exemplos da intenção do utilizador. 

Entidades mistas usar uma combinação de métodos de deteção de entidade.

## <a name="composite-entity"></a>entidade composta

Uma entidade composta é constituída por outras entidades, como entidades previamente concebidas, simples, expressão regular e entidades de lista. As entidades separadas formam uma entidade inteira. 

Esta entidade é uma boa ajustar quando os dados:

* Estão relacionados entre si. 
* Estão relacionados entre si no contexto da expressão.
* Utilize uma variedade de tipos de entidade.
* Tem de ser agrupados e processados pela aplicação de cliente como uma unidade de informações.
* Ter uma variedade de expressões de utilizador que necessitam de machine learning.

![Entidade composta](./media/luis-concept-entities/composite-entity.png)

[Tutorial](luis-tutorial-composite-entity.md)<br>
[Resposta JSON de exemplo para a entidade](luis-concept-data-extraction.md#composite-entity-data)<br>

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
|"Foi o homem que identificou seu esposa um Hat e outras histórias clínica, escrito por um American deste ano?<br>Foi **o homem que identificou seu esposa um Hat e outras histórias Clinical** escrito por um American deste ano?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br>`Was **Half Asleep in Frog Pajamas** written by an American this year?`|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br>`Was **The Particular Sadness of Lemon Cake: A Novel** written by an American this year?`|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br>`Was **There's A Wocket In My Pocket!** written by an American this year?`|

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

Sua primeira opção é remover a expressão de exemplo. 

1. Elimine a expressão de exemplo.
1. Voltar a preparar a aplicação. 
1. Adicionar novamente apenas a palavra ou frase, ou seja, a entidade, que está marcada como uma entidade pré-criados, como uma expressão de exemplo completo. A palavra ou frase continuam a ter a entidade pré-criados marcada. 
1. Selecione a entidade na expressão de exemplo sobre o **intenção** página e alterar a sua entidade personalizada e prepare-se novamente. Isso deveria impedir o LUIS marque este texto exatamente como a entidade pré-criados em quaisquer expressões de exemplo que utilizam esse texto. 
1. Adicione a expressão de exemplo original toda a intenção. A entidade personalizada deve continuar a ser marcado em vez da entidade pré-criados. Se a entidade personalizada não está marcada, terá de adicionar mais exemplos desse texto em expressões.

#### <a name="remove-prebuilt-entity-to-fix-tagging"></a>Remover entidade criados previamente para corrigir a etiquetagem

1. Remova a entidade pré-concebidos a partir da aplicação. 
1. Sobre o **intenção** página, marcar a entidade personalizada na expressão de exemplo.
1. Prepare a aplicação.
1. Adicionar a entidade pré-criados ao aplicativo e preparar a aplicação. Esta correção pressupõe que a entidade pré-criados não faz parte de uma entidade composta.

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

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Se precisar de mais do que o número máximo de entidades 

Poderá ter de utilizar entidades compostas em combinação com as funções de entidade.

Entidades compostas representam partes de um todo. Por exemplo, uma entidade composta com o nome PlaneTicketOrder pode ter entidades subordinadas companhia aérea, destino, DepartureCity, DepartureDate e PlaneTicketClass.

LUIS também fornece o tipo de entidade de lista que não é aprendidas por máquina, mas permite que a sua aplicação LUIS especificar uma lista fixa de valores. Ver [limites de LUIS](luis-boundaries.md) referência para rever os limites do tipo de entidade de lista. 

Se tive considerado estas entidades e ainda precisa de mais do que o limite, contacte o suporte. Para fazê-lo, coletar informações detalhadas sobre o seu sistema, vá para o [LUIS](luis-reference-regions.md#luis-website) Web site e, em seguida, selecione **suporte**. Se a sua subscrição do Azure inclui os serviços de suporte, contacte [suporte técnico do Azure](https://azure.microsoft.com/support/options/). 

## <a name="next-steps"></a>Passos Seguintes

Aprenda conceitos sobre o good [expressões com](luis-concept-utterance.md). 

Ver [adicionar entidades](luis-how-to-add-entities.md) para saber mais sobre como adicionar entidades à sua aplicação LUIS.
