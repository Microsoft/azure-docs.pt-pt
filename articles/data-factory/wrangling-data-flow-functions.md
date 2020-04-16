---
title: Funções de transformação de fluxo de dados em Azure Data Factory
description: Uma visão geral das funções de fluxo de dados disponíveis na Azure Data Factory
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 7235e95e5b33fb931411a51796a8dbec96c46355
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417662"
---
# <a name="transformation-functions-in-wrangling-data-flow"></a>Funções de transformação na discussão do fluxo de dados

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

O fluxo de dados em Azure Data Factory permite-lhe fazer a preparação de dados ágeis sem código e a discussão à escala de nuvem. O fluxo de dados de contorcer-se integra-se com a [Power Query Online](https://docs.microsoft.com/powerquery-m/power-query-m-reference) e disponibiliza funções de Power Query M para a disputa de dados através da execução de faíscas. 

Atualmente, nem todas as funções power query M são suportadas para a discussão de dados, apesar de estarem disponíveis durante a autoria. Ao construir os fluxos de dados de sangling, será solicitado com a seguinte mensagem de erro se uma função não for suportada:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Abaixo está uma lista de funções suportadas de Power Query M.

## <a name="column-management"></a>Gestão de Colunas

* Seleção: [Tabela.SelectColumns](https://docs.microsoft.com/powerquery-m/table-selectcolumns)
* Remoção: [Tabela.Remover Colunas](https://docs.microsoft.com/powerquery-m/table-removecolumns)
* Renomeação: [Tabela.RenameColumns,](https://docs.microsoft.com/powerquery-m/table-renamecolumns) [Tabela.PrefixColumns,](https://docs.microsoft.com/powerquery-m/table-prefixcolumns) [Tabela.TransformColumnNames](https://docs.microsoft.com/powerquery-m/table-transformcolumnnames)
* Reordenação: [Table.ReorderColumns](https://docs.microsoft.com/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>Filtragem da linha

Utilize tabela de funções [M.SelectRows](https://docs.microsoft.com/powerquery-m/table-selectrows) para filtrar nas seguintes condições:

* Igualdade e desigualdade
* Comparações numéricas, texto e data (mas não data)
* Informação numérica como [Número.IsEven](https://docs.microsoft.com/powerquery-m/number-iseven)/[Odd](https://docs.microsoft.com/powerquery-m/number-iseven)
* Contenção de texto usando [texto.Contém,](https://docs.microsoft.com/powerquery-m/text-contains) [Texto.ComeçaCom](https://docs.microsoft.com/powerquery-m/text-startswith), ou [Texto.TerminaCom](https://docs.microsoft.com/powerquery-m/text-endswith)
* Intervalos de datas, incluindo todas as funções 'IsIn' [Date)](https://docs.microsoft.com/powerquery-m/date-functions) 
* Combinações destes usando e, ou, ou não condições

## <a name="adding-and-transforming-columns"></a>Adicionar e transformar Colunas

As seguintes funções M adicionam ou transformam colunas: [Tabela.AddColumn,](https://docs.microsoft.com/powerquery-m/table-addcolumn) [Tabela.TransformColumns,](https://docs.microsoft.com/powerquery-m/table-transformcolumns) [Tabela.ReplaceValue](https://docs.microsoft.com/powerquery-m/table-replacevalue), [Tabela.DuplicateColumn](https://docs.microsoft.com/powerquery-m/table-duplicatecolumn). Abaixo estão as funções de transformação suportadas.

* Aritmética numérica
* Concatenação de texto
* Data e Tempo Aritmético (operadores aritméticos, [Data.AddDays](https://docs.microsoft.com/powerquery-m/date-adddays), [Date.AddMonths](https://docs.microsoft.com/powerquery-m/date-addmonths), [Date.AddQuarters,](https://docs.microsoft.com/powerquery-m/date-addquarters) [Date.AddWeeks, Date.AddWeeks,](https://docs.microsoft.com/powerquery-m/date-addweeks) [Date.AddYears](https://docs.microsoft.com/powerquery-m/date-addyears))
* As durações podem ser utilizadas para a aritmética da data e da hora, mas devem ser transformadas noutro tipo antes de serem escritas num lavatório (operadores aritméticos, [#duration,](https://docs.microsoft.com/powerquery-m/sharpduration) [Duração.Dias,](https://docs.microsoft.com/powerquery-m/duration-days) [Duração.Horas,](https://docs.microsoft.com/powerquery-m/duration-hours) [Duração.Minutos,](https://docs.microsoft.com/powerquery-m/duration-minutes) [Duração.Segundos,](https://docs.microsoft.com/powerquery-m/duration-seconds) [Duração.TotalDays,](https://docs.microsoft.com/powerquery-m/duration-totaldays) [Duração.TotalHoras,](https://docs.microsoft.com/powerquery-m/duration-totalhours) [Duração.TotalMinutes,](https://docs.microsoft.com/powerquery-m/duration-totalminutes) [Duração.TotalSeconds](https://docs.microsoft.com/powerquery-m/duration-totalseconds))    
* Funções numéricas mais standard, científica e trigonométrica (todas as funções em [operações,](https://docs.microsoft.com/powerquery-m/number-functions#operations) [arredondamento](https://docs.microsoft.com/powerquery-m/number-functions#rounding)e [trigonometria](https://docs.microsoft.com/powerquery-m/number-functions#trigonometry) *exceto* Número.Fatorial, Número.Permutações e Número.Combinações)
* Substituição ([Replacer.ReplaceText,](https://docs.microsoft.com/powerquery-m/replacer-replacetext) [Replacer.ReplaceValue,](https://docs.microsoft.com/powerquery-m/replacer-replacevalue) [Text.Replace,](https://docs.microsoft.com/powerquery-m/text-replace) [Text.Remove](https://docs.microsoft.com/powerquery-m/text-remove))
* Extração de texto posicional ([Text.PositionOf,](https://docs.microsoft.com/powerquery-m/text-positionof) [Text.Length](https://docs.microsoft.com/powerquery-m/text-length), [Text.Start,](https://docs.microsoft.com/powerquery-m/text-start) [Text.End](https://docs.microsoft.com/powerquery-m/text-end), [Text.Middle,](https://docs.microsoft.com/powerquery-m/text-middle) [Text.ReplaceRange,](https://docs.microsoft.com/powerquery-m/text-replacerange) [Text.RemoveRange](https://docs.microsoft.com/powerquery-m/text-removerange))
* Formatação de texto básica[(Text.Lower,](https://docs.microsoft.com/powerquery-m/text-lower) [Text.Upper](https://docs.microsoft.com/powerquery-m/text-upper), [Text.Trim](https://docs.microsoft.com/powerquery-m/text-trim)/[Start](https://docs.microsoft.com/powerquery-m/text-trimstart)/[End,](https://docs.microsoft.com/powerquery-m/text-trimend) [Text.PadStart](https://docs.microsoft.com/powerquery-m/text-padstart)/[End,](https://docs.microsoft.com/powerquery-m/text-padend) [Text.Reverse](https://docs.microsoft.com/powerquery-m/text-reverse))
* Funções de data/hora ([Data.Dia,](https://docs.microsoft.com/powerquery-m/date-day) [Data.Mês](https://docs.microsoft.com/powerquery-m/date-month), [Data.Hora do](https://docs.microsoft.com/powerquery-m/date-year) [Ano.Hora,](https://docs.microsoft.com/powerquery-m/time-hour) [Hora.Minuto,](https://docs.microsoft.com/powerquery-m/time-minute) [Hora.Segunda,](https://docs.microsoft.com/powerquery-m/time-second) [Data.DiaDa,](https://docs.microsoft.com/powerquery-m/date-dayofweek) [Data.DayOfYear,](https://docs.microsoft.com/powerquery-m/date-dayofyear) [Data.DaysInMonth](https://docs.microsoft.com/powerquery-m/date-daysinmonth))
* Se expressões (mas ramos devem ter tipos correspondentes)
* Filtros de linha como uma coluna lógica
* Número, texto, lógica, data e data constantes

<a name="mergingjoining-tables"></a>Mesas de fusão/de junção
----------------------
* A Power Query gerará uma adesão aninhada (Tabela.NestedJoin; os utilizadores também podem escrever manualmente [Table.AddJoinColumn](https://docs.microsoft.com/powerquery-m/table-addjoincolumn)).
    Os utilizadores devem então expandir a coluna de adesão aninhada para uma união não aninhada (Tabela.ExpanstableColumn, não suportada em qualquer outro contexto).
* A tabela de funções [M.Join](https://docs.microsoft.com/powerquery-m/table-join) pode ser escrita diretamente para evitar a necessidade de um passo adicional de expansão, mas o utilizador deve garantir que não existem nomes de colunas duplicados entre as tabelas unidas
* Suporte Sintetizado: [Interior,](https://docs.microsoft.com/powerquery-m/joinkind-inner) [LeftOuter,](https://docs.microsoft.com/powerquery-m/joinkind-leftouter) [RightOuter](https://docs.microsoft.com/powerquery-m/joinkind-rightouter), [FullOuter](https://docs.microsoft.com/powerquery-m/joinkind-fullouter)
* Tanto [o Valor.Iguala o](https://docs.microsoft.com/powerquery-m/value-equals) [Valor.NullableEquals](https://docs.microsoft.com/powerquery-m/value-nullableequals) são suportados como principais comparadores de igualdade

## <a name="group-by"></a>Agrupar por

Use [o Quadro.Group](https://docs.microsoft.com/powerquery-m/table-group) para agregar valores.
* Deve ser usado com uma função de agregação
* Funções de agregação suportadas: [Table.RowCount](https://docs.microsoft.com/powerquery-m/table-rowcount), [List.Sum](https://docs.microsoft.com/powerquery-m/list-sum), [List.Count](https://docs.microsoft.com/powerquery-m/list-count), [List.Average](https://docs.microsoft.com/powerquery-m/list-average), [List.Min](https://docs.microsoft.com/powerquery-m/list-min), [List.Max,](https://docs.microsoft.com/powerquery-m/list-max) [List.StandardDeviation](https://docs.microsoft.com/powerquery-m/list-standarddeviation), [List.First,](https://docs.microsoft.com/powerquery-m/list-first) [List.Last](https://docs.microsoft.com/powerquery-m/list-last)

## <a name="sorting"></a>Ordenação

Use [a Tabela.Ordenar](https://docs.microsoft.com/powerquery-m/table-sort) para classificar valores.

## <a name="reducing-rows"></a>Redução de linhas

Manter e remover top, manter gama (funções M correspondentes, apenas contagens de suporte, não condições: [Tabela.FirstN,](https://docs.microsoft.com/powerquery-m/table-firstn) [Tabela.Skip,](https://docs.microsoft.com/powerquery-m/table-skip) [Tabela.RemoveFirstN,](https://docs.microsoft.com/powerquery-m/table-removefirstn) [Tabela.Gama,](https://docs.microsoft.com/powerquery-m/table-range) [Tabela.MinN,](https://docs.microsoft.com/powerquery-m/table-minn) [Tabela.MaxN](https://docs.microsoft.com/powerquery-m/table-maxn))

## <a name="known-unsupported-functions"></a>Funções conhecidas não apoiadas

| Função | Estado |
| -- | -- |
| Table.PromoteHeaders | Não suportado. O mesmo resultado pode ser alcançado definindo "Primeira linha como cabeçalho" no conjunto de dados. |
| Table.CombineColumns | Este é um cenário comum que não é suportado diretamente mas que pode ser alcançado adicionando uma nova coluna que concatena duas colunas.  Por exemplo, Tabela.AddColumn (RemoverEmailColumn, "Name", cada & " & [Apelido]) |
| Table.TransformColumnTypes | Isto é apoiado na maioria dos casos. Os seguintes cenários não são suportados: transformar a cadeia em tipo de moeda, transformar a corda para o tipo de tempo, transformar a cadeia em tipo Percentual. |
| Table.NestedJoin | Só fazer uma adesão resultará num erro de validação. As colunas devem ser expandidas para que funcione. |
| Table.Distinct | Remover as linhas duplicadas não é suportado. |
| Table.RemoveLastN | Remova as linhas inferiores não é suportada. |
| Table.RowCount | Não suportado, mas pode ser alcançado com uma coluna de adição com todas as células vazias (coluna de condição pode ser usada) e, em seguida, usando o grupo por nessa coluna. O Grupo table.group é apoiado. | 
| Manipulação de erro de nível de linha | O manuseamento de erros de nível de linha não é suportado atualmente. Por exemplo, para filtrar valores não numéricos de uma coluna, uma abordagem seria transformar a coluna de texto num número. Todas as células que não se transformarem estarão num estado de erro e precisam de ser filtradas. Este cenário não é possível na confusão do fluxo de dados. |
| Table.Transpose | Não suportado |
| Table.Pivot | Não suportado |

## <a name="next-steps"></a>Passos seguintes

Aprenda a criar um fluxo de [dados em discussão.](wrangling-data-flow-tutorial.md)