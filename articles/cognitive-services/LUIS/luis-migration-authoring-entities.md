---
title: Migrar para a entidade de aprendizagem automática V3
description: A autoria do V3 fornece um novo tipo de entidade, a entidade de aprendizagem automática, juntamente com a capacidade de adicionar relações à entidade de aprendizagem automática e a outras entidades ou características da aplicação.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/08/2020
ms.openlocfilehash: 667226770d25ef1687420b1c13bc71863f987e33
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324693"
---
# <a name="migrate-to-v3-authoring-entity"></a>Migrar para a entidade de autoria V3

A autoria do V3 fornece um novo tipo de entidade, a entidade de aprendizagem automática, juntamente com a capacidade de adicionar relações à entidade de aprendizagem automática e a outras entidades ou características da aplicação.

## <a name="entities-are-decomposable-in-v3"></a>Entidades são decompor-se em V3

As entidades criadas com as APIs de autoria V3, quer utilizando as [APIs](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview) quer com o portal, permitem construir um modelo de entidade em camadas com um pai e filhos. O progenitor é conhecido como a **entidade de aprendizagem automática** e as crianças são conhecidas como **subentidades** da entidade aprendida com a máquina.

Cada sub-entidade é também uma entidade de aprendizagem automática, mas com as opções de configuração adicionadas das funcionalidades.

* **As funcionalidades requeridas** são regras que garantem que uma entidade é extraída quando corresponde a uma funcionalidade. A regra é definida pela característica requerida ao modelo:
    * [Entidade pré-construída](luis-reference-prebuilt-entities.md)
    * [Entidade de expressão regular](reference-entity-regular-expression.md)
    * [Entidade de lista.](reference-entity-list.md)

## <a name="how-do-these-new-relationships-compare-to-v2-authoring"></a>Como é que estas novas relações se comparam à autoria V2

A autoria da V2 forneceu entidades hierárquicas e compósitas, juntamente com funções e funcionalidades para realizar esta mesma tarefa. Como as entidades, as características e os papéis não estavam explicitamente relacionados uns com os outros, era difícil entender como o LUIS implicou as relações durante a previsão.

Com a V3, a relação é explícita e desenhada pelos autores da aplicação. Isto permite-lhe, como autor da aplicação,:

* Visualmente veja como o LUIS está a prever estas relações, no exemplo das expressões
* Teste para estas relações quer com o painel [de teste interativo](luis-interactive-test.md) quer no ponto final
* Utilize estas relações na aplicação do cliente, através de um objeto bem estruturado, nomeado, aninhado [.json](reference-entity-machine-learned-entity.md)

## <a name="planning"></a>Planeamento

Quando migrar, considere o seguinte no seu plano de migração:

* Faça o back up sua app LUIS, e execute a migração em uma aplicação separada. Ter uma aplicação V2 e V3 disponível ao mesmo tempo permite validar as alterações necessárias e o impacto nos resultados da previsão.
* Capturar métricas de sucesso de previsão atual
* Capture as informações atuais do dashboard como uma imagem do estado da aplicação
* Rever as intenções, entidades, listas de frases, padrões e testes de lote existentes
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
    * Funções - as funções só podem ser aplicadas a uma entidade de aprendizagem automática (pai). As funções não podem ser aplicadas a subentidades
    * Testes e padrões de lote que utilizam as entidades hierárquicas e compósitas

Quando conceber o seu plano de migração, deixe tempo para rever as entidades finais de aprendizagem automática, afinal todas as entidades hierárquicas e compósitas foram migradas. Enquanto uma migração direta funcionará, depois de fazer a alteração e rever os resultados dos seus testes de lote, e a previsão JSON, o JSON mais unificado pode levá-lo a fazer alterações para que as informações finais entregues na aplicação do lado do cliente sejam organizadas de forma diferente. Isto é semelhante ao refactor de códigos e deve ser tratado com o mesmo processo de revisão que a sua organização tem em vigor.

Se não tiver testes de lote para o seu modelo V2 e migrar os testes de lote para o modelo V3 como parte da migração, não será capaz de validar como a migração irá impactar os resultados da previsão do ponto final.

## <a name="migrating-from-v2-entities"></a>Migração de entidades V2

À medida que começa a mudar-se para o modelo de autoria V3, deve considerar como se deslocar para a entidade de aprendizagem automática, e as suas subentidades e funcionalidades.

O quadro seguinte observa quais as entidades que precisam de migrar de um V2 para um desenho de entidade V3.

|Tipo de entidade autora V2|Tipo de entidade autora V3|Exemplo|
|--|--|--|
|Entidade composta|Entidade aprendida com máquina|[Saiba mais](#migrate-v2-composite-entity)|
|Entidade hierárquica|papel da entidade de aprendizagem automática|[Saiba mais](#migrate-v2-hierarchical-entity)|

## <a name="migrate-v2-composite-entity"></a>Migrar V2 Entidade Composta

Cada criança do composto V2 deve ser representada com uma sub-entidade da entidade de aprendizagem automática V3. Se a criança composta for uma expressão pré-construída, de expressão regular ou de uma entidade de lista, esta deve ser aplicada como uma característica necessária na sub-entidade.

Considerações ao planear migrar uma entidade composta para uma entidade de aprendizagem automática:
* Entidades infantis não podem ser usadas em padrões
* As entidades infantis já não são partilhadas
* As entidades infantis precisam de ser rotuladas se costumavam ser não aprendidas com máquinas

### <a name="existing-features"></a>Características existentes

Qualquer lista de frases utilizada para impulsionar palavras na entidade composta deve ser aplicada como característica da entidade de aprendizagem automática (pai), da entidade sub-entidade (criança) ou da intenção (se a lista de frases se aplicar apenas a uma intenção). Planeia adicionar a funcionalidade à entidade onde deve aumentar de forma mais significativa. Não adicione genericamente a funcionalidade à entidade de aprendizagem automática (progenitor), se aumentar significativamente a previsão de uma sub-entidade (criança).

### <a name="new-features"></a>Novas funcionalidades

Na autoria da V3, adicione um passo de planeamento para avaliar as entidades como possíveis características para todas as entidades e intenções.

### <a name="example-entity"></a>Entidade exemplo

Esta entidade é apenas um exemplo. A migração da sua própria entidade pode requerer outras considerações.

Considere um composto V2 para modificar uma pizza `order` que utiliza:
* data pré-construídaV2 para o tempo de entrega
* lista de frases para impulsionar certas palavras, como pizza, torta, crosta e cobertura
* entidade de lista para detetar coberturas como cogumelos, azeitonas, pepperoni.

Um exemplo de expressão para esta entidade é:

`Change the toppings on my pie to mushrooms and delivery it 30 minutes later`

O quadro que se segue demonstra a migração:

|Modelos V2|Modelos V3|
|--|--|
|Pai - Entidade componente nomeada `Order`|Parent - entidade de aprendizagem automática nomeada `Order`|
|Criança - Data pré-construídaV2|* Migrar a entidade pré-construída para uma nova aplicação.<br>* Adicione a função necessária no progenitor para a data pré-construídaV2.|
|Criança - entidade de lista para coberturas|* Migrar a entidade da lista para a nova aplicação.<br>* Em seguida, adicione uma função necessária no progenitor para a entidade da lista.|


## <a name="migrate-v2-hierarchical-entity"></a>Migrar V2 Entidade hierárquica

Na autoria V2, foi fornecida uma entidade hierárquica antes de existirem funções no LUIS. Ambos serviam o mesmo propósito de extrair entidades com base no uso do contexto. Se tiver entidades hierárquicas, pode pensar nelas como entidades simples com papéis.

Na autoria V3:
* Uma função pode ser aplicada na entidade de aprendizagem automática (pai).
* Um papel não pode ser aplicado a nenhuma subentidade.

Esta entidade é apenas um exemplo. A migração da sua própria entidade pode requerer outras considerações.

Considere uma entidade hierárquica V2 para modificar uma `order` pizza:
* onde cada criança determina uma cobertura original ou a cobertura final

Um exemplo de expressão para esta entidade é:

`Change the topping from mushrooms to olives`

O quadro que se segue demonstra a migração:

|Modelos V2|Modelos V3|
|--|--|
|Pai - Entidade componente nomeada `Order`|Parent - entidade de aprendizagem automática nomeada `Order`|
|Criança - Entidade hierárquica com cobertura de pizza original e final|* Adicione o papel para `Order` cada cobertura.|

## <a name="api-change-constraint-replaced-with-required-feature"></a>Restrição de alteração da API substituída por recurso necessário

Esta alteração foi feita em maio de 2020 na conferência //Build e aplica-se apenas às APIs de autoria v3 onde uma aplicação está a utilizar uma funcionalidade restrita. Se estiver a migrar da autoria v2 para a autoria v3, ou não tiver utilizado características constrangidas v3, salte esta secção.

**Funcionalidade** - capacidade de exigir uma entidade existente como recurso para outro modelo e apenas extrair esse modelo se a entidade for detetada. A funcionalidade não mudou, mas a API e a terminologia mudaram.

|Terminologia anterior|Nova terminologia|
|--|--|
|`constrained feature`<br>`constraint`<br>`instanceOf`|`required feature`<br>`isRequired`|

#### <a name="automatic-migration"></a>Migração automática

A partir de 19 de junho de **2020,** não será permitido criar constrangimentos programáticamente utilizando a anterior API de autoria que expôs esta funcionalidade.

Todas as funcionalidades de restrição existentes serão automaticamente migradas para a bandeira de características necessária. Não são necessárias alterações programáticas para a sua API de previsão e nenhuma alteração resultante na qualidade da precisão da previsão.

#### <a name="luis-portal-changes"></a>Alterações no portal LUIS

O portal de pré-visualização LUIS referiu esta funcionalidade como uma **restrição.** O portal LUIS atual designa esta funcionalidade como **uma característica necessária.**

#### <a name="previous-authoring-api"></a>Anterior autoria da API

Esta funcionalidade foi aplicada na pré-visualização da criação **[da API infantil da Entidade Como](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5d86cf3c6a25a45529767d77)** parte da definição de uma entidade, utilizando a propriedade do filho de uma `instanceOf` entidade:

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

#### <a name="new-authoring-api"></a>Nova API de autoria

Esta funcionalidade é agora aplicada com a **[funcionalidade API de recurso add entity](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5d9dc1781e38aaec1c375f26)** usando o `featureName` e `isRequired` propriedades. O valor do `featureName` imóvel é o nome do modelo.

```json
{
    "featureName": "YOUR-MODEL-NAME-HERE",
    "isRequired" : true
}
```


## <a name="next-steps"></a>Passos seguintes

* [Recursos para programadores](developer-reference-resource.md)
