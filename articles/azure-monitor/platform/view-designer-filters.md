---
title: Filtros nas vistas do Monitor Azure Microsoft Docs
description: Um filtro numa vista do Azure Monitor permite que os utilizadores filtram os dados na vista pelo valor de uma determinada propriedade sem modificar a vista em si.  Este artigo descreve como usar um filtro e adicionar um a uma vista personalizada.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/22/2018
ms.openlocfilehash: d428382493e15d2e0571f4cb4b6f090cf9056fe4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449307"
---
# <a name="filters-in-azure-monitor-views"></a>Filtros nas vistas do Monitor Azure
Um **filtro** numa [vista do Azure Monitor](view-designer.md) permite que os utilizadores filtram os dados na vista pelo valor de uma determinada propriedade sem modificar a vista em si.  Por exemplo, pode permitir que os utilizadores da sua visão filtram a vista de dados apenas a partir de um determinado computador ou conjunto de computadores.  Pode criar vários filtros numa única vista para permitir que os utilizadores filtrem por várias propriedades.  Este artigo descreve como usar um filtro e adicionar um a uma vista personalizada.

## <a name="using-a-filter"></a>Usando um filtro
Clique no intervalo de tempo de data na parte superior de uma vista para abrir a queda onde pode alterar o intervalo de tempo de data para a vista.

![Screenshot do menu de dropdown da gama tempo para uma visualização no Azure Monitor, mostrando o botão de rádio para os últimos 7 dias selecionados.](media/view-designer-filters/filters-example-time.png)

Clique no **+** para adicionar um filtro utilizando filtros personalizados definidos para a vista. Selecione um valor para o filtro a partir do dropdown ou escreva um valor. Continue a adicionar filtros clicando no **+** . 


![Screenshot do diálogo para adicionar um filtro personalizado no Azure Monitor. A propriedade Computers está sendo selecionada no menu select property dropdown.](media/view-designer-filters/filters-example-custom.png)

Se remover todos os valores de um filtro, o filtro deixará de ser aplicado.


## <a name="creating-a-filter"></a>Criação de um filtro

Crie um filtro a partir do **separador Filtros** ao [editar uma vista](view-designer.md).  O filtro é global para a vista e aplica-se a todas as partes da vista.  

![Definições de filtro](media/view-designer-filters/filters-settings.png)

A tabela seguinte descreve as definições de um filtro.

| Definições | Descrição |
|:---|:---|
| Nome do Campo | Nome do campo utilizado para filtragem.  Este campo deve corresponder ao campo resumida em **Consulta de Valores**. |
| Consulta de Valores | Consulta para executar para preencher o recuo do filtro para o utilizador.  Esta consulta deve ser utilizada em [resumo](/azure/kusto/query/summarizeoperator) ou [distinto](/azure/kusto/query/distinctoperator) para fornecer valores únicos para um determinado campo, e deve corresponder ao Nome de **Campo**.  Pode utilizar [o sorteio](/azure/kusto/query/sortoperator) para classificar os valores que são apresentados ao utilizador. |
| Etiqueta | Nome para o campo que é utilizado em consultas de suporte ao filtro e também é apresentado ao utilizador. |

### <a name="examples"></a>Exemplos

A tabela a seguir inclui alguns exemplos de filtros comuns.  

| Nome do Campo | Consulta de Valores | Etiqueta |
|:--|:--|:--|
| Computador   | Batimento cardíaco &#124; tipo de &#124; de computador distinto por computador asc | Computadores |
| EventoLevelName | Evento &#124; distinto EventLevelName | Gravidade |
| SeveridadeLevel | Syslog &#124; SeverityLevel distinto | Gravidade |
| SvcChangeType | ConfiguraçãoChange &#124; svcChangeType distinto | AlterarType |


## <a name="modify-view-queries"></a>Modificar consultas de visualização

Para que um filtro tenha algum efeito, deve modificar quaisquer consultas na vista para filtrar os valores selecionados.  Se não modificar quaisquer consultas na vista, então quaisquer valores que o utilizador selecione não terão qualquer efeito.

A sintaxe para a utilização de um valor de filtro numa consulta é: 

`where ${filter name}`  

Por exemplo, se a sua visão tiver uma consulta que retorne eventos e utilize um filtro chamado _Computadores,_ poderá utilizar a seguinte consulta.

```kusto
Event | where ${Computers} | summarize count() by EventLevelName
```

Se adicionar outro filtro chamado Severity, pode utilizar a seguinte consulta para utilizar ambos os filtros.

```kusto
Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName
```

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre as [Peças de Visualização](view-designer-parts.md) que pode adicionar à sua vista personalizada.
