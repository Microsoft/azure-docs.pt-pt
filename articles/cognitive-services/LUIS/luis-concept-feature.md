---
title: Características de aprendizagem automática com LUIS
description: Adicione funcionalidades a um modelo de idioma para fornecer dicas sobre como reconhecer a entrada que deseja rotular ou classificar.
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: 02a6fd27dbe22a40b29b47515edec5506d3b2075
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075161"
---
# <a name="machine-learning-features"></a>Características de aprendizagem automática

Na aprendizagem automática, uma *característica*   é um traço ou atributo distinto de dados que o seu sistema observa e aprende através.

As características de aprendizagem automática dão ao LUIS pistas importantes para onde procurar coisas que distinguem um conceito. São indícios que o LUIS pode usar, mas não são regras difíceis. A LUIS utiliza estas dicas em conjunto com as etiquetas para encontrar os dados.

Uma característica pode ser descrita como uma função, como f(x) = y. No exemplo, a característica diz-lhe onde procurar o traço distintivo. Utilize estas informações para ajudar a criar o seu esquema.

## <a name="types-of-features"></a>Tipos de funcionalidades

O LUIS suporta as listas de frases e os modelos como características:

* Característica da lista de frases 
* Modelo (intenção ou entidade) como recurso

As características devem ser consideradas uma parte necessária do seu design de esquema.

## <a name="find-features-in-your-example-utterances"></a>Encontre funcionalidades nas suas expressões de exemplo

Como o LUIS é uma aplicação baseada na linguagem, as funcionalidades são baseadas em texto. Escolha o texto que indique o traço que pretende distinguir. Para o LUIS, a unidade mais pequena é *o símbolo.* Para a língua inglesa, um símbolo é um espaço contíguo de letras e números que não tem espaços nem pontuação.

Como os espaços e a pontuação não são símbolos, concentre-se nas pistas de texto que pode usar como funcionalidades. Lembre-se de incluir variações de palavras, tais como:

* formas plural
* verbo tenses
* abreviaturas
* ortografias e erros ortográficos

Determinar se o texto, porque distingue uma característica, tem que:

* Combine uma palavra ou frase exata: Considere adicionar uma entidade de expressão regular ou uma entidade de lista como uma característica para a entidade ou intenção.
* Combine um conceito bem conhecido como datas, horários ou nomes de pessoas: Use uma entidade pré-construída como recurso à entidade ou intenção.
* Aprenda novos exemplos ao longo do tempo: Use uma lista de frases de alguns exemplos do conceito como característica da entidade ou intenção.

## <a name="combine-features"></a>Combine funcionalidades

Você pode usar mais do que uma característica para descrever um traço ou conceito. Um emparelhamento comum é usar uma funcionalidade de lista de frases e um tipo de entidade que é frequentemente usado como uma característica:

 * entidade pré-construída
 * entidade de expressão regular
 * entidade lista

### <a name="ticket-booking-entity-example"></a>Exemplo da entidade de reserva de bilhetes

Como primeiro exemplo, considere uma aplicação para reservar um voo com uma intenção de reserva de voo e uma entidade de reserva de bilhetes.

A entidade de reserva de bilhetes é uma entidade de aprendizagem automática para o destino de voo. Para ajudar a extrair a localização, use duas funcionalidades para ajudar:

* Uma lista de frases de palavras relevantes, tais como, **avião,** **voo,** **reserva,** ou **bilhete**
* Uma entidade **de geografia pré-construídaV2** como recurso à entidade

### <a name="pizza-entity-example"></a>Exemplo de entidade de pizza

Como outro exemplo, considere uma app para encomendar uma pizza que tenha uma intenção de criar pizza-encomenda e uma entidade de pizza.

A entidade de pizza é uma entidade de aprendizagem automática para os detalhes da pizza. Para ajudar a extrair os detalhes, use duas funcionalidades para ajudar:

* Uma lista de frases de palavras relevantes, tais como, **queijo,** **crosta,** **pepperoni,** ou **ananás**
* Uma entidade **numeram pré-construída** como recurso à entidade

## <a name="create-a-phrase-list-for-a-concept"></a>Criar uma lista de frases para um conceito

Uma lista de frases é uma lista de palavras ou frases que descreve um conceito. Uma lista de frases é aplicada como uma combinação caso-insensível ao nível do símbolo.

Ao adicionar uma lista de frases, pode definir a funcionalidade como **[global](#global-features)**. Uma funcionalidade global aplica-se a toda a aplicação.

### <a name="when-to-use-a-phrase-list"></a>Quando usar uma lista de frases

Use uma lista de frases quando precisar da sua app LUIS para generalizar e identificar novos itens para o conceito. As listas de frases são como vocabulário específico de domínio. Valorizam a qualidade da compreensão para as intenções e entidades.

### <a name="how-to-use-a-phrase-list"></a>Como usar uma lista de frases

Com uma lista de frases, LUIS considera o contexto e generaliza para identificar itens semelhantes, mas não são uma correspondência de texto exata. Siga estes passos para utilizar uma lista de frases:

1. Comece com uma entidade de aprendizagem automática:
    1. Adicione palavras de exemplo.
    1. Etiquetar com uma entidade de aprendizagem automática.
1. Adicione uma lista de frases:
    1. Adicione palavras com significado semelhante. Não adicione todas as palavras ou frases possíveis. Em vez disso, adicione algumas palavras ou frases de cada vez. Em seguida, treinar e publicar.
    1. Reveja e adicione palavras sugeridas.

### <a name="a-typical-scenario-for-a-phrase-list"></a>Um cenário típico para uma lista de frases

Um cenário típico para uma lista de frases é impulsionar palavras relacionadas com uma ideia específica.

Os termos médicos são um bom exemplo de palavras que podem precisar de uma lista de frases para aumentar o seu significado. Estes termos podem ter significados físicos, químicos, terapêuticos ou abstratos específicos. LUIS não saberá que os termos são importantes para o seu domínio de assunto sem uma lista de frases.

Para extrair os termos médicos:

1. Crie palavras de exemplo e rotule termos médicos dentro dessas expressões.
2. Crie uma lista de frases com exemplos dos termos dentro do domínio do assunto. Esta lista de frases deve incluir o termo real que rotulou e outros termos que descrevem o mesmo conceito.
3. Adicione a lista de frases à entidade ou sub-entidade que extrai o conceito utilizado na lista de frases. O cenário mais comum é um componente (criança) de uma entidade de aprendizagem automática. Se a lista de frases deve ser aplicada em todas as intenções ou entidades, marque a lista de frases como uma lista de frases globais. A bandeira **activadaForAllModels** controla este âmbito de modelo na API.

### <a name="token-matches-for-a-phrase-list"></a>Token corresponde para uma lista de frases

Uma lista de frases aplica-se sempre ao nível do símbolo. A tabela seguinte mostra como uma lista de frases que tem a palavra **Ann** se aplica a variações dos mesmos caracteres nessa ordem.


| Variação simbólica de **Ann** | A lista de frases corresponde quando o símbolo é encontrado |
|--------------------------|---------------------------------------|
| **ANN**<br>**aNN**<br>           | Sim - o símbolo é **Ann**                  |
| **Ann's**                    | Sim - o símbolo é **Ann**                  |
| **Ana**                     | Não - o símbolo é **Ana**                  |

<a name="how-to-use-phrase-lists"></a>
<a name="how-to-use-a-phrase-lists"></a>
<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="a-model-as-a-feature-helps-another-model"></a>Um modelo como recurso ajuda outro modelo

Pode adicionar um modelo (intenção ou entidade) como recurso a outro modelo (intenção ou entidade). Ao adicionar uma intenção ou entidade existente como recurso, está a adicionar um conceito bem definido que tem rotulado exemplos.

Ao adicionar um modelo como recurso, pode definir a funcionalidade como:
* **[Necessário .](#required-features)** Deve ser encontrada uma característica necessária para que o modelo seja devolvido do ponto final de previsão.
* **[Global.](#global-features)** Uma funcionalidade global aplica-se a toda a aplicação.

### <a name="when-to-use-an-entity-as-a-feature-to-an-intent"></a>Quando usar uma entidade como recurso a uma intenção

Adicione uma entidade como recurso a uma intenção quando a deteção dessa entidade é significativa para a intenção.

Por exemplo, se a intenção é reservar um voo, como o **BookFlight,** e a entidade é a informação de bilhetes (como o número de lugares, origem e destino), então encontrar a entidade de informação de bilhete deve adicionar um peso significativo à previsão da intenção **do BookFlight.**

### <a name="when-to-use-an-entity-as-a-feature-to-another-entity"></a>Quando usar uma entidade como recurso a outra entidade

Uma entidade (A) deve ser adicionada como recurso a outra entidade (B) quando a deteção dessa entidade (A) for significativa para a previsão da entidade (B).

Por exemplo, se uma entidade de endereço de envio estiver contida numa subgrupo de endereço de rua, então encontrar a entidade de endereço de rua adiciona um peso significativo à previsão para a entidade de endereço de envio.

* Endereço de envio (entidade de aprendizagem automática):

    * Número de rua (sub-entidade)
    * Endereço de rua (sub-entidade)
    * Cidade (sub-entidade)
    * Estado ou Província (subgrupo)
    * País/Região (subgrupo)
    * Código postal (subgrupo)

## <a name="nested-subentities-with-features"></a>Subentidades aninhadas com características

Uma subgrupo de aprendizagem automática indica que um conceito está presente na entidade-mãe. O progenitor pode ser outra sub-entidade ou a entidade de topo. O valor da sub-entidade funciona como uma característica para o seu progenitor.

Uma sub-entidade pode ter uma lista de frases e um modelo (outra entidade) como recurso.

Quando a sub-entidade tem uma lista de frases, aumenta o vocabulário do conceito, mas não adiciona qualquer informação à resposta JSON da previsão.

Quando a sub-entidade tem uma característica de outra entidade, a resposta JSON inclui os dados extraídos dessa outra entidade.


## <a name="required-features"></a>Funcionalidades necessárias

Deve ser encontrada uma característica necessária para que o modelo seja devolvido do ponto final de previsão. Utilize uma função necessária quando souber que os seus dados de entrada devem coincidir com a funcionalidade.

Se o texto de expressão não corresponder à função necessária, não será extraído.

Uma característica necessária utiliza uma entidade que não aprende máquinas:

* Entidade de expressão regular
* Entidade de lista
* Entidade pré-construída

Se estiver confiante de que o seu modelo será encontrado nos dados, desconfiem da funcionalidade conforme necessário. Uma característica necessária não devolve nada se não for encontrada.

Continuando com o exemplo do endereço de envio:

Endereço de envio (entidade aprendida com máquinas)

 * Número de rua (sub-entidade) 
 * Endereço de rua (sub-entidade) 
 * Nome de rua (sub-entidade) 
 * Cidade (sub-entidade) 
 * Estado ou Província (subgrupo) 
 * País/Região (subgrupo) 
 * Código postal (subgrupo)

### <a name="required-feature-using-prebuilt-entities"></a>Recurso necessário usando entidades pré-construídas

A cidade, o estado e o país/região são geralmente um conjunto fechado de listas, o que significa que não mudam muito com o tempo. Estas entidades podem ter as características recomendadas relevantes e essas funcionalidades podem ser marcadas conforme necessário. Isso significa que todo o endereço de envio não é devolvido se as entidades que têm características necessárias não forem encontradas.

E se a cidade, o estado, ou o país/região estiverem na expressão, mas estão num local ou são calígas que o LUIS não espera? Se quiser fornecer algum pós-processamento para ajudar a resolver a entidade, devido a uma pontuação de baixa confiança da LUIS, não marque a funcionalidade conforme necessário.

Outro exemplo de uma característica necessária para o endereço de envio é fazer do número de rua um número [pré-construído](luis-reference-prebuilt-entities.md) necessário. Isto permite que um utilizador introduza "1 Microsoft Way" ou "One Microsoft Way". Ambos resolvem o numeral "1" para a sub-entidade de número de rua.

### <a name="required-feature-using-list-entities"></a>Recurso necessário usando entidades de lista

Uma [entidade de lista](reference-entity-list.md) é usada como uma lista de nomes canónicos juntamente com os seus sinónimos. Como recurso necessário, se a expressão não incluir nem o nome canónico nem um sinónimo, então a entidade não é devolvida como parte do ponto final da previsão.

Suponha que a sua empresa só envia para um conjunto limitado de países/regiões. Pode criar uma entidade de lista que inclui várias formas de o seu cliente fazer referência ao país/região. Se a LUIS não encontrar uma correspondência exata dentro do texto da expressão, então a entidade (que tem a característica necessária da entidade da lista) não é devolvida na previsão.

|Nome canónico|Sinónimos|
|--|--|
|Estados Unidos da América|U.S.<br>E.U.A.<br>EUA<br>E.U.A.<br>0|

Uma aplicação do cliente, como um chat bot, pode fazer uma pergunta de acompanhamento para ajudar. Isto ajuda o cliente a entender que a seleção país/região é limitada e *necessária.*

### <a name="required-feature-using-regular-expression-entities"></a>Recurso necessário usando entidades de expressão regular

Uma [entidade de expressão regular](reference-entity-regular-expression.md) que é usada como uma funcionalidade necessária fornece capacidades ricas de correspondência de texto.

No exemplo do endereço de envio, pode criar uma expressão regular que captura as regras de sintaxe dos códigos postais país/região.

## <a name="global-features"></a>Características globais

Embora o uso mais comum seja aplicar uma funcionalidade a um modelo específico, pode configurar a funcionalidade como uma **funcionalidade global** para aplicá-la a toda a sua aplicação.

O uso mais comum para uma funcionalidade global é adicionar um vocabulário adicional à app. Por exemplo, se os seus clientes usarem uma língua primária, mas esperam poder usar outra língua dentro da mesma expressão, pode adicionar uma funcionalidade que inclui palavras da língua secundária.

Como o utilizador espera utilizar a língua secundária em qualquer intenção ou entidade, adicione palavras da língua secundária à lista de frases. Configurar a lista de frases como uma característica global.

## <a name="best-practices"></a>Melhores práticas

Aprenda as [melhores práticas.](luis-concept-best-practices.md)

## <a name="next-steps"></a>Passos seguintes

* [Estenda](schema-change-prediction-runtime.md) os seus modelos de aplicativos no tempo de execução da previsão.
* Consulte [as funcionalidades De adicionar](luis-how-to-add-features.md) para saber mais sobre como adicionar funcionalidades à sua aplicação LUIS.
