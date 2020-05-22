---
title: Características - LUIS
description: Adicione funcionalidades a um modelo de idioma para fornecer dicas sobre como reconhecer a entrada que pretende rotular ou classificar.
ms.topic: conceptual
ms.date: 05/14/2020
ms.openlocfilehash: c4f19ceed2e48f3f6ec2ed0958bccb7a85cff44f
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83742719"
---
# <a name="machine-learning-ml-features"></a>Características de aprendizagem automática (ML)

No machine learning, uma **característica**   é um traço distintivo ou atributo de dados que o seu sistema observa.

As funcionalidades de machine learning dão pistas importantes para o LUIS procurar coisas que distingam um conceito. São indícios de que o LUIS pode usar, mas não regras rígidas.  Estas dicas são utilizadas em conjunto com as etiquetas para encontrar os dados.

 O LUIS apoia ambas as listas de frases e utiliza outras entidades como características:
* Recurso da lista de frases
* Modelo (intenção ou entidade) como recurso

As funcionalidades devem ser consideradas uma parte necessária do seu design de esquema.

## <a name="a-phrase-list-for-a-particular-concept"></a>Uma lista de frases para um conceito particular

Uma lista de frases é uma lista de palavras ou frases que encapsula um conceito particular.

Ao adicionar uma lista de frases, pode definir a funcionalidade como:
* **[Global.](#global-features)** Uma funcionalidade global aplica-se a toda a aplicação.

### <a name="when-to-use-a-phrase-list"></a>Quando usar uma lista de frases

Quando precisar da sua app LUIS para poder generalizar e identificar novos itens para o conceito, utilize uma lista de frases. As listas de frases são como vocabulário específico do domínio que ajudam a melhorar a qualidade de compreensão tanto das intenções como das entidades.

### <a name="how-to-use-a-phrase-list"></a>Como usar uma lista de frases

Com uma lista de frases, a LUIS considera o contexto e generaliza para identificar itens semelhantes, mas não uma correspondência exata de texto.

Passos para usar uma lista de frases:
* Comece com uma entidade de aprendizagem automática
    * Adicionar expressões de exemplo
    * Etiqueta com uma entidade de aprendizagem automática
* Adicione uma lista de frases
    * Adicione palavras com significado semelhante - **não** adicione todas as palavras ou frases possíveis. Em vez disso, adicione algumas palavras ou frases de cada vez, em seguida, retree e publique.
    * Reveja e adicione palavras sugeridas

### <a name="a-typical-scenario-for-a-phrase-list"></a>Um cenário típico para uma lista de frases

Um cenário típico para uma lista de frases é impulsionar palavras relacionadas com uma ideia específica.

Um exemplo de palavras que podem precisar de uma lista de frases para aumentar o seu significado são termos médicos. Os termos podem ter um significado físico, químico, terapêutico ou abstrato específico. Luis não saberá que os termos são importantes para o seu domínio sujeito sem uma lista de frases.

Se quiser extrair os termos médicos:
* Primeiro crie declarações de exemplo e rotule termos médicos dentro dessas expressões.
* Em seguida, crie uma lista de frases com exemplos dos termos dentro do domínio sujeito. Esta lista de frases deve incluir o termo real que rotulou e outros termos que descrevem o mesmo conceito.
* Adicione a lista de frases à entidade ou subentidade que extrai o conceito utilizado na lista de frases. O cenário mais comum é um componente (criança) de uma entidade de aprendizagem automática. Se a lista de frases deve ser aplicada em todas as intenções ou entidades, marque a lista de frases como uma lista de frases globais. A `enabledForAllModels` bandeira controla este âmbito de modelo na API.

<a name="how-to-use-phrase-lists"></a>
<a name="how-to-use-a-phrase-lists"></a>
<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="a-model-as-a-feature-helps-another-model"></a>Um modelo como recurso ajuda outro modelo

Pode adicionar um modelo (intenção ou entidade) como uma funcionalidade a outro modelo (intenção ou entidade). Ao adicionar uma intenção ou entidade existente como recurso, a sua adição de um conceito bem definido com exemplos rotulados.

Ao adicionar um modelo como funcionalidade, pode definir a funcionalidade como:
* **[Obrigatório](#required-features)**. Uma característica necessária tem de ser encontrada para que o modelo seja devolvido do ponto final da previsão.
* **[Global.](#global-features)** Uma funcionalidade global aplica-se a toda a aplicação.

### <a name="when-to-use-an-entity-as-a-feature-to-an-intent"></a>Quando usar uma entidade como recurso a uma intenção

Adicione uma entidade como uma característica a uma intenção quando a deteção dessa entidade é significativa para a intenção.

Por exemplo, se a intenção é reservar um voo, e a entidade é informação de `BookFlight` bilhetes (como o número de lugares, origem e destino), então encontrar a entidade de informação do bilhete deve adicionar um peso significativo à previsão da `BookFlight` intenção.

### <a name="when-to-use-an-entity-as-a-feature-to-another-entity"></a>Quando usar uma entidade como recurso para outra entidade

Uma entidade (A) deve ser adicionada como uma característica a outra entidade (B) quando a deteção dessa entidade (A) for significativa para a previsão da entidade (B).

Por exemplo, se a entidade de endereços de envio contivesse uma subentidade de endereço de rua, então encontrar a subentidade de endereço de rua adiciona um peso significativo à previsão para a entidade de endereço de envio.

* Endereço de envio (entidade aprendida por máquinas)
    * Número de rua (subentidade)
    * Endereço de rua (subentidade)
    * Cidade (subentidade)
    * Estado ou Província (subentidade)
    * País/Região (subentidade)
    * Código postal (subentidade)

## <a name="nested-subentities-with-features"></a>Subentidades aninhadas com características

Uma subentidade aprendida por máquinaindica que um conceito está presente na entidade-mãe, quer essa mãe seja outra subentidade ou a entidade de topo. O valor da subentidade funciona como uma característica para o seu progenitor.

Uma subentidade pode ter tanto uma lista de frases como uma funcionalidade, bem como um modelo (outra entidade) como recurso.

Quando a subentidade tem uma lista de frases, isto irá impulsionar o vocabulário do conceito, mas não adicionará qualquer informação à resposta da JSON da previsão.

Quando a subentidade tem uma característica de outra entidade, a resposta da JSON inclui os dados extraídos dessa outra entidade.

## <a name="required-features"></a>Funcionalidades necessárias

Uma característica necessária tem de ser encontrada para que o modelo seja devolvido do ponto final da previsão. Utilize uma funcionalidade necessária quando souber que os seus dados de entrada devem corresponder à funcionalidade.

Se o texto de expressão não corresponder à função necessária, não será extraído.

**Uma função necessária utiliza uma entidade não-automática aprendida:**
* Entidade de expressão regular
* Entidade de lista
* Entidade pré-construída

Quais são as boas características para marcar como necessário? Se estiver confiante de que o seu modelo será encontrado nos dados, detete a funcionalidade conforme necessário. Uma característica necessária não devolve nada, se não for encontrada.

Continuando com o exemplo do endereço de envio:
* Endereço de envio (entidade aprendida por máquinas)
    * Número de rua (subentidade)
    * Endereço de rua (subentidade)
    * Nome de rua (subentidade)
    * Cidade (subentidade)
    * Estado ou Província (subentidade)
    * País/Região (subentidade)
    * Código postal (subentidade)

### <a name="required-feature-using-prebuilt-entities"></a>Recurso necessário usando entidades pré-construídas

A cidade, o estado e o país/região são geralmente um conjunto fechado de listas, o que significa que não mudam muito com o tempo. Estas entidades podem ter as características recomendadas relevantes e essas funcionalidades podem ser marcadas conforme necessário. Isto significa que todo o endereço de envio não é devolvido é que as entidades com funcionalidades necessárias não são encontradas.

E se a cidade, o estado ou o país/região estiverem na proclamação, mas quer num local ou numa gíria que o LUIS não espera? Se pretender fornecer algum processamento pós-processamento para ajudar a resolver a entidade, devido a uma pontuação de confiança baixa da LUIS, não marque a funcionalidade conforme necessário.

Outro exemplo de uma característica necessária para o endereço de envio é fazer do número de rua um número [pré-construído](luis-reference-prebuilt-entities.md) necessário. Isto permite que um utilizador introduza "1 Microsoft Way" ou "One Microsoft Way". Ambos decidirão para uma série de "1" para a subentidade número de rua.

### <a name="required-feature-using-list-entities"></a>Recurso necessário usando entidades de lista

Uma [entidade da lista](reference-entity-list.md) é usada como uma lista de nomes canónicos juntamente com os seus sinónimos. Como característica necessária, se a expressão não incluir o nome canónico ou um sinónimo, então a entidade não é devolvida como parte do ponto final da previsão.

Continuando com o exemplo do endereço de envio, suponha que a sua empresa apenas naves para um conjunto limitado de países/regiões. Pode criar uma entidade de lista que inclui várias formas de o seu cliente fazer referência ao país. Se o LUIS não encontrar uma correspondência exata dentro do texto da expressão, então a entidade (que tem a característica necessária da entidade da lista) não é devolvida na previsão.

|Nome canónico|Sinónimos|
|--|--|
|Estados Unidos da América|U.S.<br>E.U.A.<br>EUA<br>EUA<br>0|

A aplicação do cliente, como um chat bot pode fazer uma pergunta de seguimento, para que o cliente entenda que a seleção país/região é limitada e _necessária._

### <a name="required-feature-using-regular-expression-entities"></a>Recurso necessário usando entidades de expressão regular

Uma [entidade de expressão regular](reference-entity-regular-expression.md) usada como uma funcionalidade necessária fornece capacidades ricas de correspondência de texto.

Continuando com o endereço de envio, você pode criar uma expressão regular que captura regras de sintaxe dos códigos postais país/região.

## <a name="global-features"></a>Características globais

Embora o uso mais comum seja aplicar uma funcionalidade a um modelo específico, pode configurar a funcionalidade como uma **funcionalidade global** para aplicá-la a toda a sua aplicação.

O uso mais comum para uma funcionalidade global é adicionar um vocabulário adicional, como palavras de outra língua, à app. Se os seus clientes usarem uma língua primária, mas esperam poder usar outra língua dentro da mesma expressão, pode adicionar uma funcionalidade que inclui palavras da língua secundária.

Como o utilizador esperava utilizar o segundo idioma em qualquer intenção ou entidade, deve ser adicionado numa lista de frases com a lista de frases configurada como uma característica global.

## <a name="best-practices"></a>Melhores práticas
Aprenda [as melhores práticas.](luis-concept-best-practices.md)

## <a name="next-steps"></a>Passos seguintes

* [Estenda](schema-change-prediction-runtime.md) os seus modelos de aplicações no tempo de execução da previsão
* Consulte [funcionalidades adicionais](luis-how-to-add-features.md) para saber mais sobre como adicionar funcionalidades à sua aplicação LUIS.
