---
title: SQL Server para SQL Managed Instance - Análise de desempenho
description: Aprenda a criar e comparar uma linha de base de desempenho ao migrar as bases de dados do SQL Server para Azure SQL Managed Instance.
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: mokabiru
ms.date: 11/06/2020
ms.openlocfilehash: e9fb004df5fdf8a955312ebcf16b8b60fd7c9b6a
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94592197"
---
# <a name="migration-performance-sql-server-to-sql-managed-instance-performance-analysis"></a>Desempenho da migração: SQL Server para SQL Managed Instance análise de desempenho
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Crie uma linha de base de desempenho para comparar o desempenho da sua carga de trabalho numa SqL Managed Instance com a sua carga de trabalho original em execução no SQL Server. 

## <a name="create-a-baseline"></a>Criar uma linha de base

Idealmente, o desempenho é semelhante ou melhor após a migração, por isso é importante medir e registar os valores de desempenho de base na fonte e depois compará-los com o ambiente alvo. Uma linha de base de desempenho é um conjunto de parâmetros que definem a sua carga de trabalho média na sua fonte. 

Selecione um conjunto de consultas que sejam importantes e representativas da sua carga de trabalho de negócio. Medir e documentar a duração min/média/máxima e a utilização do CPU para estas consultas, bem como métricas de desempenho no servidor de origem, tais como a utilização média/máxima do CPU, a latência média/máx do disco IO, produção, IOPS, esperança média/máxima de vida da página máxima, e o tamanho máximo médio de temperatura. 

Os seguintes recursos podem ajudar a definir uma linha de base de desempenho: 

   - [Monitorizar o uso do CPU ](https://techcommunity.microsoft.com/t5/azure-sql-database/monitor-cpu-usage-on-sql-server-and-azure-sql/ba-p/680777#M131)
   - [Monitorizar o uso da](/sql/relational-databases/performance-monitor/monitor-memory-usage)   memória e determinar a quantidade de memória utilizada por diferentes componentes, tais como piscina tampão, cache de plano, piscina de loja de colunas, [OLTP in-memory,](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage)etc. Além disso, deve encontrar valores médios e máximos do contador de desempenho da memória da esperança de vida da página. 
   - Monitorize a utilização do IO do disco na origem SQL Server, utilizando os [contadores de](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)   visualização ou [desempenho](/sql/relational-databases/performance-monitor/monitor-disk-usage)sys.dm_io_virtual_file_stats . 
   - Monitorize a carga de trabalho e o desempenho da consulta examinando As Vistas de Gestão Dinâmica (ou Loja de Consultas se estiver a migrar do SQL Server 2016 e posteriormente). Identifique a duração média e o uso do CPU das consultas mais importantes na sua carga de trabalho. 

Quaisquer problemas de desempenho na fonte SQL Server devem ser abordados antes da migração. Migrar problemas conhecidos para qualquer novo sistema pode causar resultados inesperados e invalidar qualquer comparação de desempenho. 


## <a name="compare-performance"></a>Comparar desempenho 

Depois de definir uma linha de base, compare desempenho de carga de trabalho semelhante no target SQL Managed Instance. Para maior precisão, é importante que o ambiente SQL Managed Instance seja comparável ao ambiente do SQL Server tanto quanto possível. 

Existem diferenças de infraestrutura de instância gerida SQL que tornam o desempenho correspondente exatamente improvável. Algumas consultas podem correr mais rápido do que o esperado, enquanto outras podem ser mais lentas. O objetivo desta comparação é verificar que o desempenho da carga de trabalho no caso gerido corresponde ao desempenho no SQL Server (em média) e identificar quaisquer consultas críticas com desempenho que não correspondam ao seu desempenho original. 

A comparação de desempenho é suscetível de resultar nos seguintes resultados: 

- O desempenho da carga de trabalho na instância gerida está alinhado ou melhor do que o desempenho da carga de trabalho no seu servidor SQL de origem. Neste caso, confirmou com êxito que a migração é bem sucedida. 

- A maioria dos parâmetros de desempenho e consultas na carga de trabalho funcionam como esperado, com algumas exceções que resultam em desempenho degradado. Neste caso, identifique as diferenças e a sua importância. Se existem algumas consultas importantes com desempenho degradado, investigue se os planos SQL subjacentes mudaram ou se as consultas estão a atingir os limites de recursos. Pode mitigar isso aplicando algumas dicas sobre consultas críticas (por exemplo, alterar o nível de compatibilidade, o estimador do cardinalício legado) diretamente ou usando guias de planos. Certifique-se de que as estatísticas e os índices estão atualizados e equivalentes em ambos os ambientes. 

- A maioria das consultas são mais lentas numa instância gerida em comparação com a sua origem SQL Server. Neste caso, tente identificar as causas da diferença, tais como atingir algum limite de [recursos,](../../managed-instance/resource-limits.md#service-tier-characteristics) tais como IO, memória ou limites de taxa de registo de casos. Se não houver limites de recursos que causem a diferença, tente alterar o nível de compatibilidade da base de dados ou altere as definições da base de dados como a estimativa do cardinalício e reexecute o teste. Reveja as recomendações fornecidas pela instância gerida ou pontos de vista da Loja de Consultas para identificar as consultas com desempenho regresso. 

SQL Managed Instance tem uma funcionalidade de correção de plano automático incorporada que é ativada por padrão. Esta funcionalidade garante que as consultas que funcionaram bem no passado não se degradam no futuro. Se esta função não estiver ativada, execute a carga de trabalho com as definições antigas para que a SQL Managed Instance possa aprender a linha de base de desempenho. Em seguida, ative a função e volte a executar a carga de trabalho com as novas definições. 

Faça alterações nos parâmetros do seu teste ou atualize para níveis de serviço mais elevados para alcançar a configuração ideal para o desempenho da carga de trabalho que se adequa às suas necessidades. 

## <a name="monitor-performance"></a>Monitorizar desempenho 

A SQL Managed Instance fornece ferramentas avançadas para monitorização e resolução de problemas, e deve usá-las para monitorizar o desempenho no seu caso. Algumas das métricas-chave a monitorizar são: 

- Utilização do CPU no caso para determinar se o número de vCores que a provisionou é a combinação certa para a sua carga de trabalho. 
- Esperança de vida de página no seu caso gerido para determinar se precisa de [memória adicional](https://techcommunity.microsoft.com/t5/azure-sql-database/do-you-need-more-memory-on-azure-sql-managed-instance/ba-p/563444).
-  Estatísticas como INSTANCE_LOG_GOVERNOR ou PAGEIOLATCH que identificam problemas de IO de armazenamento, especialmente no nível de Finalidade Geral, onde poderá ser necessário pré-alocar ficheiros para obter um melhor desempenho de IO. 


## <a name="considerations"></a>Considerações  

Ao comparar o desempenho, considere o seguinte: 

- As definições coincidem entre a fonte e o alvo. Validar que várias situações, base de dados e configurações temporárias são equivalentes entre os dois ambientes. Diferenças na configuração, níveis de compatibilidade, configurações de encriptação, bandeiras de traços, etc., podem distorcer o desempenho. 

- O armazenamento é configurado de acordo com as [melhores práticas.](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525) Por exemplo, para Fins Gerais, poderá ser necessário pré-alocar o tamanho dos ficheiros para melhorar o desempenho. 

- Existem [diferenças ambientais chave](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) que podem causar as diferenças de desempenho entre um caso gerido e SQL Server. Identifique riscos relevantes para o seu ambiente que possam contribuir para um problema de desempenho. 

- A loja de consultas e a sintonização automática devem ser ativadas na sua SQL Managed Instance, uma vez que ajudam a medir o desempenho da carga de trabalho e a mitigar automaticamente potenciais problemas de desempenho. 



## <a name="next-steps"></a>Passos seguintes

Para obter mais informações para otimizar o seu novo ambiente de Instância Gerida Azure SQL, consulte os seguintes recursos: 

- [Como identificar por que razão o desempenho da carga de trabalho no Azure SQL Managed Instance é diferente do SQL Server?](https://medium.com/azure-sqldb-managed-instance/what-to-do-when-azure-sql-managed-instance-is-slower-than-sql-server-dd39942aaadd)
- [Principais causas das diferenças de desempenho entre o SQL Managed Instance e o SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)
- [Boas práticas e considerações de desempenho de armazenamento para Azure SQL Gestão De Instância (Final geral)](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525)
- [Monitorização de desempenho em tempo real para Azure SQL Managed Instance (este é arquivado, é este o alvo pretendido?)](https://docs.microsoft.com/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance)
