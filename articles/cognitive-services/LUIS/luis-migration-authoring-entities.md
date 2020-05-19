---
title: Migrar para a entidade aprendida com máquinas V3
description: A autoria v3 fornece um novo tipo de entidade, a entidade aprendida por máquinas, juntamente com a capacidade de adicionar relações à entidade aprendida por máquinas e outras entidades ou funcionalidades da aplicação.
ms.topic: how-to
ms.date: 05/08/2020
ms.openlocfilehash: 79fbe261f597f55ca6caff468d4d5c154a273c42
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593227"
---
# <a name="migrate-to-v3-authoring-entity"></a>Migrar para a entidade autora da V3

A autoria v3 fornece um novo tipo de entidade, a entidade aprendida por máquinas, juntamente com a capacidade de adicionar relações à entidade aprendida por máquinas e outras entidades ou funcionalidades da aplicação.

## <a name="entities-are-decomposable-in-v3"></a>As entidades são descomponsáveis em V3

As entidades criadas com o V3 que autorize APIs, quer utilizando as [APIs](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview) ou com o portal, permitem-lhe construir um modelo de entidade em camadas com um pai e filhos. O progenitor é conhecido como a **entidade aprendida** pela máquina e as crianças são conhecidas como **subentidades** da entidade aprendida pela máquina.

Cada subentidade é também uma entidade aprendida por máquinas, mas com as opções de configuração adicionais de funcionalidades.

* **As características necessárias** são regras que garantem que uma entidade é extraída quando corresponde a uma funcionalidade. A regra é definida pela característica necessária ao modelo:
    * [Entidade pré-construída](luis-reference-prebuilt-entities.md)
    * [Entidade de expressão regular](reference-entity-regular-expression.md)
    * [Entidade de listas.](reference-entity-list.md)

## <a name="how-do-these-new-relationships-compare-to-v2-authoring"></a>Como é que estas novas relações se comparam à autoria v2

A autoria v2 forneceu entidades hierárquicas e compostas, juntamente com papéis e características para realizar esta mesma tarefa. Como as entidades, funcionalidades e papéis não estavam explicitamente relacionados uns com os outros, era difícil entender como luis implicava as relações durante a previsão.

Com o V3, a relação é explícita e desenhada pelos autores da aplicação. Isto permite-lhe, como autor da aplicação,:

* Veja visualmente como luis está prevendo estas relações, no exemplo declarações
* Teste para estas relações quer com o [painel de teste interativo](luis-interactive-test.md) quer no ponto final
* Use estas relações na aplicação do cliente, através de um objeto bem estruturado, nomeado, aninhado [.json](reference-entity-machine-learned-entity.md)

## <a name="planning"></a>Planeamento

Quando migrar, considere o seguinte no seu plano de migração:

* Faça o back up da sua aplicação LUIS e execute a migração numa aplicação separada. Ter uma aplicação V2 e V3 disponível ao mesmo tempo permite-lhe validar as alterações necessárias e o impacto nos resultados da previsão.
* Capturar as métricas de sucesso da previsão atual
* Capture as informações atuais do dashboard como uma imagem do estado da aplicação
* Rever as intenções, entidades, listas de frases, padrões e testes de lote
* Os seguintes elementos podem ser migrados **sem alterações:**
    * Intenções
    * Entidades
        * Entidade de expressão regular
        * Entidade de lista
    * Funcionalidades
        * Lista de frases
* Os seguintes elementos devem ser migrados **com alterações:**
    * Entidades
        * Entidade hierárquica
        * Entidade composta
    * Funções - funções só podem ser aplicadas a uma entidade (progenitora) aprendida por máquinas. Funções não podem ser aplicadas a subentidades
    * Testes e padrões de lote que utilizam as entidades hierárquicas e compostas

Quando conceber o seu plano de migração, deixe tempo para rever as entidades definitivas aprendidas com máquinas, depois de todas as entidades hierárquicas e compostas terem sido migradas. Enquanto uma migração direta funcionará, depois de fazer a alteração e rever os resultados dos seus testes de lote, e a previsão JSON, a JSON mais unificada pode levá-lo a fazer alterações para que a informação final entregue à aplicação do lado do cliente seja organizada de forma diferente. Isto é semelhante ao refactoring de código e deve ser tratado com o mesmo processo de revisão que a sua organização tem em vigor.

Se não tiver testes de lote no lugar para o seu modelo V2 e migrar os testes de lote para o modelo V3 como parte da migração, não será capaz de validar como a migração irá impactar os resultados da previsão do ponto final.

## <a name="migrating-from-v2-entities"></a>Migração de entidades V2

À medida que começa a passar para o modelo de autoria V3, deve considerar como se deslocar para a entidade aprendida pela máquina, e as suas subentidades e características.

O quadro seguinte indica quais as entidades que precisam de migrar de um V2 para um design de entidade V3.

|Tipo de entidade de autoria V2|Tipo de entidade de autoria V3|Exemplo|
|--|--|--|
|Entidade composta|Entidade aprendida por máquinas|[Saiba mais](#migrate-v2-composite-entity)|
|Entidade hierárquica|Papel da entidade aprendida por máquinas|[Saiba mais](#migrate-v2-hierarchical-entity)|

## <a name="migrate-v2-composite-entity"></a>Migrar v2 entidade composta

Cada criança do composto V2 deve ser representada com uma subentidade da entidade que se aprende com máquinas V3. Se a criança compósita for uma expressão pré-construída, regular ou uma entidade de lista, esta deve ser aplicada como uma característica necessária na subentidade.

Considerações ao planear migrar uma entidade composta para uma entidade aprendida por máquinas:
* As entidades infantis não podem ser usadas em padrões
* As entidades infantis já não são partilhadas
* As entidades infantis têm de ser rotuladas se não forem aprendidas

### <a name="existing-features"></a>Características existentes

Qualquer lista de frases utilizada para impulsionar palavras na entidade composta deve ser aplicada como uma característica para a entidade (mãe) aprendida pela máquina, a entidade de subentidade (criança) ou a intenção (se a lista de frases se aplicar apenas a uma intenção). Planeie adicionar a funcionalidade à entidade onde deve impulsionar de forma mais significativa. Não adicione a funcionalidade genericamente à entidade (progenitora) aprendida pela máquina, se aumentar significativamente a previsão de uma subentidade (criança).

### <a name="new-features"></a>Novas funcionalidades

Na autoria da V3, adicione um passo de planeamento para avaliar as entidades como possíveis funcionalidades para todas as entidades e intenções.

### <a name="example-entity"></a>Entidade de exemplo

Esta entidade é apenas um exemplo. A migração da sua própria entidade pode exigir outras considerações.

Considere um composto V2 para modificar uma pizza `order` que usa:
* pré-construído dataV2 para o tempo de entrega
* lista de frases para impulsionar certas palavras, como pizza, torta, crosta e cobertura
* lista entidade para detetar coberturas como cogumelos, azeitonas, pepperoni.

Um exemplo de expressão para esta entidade é:

`Change the toppings on my pie to mushrooms and delivery it 30 minutes later`

A tabela que se segue demonstra a migração:

|Modelos V2|Modelos V3|
|--|--|
|Parent - Entidade componente nomeada`Order`|Parent - Entidade aprendida por máquinas`Order`|
|Criança - Data pré-construídaV2|* Migrar entidade pré-construída para nova app.<br>* Adicione a função necessária no progenitor para datapré-construídaV2.|
|Criança - entidade da lista para coberturas|* Migrar entidade da lista para nova app.<br>* Em seguida, adicione uma funcionalidade necessária no progenitor para a entidade da lista.|


## <a name="migrate-v2-hierarchical-entity"></a>Entidade Hierárquica Migrate V2

Na autoria v2, uma entidade hierárquica foi fornecida antes de funções existentes no LUIS. Ambos serviram o mesmo propósito de extrair entidades com base no uso do contexto. Se tiver entidades hierárquicas, pode pensar nelas como entidades simples com papéis.

Na autoria v3:
* Uma função pode ser aplicada na entidade (progenitora) aprendida pela máquina.
* Um papel não pode ser aplicado a nenhuma subentidade.

Esta entidade é apenas um exemplo. A migração da sua própria entidade pode exigir outras considerações.

Considere uma entidade hierárquica V2 para modificar uma `order` pizza:
* onde cada criança determina uma cobertura original ou a cobertura final

Um exemplo de expressão para esta entidade é:

`Change the topping from mushrooms to olives`

A tabela que se segue demonstra a migração:

|Modelos V2|Modelos V3|
|--|--|
|Parent - Entidade componente nomeada`Order`|Parent - Entidade aprendida por máquinas`Order`|
|Criança - Entidade hierárquica com cobertura de pizza original e final|* Adicione papel para `Order` cada cobertura.|

## <a name="api-change-constraint-replaced-with-required-feature"></a>Restrição de alteração API substituída pela funcionalidade necessária

Esta alteração foi feita em maio de 2020 na conferência //Build e só se aplica às APIs de autor v3 onde uma aplicação está a usar uma funcionalidade restrita. Se estiver a migrar da autoria v2 para a autoria v3, ou não tiver utilizado características restritas v3, ignore esta secção.

**Funcionalidade** - capacidade de exigir uma entidade existente como funcionalidade para outro modelo e apenas extrair esse modelo se a entidade for detetada. A funcionalidade não mudou, mas a API e a terminologia mudaram.

|Terminologia anterior|Nova terminologia|
|--|--|
|`constrained feature`<br>`constraint`<br>`instanceOf`|`required feature`<br>`isRequired`|

#### <a name="automatic-migration"></a>Migração automática

A partir de 19 de junho de **2020,** não poderá criar constrangimentos programáticamente utilizando a anterior autoria da API que expôs esta funcionalidade.

Todas as funcionalidades de restrição existentes serão automaticamente migradas para a bandeira de características necessária. Não são necessárias alterações programáticas para a sua previsão de API e nenhuma alteração resultante na qualidade da precisão da previsão.

#### <a name="luis-portal-changes"></a>Alterações no portal LUIS

O portal de pré-visualização LUIS referiu esta funcionalidade como um **constrangimento.** O atual portal LUIS designa esta funcionalidade como uma **funcionalidade necessária.**

#### <a name="previous-authoring-api"></a>Anterior autoria da API

Esta funcionalidade foi aplicada na pré-visualização da autora **[Create Entity Child API](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5d86cf3c6a25a45529767d77)** como parte da definição de uma entidade, utilizando a `instanceOf` propriedade do filho de uma entidade:

```json
{
    "name" : "dayOfWeek",
    "instanceOf": "datetimeV2",
    "children": [
        {
           "name": "dayNumber",
           "instanceOf": "number",
           "children": []
        }
    ]
}
```

#### <a name="new-authoring-api"></a>Nova autoria da API

Esta funcionalidade é agora aplicada com a relação de **[característica sita](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5d9dc1781e38aaec1c375f26)** add API utilizando as `featureName` propriedades e `isRequired` propriedades. O valor do `featureName` imóvel é o nome do modelo.

```json
{
    "featureName": "YOUR-MODEL-NAME-HERE",
    "isRequired" : true
}
```


## <a name="next-steps"></a>Passos seguintes

* [Recursos para programadores](developer-reference-resource.md)
