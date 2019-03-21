---
title: Esquema de atualizações de Junho-1-2016 - Azure Logic Apps | Documentos da Microsoft
description: Versão de esquema atualizadas 2016-06-01 para definições de aplicação lógica no Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: 349d57e8-f62b-4ec6-a92f-a6e0242d6c0e
ms.topic: article
ms.date: 07/25/2016
ms.openlocfilehash: 6df29543df2b7b2609582f7e8dd9a0629182760c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57849788"
---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>Atualizações do esquema para o Azure Logic Apps - 1 de Junho de 2016

O [atualizado esquema](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json) e versão de API para o Azure Logic Apps inclui os principais melhoramentos que tornam as aplicações lógicas mais confiável e fácil de utilizar:

* [Âmbitos](#scopes) permitem-lhe agrupar ou aninhar ações como uma coleção de ações.
* [Condições e loops](#conditions-loops) agora são ações de primeira classe.
* Ordenação mais precisa para executar ações com o `runAfter` propriedade, substituindo `dependsOn`

Para atualizar as aplicações lógicas do esquema de pré-visualização de 1 de Agosto de 2015 para o esquema de 1 de Junho de 2016 [consulte a secção atualizar](#upgrade-your-schema).

<a name="scopes"></a>

## <a name="scopes"></a>Âmbitos

Esse esquema inclui os escopos, que lhe permite ações de grupo em conjunto, ou ações de aninhamento dentro uns dos outros. Por exemplo, uma condição pode conter outra condição. Saiba mais sobre [definir o âmbito sintaxe](../logic-apps/logic-apps-loops-and-scopes.md), ou reveja este exemplo básico de âmbito:

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

No esquema anterior versões, condições e loops foram parâmetros associados uma única ação. Esse esquema ascende esta limitação, para que as condições e loops estão agora disponíveis como tipos de ação. Saiba mais sobre [loops e os âmbitos](../logic-apps/logic-apps-loops-and-scopes.md), [condições](../logic-apps/logic-apps-control-flow-conditional-statement.md), ou reveja este exemplo básico que mostra uma ação de condição:

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

## <a name="runafter-property"></a>propriedade "runAfter"

O `runAfter` propriedade substitui `dependsOn`, fornecendo maior precisão quando especifica a ordem de execução de ações com base no status de ações anteriores. O `dependsOn` propriedade indicado se "a ação foi executada e foi concluída com êxito", com base em se a ação anterior foi concluída com êxito, falha, ou como ignorado - não o número de vezes que quiser executar a ação. O `runAfter` propriedade fornece flexibilidade como um objeto que especifica todos os a ação nomes depois que o objeto é executada. Esta propriedade também define uma matriz de Estados aceitáveis como disparadores. Por exemplo, se pretender que uma ação para execução após a ação A for concluída com êxito e também após ação B tiver sucesso ou falha, configurar isto `runAfter` propriedade:

```json
{
   // Other parts in action definition
   "runAfter": {
      "A": ["Succeeded"],
      "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrade-your-schema"></a>Atualizar o esquema

Para atualizar para o [esquema mais recente](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json), só precisa tomar algumas providências. O processo de atualização inclui a executar o script de atualização, Salvar como uma nova aplicação lógica e, se desejar, possivelmente substituindo a aplicação de lógica anterior.

1. No portal do Azure, abra a aplicação lógica.

2. Aceda a **descrição geral**. Na barra de ferramentas da aplicação lógica, escolha **atualizar esquema**.
   
   ![Escolha atualizar esquema][1]
   
   A definição atualizada é retornada, que pode copiar e colar uma definição do recurso, se necessário. 

   > [!IMPORTANT]
   > *Certifique-se* escolher **guardar como** para que todas as referências de ligação mantêm-se válidas na aplicação lógica atualizada.

3. Na barra de ferramentas da painel de atualização, escolha **guardar como**.

4. Introduza o nome de lógica e o estado. Para implementar a aplicação lógica atualizada, escolha **criar**.

5. Confirme que a sua aplicação lógica atualizada funciona conforme esperado.
   
   > [!NOTE]
   > Se estiver a utilizar um acionador manual ou a pedido, o URL de retorno de chamada é alterado na sua nova aplicação lógica. Teste o novo URL para se certificar de que a experiência de ponta a ponta funciona. Para preservar URLs anteriores, pode clonar ao longo da sua aplicação lógica existente.

6. *Opcional* para substituir a sua aplicação lógica anterior com a nova versão de esquema, na barra de ferramentas, escolha **Clone**, junto a **atualizar esquema**. Este passo é necessário apenas se pretender manter o mesmo ID de recurso ou acionador URL da sua aplicação lógica do pedido.

## <a name="upgrade-tool-notes"></a>Notas de ferramenta de atualização

### <a name="mapping-conditions"></a>Mapeamento de condições

Na definição atualizada, a ferramenta faz o melhor esforço em conjunto como um âmbito de agrupamento ações de ramo de VERDADEIRO ou FALSO. Especificamente, o padrão de designer `@equals(actions('a').status, 'Skipped')` é apresentado como um `else` ação. No entanto, se a ferramenta Deteta padrões irreconhecível, a ferramenta pode criar condições separadas para o verdadeiro e o ramo FALSO. É possível remapear ações após a atualização, se necessário.

#### <a name="foreach-loop-with-condition"></a>ciclo de "foreach" com a condição

No novo esquema, pode utilizar a ação de filtro para replicar o padrão que utiliza um **para cada** ciclo com uma condição por item. No entanto, a alteração ocorre automaticamente quando fizer a atualização. A condição se torne uma ação de filtro que aparece antes do **para cada** loop, retornando apenas uma matriz de itens que correspondem à condição e passar essa matriz para **para cada** ação. Por exemplo, veja [ciclos e âmbitos](../logic-apps/logic-apps-loops-and-scopes.md).

### <a name="resource-tags"></a>Etiquetas de recursos

Após a atualização, as etiquetas de recursos são removidas, pelo que tem de repô-los para o fluxo de trabalho atualizado.

## <a name="other-changes"></a>Outras alterações

### <a name="renamed-manual-trigger-to-request-trigger"></a>Acionador de 'manual' nome mudado para o acionador "request"

O `manual` tipo de Acionador foi preterido e foi renomeado para `request` com o tipo `http`. Esta alteração cria mais consistência para o tipo de padrão que o acionador é utilizado para criar.

### <a name="new-filter-action"></a>Nova ação de 'filter'

Para filtrar uma matriz grande para um conjunto menor de itens, o novo `filter` tipo aceita uma matriz e uma condição, avalia a condição para cada item e retorna uma matriz com itens que cumprem a condição.

### <a name="restrictions-for-foreach-and-until-actions"></a>Restrições para "foreach" e "until" ações

O `foreach` e `until` loop estão limitadas a uma única ação.

### <a name="new-trackedproperties-for-actions"></a>Novo "trackedProperties" para ações

Ações agora podem ter uma propriedade adicional chamada `trackedProperties`, que é colateral para o `runAfter` e `type` propriedades. Este objeto Especifica determinados entradas de ação ou saídas que pretende incluir na telemetria de diagnóstico do Azure, emitida como parte de um fluxo de trabalho. Por exemplo:

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

## <a name="next-steps"></a>Próximos Passos
* [Criar definições de fluxo de trabalho para o logic apps](../logic-apps/logic-apps-author-definitions.md)
* [Criar modelos de implementação para o logic apps](../logic-apps/logic-apps-create-deploy-template.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png
