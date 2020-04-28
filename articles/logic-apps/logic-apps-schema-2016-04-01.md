---
title: Schema atualiza junho-1-2016
description: Versão de esquema atualizada 2016-06-01 para definições de aplicações lógicas em Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 07/25/2016
ms.openlocfilehash: e2f65f1c52dc7dfb2e4e4bf66f5c7e82f4b802b8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74792872"
---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>Atualizações de Schema para Apps Lógicas Azure - 1 de junho de 2016

A versão atualizada de [esquemas](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json) e API para Apps Lógicas Azure inclui melhorias fundamentais que tornam as aplicações lógicas mais fiáveis e fáceis de usar:

* [Os âmbitos](#scopes) permitem-lhe agrupar ou nidificar ações como um conjunto de ações.
* [Condições e loops](#conditions-loops) são agora ações de primeira classe.
* Encomenda mais precisa para `runAfter` ações de execução com o imóvel, substituindo`dependsOn`

Para atualizar as suas aplicações lógicas desde o esquema de pré-visualização de 1 de agosto de 2015 até ao esquema de 1 de junho de 2016, [confira a secção](#upgrade-your-schema)de upgrade .

<a name="scopes"></a>

## <a name="scopes"></a>Âmbitos

Este esquema inclui âmbitos, que permitem agrupar ações em conjunto, ou nidificar ações dentro umas das outras. Por exemplo, uma condição pode conter outra condição. Saiba mais sobre [a sintaxe](../logic-apps/logic-apps-loops-and-scopes.md)de âmbito, ou reveja este exemplo de âmbito básico:

```json
{
   "actions": {
      "Scope": {
         "type": "Scope",
         "actions": {                
            "Http": {
               "inputs": {
                   "method": "GET",
                   "uri": "https://www.bing.com"
               },
               "runAfter": {},
               "type": "Http"
            }
         }
      }
   }
}
```

<a name="conditions-loops"></a>

## <a name="conditions-and-loops-changes"></a>Alterações de condições e loops

Em versões anteriores de esquemas, condições e loops eram parâmetros associados a uma única ação. Este esquema levanta esta limitação, pelo que as condições e os laços estão agora disponíveis como tipos de ação. Saiba mais sobre [loops e âmbitos,](../logic-apps/logic-apps-loops-and-scopes.md) [condições,](../logic-apps/logic-apps-control-flow-conditional-statement.md)ou reveja este exemplo básico que mostra uma ação de condição:

```json
{
   "Condition - If trigger is some trigger": {
      "type": "If",
      "expression": "@equals(triggerBody(), '<trigger-name>')",
      "runAfter": {},
      "actions": {
         "Http_2": {
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com"
            },
            "runAfter": {},
            "type": "Http"
         }
      },
      "else": 
      {
         "Condition - If trigger is another trigger": {}
      }  
   }
}
```

<a name="run-after"></a>

## <a name="runafter-property"></a>Propriedade 'runAfter'

A `runAfter` propriedade `dependsOn`substitui, proporcionando mais precisão quando especifica a ordem de execução de ações baseadas no estado das ações anteriores. O `dependsOn` imóvel indicou se "a ação decorreu e foi bem sucedida", com base no sucesso da ação anterior, ou como saltou - e não no número de vezes que quis executar a ação. A `runAfter` propriedade proporciona flexibilidade como objeto que especifica todos os nomes de ação após o qual o objeto é executado. Esta propriedade também define uma variedade de status que são aceitáveis como gatilhos. Por exemplo, se quiser uma ação para correr após a ação A tenha sucesso `runAfter` e também depois da ação B ter sucesso ou falhar, instale esta propriedade:

```json
{
   // Other parts in action definition
   "runAfter": {
      "A": ["Succeeded"],
      "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrade-your-schema"></a>Atualize o seu esquema

Para fazer upgrade para o [esquema mais recente,](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json)basta dar alguns passos. O processo de upgrade inclui executar o script de upgrade, economizar como uma nova aplicação lógica, e se quiser, possivelmente sobrepor a app lógica anterior.

1. No portal Azure, abra a sua aplicação lógica.

2. Vá ao **Overview.** Na barra de ferramentas lógica, escolha **Update Schema**.
   
   ![Escolha Atualização Schema][1]
   
   A definição atualizada é devolvida, que pode copiar e colar numa definição de recurso, se necessário. 

   > [!IMPORTANT]
   > *Certifique-se* de que escolhe **Save Assim** que todas as referências de ligação permanecem válidas na aplicação lógica atualizada.

3. Na barra de ferramentas de lâmina de atualização, escolha **Save As**.

4. Introduza o nome lógico e o estado. Para implementar a sua aplicação lógica atualizada, escolha **Criar**.

5. Confirme que a sua aplicação lógica atualizada funciona como esperado.
   
   > [!NOTE]
   > Se estiver a utilizar um manual ou um gatilho de pedido, o URL de callback muda na sua nova aplicação lógica. Teste o novo URL para se certificar de que a experiência de ponta a ponta funciona. Para preservar URLs anteriores, pode clonar sobre a sua aplicação lógica existente.

6. *Opcional* Para substituir a sua aplicação lógica anterior com a nova versão schema, na barra de ferramentas, escolha **Clone**, ao lado do **Update Schema**. Este passo só é necessário se pretender manter o mesmo ID de recurso ou solicitar URL de gatilho da sua aplicação lógica.

## <a name="upgrade-tool-notes"></a>Atualizar notas de ferramentas

### <a name="mapping-conditions"></a>Condições de mapeamento

Na definição atualizada, a ferramenta faz o melhor esforço para agrupar ações de filiais verdadeiras e falsas como âmbito. Especificamente, o `@equals(actions('a').status, 'Skipped')` padrão de `else` designer aparece como uma ação. No entanto, se a ferramenta detetar padrões irreconhecíveis, a ferramenta pode criar condições separadas tanto para o verdadeiro como para o ramo falso. Pode remapear as ações após a atualização, se necessário.

#### <a name="foreach-loop-with-condition"></a>Loop 'foreach' com condição

No novo esquema, pode utilizar a ação do filtro para replicar o padrão que utiliza um **Para cada** loop com uma condição por item. No entanto, a alteração ocorre automaticamente quando faz a atualização. A condição torna-se uma ação de filtro que aparece antes de **cada** loop, devolvendo apenas um conjunto de itens que correspondem à condição, e passando essa matriz para **cada** ação. Por exemplo, consulte [Loops e âmbitos](../logic-apps/logic-apps-loops-and-scopes.md).

### <a name="resource-tags"></a>Etiquetas de recursos

Após a atualização, as etiquetas de recursos são removidas, por isso deve redefini-las para o fluxo de trabalho atualizado.

## <a name="other-changes"></a>Outras alterações

### <a name="renamed-manual-trigger-to-request-trigger"></a>Gatilho 'manual' renomeado para gatilho 'pedido'

O `manual` tipo de gatilho foi depreciado `request` e `http`renomeado para com tipo . Esta mudança cria mais consistência para o tipo de padrão que o gatilho é usado para construir.

### <a name="new-filter-action"></a>Nova ação de 'filtro'

Para filtrar uma grande matriz até um conjunto `filter` menor de itens, o novo tipo aceita uma matriz e uma condição, avalia a condição de cada item, e devolve uma matriz com itens que satisfazem a condição.

### <a name="restrictions-for-foreach-and-until-actions"></a>Restrições para ações "foreach" e "until"

O `foreach` `until` e o loop estão restritos a uma única ação.

### <a name="new-trackedproperties-for-actions"></a>Novas 'trackedProperties' para ações

As ações podem agora `trackedProperties`ter uma propriedade `runAfter` adicional `type` chamada , que é irmão das propriedades e propriedades. Este objeto especifica certas inputs ou saídas de ação que pretende incluir na telemetria de Diagnóstico Azure, emitida como parte de um fluxo de trabalho. Por exemplo:

``` json
{
   "Http": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com"
      },
      "runAfter": {},
      "type": "Http",
      "trackedProperties": {
         "responseCode": "@action().outputs.statusCode",
         "uri": "@action().inputs.uri"
      }
   }
}
```

## <a name="next-steps"></a>Passos seguintes

* [Criar definições de fluxo de trabalho para aplicações lógicas](../logic-apps/logic-apps-author-definitions.md)
* [Automatizar a implementação de uma aplicação lógica](logic-apps-azure-resource-manager-templates-overview.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png
