---
title: Tipos de entidade-LUIS
titleSuffix: Azure Cognitive Services
description: 'As entidades extraem dados do expressão. Os tipos de entidade oferecem extração previsível de dados. Há dois tipos de entidades: aprendidas por máquina e não aprendidas por computador. É importante saber para qual tipo de entidade você está trabalhando no declarações.'
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 8e91a475c7fd7f207c8b38d3da8abe7affd668b2
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013504"
---
# <a name="entities-and-their-purpose-in-luis"></a>Entidades e sua finalidade no LUIS

A principal finalidade das entidades é fornecer à extração previsível dos dados pelo aplicativo cliente. Uma finalidade _opcional_e secundária é impulsionar a previsão da intenção com os descritores. 

Há dois tipos de entidades: 

* Machine-aprendi-do contexto
* Não aprendido por máquina – para correspondências exatas de texto

Sempre comece com uma entidade aprendida por máquina, pois isso fornece a mais ampla variedade de opções de extração de dados.

## <a name="entity-compared-to-intent"></a>Em comparação comparada a intenção de entidade

A entidade representa um conceito de dados dentro do expressão que você deseja extrair. 

Considere os 3 declarações a seguir:

|Expressão|Dados extraídos|Explicação|
|--|--|--|
|`Help`|-|Nada para extrair.|
|`Send Bob a present`|Bob, presente|Bob é definitivamente importante para concluir a tarefa. A presente pode ser de informações suficientes ou o bot pode precisar esclarecer o que está presente com uma pergunta de acompanhamento.|
|`Send Bob a box of chocolates.`|As duas partes importantes dos dados, Bob e a caixa de chocolates, são importantes para a conclusão da solicitação do usuário.|

Uma expressão pode incluir muitas entidades ou nenhum de todo. Um aplicativo cliente _pode_ precisar da entidade para executar sua tarefa. 

Por comparação, a previsão da intenção de um expressão é _necessária_ e representa toda a expressão. LUIS exige que o exemplo declarações esteja contido em uma intenção. Se a principal intenção do expressão não for importante para o aplicativo cliente, adicione todos os declarações à intenção nenhum. 

Se você encontrar, mais tarde no ciclo de vida do aplicativo, você deseja dividir o declarações, você pode facilmente fazer isso. Isso pode ser para organizar o declarações enquanto você estiver criando ou pode ser usar a intenção prevista no aplicativo cliente. 

Não há nenhum requisito para usar a intenção prevista no aplicativo cliente, mas ele é retornado como parte da resposta do ponto de extremidade de previsão.

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

## <a name="design-entities-for-decomposition"></a>Projetar entidades para decomposição

Comece o design de sua entidade com uma entidade aprendida por computador. Isso permite um crescimento de design fácil e alterações de sua entidade ao longo do tempo. Adicione **Subcomponentes** (entidades filho) com **restrições** e **descritores** para concluir o design de entidade. 

A criação de decomposição permite que o LUIS retorne um nível profundo de resolução de entidade para seu aplicativo cliente. Isso permite que o aplicativo cliente se concentre em regras de negócio e deixe a resolução de dados em LUIS.

### <a name="machine-learned-entities-are-primary-data-collections"></a>As entidades aprendidas por computador são coleções de dados primárias

As entidades aprendidas por computador são a unidade de dados de nível superior. Os subcomponentes são entidades filhas de entidades aprendidas por máquina. 

As **restrições** são entidades de correspondência de texto exato que aplicam regras para identificar e extrair dados. Os **descritores** são recursos aplicados para impulsionar a relevância das palavras ou frases para a previsão.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Tipos de entidades

Escolha a entidade com base em como os dados devem ser extraídos e como eles devem ser representados após serem extraídos.

|Tipo de entidade|Objetivo|
|--|--|
|[**Aprendido com o computador**](tutorial-machine-learned-entity.md)|Agrupamento pai de entidades, independentemente do tipo de entidade. As entidades aprendidas por computador aprendem com o contexto no expressão. Isso torna a variação do posicionamento no exemplo declarações significativa. |
|[**Lista**](reference-entity-list.md)|Lista de itens e seus sinônimos extraídos com **correspondência exata de texto**.|
|[**Padrão. any**](reference-entity-pattern-any.md)|Entidade na qual o fim da entidade é difícil de determinar. |
|[**Prebuilt**](luis-reference-prebuilt-entities.md)|Já treinado para extrair um tipo específico de dados, como URL ou email. Algumas dessas entidades predefinidas são definidas no projeto de texto de [reconhecedores](https://github.com/Microsoft/Recognizers-Text) de código aberto. Se sua cultura específica ou a entidade não é atualmente suportada, contribuem para o projeto.|
|[**Expressão regular**](reference-entity-regular-expression.md)|Usa uma expressão regular para **correspondência exata de texto**.|

### <a name="entity-role-defines-context"></a>Função de entidade define contexto

A função de uma entidade é o alias nomeado com base no contexto dentro do expressão. Um exemplo é um expressão para a reserva de um vôo que tem dois locais, origem e destino.

`Book a flight from Seattle to Cairo`

Os dois exemplos de uma entidade de `location` precisam ser extraídos. O aplicativo cliente precisa saber o tipo de local para cada para concluir a compra do tíquete. A entidade `location` precisa de duas funções de `origin` e `destination` e ambas precisam ser marcadas no exemplo declarações. 

Se LUIS encontrar o `location` mas não puder determinar a função, a entidade de localização ainda será retornada. O aplicativo cliente precisaria acompanhar uma pergunta para determinar o tipo de local que o usuário pretendia. 

Várias entidades podem existir em um expressão e podem ser extraídas sem usar funções. Se o contexto da sentença indicar o valor da entidade, uma função deverá ser usada.

Se o expressão incluir uma lista de locais, `I want to travel to Seattle, Cairo, and London.`, esta é uma lista em que cada item não tem um significado adicional. 

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Se precisar de mais do que o número máximo de entidades 

Se você precisar de mais do que o limite, entre em contato com o suporte. Para fazê-lo, coletar informações detalhadas sobre o seu sistema, vá para o [LUIS](luis-reference-regions.md#luis-website) Web site e, em seguida, selecione **suporte**. Se a sua subscrição do Azure inclui os serviços de suporte, contacte [suporte técnico do Azure](https://azure.microsoft.com/support/options/). 

## <a name="entity-prediction-status"></a>Status de previsão de entidade

O portal do LUIS mostra quando a entidade, em um exemplo, expressão, tem uma previsão de entidade diferente da entidade selecionada. Essa pontuação diferente é baseada no modelo treinado atual. 

## <a name="next-steps"></a>Passos seguintes

Aprenda conceitos sobre o good [expressões com](luis-concept-utterance.md). 

Ver [adicionar entidades](luis-how-to-add-entities.md) para saber mais sobre como adicionar entidades à sua aplicação LUIS.
