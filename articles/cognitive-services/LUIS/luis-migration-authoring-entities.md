---
title: Migrar para a entidade aprendida pela máquina v3
titleSuffix: Azure Cognitive Services
description: A criação v3 fornece um novo tipo de entidade, a entidade aprendida por máquina, juntamente com a capacidade de adicionar relações à entidade aprendida por máquina e a outras entidades ou recursos do aplicativo.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: diberry
ms.openlocfilehash: b5dbcd9033d9a41e43ea907d043e0c0486b236db
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563836"
---
# <a name="migrate-to-v3-authoring-entity"></a>Migrar para a entidade de criação v3

A criação v3 fornece um novo tipo de entidade, a entidade aprendida por máquina, juntamente com a capacidade de adicionar relações à entidade aprendida por máquina e a outras entidades ou recursos do aplicativo.

## <a name="entities-are-decomposable-in-v3"></a>As entidades são decompostas em v3

As entidades criadas com as APIs de criação v3, usando as [APIs](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview) ou com o [portal de visualização](https://preview.luis.ai/), permitem que você crie um modelo de entidade em camadas com um pai e filhos. O pai é conhecido como **entidade aprendida pela máquina** e os filhos são conhecidos como **Subcomponentes** da entidade aprendida do computador.

Cada Subcomponente também é uma entidade aprendida por máquina, mas com as opções de configuração adicionadas de restrições e descritores.

* As **restrições** são regras de correspondência de texto exatas que garantem que uma entidade seja extraída quando corresponder a uma regra. A regra é definida por uma entidade de correspondência de texto exata, atualmente: uma [entidade predefinida](luis-reference-prebuilt-entities.md), uma [entidade de expressão regular](reference-entity-regular-expression.md)ou uma [entidade de lista](reference-entity-list.md).
* Os **descritores** são [recursos](luis-concept-feature.md), como listas de frases ou entidades, que são usados para indicar fortemente a entidade.

A criação v3 fornece um novo tipo de entidade, a entidade aprendida por máquina, juntamente com a capacidade de adicionar relações à entidade aprendida por máquina e a outras entidades ou recursos do aplicativo.

## <a name="how-do-these-new-relationships-compare-to-v2-authoring"></a>Como essas novas relações se comparam à criação v2

A criação v2 forneceu entidades hierárquicas e compostas juntamente com funções e recursos para realizar essa mesma tarefa. Como as entidades, os recursos e as funções não estavam explicitamente relacionados entre si, era difícil entender como o LUIS implicava as relações durante a previsão.

Com v3, a relação é explícita e projetada pelos autores do aplicativo. Isso permite que você, como autor do aplicativo,:

* Veja visualmente como o LUIS está prevendo essas relações, no exemplo declarações
* Teste para essas relações com o [painel de teste interativo](luis-interactive-test.md) ou no ponto de extremidade
* Use essas relações no aplicativo cliente, por meio de um objeto bem estruturado, nomeado e aninhado [. JSON](reference-entity-machine-learned-entity.md)

## <a name="planning"></a>Planeamento

Ao migrar, considere o seguinte em seu plano de migração:

* Faça backup do aplicativo LUIS e realize a migração em um aplicativo separado. Ter um aplicativo V2 e v3 disponível ao mesmo tempo permite que você valide as alterações necessárias e o impacto nos resultados da previsão.
* Capturar métricas de sucesso de previsão atuais
* Capturar informações do painel atual como um instantâneo do status do aplicativo
* Revisar as tentativas, entidades, listas de frases, padrões e testes de lote existentes
* Os elementos a seguir podem ser migrados **sem alteração**:
    * Intenções
    * Entidades
        * Entidade de expressão regular
        * Entidade de lista
    * Funcionalidades
        * Lista de frases
* Os elementos a seguir precisam ser migrados **com as alterações**:
    * Entidades
        * Entidade hierárquica
        * Entidade composta
    * Funções – as funções só podem ser aplicadas a uma entidade aprendida por computador (pai). Funções não podem ser aplicadas a subcomponentes
    * Testes em lotes e padrões que usam as entidades hierárquicas e compostas

Ao projetar seu plano de migração, deixe o tempo de revisar as entidades finais aprendidas pela máquina, depois que todas as entidades hierárquicas e compostas tiverem sido migradas. Embora uma migração direta funcione, depois de fazer a alteração e examinar os resultados do teste do lote e prever o JSON, o JSON mais unificado pode levar você a fazer alterações para que as informações finais entregues ao aplicativo do lado do cliente sejam organizadas de forma diferente. Isso é semelhante à refatoração de código e deve ser tratado com o mesmo processo de revisão que sua organização tem em vigor.

Se você não tiver testes de lote em vigor para seu modelo V2 e migrar os testes de lote para o modelo V3 como parte da migração, não será possível validar como a migração afetará os resultados de previsão do ponto de extremidade.

## <a name="migrating-from-v2-entities"></a>Migrando de entidades v2

Ao começar a mudar para o modelo de criação v3, você deve considerar como mover para a entidade aprendida por computador e seus subcomponentes, incluindo as restrições e os descritores.

A tabela a seguir anota quais entidades precisam migrar de um design de entidade v2 para um v3.

|Tipo de entidade de criação v2|Tipo de entidade de criação v3|Exemplo|
|--|--|--|
|Entidade composta|Entidade aprendida do computador|[Saiba Mais](#migrate-v2-composite-entity)|
|Entidade hierárquica|Função da entidade aprendida pelo computador|[Saiba Mais](#migrate-v2-hierarchical-entity)|

## <a name="migrate-v2-composite-entity"></a>Migrar entidade composta v2

Cada filho da composição v2 deve ser representado com um subcomponente da entidade do aprendida pela máquina v3. Se o filho composto for uma expressão predefinida, regular ou uma entidade de lista, isso deverá ser aplicado como uma **restrição** no subcomponente que representa o filho.

Considerações ao planejar a migração de uma entidade composta para uma entidade aprendida por máquina:
* Entidades filho não podem ser usadas em padrões
* Entidades filho não são mais compartilhadas
* Entidades filho precisam ser rotuladas se usadas para serem aprendidas sem computador

### <a name="existing-descriptors"></a>Descritores existentes

Qualquer lista de frases usada para impulsionar palavras na entidade composta deve ser aplicada como um descritor à entidade aprendida (pai) da máquina, à entidade de subcomponente (filho) ou à intenção (se a lista de frases se aplicar apenas a uma intenção). Planeje adicionar o descritor à entidade que ele deve aumentar mais significativamente. Não adicione o descritor genericamente à entidade aprendida pela máquina (pai), caso ela aumente significativamente a previsão de um subcomponente (filho).

### <a name="new-descriptors"></a>Novos descritores

Na criação v3, adicione uma etapa de planejamento para avaliar entidades como possíveis descritores para todas as entidades e intenções.

### <a name="example-entity"></a>Entidade de exemplo

Essa entidade é apenas um exemplo. Sua própria migração de entidade pode exigir outras considerações.

Considere uma composição v2 para modificar um `order` de pizza que usa:
* datetimeV2 predefinidos para tempo de entrega
* lista de frases para aumentar determinadas palavras, como pizza, pizza, Crust e ingredientes
* listar entidade para detectar ingredientes como cogumelos, olivas, pepperoni.

Um exemplo de expressão para essa entidade é:

`Change the toppings on my pie to mushrooms and delivery it 30 minutes later`

A tabela a seguir demonstra a migração:

|Modelos v2|Modelos v3|
|--|--|
|Entidade pai-componente chamada `Order`|Entidade do pai-máquina aprendida denominada `Order`|
|DatetimeV2 predefinido filho|* Migre a entidade predefinida para o novo aplicativo.<br>* Adicionar restrição no pai para datetimeV2 predefinidos.|
|Entidade de lista de filhos para ingredientes|* Migre a entidade de lista para o novo aplicativo.<br>* Em seguida, adicione uma restrição ao pai para a entidade de lista.|


## <a name="migrate-v2-hierarchical-entity"></a>Migrar entidade hierárquica v2

Na criação v2, uma entidade hierárquica foi fornecida antes das funções existentes em LUIS. Ambos serviam a mesma finalidade de extrair entidades com base no uso de contexto. Se você tiver entidades hierárquicas, poderá considerá-las como entidades simples com funções.

Na criação V3:
* Uma função pode ser aplicada na entidade (pai) aprendida pela máquina.
* Uma função não pode ser aplicada a nenhum subcomponente.

Essa entidade é apenas um exemplo. Sua própria migração de entidade pode exigir outras considerações.

Considere uma entidade hierárquica v2 para modificar um `order`de pizza:
* onde cada filho determina um ingredientes original ou o topo de cima

Um exemplo de expressão para essa entidade é:

`Change the topping from mushrooms to olives`

A tabela a seguir demonstra a migração:

|Modelos v2|Modelos v3|
|--|--|
|Entidade pai-componente chamada `Order`|Entidade do pai-máquina aprendida denominada `Order`|
|Entidade filho-hierárquica com topo de pizza original e final|* Adicionar função a `Order` para cada ingredientes.|

## <a name="next-steps"></a>Passos seguintes

* [Recursos para desenvolvedores](developer-reference-resource.md)
