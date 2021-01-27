---
title: Funções de conflito de dados na Azure Data Factory
description: Uma visão geral das funções disponíveis de Data Wrangling na Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: 659f6527d43e1b45a11fddf774050ca6d42bfe12
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896668"
---
# <a name="transformation-functions-in-power-query-for-data-wrangling"></a>Funções de transformação na Consulta de Poder para a luta de dados

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A disputa de dados na Azure Data Factory permite-lhe fazer a preparação de dados ágeis sem código e a luta à escala de nuvem, traduzindo scripts de consulta de energia ```M``` no script do Fluxo de Dados. A ADF integra-se com [a Power Query Online](/powerquery-m/power-query-m-reference) e disponibiliza funções de Consulta de Energia para a ```M``` execução de dados através da execução de Spark utilizando a infraestrutura de fluxo de dados Spark. 

> [!NOTE]
> A consulta de energia em ADF é atualmente avilável na pré-visualização pública

Atualmente nem todas as funções power query M são suportadas para a luta de dados, apesar de estarem disponíveis durante a autoria. Ao construir os seus mash-ups, será solicitado com a seguinte mensagem de erro se uma função não for suportada:

`UserQuery : Expression.Error: The transformation logic is not supported as it requires dynamic access to rows of data, which cannot be scaled out.`

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
* As durações podem ser utilizadas para a aritmética de data e hora, mas devem ser transformadas noutro tipo antes de serem escritas para um lavatório (operadores aritméticos, [#duration,](/powerquery-m/sharpduration) [duração.Dias,](/powerquery-m/duration-days) [Duração.Duração,Duração.Minutos,](/powerquery-m/duration-minutes) [](/powerquery-m/duration-hours) [Duração.Dias](/powerquery-m/duration-seconds) [Totais,](/powerquery-m/duration-totaldays) [Duração.Total Dehoras,](/powerquery-m/duration-totalhours) [Duração.TotalSegundos](/powerquery-m/duration-totalseconds)) [](/powerquery-m/duration-totalminutes)    
* A maioria das funções numéricas padrão, científicas e trigonométricas (Todas as funções em [Operações,](/powerquery-m/number-functions#operations) [Arredondamento](/powerquery-m/number-functions#rounding)e [Trigonometria,](/powerquery-m/number-functions#trigonometry) *com exceção* do Número.Fatorial, Número.Permutações e Número.Combinações)
* Substituição[(Replacer.ReplaceText,](/powerquery-m/replacer-replacetext) [Replacer.ReplaceValue,](/powerquery-m/replacer-replacevalue) [Text.Replace,](/powerquery-m/text-replace) [Text.Remove](/powerquery-m/text-remove))
* Extração de texto posicional[(Text.PositionOf](/powerquery-m/text-positionof), [Text.Length](/powerquery-m/text-length), [Text.Start](/powerquery-m/text-start), [Text.End](/powerquery-m/text-end), [Text.Middle](/powerquery-m/text-middle), [Text.ReplaceRange](/powerquery-m/text-replacerange), [Text.RemoveRange](/powerquery-m/text-removerange))
* Formatação básica de texto[(Text.Lower](/powerquery-m/text-lower), [Text.Upper](/powerquery-m/text-upper), [Text.Trim](/powerquery-m/text-trim) / [Start](/powerquery-m/text-trimstart) / [End](/powerquery-m/text-trimend), [Text.PadStart](/powerquery-m/text-padstart) / [End](/powerquery-m/text-padend), [Text.Reverse](/powerquery-m/text-reverse))
* Funções data/hora[(Data.Dia,](/powerquery-m/date-day) [Data.Mês,](/powerquery-m/date-month) [Data.Tempo do ano.Hora,](/powerquery-m/date-year) [](/powerquery-m/time-hour) [Hora.Minuto,](/powerquery-m/time-minute) [Hora.Segundo,](/powerquery-m/time-second) [Data.DayOfWeek](/powerquery-m/date-dayofweek), [Data.DiaOfYear](/powerquery-m/date-dayofyear), [Data.DiasInseth)](/powerquery-m/date-daysinmonth)
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
| Manipulação de erros de nível de linha | Atualmente, o tratamento de erros de nível de linha não está suportado. Por exemplo, para filtrar valores não numéricos de uma coluna, uma abordagem seria transformar a coluna de texto num número. Todas as células que não se transformam estarão num estado de erro e precisam de ser filtradas. Este cenário não é possível em M escalado. |
| Table.Transpose | Não suportado |
| Table.Pivot | Não suportado |

## <a name="next-steps"></a>Passos seguintes

Saiba como [criar uma consulta de energia de conflito de dados em ADF](wrangling-tutorial.md).
