---
title: Atualizações de esquema de junho-1-2016-aplicativos lógicos do Azure | Microsoft Docs
description: Versão de esquema 2016-06-01 atualizada para definições de aplicativo lógico em aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: 349d57e8-f62b-4ec6-a92f-a6e0242d6c0e
ms.topic: article
ms.date: 07/25/2016
ms.openlocfilehash: 0558c309cc22f39c2ed439b7930443ca0adb071e
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385373"
---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>Atualizações de esquema para aplicativos lógicos do Azure – 1º de junho de 2016

O [esquema atualizado](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json) e a versão de API para aplicativos lógicos do Azure incluem melhorias importantes que tornam os aplicativos lógicos mais confiáveis e mais fáceis de usar:

* [](#scopes) Os escopos permitem agrupar ou aninhar ações como uma coleção de ações.
* As [condições e](#conditions-loops) os loops agora são ações de primeira classe.
* Ordem mais precisa para executar ações com a `runAfter` Propriedade, substituindo`dependsOn`

Para atualizar seus aplicativos lógicos do esquema de visualização de 1º de agosto de 2015 para o esquema de 1º de junho de 2016, [Confira a seção atualização](#upgrade-your-schema).

<a name="scopes"></a>

## <a name="scopes"></a>Âmbitos

Esse esquema inclui escopos, que permitem agrupar ações ou aninhar ações umas nas outras. Por exemplo, uma condição pode conter outra condição. Saiba mais sobre a [sintaxe de escopo](../logic-apps/logic-apps-loops-and-scopes.md)ou examine este exemplo de escopo básico:

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

Nas versões de esquema anteriores, as condições e os loops eram parâmetros associados a uma única ação. Esse esquema levanta essa limitação, portanto, as condições e os loops agora estão disponíveis como tipos de ação. Saiba mais sobre [loops e](../logic-apps/logic-apps-loops-and-scopes.md)escopos, [condições](../logic-apps/logic-apps-control-flow-conditional-statement.md)ou examine este exemplo básico que mostra uma ação de condição:

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

## <a name="runafter-property"></a>Propriedade ' runAfter '

A `runAfter` propriedade substitui `dependsOn`, fornecendo mais precisão quando você especifica a ordem de execução para ações com base no status das ações anteriores. A `dependsOn` Propriedade indicou se "a ação foi executada e foi bem-sucedida", com base em se a ação anterior foi bem-sucedida, falhou ou se foi ignorada-não o número de vezes que você queria executar a ação. A `runAfter` propriedade fornece flexibilidade como um objeto que especifica todos os nomes de ação após os quais o objeto é executado. Essa propriedade também define uma matriz de status que são aceitáveis como gatilhos. Por exemplo, se você quiser que uma ação seja executada após a ação ter êxito e também após A ação B ter êxito ou falhar, configure `runAfter` essa propriedade:

```json
{
   // Other parts in action definition
   "runAfter": {
      "A": ["Succeeded"],
      "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrade-your-schema"></a>Atualizar seu esquema

Para atualizar para o [esquema mais recente](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json), você precisa executar apenas algumas etapas. O processo de atualização inclui executar o script de atualização, salvar como um novo aplicativo lógico e, se desejar, possivelmente substituindo o aplicativo lógico anterior.

1. No portal do Azure, abra seu aplicativo lógico.

2. Acesse **visão geral**. Na barra de ferramentas do aplicativo lógico, escolha **Atualizar esquema**.
   
   ![Escolher esquema de atualização][1]
   
   A definição atualizada é retornada, que você pode copiar e colar em uma definição de recurso, se necessário. 

   > [!IMPORTANT]
   > *Certifique-se* de escolher **salvar como** para que todas as referências de conexão permaneçam válidas no aplicativo lógico atualizado.

3. Na barra de ferramentas da folha atualizar, escolha **salvar como**.

4. Insira o nome e o status da lógica. Para implantar seu aplicativo lógico atualizado, escolha **criar**.

5. Confirme se o aplicativo lógico atualizado funciona conforme o esperado.
   
   > [!NOTE]
   > Se você estiver usando um gatilho manual ou de solicitação, a URL de retorno de chamada será alterada em seu novo aplicativo lógico. Teste a nova URL para garantir que a experiência de ponta a ponta funcione. Para preservar as URLs anteriores, você pode clonar o aplicativo lógico existente.

6. *Opcional* Para substituir o aplicativo lógico anterior pela nova versão do esquema, na barra de ferramentas, escolha clonar, ao lado de **Atualizar esquema**. Essa etapa será necessária apenas se você quiser manter a mesma ID de recurso ou URL de gatilho de solicitação do seu aplicativo lógico.

## <a name="upgrade-tool-notes"></a>Notas da ferramenta de atualização

### <a name="mapping-conditions"></a>Condições de mapeamento

Na definição atualizada, a ferramenta torna o melhor esforço no agrupamento de ações de ramificação true e false em conjunto como um escopo. Especificamente, o padrão de designer `@equals(actions('a').status, 'Skipped')` de aparece como `else` uma ação. No entanto, se a ferramenta detectar padrões não reconhecíveis, a ferramenta poderá criar condições separadas para a ramificação true e false. Você pode remapear ações após a atualização, se necessário.

#### <a name="foreach-loop-with-condition"></a>loop ' foreach ' com condição

No novo esquema, você pode usar a ação de filtro para replicar o padrão que usa um loop **for each** com uma condição por item. No entanto, a alteração ocorre automaticamente quando você atualiza. A condição torna-se uma ação de filtro que aparece antes do loop **for each** , retornando apenas uma matriz de itens que correspondem à condição e passando essa matriz para **cada** ação. Para obter um exemplo, consulte [loops e](../logic-apps/logic-apps-loops-and-scopes.md)escopos.

### <a name="resource-tags"></a>Etiquetas de recursos

Após a atualização, as marcas de recurso são removidas, portanto, você deve redefini-las para o fluxo de trabalho atualizado.

## <a name="other-changes"></a>Outras alterações

### <a name="renamed-manual-trigger-to-request-trigger"></a>Gatilho ' manual ' renomeado para o gatilho ' request '

O `manual` tipo de gatilho foi preterido e renomeado como `http` `request` com o tipo. Essa alteração cria mais consistência para o tipo de padrão que o gatilho é usado para criar.

### <a name="new-filter-action"></a>Nova ação de ' filtro '

Para filtrar uma matriz grande para um conjunto menor de itens, o novo `filter` tipo aceita uma matriz e uma condição, avalia a condição de cada item e retorna uma matriz com itens que atendem à condição.

### <a name="restrictions-for-foreach-and-until-actions"></a>Restrições para as ações ' foreach ' e ' Until '

O `foreach` loop `until` and é restrito a uma única ação.

### <a name="new-trackedproperties-for-actions"></a>Novo ' reacompanhadoproperties ' para ações

Agora, as ações podem ter uma propriedade `trackedProperties`adicional chamada, que é irmã `runAfter` das `type` Propriedades e. Esse objeto especifica determinadas entradas de ação ou saídas que você deseja incluir na telemetria de diagnóstico do Azure, emitida como parte de um fluxo de trabalho. Por exemplo:

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

## <a name="next-steps"></a>Passos Seguintes

* [Criar definições de fluxo de trabalho para aplicativos lógicos](../logic-apps/logic-apps-author-definitions.md)
* [Automatizar a implantação do aplicativo lógico](logic-apps-azure-resource-manager-templates-overview.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png
