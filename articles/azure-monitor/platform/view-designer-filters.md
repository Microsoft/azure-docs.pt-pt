---
title: Filtros em vistas do Monitor Azure / Microsoft Docs
description: Um filtro numa vista Do Monitor Azure permite que os utilizadores filtram os dados à vista pelo valor de uma determinada propriedade sem modificar a própria vista.  Este artigo descreve como usar um filtro e adicionar um a uma vista personalizada.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/22/2018
ms.openlocfilehash: b4840ed30eb1f6dc8d6e6cef47da17807f9644d5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77658579"
---
# <a name="filters-in-azure-monitor-views"></a>Filtros nas vistas do Monitor Azure
Um **filtro** numa [vista Do Monitor Azure](view-designer.md) permite que os utilizadores filtram os dados à vista pelo valor de uma determinada propriedade sem modificar a própria vista.  Por exemplo, pode permitir que os utilizadores da sua visão filtram a vista para dados apenas a partir de um determinado computador ou conjunto de computadores.  Pode criar vários filtros numa única vista para permitir que os utilizadores filtram por várias propriedades.  Este artigo descreve como usar um filtro e adicionar um a uma vista personalizada.

## <a name="using-a-filter"></a>Usando um filtro
Clique na faixa de hora da data no topo de uma vista para abrir a gota para baixo onde pode alterar o intervalo de hora de data para a vista.

![Exemplo de filtro](media/view-designer-filters/filters-example-time.png)

Clique **+** no filtro para adicionar um filtro utilizando filtros personalizados que estão definidos para a vista. Selecione um valor para o filtro a partir do dropdown ou escreva num valor. Continue a adicionar filtros **+** clicando no . 


![Exemplo de filtro](media/view-designer-filters/filters-example-custom.png)

Se remover todos os valores para um filtro, então esse filtro deixará de ser aplicado.


## <a name="creating-a-filter"></a>Criar um filtro

Crie um filtro a partir do separador **Filtros** ao [editar uma vista](view-designer.md).  O filtro é global para a vista e aplica-se a todas as partes da vista.  

![Definições de filtro](media/view-designer-filters/filters-settings.png)

A tabela seguinte descreve as definições para um filtro.

| Definição | Descrição |
|:---|:---|
| Nome do Campo | Nome do campo utilizado para filtrar.  Este campo deve coincidir com o campo de resumo em **Consulta de Valores**. |
| Consulta de Valores | Consulta para executar para povoar a queda do filtro para o utilizador.  Esta consulta deve utilizar [resumo](/azure/kusto/query/summarizeoperator) ou [distinto](/azure/kusto/query/distinctoperator) para fornecer valores únicos para um determinado campo, e deve corresponder ao Nome de **Campo**.  Pode utilizar [o tipo](/azure/kusto/query/sortoperator) para classificar os valores apresentados ao utilizador. |
| Etiqueta | Nome para o campo que é usado em consultas de suporte ao filtro e também é apresentado ao utilizador. |

### <a name="examples"></a>Exemplos

A tabela seguinte inclui alguns exemplos de filtros comuns.  

| Nome do Campo | Consulta de Valores | Etiqueta |
|:--|:--|:--|
| Computador   | Batimento cardíaco &#124; &#124; de computador distinto saem por computador asc | Computadores |
| Nome de nível de evento | Evento &#124; nome distinto do EventLevelName | Gravidade |
| Nível de gravidade | Syslog &#124; nível distinto de Gravidade | Gravidade |
| SvcChangeType | ConfiguraçãoMudar &#124; svcChangeType distinto | Tipo de mudança |


## <a name="modify-view-queries"></a>Modificar consultas de visualização

Para que um filtro tenha qualquer efeito, deve modificar quaisquer consultas na vista para filtrar os valores selecionados.  Se não modificar quaisquer dúvidas na visualização, quaisquer valores que o utilizador selecione não terão qualquer efeito.

A sintaxe para a utilização de um valor de filtro numa consulta é: 

    where ${filter name}  

Por exemplo, se a sua visão tiver uma consulta que desliga eventos e usa um filtro chamado _Computadores,_ pode utilizar a seguinte consulta.

    Event | where ${Computers} | summarize count() by EventLevelName

Se adicionar outro filtro chamado Severity, pode utilizar a seguinte consulta para utilizar ambos os filtros.

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre as Peças de [Visualização](view-designer-parts.md) que pode adicionar à sua visão personalizada.
