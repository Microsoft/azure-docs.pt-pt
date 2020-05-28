---
title: Tecnologias in-Memory
description: As tecnologias in-Memory melhoram consideravelmente o desempenho das cargas de trabalho transacionais e analíticas na Base de Dados Azure SQL e na Instância Gerida Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/19/2019
ms.openlocfilehash: c9b25912e1386520d61412a8ba05f6b02224fbe6
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84046895"
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-azure-sql-database-and-azure-sql-managed-instance"></a>Otimizar o desempenho utilizando tecnologias In-Memory na Base de Dados Azure SQL e na Instância Gerida Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

As tecnologias In-Memory permitem-lhe melhorar o desempenho da sua aplicação e potencialmente reduzir o custo da sua base de dados.

## <a name="when-to-use-in-memory-technologies"></a>Quando utilizar tecnologias In-Memory

Ao utilizar tecnologias In-Memory, pode obter melhorias de desempenho com várias cargas de trabalho:

- **Transacional** (processamento transacional on-line (OLTP)) onde a maioria dos pedidos lê ou atualiza um conjunto de dados menores (por exemplo, operações CRUD).
- **Analítica** (processamento analítico on-line (OLAP)) onde a maioria das consultas têm cálculos complexos para as finalidades de reporte, com um certo número de consultas que carregam e anexam dados às tabelas existentes (a chamada carga a granel), ou eliminam os dados das tabelas.
- **Mistas** (transação híbrida/processamento analítico (HTAP)) em que tanto as consultas OLTP como o OLAP são executadas no mesmo conjunto de dados.

As tecnologias na memória podem melhorar o desempenho destas cargas de trabalho mantendo os dados que devem ser processados na memória, utilizando a compilação nativa das consultas, ou o processamento avançado, como o processamento de lotes e instruções SIMD que estão disponíveis no hardware subjacente.

## <a name="overview"></a>Descrição geral

A Base de Dados Azure SQL e a Instância Gerida Azure SQL têm as seguintes tecnologias in-memory:

- *[In-Memory OLTP](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)* aumenta o número de transações por segundo e reduz a latência para o processamento de transações. Os cenários que beneficiam do OLTP em Memória são: processamento de transações de alta viação, tais como trading e gaming, ingestão de dados de eventos ou dispositivos IoT, cache, carga de dados e cenários variáveis de mesa e mesa temporárias.
- *Os índices de lojas de colunas agrupadas* reduzem a sua pegada de armazenamento (até 10 vezes) e melhoram o desempenho para consultas de reporte e análise. Pode usá-lo com tabelas de factos nos seus mercados de dados para encaixar mais dados na sua base de dados e melhorar o desempenho. Além disso, pode utilizá-lo com dados históricos na sua base de dados operacional para arquivar e poder consultar até 10 vezes mais dados.
- Os índices de lojas de *colunas não agrupados* para o HTAP ajudam-no a obter informações em tempo real sobre o seu negócio através da consulta direta da base de dados operacional, sem a necessidade de executar um processo caro de extração, transformação e carga (ETL) e esperar que o armazém de dados seja povoado. Os índices de lojas de colunas não agrupados permitem a rápida execução de consultas de análise na base de dados OLTP, reduzindo ao mesmo tempo o impacto na carga de trabalho operacional.
- *Os índices de colunas agrupadas otimizados* pela memória para o HTAP permitem-lhe realizar um processamento rápido de transações e executar *simultaneamente* consultas de análise muito rapidamente nos mesmos dados.

Tanto os índices de colunas como o OLTP em Memória fazem parte do produto SQL Server desde 2012 e 2014, respectivamente. A Base de Dados Azure SQL, a Instância Gerida Azure SQL e o Servidor SQL partilham a mesma implementação das tecnologias In-Memory.

## <a name="benefits-of-in-memory-technology"></a>Benefícios da tecnologia de memória

Devido à consulta mais eficiente e ao processamento de transações, as tecnologias In-Memory também ajudam a reduzir os custos. Normalmente não é necessário atualizar o nível de preços da base de dados para obter ganhos de desempenho. Em alguns casos, poderá até ser capaz de reduzir o nível de preços, ao mesmo tempo que vê melhorias de desempenho com tecnologias In-Memory.

Aqui estão dois exemplos de como o OLTP em Memória ajudou a melhorar significativamente o desempenho:

- Ao utilizar o OLTP in-memory, a [Quorum Business Solutions conseguiu duplicar a sua carga de trabalho, melhorando as DTUs em 70%.](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)
- O vídeo seguinte demonstra uma melhoria significativa no consumo de recursos com uma amostra de carga de trabalho: [In-Memory OLTP Video](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB). Para mais informações, consulte a publicação do blog: [In-Memory OLTP](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

> [!NOTE]  
> As tecnologias in-Memory estão disponíveis nos níveis Premium e Business Critical.

O vídeo que se segue explica potenciais ganhos de desempenho com tecnologias In-Memory. Lembre-se que o ganho de desempenho que vê depende sempre de muitos fatores, incluindo a natureza da carga de trabalho e dados, padrão de acesso da base de dados, e assim por diante.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>

Este artigo descreve aspetos de OLTP in-Memory e índices de lojas de colunas específicos da Base de Dados Azure SQL e da Instância Gerida Azure SQL, e também inclui amostras:

- Você verá o impacto destas tecnologias nos limites de armazenamento e tamanho de dados.
- Verá como gerir o movimento de bases de dados que utilizam estas tecnologias entre os diferentes níveis de preços.
- Verá duas amostras que ilustram o uso de OLTP in-memory, bem como índices de lojas de colunas.

Para mais informações sobre in-memória no Servidor SQL, consulte:

- [Cenários de visão geral e utilização do OLTP em memória](/sql/relational-databases/in-memory-oltp/overview-and-usage-scenarios) (inclui referências a estudos de caso seletivas do cliente e informações para começar)
- [Documentação para OLTP em Memória](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
- [Guia de Índices de Lojas de Colunas](/sql/relational-databases/indexes/columnstore-indexes-overview)
- Processamento transalítico/analítico híbrido (HTAP), também conhecido como [análise operacional em tempo real](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics)

## <a name="in-memory-oltp"></a>OLTP em memória

A tecnologia OLTP em memória fornece operações de acesso a dados extremamente rápidas, mantendo todos os dados na memória. Também utiliza índices especializados, compilação nativa de consultas e acesso de dados sem trincos para melhorar o desempenho da carga de trabalho oLTP. Existem duas formas de organizar os seus dados OLTP em Memória:

- **Formato** de loja de remo otimizado pela memória onde cada linha é um objeto de memória separado. Este é um formato Clássico OLTP em Memória otimizado para cargas de trabalho OLTP de alto desempenho. Existem dois tipos de tabelas otimizadas pela memória que podem ser usadas no formato de loja de remo otimizada pela memória:

  - *Tabelas duráveis* (SCHEMA_AND_DATA) onde as linhas colocadas na memória são preservadas após o reinício do servidor. Este tipo de tabelas comporta-se como uma mesa tradicional de loja de remo com os benefícios adicionais de otimizações na memória.
  - *Tabelas* não duráveis (SCHEMA_ONLY) onde as linhas não são preservadas após o reinício. Este tipo de tabela é projetado para dados temporários (por exemplo, substituição de tabelas temporárias), ou tabelas onde você precisa carregar rapidamente os dados antes de movê-lo para alguma tabela persistida (as chamadas tabelas de preparação).

- **Formato de loja de colunas otimizado para memória** onde os dados são organizados num formato colunaar. Esta estrutura foi concebida para cenários HTAP onde é necessário fazer consultas analíticas na mesma estrutura de dados onde a sua carga de trabalho OLTP está em execução.

> [!Note]
> A tecnologia In-Memory OLTP foi concebida para as estruturas de dados que podem residir plenamente na memória. Uma vez que os dados da memória innão podem ser descarregados para o disco, certifique-se de que está a utilizar uma base de dados que tenha memória suficiente. Consulte o tamanho dos dados e a tampa de [armazenamento para OLTP em Memória](#data-size-and-storage-cap-for-in-memory-oltp) para obter mais detalhes.

Um primer rápido em In-Memory OLTP: [Quickstart 1: In-Memory OLTP Technologies for Faster T-SQL Performance](/sql/relational-databases/in-memory-oltp/survey-of-initial-areas-in-in-memory-oltp) (outro artigo para ajudá-lo a começar)

Vídeos aprofundados sobre as tecnologias:

- [In-Memory OLTP](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (que contém uma demonstração de benefícios de desempenho e passos para reproduzir estes resultados por si mesmo)
- [Vídeos OLTP Em Memória: O que é e Quando/Como usá-lo](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/)

Existe uma forma programática de perceber se uma determinada base de dados suporta o OLTP in-Memory. Pode executar a seguinte consulta Transact-SQL:

```sql
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```

Se a consulta devolver **1**, o OLTP em Memória é suportado nesta base de dados. As seguintes consultas identificam todos os objetos que precisam de ser removidos antes de uma base de dados poder ser desclassificado para propósito geral, Standard ou Basic:

```sql
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Tamanho de dados e tampa de armazenamento para OLTP em Memória

O OLTP in-Memory inclui tabelas otimizadas pela memória, que são usadas para armazenar dados do utilizador. Estas mesas são necessárias para caber na memória. Uma vez que gere a memória diretamente no serviço Debase de dados SQL, temos o conceito de uma quota para dados do utilizador. Esta ideia é referida como *armazenamento OLTP em Memória.*

Cada um suportado um único nível de preços de base de dados e cada elástico de preços do pool inclui uma certa quantidade de armazenamento OLTP em Memória.

- [Limites de recursos baseados em DTU - base de dados única](database/resource-limits-dtu-single-databases.md)
- [Limites de recursos baseados em DTU - piscinas elásticas](database/resource-limits-dtu-elastic-pools.md)
- [limites de recursos baseados em vCore - bases de dados únicas](database/resource-limits-vcore-single-databases.md)
- [limites de recursos baseados em vCore - piscinas elásticas](database/resource-limits-vcore-elastic-pools.md)
- [limites de recursos baseados em vCore - instância gerida](managed-instance/resource-limits.md)

Os seguintes itens contam para a sua tampa de armazenamento OLTP in-memory:

- Linhas de dados de utilizadores ativos em tabelas otimizadas pela memória e variáveis de tabela. Note que as versões antigas não contam para o boné.
- Índices em tabelas otimizadas pela memória.
- Sobrecarga operacional das operações alter table.

Se atingir a tampa, recebe um erro fora de quota e já não é capaz de inserir ou atualizar dados. Para mitigar este erro, elimine dados ou aumente o nível de preços da base de dados ou do pool.

Para mais detalhes sobre a monitorização da utilização do armazenamento In-Memory OLTP e alertas de configuração quando quase atingir a tampa, consulte o [armazenamento monitor In-Memory](in-memory-oltp-monitor-space.md).

#### <a name="about-elastic-pools"></a>Sobre piscinas elásticas

Com piscinas elásticas, o armazenamento OLTP in-memory é partilhado em todas as bases de dados da piscina. Portanto, a utilização numa base de dados pode potencialmente afetar outras bases de dados. Duas mitigações para isto são:

- Configure um ou para bases de `Max-eDTU` `MaxvCore` dados inferiores à contagem de eDTU ou vCore para a piscina como um todo. Isto cobre a utilização de armazenamento OLTP em Memória, em qualquer base de dados da piscina, ao tamanho correspondente à contagem de eDTU.
- Configure a `Min-eDTU` ou que seja maior que `MinvCore` 0. Este mínimo garante que cada base de dados do pool tem a quantidade de armazenamento OLTP disponível em Memória que corresponde ao configurado `Min-eDTU` ou `vCore` .

### <a name="changing-service-tiers-of-databases-that-use-in-memory-oltp-technologies"></a>Alteração dos níveis de serviço das bases de dados que utilizam tecnologias OLTP em Memória

Pode sempre atualizar a sua base de dados ou instância para um nível mais elevado, como de Propósito Geral para Business Critical (ou Standard to Premium). A funcionalidade e os recursos disponíveis apenas aumentam.

Mas a degradação do nível pode ter um impacto negativo na sua base de dados. O impacto é especialmente evidente quando se desvaloriza do Business Critical para o General Purpose (ou Premium para Standard ou Basic) quando a sua base de dados contém objetos OLTP in-memory. As tabelas otimizadas pela memória não estão disponíveis após a desvalorização (mesmo que permaneçam visíveis). As mesmas considerações aplicam-se quando se está a baixar o nível de preços de uma piscina elástica, ou a mover uma base de dados com tecnologias In-Memory, para um conjunto elástico de Propósito Geral, Standard ou Basic.

> [!Important]
> In-Memory OLTP não é suportado no nível De Propósito Geral, Padrão ou Básico. Portanto, não é possível mover uma base de dados que tenha quaisquer objetos OLTP in-memory para um destes níveis.

Antes de desvalorizar a base de dados para Propósito Geral, Standard ou Basic, remova todas as tabelas e tipos de tabelas otimizados pela memória, bem como todos os módulos T-SQL compilados de forma nativa.

*Recursos de escalonamento no nível Business Critical*: Os dados em tabelas otimizadas pela memória devem caber no armazenamento OLTP em Memória que esteja associado ao nível da base de dados ou instância gerida, ou está disponível no pool elástico. Se tentar reduzir o nível ou mover a base de dados para uma piscina que não tenha armazenamento OLTP disponível o suficiente, a operação falha.

## <a name="in-memory-columnstore"></a>Loja de colunas na memória

A tecnologia de loja de colunas na memória está a permitir-lhe armazenar e consultar uma grande quantidade de dados nas tabelas. A tecnologia Columnstore utiliza o formato de armazenamento de dados baseado em colunas e o processamento de consulta de lote para obter ganhos até 10 vezes o desempenho da consulta nas cargas de trabalho OLAP sobre o armazenamento tradicional orientado para a linha. Também pode obter ganhos até 10 vezes a compressão de dados sobre o tamanho dos dados não comprimidos.
Existem dois tipos de modelos de lojas de colunas que pode utilizar para organizar os seus dados:

- **Loja de colunas agrupada** onde todos os dados da tabela são organizados no formato colunaar. Neste modelo, todas as linhas da tabela são colocadas em formato colunaar que comprime fortemente os dados e permite executar consultas e relatórios analíticos rápidos na tabela. Dependendo da natureza dos seus dados, o tamanho dos seus dados pode ser diminuído 10x-100x. O modelo de loja de colunas agrupado também permite a ingestão rápida de uma grande quantidade de dados (carga a granel), uma vez que grandes lotes de dados superiores a 100K são comprimidos antes de serem armazenados em disco. Este modelo é uma boa escolha para os cenários clássicos do armazém de dados.
- **Loja de colunas não agrupada** onde os dados são armazenados na tabela tradicional da loja de filas e há um índice no formato de loja de colunas que é usado para as consultas analíticas. Este modelo permite o Processamento Transaccional-Analítico Híbrido (HTAP): a capacidade de executar análises performantes em tempo real numa carga de trabalho transacional. As consultas oLTP são executadas na tabela de lojas de remo que é otimizada para aceder a um pequeno conjunto de linhas, enquanto as consultas OLAP são executadas no índice de lojas de colunas que é melhor escolha para digitalizações e análises. O optimizador de consultas escolhe dinamicamente o formato de rowstore ou columnstore com base na consulta. Os índices de lojas de colunas não agrupados não diminuem o tamanho dos dados, uma vez que o conjunto de dados original é mantido na tabela original da loja de filas sem qualquer alteração. No entanto, o tamanho do índice adicional de colunas deve ser por ordem de magnitude inferior ao índice equivalente de árvore B.

> [!Note]
> A tecnologia de loja de colunas na memória guarda apenas os dados necessários para o processamento na memória, enquanto os dados que não podem caber na memória são armazenados no disco. Portanto, a quantidade de dados nas estruturas de colunas em memória pode exceder a quantidade de memória disponível.

Vídeo aprofundado sobre a tecnologia:

- [Índice de Colunas: Vídeos de Análise em Memória da Ignite 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/)

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Tamanho e armazenamento de dados para índices de lojas de colunas

Os índices da Columnstore não são necessários para se encaixarem na memória. Portanto, a única tampa sobre o tamanho dos índices é o tamanho máximo da base de dados global, que está documentado no modelo de [compra baseado em DTU](database/service-tiers-dtu.md) e nos [modelos de modelos de compra baseados em vCore.](database/service-tiers-vcore.md)

Quando utiliza índices de lojas de colunas agrupadas, a compressão colunaar é utilizada para o armazenamento da mesa base. Esta compressão pode reduzir significativamente a pegada de armazenamento dos dados dos seus utilizadores, o que significa que pode encaixar mais dados na base de dados. E a compressão pode ser aumentada com compressão de [arquivo colunaar](https://msdn.microsoft.com/library/cc280449.aspx#using-columnstore-and-columnstore-archive-compression). A quantidade de compressão que pode obter depende da natureza dos dados, mas 10 vezes a compressão não é incomum.

Por exemplo, se tiver uma base de dados com um tamanho máximo de 1 terabyte (TB) e conseguir 10 vezes a compressão utilizando índices de lojas de colunas, pode encaixar um total de 10 TB de dados do utilizador na base de dados.

Quando utiliza índices de lojas de colunas não agrupadas, a tabela base ainda é armazenada no formato tradicional da loja de filas. Portanto, as poupanças de armazenamento não são tão grandes como com os índices de lojas de colunas agrupadas. No entanto, se estiver a substituir uma série de índices tradicionais não agrupados por um único índice de colunas, ainda pode ver uma poupança global na pegada de armazenamento para a tabela.

### <a name="changing-service-tiers-of-databases-containing-columnstore-indexes"></a>Alteração dos níveis de serviço das bases de dados que contenham índices de Colunas

*A degradação da base de dados única para Basic ou Standard* pode não ser possível se o seu nível de alvo estiver abaixo do S3. Os índices de colunas são suportados apenas no nível de preços Business Critical/Premium e no nível Standard, S3 e superior, e não no nível Básico. Quando desclassifica a sua base de dados para um nível ou nível não suportado, o seu índice de loja de colunas fica indisponível. O sistema mantém o índice da loja de colunas, mas nunca aproveita o índice. Se posteriormente atualizar para um nível ou nível suportado, o índice da loja de colunas está imediatamente pronto para ser alavancado novamente.

Se tiver um índice de loja de colunas **agrupado,** toda a tabela fica indisponível após a desvalorização. Por isso, recomendamos que deixe cair todos os índices de lojas de colunas *agrupadas* antes de desvalorizar a sua base de dados para um nível ou nível não suportado.

> [!Note]
> A Instância Gerida suporta os índices Da ColumnStore em todos os níveis.

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

## <a name="next-steps"></a>Próximos passos

- [Quickstart 1: Tecnologias OLTP em Memória para um desempenho T-SQL mais rápido](https://msdn.microsoft.com/library/mt694156.aspx)
- [Utilizar o OLTP em Memória numa aplicação Azure SQL existente](in-memory-oltp-configure.md)
- [Monitor armazenamento OLTP em memória](in-memory-oltp-monitor-space.md) para OLTP em memória
- [Experimente funcionalidades de memória](in-memory-sample.md)

## <a name="additional-resources"></a>Recursos adicionais

### <a name="deeper-information"></a>Informação mais profunda

- [Saiba como o Quorum duplica a carga de trabalho da base de dados chave enquanto baixa o DTU em 70% com o OLTP in-memory na Base de Dados SQL](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)
- [Post de blog oLTP em memória](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)
- [Saiba mais sobre OLTP in-memory](https://msdn.microsoft.com/library/dn133186.aspx)
- [Conheça os índices de lojas de colunas](https://msdn.microsoft.com/library/gg492088.aspx)
- [Conheça a análise operacional em tempo real](https://msdn.microsoft.com/library/dn817827.aspx)
- Consulte padrões comuns de carga de [trabalho e considerações de migração](https://msdn.microsoft.com/library/dn673538.aspx) (que descreve padrões de carga de trabalho onde o OLTP em memória geralmente proporciona ganhos significativos de desempenho)

### <a name="application-design"></a>Design da aplicação

- [OLTP in-memory (Otimização em Memória)](https://msdn.microsoft.com/library/dn133186.aspx)
- [Utilizar o OLTP em Memória numa aplicação Azure SQL existente](in-memory-oltp-configure.md)

### <a name="tools"></a>Ferramentas

- [Portal do Azure](https://portal.azure.com/)
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
