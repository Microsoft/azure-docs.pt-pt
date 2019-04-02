---
title: Recomendações de desempenho - base de dados SQL do Azure | Documentos da Microsoft
description: Base de dados SQL do Azure fornece recomendações para bases de dados SQL que podem melhorar o desempenho de consulta atual.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: 348183e1a164dd0a0f5f9672346423b95c27eba4
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793707"
---
# <a name="performance-recommendations-for-sql-database"></a>Recomendações de desempenho para a base de dados SQL

Base de dados SQL do Azure aprende e adapta-se com a sua aplicação. Fornece recomendações personalizadas que permitem-lhe maximizar o desempenho das bases de dados SQL. Base de dados SQL avalia de forma contínua e analisa o histórico de utilização de bases de dados SQL. As recomendações fornecidas se baseiam em padrões de carga de trabalho exclusivo para a base de dados e ajudar a melhorar o desempenho.

> [!TIP]
> [A otimização automática](sql-database-automatic-tuning.md) é o método recomendado para ajuste automaticamente alguns dos problemas de desempenho de base de dados mais comuns. [Informações de desempenho de consulta](sql-database-query-performance.md) é o método recomendado para necessidades de monitorização do desempenho de base de dados do Azure SQL básico. [Análise de SQL do Azure](../azure-monitor/insights/azure-sql.md) é o método recomendado para a monitorização de desempenho de base de dados em escala, com a inteligência incorporada para resolução de problemas de desempenho automatizado avançada.
>

## <a name="create-index-recommendations"></a>Criar recomendações de índice
Base de dados SQL continuamente monitoriza as consultas que estejam a executar e identifica os índices que podem melhorar o desempenho. Depois que existe suficiente confiança que um determinado índice está em faltando, uma nova **criar índice** recomendação é criada.

 Base de dados SQL do Azure baseia-se confiança, estimar o ganho de desempenho que o índice colocariam longo do tempo. Consoante o ganho de desempenho estimado, as recomendações são categorizadas como alta, média ou baixa. 

Índices que são criados com recomendações sempre são sinalizados como índices criada automaticamente. Pode ver a quais índices são criados automaticamente ao consultar a vista sys. Indexes. Criada automaticamente índices não bloqueiam comandos de mudança de nome/ALTER. 

Se tentar remover a coluna que tem um índice criada automaticamente sobre ele, passa o comando. O índice criada automaticamente é removido com o comando também. Índices regulares bloqueiam o comando ALTER/mudança de nome em colunas que são indexados.

Após é aplicada a recomendação do índice de criar, a base de dados do Azure SQL compara o desempenho das consultas com o desempenho de linha de base. Se o novo índice de desempenho aprimorado, a recomendação é sinalizada conclusão com êxito e o relatório de impacto está disponível. Se o índice não melhorar o desempenho, foi revertida automaticamente. Base de dados SQL utiliza este processo para garantir que recomendações de melhorar o desempenho de base de dados.

Qualquer **criar índice** recomendação tem uma política de término que não permite a aplicar a recomendação, se a utilização de recursos de um banco de dados ou um conjunto é elevada. A política de término leva em conta da CPU, e/s de dados, e/s de registo e armazenamento disponível. 

Se a CPU, e/s de dados ou e/s de registo for superior a 80% nos 30 minutos anteriores, é adiar a recomendação do índice de criar. Se o armazenamento disponível for inferior a 10% depois do índice é criado, a recomendação entra num Estado de erro. Se, após alguns dias, a otimização automática ainda acredita que o índice seria benéfico, o processo é iniciado novamente. 

O processo se repete até que não existe armazenamento suficiente disponível para criar um índice, ou até que o índice não é visto como benéfico mais.

## <a name="drop-index-recommendations"></a>Recomendações de índice de lista
Além de detectar índices ausentes, base de dados SQL analisa continuamente o desempenho de índices existentes. Se não for utilizado um índice, a base de dados do Azure SQL recomenda soltando-o. Remover um índice é recomendado em dois casos:
* O índice é um duplicado de outro índice (mesmo indexados e incluído coluna, esquema de partição e filtros).
* O índice não tiver sido usado por um período prolongado (93 dias).

Remova o índice recomendações também passam por verificação após a implementação. Se melhora o desempenho, o relatório de impacto está disponível. Se degrada o desempenho, a recomendação está revertida.


## <a name="parameterize-queries-recommendations"></a>Parametrizar recomendações de consultas
*Parametrizar consultas* recomendações são apresentadas quando tem um ou mais consultas que são constantemente está sendo compilado novamente mas final cópia de segurança com o mesmo plano de execução de consulta. Esta condição cria uma oportunidade para aplicar a parametrização forçada. A parametrização forçada, por sua vez, permite a planos de consulta para ser armazenado em cache e reutilizado no futuro, que melhora o desempenho e reduz a utilização de recursos. 

Todas as consultas que é emitida relativamente ao SQL Server inicialmente precisam ser compilado para gerar um plano de execução. Cada plano gerado é adicionado à cache de planos. As execuções subsequentes da mesma consulta podem reutilizar este plano da cache, o que elimina a necessidade de compilação adicional. 

Consultas com valores não parametrizada podem levar a sobrecarga de desempenho, porque o plano de execução é recompilado sempre que os valores não parametrizada são diferentes. Em muitos casos, as mesmas consultas com valores de parâmetros diferentes geram planos de execução do mesmo. Estes planos, no entanto, ainda em separado são adicionados à cache de plano. 

O processo de recompilar planos de execução utiliza recursos de base de dados, aumenta a consulta de tempo de duração e estouros de cache de planos. Estes eventos, por sua vez, causam planos sejam expulsos da cache. Este comportamento do SQL Server pode ser alterado ao definir a opção de parametrização forçado no banco de dados. 

Para ajudar a calcular o impacto desta recomendação, é fornecida uma comparação entre a utilização de CPU real e a utilização de CPU prevista (como se a recomendação foram aplicada). Esta recomendação pode ajudá-lo a obter poupanças de CPU. Ele também pode ajudar a reduzir a duração de consulta e sobrecarga para a cache de planos, que significa que mais os planos podem ser mantidos na cache e ser reutilizados. Pode aplicar esta recomendação rapidamente, selecionando o **aplicar** comando. 

Depois de aplicar esta recomendação, permite que a parametrização forçada dentro de poucos minutos na sua base de dados. Inicia o processo de monitorização, que tem uma duração de aproximadamente 24 horas. Após este período, pode ver o relatório de validação. Este relatório mostra a utilização da CPU da base de dados de 24 horas antes e depois da recomendação foi aplicada. Assistente da base de dados SQL tem um mecanismo de segurança que reverte automaticamente a recomendação aplicada se foi detetada uma regressão de desempenho.

## <a name="fix-schema-issues-recommendations-preview"></a>Corrigir problemas de esquema recomendações (pré-visualização)

> [!IMPORTANT]
> Microsoft atualmente está a descontinuar a recomendações "Corrigir o problema de esquema". Recomendamos que utilize [informações inteligentes](sql-database-intelligent-insights.md) para monitorizar os seus problemas de desempenho da base de dados, incluindo problemas de esquema que as recomendações "Corrigir o problema de esquema" abordadas anteriormente.
> 

**Corrigir problemas de esquema** recomendações são apresentadas quando o serviço de base de dados SQL avisos uma anomalia no número de erros do SQL relacionadas com o esquema que estavam a acontecer na sua base de dados SQL. Esta recomendação, normalmente, é apresentada quando a sua base de dados encontra vários erros relacionados com o esquema (nome de coluna inválido, nome de objeto inválido e assim por diante) dentro de uma hora.

"Problemas de esquema" são uma classe de erros de sintaxe no SQL Server. Que ocorrem quando a definição da consulta SQL e a definição do esquema da base de dados não estão alinhados. Por exemplo, uma das colunas que é esperado pela consulta pode estar em falta na tabela de destino ou vice versa. 

A recomendação "Corrigir o problema de esquema" é apresentada quando o serviço de base de dados do Azure SQL avisos uma anomalia no número de erros do SQL relacionadas com o esquema que estavam a acontecer na sua base de dados SQL. A tabela seguinte mostra os erros relacionados com problemas de esquema:

| Código de erro SQL | Mensagem |
| --- | --- |
| 201 |Procedimento ou função '*'espera o parâmetro'*', que não foi fornecido. |
| 207 |Nome de coluna inválido ' *'. |
| 208 |Nome de objeto inválido ' *'. |
| 213 |Nome de coluna ou o número de valores fornecidos não corresponde a definição da tabela. |
| 2812 |Não foi possível encontrar o procedimento armazenado ' *'. |
| 8144 |Função ou procedimento * tem demasiados argumentos especificados. |

## <a name="custom-applications"></a>Aplicações personalizadas

Os desenvolvedores podem optar por desenvolver aplicações personalizadas com recomendações de desempenho para a base de dados do Azure SQL. Todas as recomendações apresentadas no portal para uma base de dados pode ser acedido através de [Get-AzureRmSqlDatabaseRecommendedAction](https://docs.microsoft.com/en-us/powershell/module/AzureRM.Sql/Get-AzureRmSqlDatabaseRecommendedAction) API.

## <a name="next-steps"></a>Passos Seguintes
Monitorizar as suas recomendações e continuar a aplicá-las para refinar o desempenho. Cargas de trabalho de base de dados são dinâmicas e altera-se continuamente. SQL Database Advisor continua a monitorizar e facultar recomendações que podem melhorar potencialmente o desempenho da sua base de dados. 

* Para obter mais informações sobre a otimização automática de índices de base de dados e planos de execução de consulta, consulte [otimização automática da base de dados do Azure SQL](sql-database-automatic-tuning.md).
* Para obter mais informações sobre como monitorizar automaticamente o desempenho de base de dados de diagnósticos automáticos e análise da causa raiz dos problemas de desempenho, consulte [informações inteligentes do SQL Azure](sql-database-intelligent-insights.md).
*  Para obter mais informações sobre como utilizar as recomendações de desempenho no portal do Azure, consulte [recomendações de desempenho no portal do Azure](sql-database-advisor-portal.md).
* Ver [informações de desempenho de consulta](sql-database-query-performance.md) para conhecer e ver o impacto de desempenho de suas consultas principais.


