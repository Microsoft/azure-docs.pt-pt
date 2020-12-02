---
title: Funções de transformação de fluxo de dados em Azure Data Factory
description: Uma visão geral das funções de fluxo de dados disponíveis na Azure Data Factory
author: dcstwh
ms.author: weetok
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 28c6228d4401db0fd1301137167a86cb6f18cbeb
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96494924"
---
# <a name="transformation-functions-in-wrangling-data-flow"></a>Funções de transformação no fluxo de dados de estrangulamento

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

O fluxo de dados de estrangulamento na Azure Data Factory permite-lhe fazer a preparação de dados ágeis sem código e a luta à escala de nuvens. O fluxo de dados de estrangulamento integra-se com [a Power Query Online](/powerquery-m/power-query-m-reference) e disponibiliza funções de Power Query M para a execução de dados através da execução de faíscas. 

> [!NOTE]
> O fluxo de dados de estrangulamento é atualmente avilável na visualização pública

Atualmente nem todas as funções power query M são suportadas para a luta de dados, apesar de estarem disponíveis durante a autoria. Ao construir os fluxos de dados de luta, será solicitado com a seguinte mensagem de erro se uma função não for suportada:

`The Wrangling Data Flow is invalid. Expression.Error: The transformation logic is not supported. Please try a simpler expression.`

Abaixo está uma lista de funções de Consulta de Energia M suportadas.

## <a name="column-management"></a>Gestão de Colunas

* Seleção: [Table.SelectColumns](/powerquery-m/table-selectcolumns)
* Remoção: [Tabela.RemoveColumns](/powerquery-m/table-removecolumns)
* Renomeação: [Table.RenameColumns,](/powerquery-m/table-renamecolumns) [Table.PrefixColumns,](/powerquery-m/table-prefixcolumns) [Table.TransformColumnNames](/powerquery-m/table-transformcolumnnames)
* Reordenamento: [Tabela.ReorderColumns](/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>Filtragem de linha

Utilizar tabela de funções [M.SelectRows](/powerquery-m/table-selectrows) para filtrar nas seguintes condições:

* Igualdade e desigualdade
* Comparações numéricas, textos e datas (mas não datatime)
* Informações numéricas tais como [Number.IsEven](/powerquery-m/number-iseven) / [Odd](/powerquery-m/number-iseven)
* Contenção de texto usando [texto.Contém](/powerquery-m/text-contains), [Texto.Começa Com](/powerquery-m/text-startswith), ou [Texto.TerminaCom](/powerquery-m/text-endswith)
* Intervalos de data, incluindo todas as [funções](/powerquery-m/date-functions)'IsIn' Date ) 
* Combinações destes usando e, ou, ou não condições

## <a name="adding-and-transforming-columns"></a>Adicionar e Transformar Colunas

As seguintes funções M adicionam ou transformam colunas: [Table.AddColumn](/powerquery-m/table-addcolumn), [Table.TransformColumns,](/powerquery-m/table-transformcolumns) [Table.ReplaceValue](/powerquery-m/table-replacevalue), [Table.DuplicateColumn](/powerquery-m/table-duplicatecolumn). Abaixo estão as funções de transformação suportadas.

* Aritmética numérica
* Concatenação de texto
* Data e Aritmética Tempo (operadores aritméticos, [Date.AddDays](/powerquery-m/date-adddays), [Date.AddMonths](/powerquery-m/date-addmonths), [Date.AddQuarters](/powerquery-m/date-addquarters), [Date.AddWeeks](/powerquery-m/date-addweeks), [Date.AddYears](/powerquery-m/date-addyears))
* As durações podem ser utilizadas para a aritmética de data e hora, mas devem ser transformadas noutro tipo antes de serem escritas para um lavatório (operadores aritméticos, [#duration,](/powerquery-m/sharpduration) [duração.Dias,](/powerquery-m/duration-days) [Duração.Duração,Duração.Minutos,](/powerquery-m/duration-minutes) [Duration.Hours](/powerquery-m/duration-hours) [Duração.Dias](/powerquery-m/duration-seconds) [Totais,](/powerquery-m/duration-totaldays) [Duração.Total Dehoras,](/powerquery-m/duration-totalhours) [Duração.TotalSegundos](/powerquery-m/duration-totalseconds)) [Duration.TotalMinutes](/powerquery-m/duration-totalminutes)    
* A maioria das funções numéricas padrão, científicas e trigonométricas (Todas as funções em [Operações,](/powerquery-m/number-functions#operations) [Arredondamento](/powerquery-m/number-functions#rounding)e [Trigonometria,](/powerquery-m/number-functions#trigonometry) *com exceção* do Número.Fatorial, Número.Permutações e Número.Combinações)
* Substituição[(Replacer.ReplaceText,](/powerquery-m/replacer-replacetext) [Replacer.ReplaceValue,](/powerquery-m/replacer-replacevalue) [Text.Replace,](/powerquery-m/text-replace) [Text.Remove](/powerquery-m/text-remove))
* Extração de texto posicional[(Text.PositionOf](/powerquery-m/text-positionof), [Text.Length](/powerquery-m/text-length), [Text.Start](/powerquery-m/text-start), [Text.End](/powerquery-m/text-end), [Text.Middle](/powerquery-m/text-middle), [Text.ReplaceRange](/powerquery-m/text-replacerange), [Text.RemoveRange](/powerquery-m/text-removerange))
* Formatação básica de texto[(Text.Lower](/powerquery-m/text-lower), [Text.Upper](/powerquery-m/text-upper), [Text.Trim](/powerquery-m/text-trim) / [Start](/powerquery-m/text-trimstart) / [End](/powerquery-m/text-trimend), [Text.PadStart](/powerquery-m/text-padstart) / [End](/powerquery-m/text-padend), [Text.Reverse](/powerquery-m/text-reverse))
* Funções data/hora[(Data.Dia,](/powerquery-m/date-day) [Data.Mês,](/powerquery-m/date-month) [Data.Tempo do ano.Hora,](/powerquery-m/date-year) [Time.Hour](/powerquery-m/time-hour) [Hora.Minuto,](/powerquery-m/time-minute) [Hora.Segundo,](/powerquery-m/time-second) [Data.DayOfWeek](/powerquery-m/date-dayofweek), [Data.DiaOfYear](/powerquery-m/date-dayofyear), [Data.DiasInseth)](/powerquery-m/date-daysinmonth)
* Se expressões (mas os ramos devem ter tipos de correspondência)
* Filtros de linha como uma coluna lógica
* Número, texto, lógica, data e data

<a name="mergingjoining-tables"></a>Tabelas de fusão/junção
----------------------
* A Power Query gerará uma junção aninhada (Table.NestedJoin; os utilizadores também podem escrever manualmente [Table.AddJoinColumn).](/powerquery-m/table-addjoincolumn)
    Os utilizadores devem então expandir a coluna de união aninhada para uma junção não aninhada (Table.ExpandTableColumn, não suportado em nenhum outro contexto).
* A tabela de funções   [M.Se pode](/powerquery-m/table-join) ser escrita diretamente para evitar a necessidade de um passo de expansão adicional, mas o utilizador deve garantir que não existem nomes de colunas duplicados entre as tabelas unidas
* Tipos de Junção Suportados:   [Interior,](/powerquery-m/joinkind-inner)   [LeftOuter,](/powerquery-m/joinkind-leftouter)   [RightOuter,](/powerquery-m/joinkind-rightouter)   [FullOuter](/powerquery-m/joinkind-fullouter)
* Tanto   [o Valor.Iguais como](/powerquery-m/value-equals) o   [Valor.NullableEquals](/powerquery-m/value-nullableequals) são suportados como principais comparadores de igualdade

## <a name="group-by"></a>Agrupar por

Utilize [o Quadro.Grupo](/powerquery-m/table-group) para valores agregados.
* Deve ser usado com uma função de agregação
* Funções de agregação suportadas:   [List.Sum](/powerquery-m/list-sum),   [List.Count](/powerquery-m/list-count),   [List.Average](/powerquery-m/list-average),   [List.Min](/powerquery-m/list-min),   [List.Max](/powerquery-m/list-max),   [List.StandardDeviation](/powerquery-m/list-standarddeviation),   [List.First](/powerquery-m/list-first),   [List.Last](/powerquery-m/list-last)

## <a name="sorting"></a>Ordenação

Use [tabela.Ordenar](/powerquery-m/table-sort) para classificar valores.

## <a name="reducing-rows"></a>Redução de Linhas

Manter e remover a parte superior, manter o alcance (funções M correspondentes, apenas as contagens de suporte, não as condições: [Tabela.FirstN,](/powerquery-m/table-firstn) [Tabela.Skip,](/powerquery-m/table-skip) [Tabela.RemoveFirstN,](/powerquery-m/table-removefirstn) [Mesa.Range](/powerquery-m/table-range), [Table.MinN](/powerquery-m/table-minn), [Table.MaxN](/powerquery-m/table-maxn))

## <a name="known-unsupported-functions"></a>Funções conhecidas não apoiadas

| Função | Estado |
| -- | -- |
| Table.PromoteHeaders | Não suportado. O mesmo resultado pode ser alcançado definindo "Primeira linha como cabeçalho" no conjunto de dados. |
| Table.CombineColumns | Este é um cenário comum que não é apoiado diretamente, mas que pode ser alcançado adicionando uma nova coluna que concatena duas colunas dadas.  Por exemplo, Table.AddColumn (RemoveEmailColumn, "Name", cada um [FirstName] & " & [LastName]) |
| Table.TransformColumnTypes | Isto é apoiado na maioria dos casos. Os seguintes cenários não são suportados: transformar a corda em tipo de moeda, transformar a corda em tipo de tempo, transformando a corda em percentagem. |
| Table.NestedJoin | Só fazer uma junção resultará num erro de validação. As colunas devem ser expandidas para que funcione. |
| Table.Distinct | A remoção de linhas duplicadas não está suportada. |
| Table.RemoveLastN | Remover as linhas inferiores não está suportada. |
| Table.RowCount | Não suportado, mas pode ser alcançado adicionando uma coluna personalizada contendo o valor 1, em seguida, agregando essa coluna com List.Sum. O Grupo.Grupo é apoiado. | 
| Manipulação de erros de nível de linha | Atualmente, o tratamento de erros de nível de linha não está suportado. Por exemplo, para filtrar valores não numéricos de uma coluna, uma abordagem seria transformar a coluna de texto num número. Todas as células que não se transformam estarão num estado de erro e precisam de ser filtradas. Este cenário não é possível na discussão do fluxo de dados. |
| Table.Transpose | Não suportado |
| Table.Pivot | Não suportado |

## <a name="next-steps"></a>Passos seguintes

Saiba como [criar um fluxo de dados de estrangulamento.](wrangling-data-flow-tutorial.md)
