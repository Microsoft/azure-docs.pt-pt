---
title: Filtros em modos de exibição do Log Analytics do Azure | Documentos da Microsoft
description: Um filtro a uma vista do Log Analytics permite aos utilizadores filtrar os dados na vista pelo valor de uma determinada propriedade sem modificar o modo de exibição em si.  Este artigo descreve como utilizar um filtro e adicione uma para uma vista personalizada.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: bwren
ms.openlocfilehash: 6a4ac2f26c01555ef54a4ee2248db7cd2818661e
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53189426"
---
# <a name="filters-in-log-analytics-views"></a>Filtros em modos de exibição do Log Analytics
R **filtro** num [ver do Log Analytics](view-designer.md) permite aos utilizadores filtrar os dados na vista pelo valor de uma determinada propriedade sem modificar a própria exibição.  Por exemplo, pode permitir que os utilizadores da sua vista para filtrar a vista de dados apenas a partir de um determinado computador ou um conjunto de computadores.  Pode criar vários filtros numa única vista para permitir que os utilizadores filtrar por várias propriedades.  Este artigo descreve como utilizar um filtro e adicione uma para uma vista personalizada.

## <a name="using-a-filter"></a>Utilizar um filtro
Clique no intervalo de tempo de dados na parte superior de uma vista para abrir o menu pendente para alterar o intervalo de tempo de dados para a vista.

![Exemplo de filtro](media/view-designer-filters/filters-example-time.png)

Clique nas **+** para adicionar um filtro com filtros personalizados que estão definidos para o modo de exibição. Selecionar um valor para o filtro da lista pendente ou escreva um valor. Continue a adicionar filtros ao clicar o **+**. 


![Exemplo de filtro](media/view-designer-filters/filters-example-custom.png)

Se remover todos os valores para um filtro, esse filtro já não será aplicado.


## <a name="creating-a-filter"></a>Criar um filtro

Criar um filtro do **filtros** separador quando [uma vista de edição](view-designer.md).  O filtro é global para o modo de exibição e aplica-se a todas as partes na vista.  

![Definições de filtro](media/view-designer-filters/filters-settings.png)

A tabela seguinte descreve as definições para um filtro.

| Definição | Descrição |
|:---|:---|
| Nome do Campo | Nome do campo utilizado para filtrar.  Isto deve corresponder ao campo de summarize **consulta para valores**. |
| Consulta para valores | Consulta seja executada para preencher a lista pendente de filtro para o utilizador.  Isso tem de utilizar qualquer um [resumir](/azure/kusto/query/summarizeoperator) ou [distintos](/azure/kusto/query/distinctoperator) para fornecer valores exclusivos para um campo específico e ele corresponde do **nome do campo**.  Pode usar [ordenação](/azure/kusto/query/sortoperator) para ordenar os valores que são apresentados ao utilizador. |
| Etiqueta | Nome para o campo que é utilizado em consultas que suporta o filtro e também é apresentado ao utilizador. |

### <a name="examples"></a>Exemplos

A tabela seguinte inclui alguns exemplos de filtros comuns.  

| Nome do Campo | Consulta para valores | Etiqueta |
|:--|:--|:--|
| Computador   | Heartbeat &#124; computador distinto &#124; ordenar por computador asc | Computadores |
| EventLevelName | Evento &#124; EventLevelName distinto | Gravidade |
| SeverityLevel | Syslog &#124; SeverityLevel distinto | Gravidade |
| SvcChangeType | ConfigurationChange &#124; svcChangeType distinto | ChangeType |


## <a name="modify-view-queries"></a>Modificar as consultas de vista

Para um filtro para ter qualquer efeito, tem de modificar qualquer consulta na vista para filtrar os valores selecionados.  Se não modificar quaisquer consultas na vista de, em seguida, quaisquer valores que o utilizador seleciona não terá efeito.

A sintaxe para usar um valor de filtro numa consulta é: 

    where ${filter name}  

Por exemplo, se a sua vista tem uma consulta os eventos de devolve e utiliza um filtro chamado computadores, poderia usar o seguinte.

    Event | where ${Computers} | summarize count() by EventLevelName

Se adicionar outro filtro denominado severidade, pode utilizar a seguinte consulta para utilizar ambos os filtros.

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre o [partes de visualização](view-designer-parts.md) pode adicionar à sua vista personalizada.
