---
title: Recomendações de desempenho
description: O banco de dados SQL do Azure fornece recomendações para seus bancos de dados SQL que podem melhorar o desempenho de consulta atual.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik
ms.date: 11/12/2019
ms.openlocfilehash: a113ea3fd4828a498d1f53ea7604df7bc8588eb5
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048413"
---
# <a name="performance-recommendations-for-sql-database"></a>Recomendações de desempenho para o banco de dados SQL

O banco de dados SQL do Azure aprende e se adapta ao seu aplicativo. Ele fornece recomendações personalizadas que permitem que você maximize o desempenho de seus bancos de dados SQL. O banco de dados SQL avalia e analisa continuamente o histórico de uso de seus bancos de dados SQL. As recomendações fornecidas são baseadas em padrões de carga de trabalho exclusivos de banco de dados e ajudam a melhorar o desempenho.

> [!TIP]
> O [ajuste automático](sql-database-automatic-tuning.md) é o método recomendado para ajustar automaticamente alguns dos problemas de desempenho de banco de dados mais comuns. As [informações de desempenho de consulta](sql-database-query-performance.md) são o método recomendado para as necessidades básicas de monitoramento de desempenho do banco de dados SQL do Azure. [Análise de SQL do Azure](../azure-monitor/insights/azure-sql.md) é o método recomendado para o monitoramento avançado do desempenho do banco de dados em grande escala, com inteligência interna para a solução de problemas de desempenho automatizado.
>

## <a name="performance-recommendations-options"></a>Opções de recomendações de desempenho

As opções de recomendação de desempenho disponíveis do banco de dados SQL do Azure são:

| Recomendação de desempenho | Banco de dados individual e suporte a banco de dados em pool | Suporte a banco de dados de instância |
| :----------------------------- | ----- | ----- |
| **Criar recomendações de índice** – recomenda a criação de índices que podem melhorar o desempenho da carga de trabalho. | Sim | Não | 
| **Recomendações de drop index** – recomenda a remoção de índices redundantes e duplicados diariamente, exceto índices exclusivos, e índices que não foram usados por um longo período (> 90 dias). Observe que essa opção não é compatível com aplicativos que usam a alternância de partição e dicas de índice. Não há suporte para a remoção de índices não utilizados para as camadas de serviço Premium e Comercialmente Crítico. | Sim | Não |
| **Parametrizar consultas recomendações (visualização)** – recomenda parametrização forçadas em casos em que você tem uma ou mais consultas que estão constantemente sendo recompiladas, mas terminam com o mesmo plano de execução de consulta. | Sim | Não |
| **Corrigir recomendações de problemas de esquema (versão prévia)** – as recomendações para correção de esquema aparecem quando o serviço do banco de dados SQL observa uma anomalia no número de erros SQL relacionados ao esquema que estão ocorrendo no banco de dados SQL. Atualmente, a Microsoft está preterindo recomendações "corrigir problema de esquema". | Sim | Não |

## <a name="create-index-recommendations"></a>Criar recomendações de índice
O banco de dados SQL monitora continuamente as consultas que estão em execução e identifica os índices que podem melhorar o desempenho. Depois que houver confiança suficiente de que um determinado índice está ausente, uma nova recomendação **criar índice** será criada.

 O banco de dados SQL do Azure cria confiança ao estimar o lucro de desempenho que o índice levaria ao longo do tempo. Dependendo do lucro estimado de desempenho, as recomendações são categorizadas como alta, média ou baixa. 

Os índices criados usando recomendações sempre são sinalizados como índices criados automaticamente. Você pode ver quais índices são criados automaticamente examinando a exibição sys. indexes. Os índices criados automaticamente não bloqueiam os comandos ALTER/RENAME. 

Se você tentar descartar a coluna que tem um índice criado automaticamente, o comando passará. O índice criado automaticamente é Descartado com o comando também. Índices regulares bloqueiam o comando ALTER/RENAME em colunas que são indexadas.

Após a aplicação da recomendação de criar índice, o banco de dados SQL do Azure compara o desempenho das consultas com o desempenho de linha de base. Se o novo índice melhorar o desempenho, a recomendação será sinalizada como bem-sucedida e o relatório de impacto estará disponível. Se o índice não melhorar o desempenho, ele será revertido automaticamente. O banco de dados SQL usa esse processo para garantir que as recomendações melhorem o desempenho do banco de dados

Qualquer recomendação para **criar índice** tem uma política de retirada que não permite aplicar a recomendação se o uso de recursos de um banco de dados ou pool for alto. A política de retirada leva em conta CPU, e/s de dados, e/s de log e armazenamento disponível. 

Se a CPU, a e/s de dados ou a e/s de log for maior que 80% nos 30 minutos anteriores, a recomendação criar índice será adiada. Se o armazenamento disponível estiver abaixo de 10% após a criação do índice, a recomendação entrará em um estado de erro. Se, após alguns dias, o ajuste automático ainda acreditar que o índice seria benéfico, o processo será iniciado novamente. 

Esse processo se repete até que haja armazenamento disponível suficiente para criar um índice ou até que o índice não seja visto mais como benéfico.

## <a name="drop-index-recommendations"></a>Recomendações de drop index
Além de detectar índices ausentes, o banco de dados SQL analisa continuamente o desempenho de índices existentes. Se um índice não for usado, o banco de dados SQL do Azure recomendará soltá-lo. O descarte de um índice é recomendado em dois casos:
* O índice é uma duplicata de outro índice (mesma coluna indexada e incluída, esquema de partição e filtros).
* O índice não foi usado por um período prolongado (93 dias).

As recomendações de drop index também passam pela verificação após a implementação. Se o desempenho melhorar, o relatório de impacto estará disponível. Se o desempenho degradar, a recomendação será revertida.

## <a name="parameterize-queries-recommendations-preview"></a>Recomendações de parametrização de consultas (versão prévia)
As recomendações de *parametrização de consultas* aparecem quando você tem uma ou mais consultas que estão constantemente sendo recompiladas, mas terminam com o mesmo plano de execução de consulta. Essa condição cria uma oportunidade de aplicar parametrização forçada. A parametrização forçada, por sua vez, permite que os planos de consulta sejam armazenados em cache e reutilizados no futuro, o que melhora o desempenho e reduz o uso de recursos. 

Cada consulta emitida em SQL Server inicialmente precisa ser compilada para gerar um plano de execução. Cada plano gerado é adicionado ao cache de planos. As execuções subsequentes da mesma consulta podem reutilizar esse plano do cache, o que elimina a necessidade de compilação adicional. 

Consultas com valores não parametrizados podem levar à sobrecarga de desempenho porque o plano de execução é recompilado cada vez que os valores não parametrizados são diferentes. Em muitos casos, as mesmas consultas com valores de parâmetros diferentes geram os mesmos planos de execução. No entanto, esses planos ainda são adicionados separadamente ao cache de planos. 

O processo de recompilação de planos de execução usa recursos do banco de dados, aumenta o tempo de duração da consulta e estoura o cache do plano. Esses eventos, por sua vez, fazem com que os planos sejam removidos do cache. Esse comportamento de SQL Server pode ser alterado definindo a opção de parametrização forçada no banco de dados. 

Para ajudá-lo a estimar o impacto dessa recomendação, você receberá uma comparação entre o uso real da CPU e o uso projetado da CPU (como se a recomendação fosse aplicada). Essa recomendação pode ajudá-lo a obter economia de CPU. Ele também pode ajudá-lo a diminuir a duração da consulta e a sobrecarga para o cache de planos, o que significa que mais dos planos podem permanecer no cache e serem reutilizados. Você pode aplicar essa recomendação rapidamente selecionando o comando **aplicar** . 

Depois de aplicar essa recomendação, ela permite a parametrização forçada em minutos em seu banco de dados. Ele inicia o processo de monitoramento, que dura aproximadamente 24 horas. Após esse período, você poderá ver o relatório de validação. Este relatório mostra o uso da CPU do seu banco de dados 24 horas antes e depois que a recomendação foi aplicada. Assistente do Banco de Dados SQL tem um mecanismo de segurança que reverte automaticamente a recomendação aplicada se a regressão de desempenho tiver sido detectada.

## <a name="fix-schema-issues-recommendations-preview"></a>Recomendações de correção de problemas de esquema (versão prévia)

> [!IMPORTANT]
> Atualmente, a Microsoft está preterindo recomendações "corrigir problema de esquema". Recomendamos que você use [Intelligent insights](sql-database-intelligent-insights.md) para monitorar os problemas de desempenho do banco de dados, incluindo problemas de esquema que as recomendações de "corrigir problema de esquema" já abordaram anteriormente.
> 

As recomendações de **correção de problemas de esquema** aparecem quando o serviço do banco de dados SQL observa uma anomalia no número de erros SQL relacionados ao esquema que estão ocorrendo no banco de dados SQL. Essa recomendação normalmente aparece quando o banco de dados encontra vários erros relacionados ao esquema (nome de coluna inválido, nome de objeto inválido e assim por diante) em uma hora.

"Problemas de esquema" são uma classe de erros de sintaxe no SQL Server. Elas ocorrem quando a definição da consulta SQL e a definição do esquema de banco de dados não estão alinhadas. Por exemplo, uma das colunas esperadas pela consulta pode estar ausente na tabela de destino ou vice-versa. 

A recomendação "corrigir problema do esquema" aparece quando o serviço do banco de dados SQL do Azure observa uma anomalia no número de erros SQL relacionados ao esquema que estão ocorrendo no banco de dados SQL. A tabela a seguir mostra os erros relacionados a problemas de esquema:

| Código de erro do SQL | Mensagem |
| --- | --- |
| 201 |O procedimento ou a função ' *' espera o parâmetro '* ', que não foi fornecido. |
| 207 |Nome de coluna inválido ' * '. |
| 208 |Nome de objeto ' * ' inválido. |
| 213 |O nome da coluna ou o número de valores fornecidos não corresponde à definição da tabela. |
| 2812 |Não foi possível encontrar o procedimento armazenado ' * '. |
| 8144 |O procedimento ou a função * tem muitos argumentos especificados. |

## <a name="custom-applications"></a>Aplicações personalizadas

Os desenvolvedores podem considerar o desenvolvimento de aplicativos personalizados usando recomendações de desempenho para o banco de dados SQL do Azure. Todas as recomendações listadas no portal para um banco de dados podem ser acessadas por meio da API [Get-AzSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction) .

## <a name="next-steps"></a>Passos seguintes
Monitore suas recomendações e continue a aplicá-las para refinar o desempenho. As cargas de trabalho de banco de dados são dinâmicas e mudam continuamente. Assistente do Banco de Dados SQL continua a monitorar e fornecer recomendações que podem melhorar o desempenho do seu banco de dados. 

* Para obter mais informações sobre o ajuste automático de índices de banco de dados e planos de execução de consulta, consulte [ajuste automático do banco de dados SQL do Azure](sql-database-automatic-tuning.md).
* Para obter mais informações sobre o monitoramento automático do desempenho do banco de dados com diagnóstico automatizado e análise da causa raiz de problemas de desempenho, consulte [Azure SQL Intelligent insights](sql-database-intelligent-insights.md).
*  Para obter mais informações sobre como usar as recomendações de desempenho no portal do Azure, consulte [recomendações de desempenho no portal do Azure](sql-database-advisor-portal.md).
* Consulte análise de [desempenho de consultas](sql-database-query-performance.md) para saber mais e exibir o impacto no desempenho de suas principais consultas.


