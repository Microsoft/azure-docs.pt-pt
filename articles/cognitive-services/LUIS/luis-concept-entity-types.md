---
title: Tipos de entidades - LUIS
titleSuffix: Azure Cognitive Services
description: 'As entidades extraem dados da expressão. Os tipos de entidades dão-lhe uma extração previsível de dados. Existem dois tipos de entidades: aprendizagem automática e não-máquina aprendida. É importante saber com que tipo de entidade está a trabalhar em declarações.'
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 6ee156efb5512c92d86ba05513b6a2b91df4eae8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79221031"
---
# <a name="entities-and-their-purpose-in-luis"></a>Entidades e o seu propósito na LUIS

O principal objetivo das entidades é dar à aplicação cliente uma extração previsível de dados. Um propósito _opcional,_ secundário é impulsionar a previsão da intenção ou de outras entidades com descritores.

Existem dois tipos de entidades:

* machine-learned - do contexto
* não-aprendido sem máquina - para correspondências de texto exatos, correspondências de padrões ou deteção por entidades pré-construídas

As entidades aprendidas com máquinas fornecem a mais ampla gama de opções de extração de dados. As entidades não-aprendidas com máquinas trabalham por correspondência de texto e podem ser utilizadas de forma independente ou como [restrição](#design-entities-for-decomposition) a uma entidade aprendida por máquinas.

## <a name="entities-represent-data"></a>Entidades representam dados

As entidades são dados que pretende retirar da expressão, tais como nomes, datas, nomes de produtos ou qualquer grupo significativo de palavras. Uma expressão pode incluir muitas entidades ou nenhum de todo. Uma aplicação do cliente _pode_ precisar dos dados para executar a sua tarefa.

As entidades precisam de ser rotuladas de forma consistente em todas as declarações de formação para cada intenção de um modelo.

 Pode definir as suas próprias entidades ou utilizar entidades pré-construídas para economizar tempo para conceitos comuns como [dataV2](luis-reference-prebuilt-datetimev2.md), [ordinal,](luis-reference-prebuilt-ordinal.md) [e-mail](luis-reference-prebuilt-email.md)e número de [telefone.](luis-reference-prebuilt-phonenumber.md)

|Expressão|Entidade|Dados|
|--|--|--|
|Comprar 3 pedidos de Nova Iorque|Número pré-criados<br>Location.Destination|3<br>Nova Iorque|
|Comprar um pedido de nova York para Londres em 5 de Março|Location.Origin<br>Location.Destination<br>DatetimeV2 pré-criados|Nova Iorque<br>Londres<br>5 de março de 2018|

### <a name="entities-are-optional"></a>As entidades são opcionais

Enquanto os objetivos são necessários, as entidades são opcionais. Não precisa de criar entidades para todos os conceitos da sua app, mas apenas para que a aplicação do cliente tome medidas.

Se as suas declarações não tiverem dados que a aplicação do cliente necessita, não precisa de adicionar entidades. À medida que a sua aplicação se desenvolve e uma nova necessidade de dados é identificada, pode adicionar as entidades apropriadas ao seu modelo LUIS mais tarde.

## <a name="entity-compared-to-intent"></a>Em comparação comparada a intenção de entidade

A entidade representa um conceito de dados dentro da expressão que pretende extrair.

Uma expressão pode incluir opcionalmente entidades. Em comparação, a previsão da intenção de uma expressão é _necessária_ e representa toda a expressão. Luis requer que as declarações exemplo sinuosas estejam contidas numa intenção.

Considere as seguintes 4 expressões:

|Expressão|Intenção prevista|Entidades extraídas|Explicação|
|--|--|--|--|
|Ajuda|ajuda|-|Nada para extrair.|
|Enviar algo|enviar Algo|-|Nada para extrair. O modelo não foi treinado para extrair `something` neste contexto, e também não há destinatário.|
|Envie um presente ao Bob.|enviar Algo|`Bob`, `present`|O modelo foi treinado com a entidade preconstruída [personName,](luis-reference-prebuilt-person.md) que extraiu o nome `Bob`. Uma entidade aprendida por máquinas foi usada para extrair `present`.|
|Mande ao Bob uma caixa de chocolates.|enviar Algo|`Bob`, `box of chocolates`|Os dois dados importantes, `Bob` e o `box of chocolates`, foram extraídos por entidades.|

## <a name="design-entities-for-decomposition"></a>Entidades de design para decomposição

É um bom design de entidade fazer da sua entidade de alto nível uma entidade aprendida por máquinas. Isto permite alterações ao design da sua entidade ao longo do tempo e a utilização de **subcomponentes (entidades infantis),** opcionalmente com **constrangimentos** e **descritores,** para decompor a entidade de alto nível nas peças necessárias pela aplicação do cliente.

A conceção para decomposição permite à LUIS devolver um profundo grau de resolução de entidades à sua aplicação de cliente. Isto permite que a sua aplicação de cliente se concentre nas regras do negócio e deixe a resolução de dados para a LUIS.

### <a name="machine-learned-entities-are-primary-data-collections"></a>Entidades aprendidas com máquinas são recolhas primárias de dados

[**As entidades aprendidas com máquinas**](tutorial-machine-learned-entity.md) são a unidade de dados de alto nível. Subcomponentes são entidades infantis de entidades aprendidas por máquinas.

Uma entidade aprendida por máquinas dispara com base no contexto aprendido através de expressões de treino. **Os constrangimentos** são regras opcionais aplicadas a uma entidade aprendida por máquinas que restringe ainda mais o desencadeamento com base na definição exata de correspondência de texto de uma entidade não-aprendida por máquinas, como uma [Lista](reference-entity-list.md) ou [Regex](reference-entity-regular-expression.md). Por exemplo, uma entidade `size` aprendida com máquinas pode ter um constrangimento de uma entidade de lista de `sizeList` que limita a entidade `size` a desencadear apenas quando os valores contidos na entidade `sizeList` forem encontrados.

[**Os descritores**](luis-concept-feature.md) são funcionalidades aplicadas para aumentar a relevância das palavras ou frases para a previsão. São *chamados descritores* porque são usados para *descrever* uma intenção ou entidade. Os descritores descrevem traços distintivos ou atributos de dados, tais como palavras importantes ou frases que o LUIS observa e aprende.

Quando cria uma funcionalidade de lista de frases na sua aplicação LUIS, é ativada globalmente por padrão e aplica-se uniformemente em todas as intenções e entidades. No entanto, se aplicar a lista de frases como descritor (recurso) de uma entidade (ou *modelo)* aprendida por máquinas, o seu âmbito reduz-se a aplicar-se apenas a esse modelo e já não é utilizado com todos os outros modelos. Usar uma lista de frases como descritor de um modelo ajuda a decompor-se ajudando com a precisão do modelo a que é aplicado.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Tipos de entidades

Escolha a entidade com base na forma como os dados devem ser extraídos e como devem ser representados após a sua extração.

|Tipo de entidade|Objetivo|
|--|--|
|[**Aprendido em máquina**](tutorial-machine-learned-entity.md)|Entidades aprendizes de máquinas aprendem com o contexto na expressão. Agrupamento de entidades, independentemente do tipo de entidade. Isto torna significativa a variação da colocação em declarações por exemplo. |
|[**Lista**](reference-entity-list.md)|Lista de itens e seus sinónimos extraídos com **correspondência exata**de texto .|
|[**Padrão.qualquer**](reference-entity-pattern-any.md)|Entidade onde o fim da entidade é difícil de determinar. |
|[**Pré-construído**](luis-reference-prebuilt-entities.md)|Já treinado para extrair dados específicos, como URL ou e-mail. Algumas destas entidades pré-construídas são definidas no projeto [Recognisers-Text](https://github.com/Microsoft/Recognizers-Text) de código aberto. Se sua cultura específica ou a entidade não é atualmente suportada, contribuem para o projeto.|
|[**Expressão Regular**](reference-entity-regular-expression.md)|Utiliza expressão regular para **uma correspondência de texto exata**.|

## <a name="extracting-contextually-related-data"></a>Extração de dados contexicamente relacionados

Uma expressão pode conter duas ou mais ocorrências de uma entidade onde o significado dos dados se baseie no contexto dentro da expressão. Um exemplo é uma expressão para reservar um voo que tem dois locais, origem e destino.

`Book a flight from Seattle to Cairo`

Os dois exemplos de uma entidade `location` precisam de ser extraídos. A aplicação cliente precisa de saber o tipo de localização para cada um para completar a compra do bilhete.

Existem duas técnicas para extrair dados contexinalmente relacionados:

 * A entidade `location` é uma entidade aprendida por máquinas e utiliza duas entidades subcomponentes para capturar o `origin` e `destination` (preferido)
 * A entidade `location` utiliza dois **papéis** de `origin` e `destination`

Várias entidades podem existir numa expressão e podem ser extraídas sem usar decomposição ou funções se o contexto em que são utilizadas não tiver qualquer significado. Por exemplo, se a expressão inclui uma lista de locais, `I want to travel to Seattle, Cairo, and London.`, esta é uma lista onde cada item não tem um significado adicional.

### <a name="using-subcomponent-entities-of-a-machine-learned-entity-to-define-context"></a>Utilização de entidades subcomponentes de uma entidade aprendida por máquinas para definir contexto

Pode utilizar uma [**entidade aprendida por máquinas**](tutorial-machine-learned-entity.md) para extrair os dados que descrevem a ação de reservar um voo e, em seguida, decompor a entidade de alto nível nas partes separadas necessárias pela aplicação do cliente.

Neste exemplo, `Book a flight from Seattle to Cairo`, a entidade de alto nível poderia ser `travelAction` e rotulada para extrair `flight from Seattle to Cairo`. Em seguida, são criadas duas entidades subcomponentes, chamadas `origin` e `destination`, ambas com uma restrição aplicada à entidade `geographyV2` pré-construída. Nas declarações de formação, os `origin` e os `destination` são rotulados adequadamente.

### <a name="using-entity-role-to-define-context"></a>Usar o papel da Entidade para definir o contexto

Um papel é um pseudónimo nomeado para uma entidade baseada no contexto dentro da expressão. Uma função pode ser usada com qualquer tipo de entidade pré-construída ou personalizada, e usada em ambos os exemplos e padrões. Neste exemplo, a entidade `location` precisa de dois papéis de `origin` e `destination` e ambos precisam de ser marcados nas declarações de exemplo.

Se a LUIS encontrar o `location` mas não conseguir determinar o papel, a entidade local ainda é devolvida. A aplicação do cliente teria de acompanhar uma pergunta para determinar que tipo de localização o utilizador significava.


## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Se precisar de mais do que o número máximo de entidades

Se precisar de mais do que o limite, suporte de contato. Para tal, recolha informações detalhadas sobre o seu sistema, vá ao site da [LUIS](luis-reference-regions.md#luis-website) e, em seguida, selecione **Suporte**. Se a sua subscrição Azure incluir serviços de suporte, contacte o [suporte técnico do Azure.](https://azure.microsoft.com/support/options/)

## <a name="entity-prediction-status"></a>Estado de previsão da entidade

O portal LUIS mostra quando a entidade, num exemplo, tem uma previsão de entidade diferente da entidade que selecionou. Esta pontuação diferente baseia-se no modelo treinado atualmente.

## <a name="next-steps"></a>Passos seguintes

Aprenda conceitos sobre boas [expressões.](luis-concept-utterance.md)

Consulte [adicionar entidades](luis-how-to-add-entities.md) para saber mais sobre como adicionar entidades à sua app LUIS.

Ver [Tutorial: Extrair dados estruturados a partir da expressão do utilizador com entidades aprendizes de máquinas em Compreensão linguística (LUIS)](tutorial-machine-learned-entity.md) para aprender a extrair dados estruturados de uma expressão utilizando a entidade aprendida por máquinas.
 
