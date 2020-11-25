---
title: Schema atualiza junho-1-2016
description: Versão de esquema atualizada 2016-06-01 para definições de aplicações lógicas em Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 07/25/2016
ms.openlocfilehash: ccc7df5bfac327fabf05f210764dbe10658b5015
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000322"
---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>Atualizações de Schema para Azure Logic Apps - 1 de junho de 2016

O [esquema atualizado](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json) e a versão API para Azure Logic Apps inclui melhorias fundamentais que tornam as aplicações lógicas mais fiáveis e fáceis de usar:

* [Os âmbitos](#scopes) permitem-lhe agrupar ou nidificar ações como uma coleção de ações.
* [As condições e os laços](#conditions-loops) são agora ações de primeira classe.
* Pedido mais preciso para executar ações com o `runAfter` imóvel, substituindo `dependsOn`

Para atualizar as suas aplicações lógicas a partir do esquema de pré-visualização de 1 de agosto de 2015 para o esquema de 1 de junho de 2016, [confira a secção de upgrade](#upgrade-your-schema).

<a name="scopes"></a>

## <a name="scopes"></a>Âmbitos

Este esquema inclui âmbitos, que permitem agrupar ações em conjunto, ou ações de ninho dentro umas das outras. Por exemplo, uma condição pode conter outra condição. Saiba mais sobre [a sintaxe de âmbito,](./logic-apps-control-flow-loops.md)ou reveja este exemplo básico de âmbito:

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

Em versões de esquema anteriores, as condições e os loops eram parâmetros associados a uma única ação. Este esquema levanta esta limitação, pelo que as condições e os laços estão agora disponíveis como tipos de ação. Saiba mais sobre [loops e âmbitos,](./logic-apps-control-flow-loops.md) [condições,](../logic-apps/logic-apps-control-flow-conditional-statement.md)ou reveja este exemplo básico que mostra uma ação de condição:

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

A `runAfter` propriedade `dependsOn` substitui, proporcionando mais precisão quando especifica a ordem de execução para ações com base no estado das ações anteriores. O `dependsOn` imóvel indicou se "a ação correu e foi bem sucedida", com base no facto de a ação anterior ter sido bem sucedida, falhada ou ignorada - e não o número de vezes que quis executar a ação. A `runAfter` propriedade proporciona flexibilidade como objeto que especifica todos os nomes de ação após o qual o objeto é executado. Esta propriedade também define uma série de status que são aceitáveis como gatilhos. Por exemplo, se você quiser que uma ação seja executada após a ação A tem sucesso e também após a ação B ter sucesso ou falhar, crie esta `runAfter` propriedade:

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

Para atualizar para o [esquema mais recente,](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json)basta dar alguns passos. O processo de atualização inclui executar o script de upgrade, economizar como uma nova aplicação lógica, e se quiser, possivelmente sobre-escrever a aplicação lógica anterior.

1. No portal Azure, abra a sua aplicação lógica.

2. Ir ao **Overview**. Na barra de ferramentas de aplicação lógica, escolha **Update Schema**.
   
   ![Escolha atualização de Schema][1]
   
   A definição atualizada é devolvida, que pode copiar e colar numa definição de recurso, se necessário. 

   > [!IMPORTANT]
   > *Certifique-se* de que escolhe **Guardar para** que todas as referências de ligação permaneçam válidas na aplicação lógica atualizada.

3. Na barra de ferramentas da lâmina de atualização, escolha **Guardar As**.

4. Insira o nome e o estado da lógica. Para implementar a sua aplicação lógica atualizada, escolha **Criar.**

5. Confirme que a sua aplicação lógica atualizada funciona como esperado.
   
   > [!NOTE]
   > Se estiver a utilizar um gatilho manual ou de pedido, o URL de retorno muda na sua nova aplicação lógica. Teste o novo URL para se certificar de que a experiência de ponta a ponta funciona. Para preservar URLs anteriores, pode clonar-se sobre a sua aplicação lógica existente.

6. *Opcional* Para substituir a sua aplicação lógica anterior com a nova versão de esquema, na barra de ferramentas, escolha **Clone**, ao lado **do Update Schema**. Este passo só é necessário se pretender manter o mesmo ID de recurso ou solicitar o URL de gatilho da sua aplicação lógica.

## <a name="upgrade-tool-notes"></a>Atualizar notas de ferramentas

### <a name="mapping-conditions"></a>Condições de mapeamento

Na definição atualizada, a ferramenta faz o melhor esforço para agrupar ações de ramo verdadeiro e falso como um âmbito. Especificamente, o padrão de designer `@equals(actions('a').status, 'Skipped')` aparece como uma `else` ação. No entanto, se a ferramenta detetar padrões irreconhecíveis, a ferramenta pode criar condições separadas tanto para o ramo verdadeiro como para o ramo falso. Pode remaptar as ações após a atualização, se necessário.

#### <a name="foreach-loop-with-condition"></a>'foreach' loop com condição

No novo esquema, pode utilizar a ação do filtro para replicar o padrão que utiliza um **Loop para cada** loop com uma condição por item. No entanto, a alteração ocorre automaticamente quando faz a atualização. A condição torna-se uma ação de filtro que aparece antes do **For cada** loop, devolvendo apenas uma série de itens que correspondem à condição, e passando essa matriz para **Para cada** ação. Por exemplo, consulte [Loops e scopes](./logic-apps-control-flow-loops.md).

### <a name="resource-tags"></a>Etiquetas de recursos

Depois de atualizar, as etiquetas de recursos são removidas, pelo que deve repor-as para o fluxo de trabalho atualizado.

## <a name="other-changes"></a>Outras alterações

### <a name="renamed-manual-trigger-to-request-trigger"></a>Rebatizado gatilho 'manual' para gatilho 'request'

O `manual` tipo de gatilho foi depreciado e renomeado para com o tipo `request` `http` . Esta mudança cria mais consistência para o tipo de padrão que o gatilho é usado para construir.

### <a name="new-filter-action"></a>Nova ação de 'filtro'

Para filtrar uma grande matriz até um conjunto menor de itens, o novo `filter` tipo aceita uma matriz e uma condição, avalia a condição de cada item e devolve uma matriz com itens que cumprem a condição.

### <a name="restrictions-for-foreach-and-until-actions"></a>Restrições para ações "foreach" e "até"

O `foreach` loop e o loop são `until` restritos a uma única ação.

### <a name="new-trackedproperties-for-actions"></a>Novas 'vias de rastreio' para ações

As ações podem agora ter uma propriedade adicional chamada `trackedProperties` , que é irmão das `runAfter` `type` propriedades. Este objeto especifica determinadas entradas de ação ou saídas que pretende incluir na telemetria Azure Diagnostic, emitida como parte de um fluxo de trabalho. Por exemplo:

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

* [Criar definições de fluxo de trabalho para apps lógicas](../logic-apps/logic-apps-author-definitions.md)
* [Automatizar a implementação de uma aplicação lógica](logic-apps-azure-resource-manager-templates-overview.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png
