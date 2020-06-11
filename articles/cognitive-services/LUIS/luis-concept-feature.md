---
title: Características - LUIS
description: Adicione funcionalidades a um modelo de idioma para fornecer dicas sobre como reconhecer a entrada que deseja rotular ou classificar.
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: 823c51f0b58481e30ff54814dde03285ad094b9e
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84677596"
---
# <a name="machine-learning-ml-features"></a>Características de aprendizagem automática (ML)

Na aprendizagem automática, uma **característica**   é um traço ou atributo distinto de dados que o seu sistema observa e aprende através.

As características de machine learning dão ao LUIS pistas importantes para onde procurar coisas que vão distinguir um conceito. São indícios que o LUIS pode usar, mas não regras difíceis.  Estas dicas são usadas em conjunto com as etiquetas para encontrar os dados.

## <a name="what-is-a-feature"></a>O que é uma característica

Uma característica é um traço distintivo, que pode ser descrito como uma função: f(x) = y. A característica é usada para saber onde procurar, no exemplo, para o traço distintivo. Ao criar o seu esquema, o que sabe sobre o exemplo que indica o traço? A sua resposta é o seu melhor guia para criar funcionalidades.

## <a name="types-of-features"></a>Tipos de funcionalidades

 O LUIS suporta as listas de frases e os modelos como características:
* Característica da lista de frases
* Modelo (intenção ou entidade) como recurso

As características devem ser consideradas uma parte necessária do seu design de esquema.

## <a name="how-you-find-features-in-your-example-utterances"></a>Como encontra funcionalidades nas suas expressões de exemplo

Como o LUIS é uma aplicação baseada em linguagem, as funcionalidades serão baseadas em texto. Escolha o texto que indique o traço que pretende distinguir. Para o LUIS, a unidade mais pequena baseada em texto é o símbolo. Para a língua inglesa, um símbolo é um vão contíguo, sem espaços ou pontuação, de letras e números. Um espaço não é um símbolo.

Como os espaços e a pontuação não são símbolos, concentre-se nas pistas de texto que pode usar como funcionalidades. Lembre-se de incluir variações de palavras tais:
* formas plural
* verbo tenso
* abreviatura
* ortografia e erro ortográfico

O texto, como traço distintivo, tem de:
* Match a exact word or phrase - considere adicionar uma entidade de expressão regular, ou uma entidade de lista como uma característica para a entidade ou intenção
* Combine um conceito bem conhecido, como datas, horários ou nomes de pessoas - use uma entidade pré-construída como recurso à entidade ou intenção
* Aprenda novos exemplos ao longo do tempo - use uma lista de frases de alguns exemplos do conceito como uma característica para a entidade ou intenção

## <a name="combine-features"></a>Combine funcionalidades

Como existem várias escolhas na forma como um traço é descrito, você pode usar mais do que uma característica que ajuda a descrever esse traço ou conceito. Um emparelhamento comum é usar uma funcionalidade de lista de frases e um dos tipos de entidades geralmente usados como características: entidade pré-construída, entidade de expressão regular ou entidade de lista.

### <a name="ticket-booking-entity-example"></a>Exemplo da entidade de reserva de bilhetes

Como primeiro exemplo, considere uma aplicação para reservar um voo com uma intenção de reserva de voo e uma entidade de reserva de bilhetes.

A entidade de reserva de bilhetes é uma entidade aprendida com máquinas para o destino de voo. Para ajudar a extrair a localização, use duas funcionalidades para ajudar:
* Lista de frases de palavras relevantes `plane` `flight` como, , `reservation``ticket`
* Entidade pré-construída `geographyV2` como característica da entidade

### <a name="pizza-entity-example"></a>Exemplo de entidade de pizza

Como outro exemplo, considere uma app para encomendar uma pizza com uma intenção de encomenda de pizza Create e uma entidade de pizza.

A entidade da pizza é uma entidade aprendida para os detalhes da pizza. Para ajudar a extrair os detalhes, use duas funcionalidades para ajudar:
* Lista de frases de palavras relevantes `cheese` `crust` como, , `pepperoni``pineapple`
* Entidade pré-construída `number` como característica da entidade

## <a name="a-phrase-list-for-a-particular-concept"></a>Uma lista de frases para um conceito particular

Uma lista de frases é uma lista de palavras ou frases que encapsulam um determinado conceito e é aplicada como uma combinação caso-insensível ao nível do símbolo.

Ao adicionar uma lista de frases, pode definir a funcionalidade como:
* **[Global.](#global-features)** Uma funcionalidade global aplica-se a toda a aplicação.

### <a name="when-to-use-a-phrase-list"></a>Quando usar uma lista de frases

Quando precisar da sua app LUIS para generalizar e identificar novos itens para o conceito, utilize uma lista de frases. As listas de frases são como vocabulário específico de domínio que ajuda a melhorar a qualidade de compreensão tanto das intenções como das entidades.

### <a name="how-to-use-a-phrase-list"></a>Como usar uma lista de frases

Com uma lista de frases, LUIS considera o contexto e generaliza para identificar itens semelhantes, mas não uma correspondência de texto exata.

Passos para usar uma lista de frases:
* Comece com uma entidade de aprendizagem automática
    * Adicionar expressões de exemplo
    * Etiqueta com uma entidade de aprendizagem automática
* Adicione uma lista de frases
    * Adicione palavras com significado semelhante - **não** adicione cada palavra ou frase possível. Em vez disso, adicione algumas palavras ou frases de cada vez, em seguida, retreinar e publicar.
    * Rever e adicionar palavras sugeridas

### <a name="a-typical-scenario-for-a-phrase-list"></a>Um cenário típico para uma lista de frases

Um cenário típico para uma lista de frases é impulsionar palavras relacionadas com uma ideia específica.

Um exemplo de palavras que podem precisar de uma lista de frases para aumentar o seu significado são termos médicos. Os termos podem ter significado físico, químico, terapêutico ou abstrato específico. LUIS não saberá que os termos são importantes para o seu domínio de assunto sem uma lista de frases.

Se quiser extrair os termos médicos:
* Primeiro criar palavras de exemplo e rotular termos médicos dentro dessas expressões.
* Em seguida, crie uma lista de frases com exemplos dos termos dentro do domínio do assunto. Esta lista de frases deve incluir o termo real que rotulou e outros termos que descrevem o mesmo conceito.
* Adicione a lista de frases à entidade ou sub-entidade que extrai o conceito utilizado na lista de frases. O cenário mais comum é um componente (criança) de uma entidade de aprendizagem automática. Se a lista de frases deve ser aplicada em todas as intenções ou entidades, marque a lista de frases como uma lista global de frases. A `enabledForAllModels` bandeira controla este âmbito de modelo na API.

### <a name="token-matches-for-a-phrase-list"></a>Token corresponde para uma lista de frases

Uma lista de frases aplica-se ao nível do símbolo, independentemente do caso. O gráfico seguinte mostra como uma lista de frases contendo a palavra `Ann` é aplicada a variações dos mesmos caracteres nessa ordem.


| Variação simbólica de`Ann` | Correspondência de lista de frases quando o símbolo é encontrado |
|--------------------------|---------------------------------------|
| ANN<br>aNN<br>           | Sim - o símbolo é`Ann`                  |
| Ann's                    | Sim - o símbolo é`Ann`                  |
| Ana                     | Não - o símbolo é`Anne`                  |


<a name="how-to-use-phrase-lists"></a>
<a name="how-to-use-a-phrase-lists"></a>
<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="a-model-as-a-feature-helps-another-model"></a>Um modelo como recurso ajuda outro modelo

Pode adicionar um modelo (intenção ou entidade) como recurso a outro modelo (intenção ou entidade). Ao adicionar uma intenção ou entidade existente como recurso, a sua adição de um conceito bem definido com exemplos rotulados.

Ao adicionar um modelo como recurso, pode definir a funcionalidade como:
* **[Necessário .](#required-features)** Deve ser encontrada uma característica necessária para que o modelo seja devolvido do ponto final de previsão.
* **[Global.](#global-features)** Uma funcionalidade global aplica-se a toda a aplicação.

### <a name="when-to-use-an-entity-as-a-feature-to-an-intent"></a>Quando usar uma entidade como recurso a uma intenção

Adicione uma entidade como recurso a uma intenção quando a deteção dessa entidade é significativa para a intenção.

Por exemplo, se a intenção é reservar um voo, `BookFlight` e a entidade é a informação sobre bilhetes (como o número de lugares, origem e destino), então encontrar a entidade de informação de bilhete deve acrescentar um peso significativo à previsão da `BookFlight` intenção.

### <a name="when-to-use-an-entity-as-a-feature-to-another-entity"></a>Quando usar uma entidade como recurso a outra entidade

Uma entidade (A) deve ser adicionada como recurso a outra entidade (B) quando a deteção dessa entidade (A) for significativa para a previsão da entidade (B).

Por exemplo, se a entidade n endereço de envio contiver uma sub-entidade de endereço de rua, então encontrar a sub-entidade de endereço de rua adiciona um peso significativo à previsão para a entidade de endereço de envio.

* Endereço de envio (entidade aprendida com máquinas)
    * Número de rua (sub-entidade)
    * Endereço de rua (sub-entidade)
    * Cidade (sub-entidade)
    * Estado ou Província (subgrupo)
    * País/Região (subgrupo)
    * Código postal (subgrupo)

## <a name="nested-subentities-with-features"></a>Subentidades aninhadas com características

Uma entidade aprendida por máquina indica que um conceito está presente na entidade-mãe, quer esse progenitor seja outra sub-entidade ou a entidade de topo. O valor da sub-entidade funciona como uma característica para o seu progenitor.

Uma subemina pode ter uma lista de frases como recurso, bem como um modelo (outra entidade) como recurso.

Quando a sub-entidade tem uma lista de frases, isto irá impulsionar o vocabulário do conceito, mas não adicionará nenhuma informação à resposta JSON da previsão.

Quando a sub-entidade tem uma característica de outra entidade, a resposta JSON inclui os dados extraídos dessa outra entidade.

## <a name="required-features"></a>Funcionalidades necessárias

Deve ser encontrada uma característica necessária para que o modelo seja devolvido do ponto final de previsão. Utilize uma função necessária quando souber que os seus dados de entrada devem coincidir com a funcionalidade.

Se o texto de expressão não corresponder à função necessária, não será extraído.

**Uma característica necessária utiliza uma entidade não-máquina aprendida:**
* Entidade de expressão regular
* Entidade de lista
* Entidade pré-construída

Quais são as boas características para marcar como necessário? Se estiver confiante de que o seu modelo será encontrado nos dados, desconfiem da funcionalidade conforme necessário. Uma característica necessária não devolve nada, se não for encontrada.

Continuando com o exemplo do endereço de envio:
* Endereço de envio (entidade aprendida com máquinas)
    * Número de rua (sub-entidade)
    * Endereço de rua (sub-entidade)
    * Nome de rua (sub-entidade)
    * Cidade (sub-entidade)
    * Estado ou Província (subgrupo)
    * País/Região (subgrupo)
    * Código postal (subgrupo)

### <a name="required-feature-using-prebuilt-entities"></a>Recurso necessário usando entidades pré-construídas

A cidade, o estado e o país/região são geralmente um conjunto fechado de listas, o que significa que não mudam muito com o tempo. Estas entidades podem ter as características recomendadas relevantes e essas funcionalidades podem ser marcadas conforme necessário. Isto significa que todo o endereço de envio não é devolvido é que as entidades com características necessárias não são encontradas.

E se a cidade, o estado, ou o país/região estiverem na expressão, mas num local ou numa gíria que o LUIS não espera? Se pretender fornecer algum pós-processamento para ajudar a resolver a entidade, devido a uma pontuação de baixa confiança da LUIS, não marque a funcionalidade conforme necessário.

Outro exemplo de uma característica necessária para o endereço de envio é fazer do número de rua um número [pré-construído](luis-reference-prebuilt-entities.md) necessário. Isto permite que um utilizador introduza "1 Microsoft Way" ou "One Microsoft Way". Ambos resolverão um número de "1" para a sub-entidade número de Rua.

### <a name="required-feature-using-list-entities"></a>Recurso necessário usando entidades de lista

Uma [entidade de lista](reference-entity-list.md) é usada como uma lista de nomes canónicos juntamente com os seus sinónimos. Como recurso necessário, se a expressão não incluir nem o nome canónico nem um sinónimo, então a entidade não é devolvida como parte do ponto final da previsão.

Continuando com o exemplo do endereço de envio, suponha que a sua empresa apenas naveca para um conjunto limitado de países/regiões. Pode criar uma entidade de lista que inclua várias formas de o seu cliente fazer referência ao país. Se a LUIS não encontrar uma correspondência exata dentro do texto da expressão, então a entidade (que tem a característica necessária da entidade da lista) não é devolvida na previsão.

|Nome canónico|Sinónimos|
|--|--|
|Estados Unidos da América|U.S.<br>E.U.A.<br>EUA<br>EUA<br>0|

A aplicação do cliente, como um chat bot pode fazer uma pergunta seguinte, para que o cliente entenda que a seleção país/região é limitada e _necessária._

### <a name="required-feature-using-regular-expression-entities"></a>Recurso necessário usando entidades de expressão regular

Uma [entidade de expressão regular](reference-entity-regular-expression.md) utilizada como recurso necessário fornece capacidades ricas de correspondência de texto.

Continuando com o endereço de envio, você pode criar uma expressão regular que captura regras de sintaxe dos códigos postais país/região.

## <a name="global-features"></a>Características globais

Embora o uso mais comum seja aplicar uma funcionalidade a um modelo específico, pode configurar a funcionalidade como uma **funcionalidade global** para aplicá-la a toda a sua aplicação.

O uso mais comum para uma funcionalidade global é adicionar um vocabulário adicional, como palavras de outro idioma, à app. Se os seus clientes usarem uma língua primária, mas esperam poder usar outra língua dentro da mesma expressão, pode adicionar uma funcionalidade que inclui palavras da língua secundária.

Uma vez que o utilizador espera utilizar o segundo idioma em qualquer intenção ou entidade, deve ser adicionado numa lista de frases com a lista de frases configurada como uma característica global.

## <a name="best-practices"></a>Melhores práticas
Aprenda as [melhores práticas.](luis-concept-best-practices.md)

## <a name="next-steps"></a>Próximos passos

* [Prolongue](schema-change-prediction-runtime.md) os seus modelos de aplicativos no tempo de execução da previsão
* Consulte [Adicionar Funcionalidades](luis-how-to-add-features.md) para saber mais sobre como adicionar funcionalidades à sua aplicação LUIS.
