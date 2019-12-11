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
ms.openlocfilehash: 6ee156efb5512c92d86ba05513b6a2b91df4eae8
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976966"
---
# <a name="entities-and-their-purpose-in-luis"></a>Entidades e sua finalidade no LUIS

A principal finalidade das entidades é fornecer à extração previsível dos dados pelo aplicativo cliente. Uma finalidade _opcional_e secundária é impulsionar a previsão da intenção ou de outras entidades com descritores.

Há dois tipos de entidades:

* Machine-aprendi-do contexto
* Não aprendido por máquina – para correspondências exatas de texto, correspondências de padrões ou detecção por entidades predefinidas

As entidades aprendidas por máquina fornecem a mais ampla variedade de opções de extração de dados. As entidades não aprendidas por computador funcionam por correspondência de texto e podem ser usadas de forma independente ou como uma [restrição](#design-entities-for-decomposition) em uma entidade aprendida por máquina.

## <a name="entities-represent-data"></a>Entidades representam dados

As entidades são dados que você deseja extrair do expressão, como nomes, datas, nomes de produtos ou qualquer grupo significativo de palavras. Uma expressão pode incluir muitas entidades ou nenhum de todo. Um aplicativo cliente _pode_ precisar dos dados para executar sua tarefa.

As entidades precisam ser rotuladas consistentemente em todos os declarações de treinamento para cada tentativa em um modelo.

 Você pode definir suas próprias entidades ou usar entidades predefinidas para economizar tempo para conceitos comuns, como [datetimeV2](luis-reference-prebuilt-datetimev2.md), [ordinal](luis-reference-prebuilt-ordinal.md), [email](luis-reference-prebuilt-email.md)e [número de telefone](luis-reference-prebuilt-phonenumber.md).

|Expressão|Entidade|Dados|
|--|--|--|
|Comprar 3 pedidos de Nova Iorque|Número pré-criados<br>Location.Destination|3<br>Nova Iorque|
|Comprar um pedido de nova York para Londres em 5 de Março|Location.Origin<br>Location.Destination<br>DatetimeV2 pré-criados|Nova Iorque<br>Londres<br>5 de março de 2018|

### <a name="entities-are-optional"></a>As entidades são opcionais

Enquanto os objetivos são necessários, as entidades são opcionais. Você não precisa criar entidades para todos os conceitos em seu aplicativo, mas apenas para aqueles necessários para que o aplicativo cliente execute uma ação.

Se seus declarações não tiverem dados exigidos pelo aplicativo cliente, você não precisará adicionar entidades. Conforme o aplicativo é reparado e uma nova necessidade de dados é identificada, você pode adicionar entidades apropriadas ao modelo LUIS mais tarde.

## <a name="entity-compared-to-intent"></a>Em comparação comparada a intenção de entidade

A entidade representa um conceito de dados dentro do expressão que você deseja extrair.

Um expressão pode, opcionalmente, incluir entidades. Por comparação, a previsão da intenção de um expressão é _necessária_ e representa toda a expressão. LUIS exige que o exemplo declarações esteja contido em uma intenção.

Considere os quatro declarações a seguir:

|Expressão|Intenção prevista|Entidades extraídas|Explicação|
|--|--|--|--|
|Ajuda|Obter ajuda|-|Nada para extrair.|
|Enviar algo|sendSomething|-|Nada para extrair. O modelo não foi treinado para extrair `something` nesse contexto, e não há um destinatário.|
|Enviar Bob a presente|sendSomething|`Bob`, `present`|O modelo foi treinado com a entidade predefinida [PersonName](luis-reference-prebuilt-person.md) , que extraiu o nome `Bob`. Uma entidade aprendida por máquina foi usada para extrair `present`.|
|Enviar Bob a caixa de chocolates|sendSomething|`Bob`, `box of chocolates`|As duas partes importantes de dados, `Bob` e `box of chocolates`, foram extraídas por entidades.|

## <a name="design-entities-for-decomposition"></a>Projetar entidades para decomposição

É bom design de entidade para tornar sua entidade de nível superior uma entidade aprendida por máquina. Isso permite alterações no design de sua entidade ao longo do tempo e ao uso de **Subcomponentes** (entidades filho), opcionalmente com **restrições** e **descritores**, para decompor a entidade de nível superior nas partes necessárias para o aplicativo cliente.

A criação de decomposição permite que o LUIS retorne um nível profundo de resolução de entidade para seu aplicativo cliente. Isso permite que o aplicativo cliente se concentre em regras de negócio e deixe a resolução de dados em LUIS.

### <a name="machine-learned-entities-are-primary-data-collections"></a>As entidades aprendidas por computador são coleções de dados primárias

As [**entidades aprendidas por computador**](tutorial-machine-learned-entity.md) são a unidade de dados de nível superior. Os subcomponentes são entidades filhas de entidades aprendidas por máquina.

Um gatilho de entidade aprendida por máquina com base no contexto aprendido por meio de treinamento declarações. As **restrições** são regras opcionais aplicadas a uma entidade aprendida por máquina que restringe ainda mais o disparo com base na definição de correspondência de texto exato de uma entidade não aprendida por computador, como uma [lista](reference-entity-list.md) ou [Regex](reference-entity-regular-expression.md). Por exemplo, um `size` entidade aprendida por computador pode ter uma restrição de uma entidade de lista de `sizeList` que restringe a entidade `size` para disparar somente quando os valores contidos na entidade `sizeList` forem encontrados.

Os [**descritores**](luis-concept-feature.md) são recursos aplicados para impulsionar a relevância das palavras ou frases para a previsão. Eles são chamados de *descritores* porque são usados para *descrever* uma intenção ou entidade. Os descritores descrevem a distinção de características ou atributos de dados, como palavras importantes ou frases que LUIS observa e aprende.

Quando você cria um recurso de lista de frases em seu aplicativo LUIS, ele é habilitado globalmente por padrão e aplica-se uniformemente em todas as intenções e entidades. No entanto, se você aplicar a lista de frases como um descritor (recurso) de uma entidade (ou *modelo*) aprendida por computador, seu escopo será reduzido para ser aplicado somente a esse modelo e não será mais usado com todos os outros modelos. Usar uma lista de frases como um descritor para um modelo ajuda a decomposição ao auxiliar na precisão do modelo ao qual ela é aplicada.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Tipos de entidades

Escolha a entidade com base em como os dados devem ser extraídos e como eles devem ser representados após serem extraídos.

|Entity type|Finalidade|
|--|--|
|[**Aprendido com o computador**](tutorial-machine-learned-entity.md)|As entidades aprendidas por computador aprendem com o contexto no expressão. Agrupamento pai de entidades, independentemente do tipo de entidade. Isso torna a variação do posicionamento no exemplo declarações significativa. |
|[**Lista**](reference-entity-list.md)|Lista de itens e seus sinônimos extraídos com **correspondência exata de texto**.|
|[**Padrão. any**](reference-entity-pattern-any.md)|Entidade na qual o fim da entidade é difícil de determinar. |
|[**Prebuilt**](luis-reference-prebuilt-entities.md)|Já treinado para extrair um tipo específico de dados, como URL ou email. Algumas dessas entidades predefinidas são definidas no projeto de texto de [reconhecedores](https://github.com/Microsoft/Recognizers-Text) de código aberto. Se sua cultura específica ou a entidade não é atualmente suportada, contribuem para o projeto.|
|[**Expressão regular**](reference-entity-regular-expression.md)|Usa uma expressão regular para **correspondência exata de texto**.|

## <a name="extracting-contextually-related-data"></a>Extraindo dados relacionados contextualmente

Um expressão pode conter duas ou mais ocorrências de uma entidade em que o significado dos dados é baseado no contexto dentro do expressão. Um exemplo é um expressão para a reserva de um vôo que tem dois locais, origem e destino.

`Book a flight from Seattle to Cairo`

Os dois exemplos de uma entidade de `location` precisam ser extraídos. O aplicativo cliente precisa saber o tipo de local para cada para concluir a compra do tíquete.

Há duas técnicas para extrair dados relacionados contextuais:

 * A entidade `location` é uma entidade aprendida por computador e usa duas entidades de subcomponente para capturar o `origin` e `destination` (preferencial)
 * A entidade `location` usa duas **funções** de `origin` e `destination`

Várias entidades podem existir em um expressão e podem ser extraídas sem usar a decomposição ou funções se o contexto no qual elas são usadas não tiver significado. Por exemplo, se o expressão incluir uma lista de locais, `I want to travel to Seattle, Cairo, and London.`, esta é uma lista em que cada item não tem um significado adicional.

### <a name="using-subcomponent-entities-of-a-machine-learned-entity-to-define-context"></a>Usando entidades de subcomponente de uma entidade aprendida por máquina para definir o contexto

Você pode usar uma [**entidade aprendida por máquina**](tutorial-machine-learned-entity.md) para extrair os dados que descrevem a ação de reservar um vôo e, em seguida, decompor a entidade de nível superior nas partes separadas necessárias pelo aplicativo cliente.

Neste exemplo, `Book a flight from Seattle to Cairo`, a entidade de nível superior poderia ser `travelAction` e rotulada para extrair `flight from Seattle to Cairo`. Em seguida, duas entidades de subcomponente são criadas, chamadas `origin` e `destination`, com uma restrição aplicada à entidade de `geographyV2` predefinida. No declarações de treinamento, os `origin` e `destination` são rotulados adequadamente.

### <a name="using-entity-role-to-define-context"></a>Usando a função de entidade para definir o contexto

Uma função é um alias nomeado para uma entidade com base no contexto dentro de expressão. Uma função pode ser usada com qualquer tipo de entidade predefinida ou personalizada e usada nos dois exemplos de declarações e padrões. Neste exemplo, a entidade `location` precisa de duas funções de `origin` e `destination` e ambas precisam ser marcadas no exemplo declarações.

Se LUIS encontrar o `location` mas não puder determinar a função, a entidade de localização ainda será retornada. O aplicativo cliente precisaria acompanhar uma pergunta para determinar o tipo de local que o usuário pretendia.


## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Se precisar de mais do que o número máximo de entidades

Se você precisar de mais do que o limite, entre em contato com o suporte. Para fazê-lo, coletar informações detalhadas sobre o seu sistema, vá para o [LUIS](luis-reference-regions.md#luis-website) Web site e, em seguida, selecione **suporte**. Se a sua subscrição do Azure inclui os serviços de suporte, contacte [suporte técnico do Azure](https://azure.microsoft.com/support/options/).

## <a name="entity-prediction-status"></a>Status de previsão de entidade

O portal do LUIS mostra quando a entidade, em um exemplo, expressão, tem uma previsão de entidade diferente da entidade selecionada. Essa pontuação diferente é baseada no modelo treinado atual.

## <a name="next-steps"></a>Passos seguintes

Aprenda conceitos sobre o good [expressões com](luis-concept-utterance.md).

Ver [adicionar entidades](luis-how-to-add-entities.md) para saber mais sobre como adicionar entidades à sua aplicação LUIS.

Consulte [tutorial: extrair dados estruturados do usuário expressão com entidades aprendidas por máquina no reconhecimento vocal (Luis)](tutorial-machine-learned-entity.md) para saber como extrair dados estruturados de um expressão usando a entidade aprendida por máquina.
 
