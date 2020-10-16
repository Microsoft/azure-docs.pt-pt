---
title: Funções de transformação de fluxo de dados em Azure Data Factory
description: Uma visão geral das funções de fluxo de dados disponíveis na Azure Data Factory
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: e63c3f329cb9c1fd5ca91274540f5145c3ad098a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85921558"
---
# <a name="transformation-functions-in-wrangling-data-flow"></a>Funções de transformação no fluxo de dados de estrangulamento

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

O fluxo de dados de estrangulamento na Azure Data Factory permite-lhe fazer a preparação de dados ágeis sem código e a luta à escala de nuvens. O fluxo de dados de estrangulamento integra-se com [a Power Query Online](https://docs.microsoft.com/powerquery-m/power-query-m-reference) e disponibiliza funções de Power Query M para a execução de dados através da execução de faíscas. 

> [!NOTE]
> O fluxo de dados de estrangulamento é atualmente avilável na visualização pública

Atualmente nem todas as funções power query M são suportadas para a luta de dados, apesar de estarem disponíveis durante a autoria. Ao construir os fluxos de dados de luta, será solicitado com a seguinte mensagem de erro se uma função não for suportada:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Abaixo está uma lista de funções de Consulta de Energia M suportadas.

## <a name="column-management"></a>Gestão de Colunas

* Seleção: [Table.SelectColumns](https://docs.microsoft.com/powerquery-m/table-selectcolumns)
* Remoção: [Tabela.RemoveColumns](https://docs.microsoft.com/powerquery-m/table-removecolumns)
* Renomeação: [Table.RenameColumns,](https://docs.microsoft.com/powerquery-m/table-renamecolumns) [Table.PrefixColumns,](https://docs.microsoft.com/powerquery-m/table-prefixcolumns) [Table.TransformColumnNames](https://docs.microsoft.com/powerquery-m/table-transformcolumnnames)
* Reordenamento: [Tabela.ReorderColumns](https://docs.microsoft.com/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>Filtragem de linha

Utilizar tabela de funções [M.SelectRows](https://docs.microsoft.com/powerquery-m/table-selectrows) para filtrar nas seguintes condições:

* Igualdade e desigualdade
* Comparações numéricas, textos e datas (mas não datatime)
* Informações numéricas tais como [Number.IsEven](https://docs.microsoft.com/powerquery-m/number-iseven) / [Odd](https://docs.microsoft.com/powerquery-m/number-iseven)
* Contenção de texto usando [texto.Contém](https://docs.microsoft.com/powerquery-m/text-contains), [Texto.Começa Com](https://docs.microsoft.com/powerquery-m/text-startswith), ou [Texto.TerminaCom](https://docs.microsoft.com/powerquery-m/text-endswith)
* Intervalos de data, incluindo todas as [funções](https://docs.microsoft.com/powerquery-m/date-functions)'IsIn' Date ) 
* Combinações destes usando e, ou, ou não condições

## <a name="adding-and-transforming-columns"></a>Adicionar e Transformar Colunas

As seguintes funções M adicionam ou transformam colunas: [Table.AddColumn](https://docs.microsoft.com/powerquery-m/table-addcolumn), [Table.TransformColumns,](https://docs.microsoft.com/powerquery-m/table-transformcolumns) [Table.ReplaceValue](https://docs.microsoft.com/powerquery-m/table-replacevalue), [Table.DuplicateColumn](https://docs.microsoft.com/powerquery-m/table-duplicatecolumn). Abaixo estão as funções de transformação suportadas.

* Aritmética numérica
* Concatenação de texto
* Data e Aritmética Tempo (operadores aritméticos, [Date.AddDays](https://docs.microsoft.com/powerquery-m/date-adddays), [Date.AddMonths](https://docs.microsoft.com/powerquery-m/date-addmonths), [Date.AddQuarters](https://docs.microsoft.com/powerquery-m/date-addquarters), [Date.AddWeeks](https://docs.microsoft.com/powerquery-m/date-addweeks), [Date.AddYears](https://docs.microsoft.com/powerquery-m/date-addyears))
* As durações podem ser utilizadas para a aritmética de data e hora, mas devem ser transformadas noutro tipo antes de serem escritas para um lavatório (operadores aritméticos, [#duration,](https://docs.microsoft.com/powerquery-m/sharpduration) [duração.Dias,](https://docs.microsoft.com/powerquery-m/duration-days) [Duração.Duração,Duração.Minutos,](https://docs.microsoft.com/powerquery-m/duration-minutes) [Duration.Hours](https://docs.microsoft.com/powerquery-m/duration-hours) [Duração.Dias](https://docs.microsoft.com/powerquery-m/duration-seconds) [Totais,](https://docs.microsoft.com/powerquery-m/duration-totaldays) [Duração.Total Dehoras,](https://docs.microsoft.com/powerquery-m/duration-totalhours) [Duração.TotalSegundos](https://docs.microsoft.com/powerquery-m/duration-totalseconds)) [Duration.TotalMinutes](https://docs.microsoft.com/powerquery-m/duration-totalminutes)    
* A maioria das funções numéricas padrão, científicas e trigonométricas (Todas as funções em [Operações,](https://docs.microsoft.com/powerquery-m/number-functions#operations) [Arredondamento](https://docs.microsoft.com/powerquery-m/number-functions#rounding)e [Trigonometria,](https://docs.microsoft.com/powerquery-m/number-functions#trigonometry) *com exceção* do Número.Fatorial, Número.Permutações e Número.Combinações)
* Substituição[(Replacer.ReplaceText,](https://docs.microsoft.com/powerquery-m/replacer-replacetext) [Replacer.ReplaceValue,](https://docs.microsoft.com/powerquery-m/replacer-replacevalue) [Text.Replace,](https://docs.microsoft.com/powerquery-m/text-replace) [Text.Remove](https://docs.microsoft.com/powerquery-m/text-remove))
* Extração de texto posicional[(Text.PositionOf](https://docs.microsoft.com/powerquery-m/text-positionof), [Text.Length](https://docs.microsoft.com/powerquery-m/text-length), [Text.Start](https://docs.microsoft.com/powerquery-m/text-start), [Text.End](https://docs.microsoft.com/powerquery-m/text-end), [Text.Middle](https://docs.microsoft.com/powerquery-m/text-middle), [Text.ReplaceRange](https://docs.microsoft.com/powerquery-m/text-replacerange), [Text.RemoveRange](https://docs.microsoft.com/powerquery-m/text-removerange))
* Formatação básica de texto[(Text.Lower](https://docs.microsoft.com/powerquery-m/text-lower), [Text.Upper](https://docs.microsoft.com/powerquery-m/text-upper), [Text.Trim](https://docs.microsoft.com/powerquery-m/text-trim) / [Start](https://docs.microsoft.com/powerquery-m/text-trimstart) / [End](https://docs.microsoft.com/powerquery-m/text-trimend), [Text.PadStart](https://docs.microsoft.com/powerquery-m/text-padstart) / [End](https://docs.microsoft.com/powerquery-m/text-padend), [Text.Reverse](https://docs.microsoft.com/powerquery-m/text-reverse))
* Funções data/hora[(Data.Dia,](https://docs.microsoft.com/powerquery-m/date-day) [Data.Mês,](https://docs.microsoft.com/powerquery-m/date-month) [Data.Tempo do ano.Hora,](https://docs.microsoft.com/powerquery-m/date-year) [Time.Hour](https://docs.microsoft.com/powerquery-m/time-hour) [Hora.Minuto,](https://docs.microsoft.com/powerquery-m/time-minute) [Hora.Segundo,](https://docs.microsoft.com/powerquery-m/time-second) [Data.DayOfWeek](https://docs.microsoft.com/powerquery-m/date-dayofweek), [Data.DiaOfYear](https://docs.microsoft.com/powerquery-m/date-dayofyear), [Data.DiasInseth)](https://docs.microsoft.com/powerquery-m/date-daysinmonth)
* Se expressões (mas os ramos devem ter tipos de correspondência)
* Filtros de linha como uma coluna lógica
* Número, texto, lógica, data e data

<a name="mergingjoining-tables"></a>Tabelas de fusão/junção
----------------------
* A Power Query gerará uma junção aninhada (Table.NestedJoin; os utilizadores também podem escrever manualmente [Table.AddJoinColumn).](https://docs.microsoft.com/powerquery-m/table-addjoincolumn)
    Os utilizadores devem então expandir a coluna de união aninhada para uma junção não aninhada (Table.ExpandTableColumn, não suportado em nenhum outro contexto).
* A tabela de funções   [M.Se pode](https://docs.microsoft.com/powerquery-m/table-join) ser escrita diretamente para evitar a necessidade de um passo de expansão adicional, mas o utilizador deve garantir que não existem nomes de colunas duplicados entre as tabelas unidas
* Tipos de Junção Suportados:   [Interior,](https://docs.microsoft.com/powerquery-m/joinkind-inner)   [LeftOuter,](https://docs.microsoft.com/powerquery-m/joinkind-leftouter)   [RightOuter,](https://docs.microsoft.com/powerquery-m/joinkind-rightouter)   [FullOuter](https://docs.microsoft.com/powerquery-m/joinkind-fullouter)
* Tanto   [o Valor.Iguais como](https://docs.microsoft.com/powerquery-m/value-equals) o   [Valor.NullableEquals](https://docs.microsoft.com/powerquery-m/value-nullableequals) são suportados como principais comparadores de igualdade

## <a name="group-by"></a>Agrupar por

Utilize [o Quadro.Grupo](https://docs.microsoft.com/powerquery-m/table-group) para valores agregados.
* Deve ser usado com uma função de agregação
* Funções de agregação suportadas:   [Table.RowCount](https://docs.microsoft.com/powerquery-m/table-rowcount),   [List.Sum](https://docs.microsoft.com/powerquery-m/list-sum),   [List.Count](https://docs.microsoft.com/powerquery-m/list-count),   [List.Average](https://docs.microsoft.com/powerquery-m/list-average),   [List.Min](https://docs.microsoft.com/powerquery-m/list-min),   [List.Max](https://docs.microsoft.com/powerquery-m/list-max),   [List.StandardDeviation](https://docs.microsoft.com/powerquery-m/list-standarddeviation),   [List.First](https://docs.microsoft.com/powerquery-m/list-first),   [List.Last](https://docs.microsoft.com/powerquery-m/list-last)

## <a name="sorting"></a>Ordenação

Use [tabela.Ordenar](https://docs.microsoft.com/powerquery-m/table-sort) para classificar valores.

## <a name="reducing-rows"></a>Redução de Linhas

Manter e remover a parte superior, manter o alcance (funções M correspondentes, apenas as contagens de suporte, não as condições: [Tabela.FirstN,](https://docs.microsoft.com/powerquery-m/table-firstn) [Tabela.Skip,](https://docs.microsoft.com/powerquery-m/table-skip) [Tabela.RemoveFirstN,](https://docs.microsoft.com/powerquery-m/table-removefirstn) [Mesa.Range](https://docs.microsoft.com/powerquery-m/table-range), [Table.MinN](https://docs.microsoft.com/powerquery-m/table-minn), [Table.MaxN](https://docs.microsoft.com/powerquery-m/table-maxn))

## <a name="known-unsupported-functions"></a>Funções conhecidas não apoiadas

| Função | Estado |
| -- | -- |
| Table.PromoteHeaders | Não suportado. O mesmo resultado pode ser alcançado definindo "Primeira linha como cabeçalho" no conjunto de dados. |
| Table.CombineColumns | Este é um cenário comum que não é apoiado diretamente, mas que pode ser alcançado adicionando uma nova coluna que concatena duas colunas dadas.  Por exemplo, Table.AddColumn (RemoveEmailColumn, "Name", cada um [FirstName] & " & [LastName]) |
| Table.TransformColumnTypes | Isto é apoiado na maioria dos casos. Os seguintes cenários não são suportados: transformar a corda em tipo de moeda, transformar a corda em tipo de tempo, transformando a corda em percentagem. |
| Table.NestedJoin | Só fazer uma junção resultará num erro de validação. As colunas devem ser expandidas para que funcione. |
| Table.Distinct | A remoção de linhas duplicadas não está suportada. |
| Table.RemoveLastN | Remover as linhas inferiores não está suportada. |
| Table.RowCount | Não suportado, mas pode ser alcançado com uma coluna de adicionar com todas as células vazias (a coluna de condição pode ser usada) e, em seguida, usando o grupo por nessa coluna. O Grupo.Grupo é apoiado. | 
| Manipulação de erros de nível de linha | Atualmente, o tratamento de erros de nível de linha não está suportado. Por exemplo, para filtrar valores não numéricos de uma coluna, uma abordagem seria transformar a coluna de texto num número. Todas as células que não se transformam estarão num estado de erro e precisam de ser filtradas. Este cenário não é possível na discussão do fluxo de dados. |
| Table.Transpose | Não suportado |
| Table.Pivot | Não suportado |

## <a name="next-steps"></a>Passos seguintes

Saiba como [criar um fluxo de dados de estrangulamento.](wrangling-data-flow-tutorial.md)