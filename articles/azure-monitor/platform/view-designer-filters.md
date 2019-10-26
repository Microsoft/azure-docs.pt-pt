---
title: Filtros em exibições de Azure Monitor | Microsoft Docs
description: Um filtro em uma exibição de Azure Monitor permite que os usuários filtrem os dados na exibição pelo valor de uma propriedade específica sem modificar a exibição em si.  Este artigo descreve como usar um filtro e adicionar um a um modo de exibição personalizado.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/22/2018
ms.openlocfilehash: 03950c7c87f659c5d1c032b5d3c1f74d136697c7
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931971"
---
# <a name="filters-in-azure-monitor-views"></a>Filtros em exibições de Azure Monitor
Um **filtro** em uma [exibição de Azure monitor](view-designer.md) permite que os usuários filtrem os dados na exibição pelo valor de uma propriedade específica sem modificar a exibição em si.  Por exemplo, você pode permitir que os usuários da exibição filtrem a exibição de dados somente de um determinado computador ou conjunto de computadores.  Você pode criar vários filtros em uma única exibição para permitir que os usuários filtrem por várias propriedades.  Este artigo descreve como usar um filtro e adicionar um a um modo de exibição personalizado.

## <a name="using-a-filter"></a>Usando um filtro
Clique no intervalo de data e hora na parte superior de uma exibição para abrir o menu suspenso onde você pode alterar o intervalo de data e hora da exibição.

![Exemplo de filtro](media/view-designer-filters/filters-example-time.png)

Clique no **+** para adicionar um filtro usando filtros personalizados definidos para a exibição. Selecione um valor para o filtro na lista suspensa ou digite um valor. Continue a adicionar filtros clicando no **+** . 


![Exemplo de filtro](media/view-designer-filters/filters-example-custom.png)

Se você remover todos os valores de um filtro, esse filtro não será mais aplicado.


## <a name="creating-a-filter"></a>Criando um filtro

Crie um filtro na guia **filtros** ao [Editar uma exibição](view-designer.md).  O filtro é global para a exibição e se aplica a todas as partes na exibição.  

![Configurações de filtro](media/view-designer-filters/filters-settings.png)

A tabela a seguir descreve as configurações de um filtro.

| Definição | Descrição |
|:---|:---|
| Nome do Campo | Nome do campo usado para filtragem.  Este campo deve corresponder ao campo resumir na **consulta de valores**. |
| Consulta de valores | Consulta a ser executada para popular o menu suspenso de filtro para o usuário.  Essa consulta deve usar [resumir](/azure/kusto/query/summarizeoperator) ou [DISTINCT](/azure/kusto/query/distinctoperator) para fornecer valores exclusivos para um campo específico e deve corresponder ao nome do **campo**.  Você pode usar [Sort](/azure/kusto/query/sortoperator) para classificar os valores que são exibidos para o usuário. |
| Etiqueta | Nome do campo usado em consultas que dão suporte ao filtro e também é exibido para o usuário. |

### <a name="examples"></a>Exemplos

A tabela a seguir inclui alguns exemplos de filtros comuns.  

| Nome do Campo | Consulta de valores | Etiqueta |
|:--|:--|:--|
| Computador   | Classificação &#124; de computador &#124; distinta de pulsação por computador ASC | Computadores |
| EventLevelName | EventLevelName &#124; de evento distinto | Gravidade |
| Nível | Nível &#124; distinto do syslog | Gravidade |
| SvcChangeType | SvcChangeType &#124; ConfigurationChange DISTINCT | ChangeType |


## <a name="modify-view-queries"></a>Modificar consultas de exibição

Para que um filtro tenha qualquer efeito, você deve modificar todas as consultas na exibição para filtrar os valores selecionados.  Se você não modificar nenhuma consulta na exibição, os valores que o usuário selecionar não terão nenhum efeito.

A sintaxe para usar um valor de filtro em uma consulta é: 

    where ${filter name}  

Por exemplo, se o modo de exibição tiver uma consulta que retorne eventos e usar um filtro chamado _computadores_, você poderá usar a consulta a seguir.

    Event | where ${Computers} | summarize count() by EventLevelName

Se você adicionou outro filtro chamado gravidade, poderá usar a consulta a seguir para usar ambos os filtros.

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre as [partes de visualização](view-designer-parts.md) que você pode adicionar à sua exibição personalizada.
