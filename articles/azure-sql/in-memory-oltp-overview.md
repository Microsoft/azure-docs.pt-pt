---
title: Tecnologias dentro da memória
description: As tecnologias de memória melhoram consideravelmente o desempenho das cargas de trabalho transacionais e analíticas na Base de Dados Azure SQL e na Azure SQL Managed Instance.
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
ms.openlocfilehash: 10f4a0012076deef0757743f206f937ab43dc8b8
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84345296"
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-azure-sql-database-and-azure-sql-managed-instance"></a>Otimizar o desempenho utilizando tecnologias de memória na Base de Dados Azure SQL e na Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

As tecnologias de memória permitem-lhe melhorar o desempenho da sua aplicação e reduzir potencialmente o custo da sua base de dados.

## <a name="when-to-use-in-memory-technologies"></a>Quando usar tecnologias na memória

Ao utilizar tecnologias na memória, pode obter melhorias de desempenho com várias cargas de trabalho:

- **Transacional** (processamento transacional online (OLTP)) onde a maioria dos pedidos lê ou atualiza um conjunto menor de dados (por exemplo, operações CRUD).
- **Analítico** (processamento analítico online (OLAP)) onde a maioria das consultas têm cálculos complexos para as finalidades de reporte, com um certo número de consultas que carregam e anexam dados às tabelas existentes (chamadas carga a granel), ou eliminam os dados das tabelas.
- **Misto** (transação híbrida/processamento analítico (HTAP)) onde as consultas OLTP e OLAP são executadas no mesmo conjunto de dados.

As tecnologias na memória podem melhorar o desempenho destas cargas de trabalho mantendo os dados que devem ser processados na memória, utilizando a compilação nativa das consultas, ou o processamento avançado, como o processamento de lotes e instruções SIMD que estão disponíveis no hardware subjacente.

## <a name="overview"></a>Descrição geral

Azure SQL Database e Azure SQL Managed Instance têm as seguintes tecnologias de memória:

- *[O OLTP em Memória](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)* aumenta o número de transações por segundo e reduz a latência para o processamento de transações. Os cenários que beneficiam do OLTP in-memory são: processamento de transações de alto rendimento, como trading e jogos, ingestão de dados de eventos ou dispositivos IoT, caching, carga de dados e cenários temporários de tabela e tabela variável.
- *Os índices de lojas de colunas agrupadas* reduzem a sua pegada de armazenamento (até 10 vezes) e melhoram o desempenho para consultas de reporte e análise. Pode usá-lo com tabelas de fatos nas suas contas de dados para encaixar mais dados na sua base de dados e melhorar o desempenho. Além disso, pode usá-lo com dados históricos na sua base de dados operacional para arquivar e ser capaz de consultar até 10 vezes mais dados.
- *Os índices de loja de colunas não agrupados* para hTAP ajudam-no a obter informações em tempo real no seu negócio através da consulta direta da base de dados operacional, sem a necessidade de executar um processo caro de extrato, transformação e carga (ETL) e esperar que o armazém de dados seja povoado. Os índices de lojas de colunas não agrupados permitem uma rápida execução de consultas de análise na base de dados OLTP, reduzindo ao mesmo tempo o impacto na carga de trabalho operacional.
- *Os índices de loja de colunas agrupados otimizados* para HTAP permitem-lhe realizar o processamento rápido de transações e executar simultaneamente consultas de análise muito *rapidamente* nos mesmos dados.

Ambos os índices de lojas de colunas e OLTP in-memory fazem parte do produto SQL Server desde 2012 e 2014, respectivamente. Azure SQL Database, Azure SQL Managed Instance e SQL Server partilham a mesma implementação de tecnologias de memória.

## <a name="benefits-of-in-memory-technology"></a>Benefícios da tecnologia na memória

Devido à consulta mais eficiente e ao processamento de transações, as tecnologias na memória também ajudam a reduzir os custos. Normalmente, não é necessário atualizar o nível de preços da base de dados para obter ganhos de desempenho. Em alguns casos, pode até ser capaz de reduzir o nível de preços, ao mesmo tempo que vê melhorias de desempenho com tecnologias de memória.

Aqui estão dois exemplos de como o OLTP in-memory ajudou a melhorar significativamente o desempenho:

- Ao utilizar o OLTP in-memory, [a Quorum Business Solutions conseguiu duplicar a sua carga de trabalho, melhorando os DTUs em 70%.](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)
- O vídeo a seguir demonstra uma melhoria significativa no consumo de recursos com uma carga de trabalho de amostra: [In-Memory OLTP Video](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB). Para mais informações, consulte o post do blog: [In-Memory OLTP](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

> [!NOTE]  
> As tecnologias de memória estão disponíveis nos níveis Premium e Business Critical.

O vídeo seguinte explica potenciais ganhos de desempenho com tecnologias de memória. Lembre-se que o ganho de desempenho que vê depende sempre de muitos fatores, incluindo a natureza da carga de trabalho e dados, o padrão de acesso da base de dados, e assim por diante.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>

Este artigo descreve aspetos de índices OLTP e columnstore in-memory que são específicos da Base de Dados Azure SQL e da Azure SQL Managed Instance, e também inclui amostras:

- Você verá o impacto destas tecnologias no armazenamento e limites de tamanho de dados.
- Você verá como gerir o movimento de bases de dados que usam estas tecnologias entre os diferentes níveis de preços.
- Verá duas amostras que ilustram o uso de OLTP in-memory, bem como índices de lojas de colunas.

Para obter mais informações sobre a memória no SQL Server, consulte:

- [Cenários gerais e de utilização da Memória OLTP](/sql/relational-databases/in-memory-oltp/overview-and-usage-scenarios) (inclui referências a estudos de caso de clientes e informações para começar)
- [Documentação para OLTP in-memory](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
- [Guia de Índices de Colunas](/sql/relational-databases/indexes/columnstore-indexes-overview)
- Processamento híbrido transacional/analítico (HTAP), também conhecido como [análise operacional em tempo real](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics)

## <a name="in-memory-oltp"></a>OLTP dentro da memória

A tecnologia In-Memory OLTP fornece operações de acesso a dados extremamente rápidas mantendo todos os dados na memória. Também utiliza índices especializados, compilação nativa de consultas e acesso a dados sem fecho para melhorar o desempenho da carga de trabalho OLTP. Existem duas formas de organizar os seus dados OLTP in-memory:

- **Formato de loja de linha otimizado para memória** onde cada linha é um objeto de memória separado. Este é um formato clássico de OLTP in-memory otimizado para cargas de trabalho OLTP de alto desempenho. Existem dois tipos de tabelas otimizadas pela memória que podem ser usadas no formato de loja de remo otimizada pela memória:

  - *Tabelas duradouras* (SCHEMA_AND_DATA) onde as linhas colocadas na memória são preservadas após o reinício do servidor. Este tipo de tabelas comporta-se como uma mesa de loja tradicional com os benefícios adicionais de otimizações na memória.
  - *Tabelas não duráveis* (SCHEMA_ONLY) onde as linhas não são conservadas após o reinício. Este tipo de tabela é projetado para dados temporários (por exemplo, substituição de tabelas temporárias), ou tabelas onde é necessário carregar rapidamente dados antes de os mover para alguma mesa persistiu (as chamadas tabelas de preparação).

- **Formato de loja de colunas otimizado em memória** onde os dados são organizados num formato colunar. Esta estrutura é projetada para cenários HTAP onde você precisa executar consultas analíticas na mesma estrutura de dados onde a sua carga de trabalho OLTP está em execução.

> [!Note]
> A tecnologia OLTP in-Memory é projetada para as estruturas de dados que podem residir plenamente na memória. Uma vez que os dados de memória não podem ser descarregados para o disco, certifique-se de que está a utilizar a base de dados que tem memória suficiente. Consulte [o tamanho dos dados e a tampa de armazenamento para OLTP in-memory](#data-size-and-storage-cap-for-in-memory-oltp) para obter mais detalhes.

Um primer rápido no OLTP in-memory: [Quickstart 1: In-Memory OLTP Technologies for Faster T-SQL Performance](/sql/relational-databases/in-memory-oltp/survey-of-initial-areas-in-in-memory-oltp) (outro artigo para ajudá-lo a começar)

Vídeos aprofundados sobre as tecnologias:

- [In-Memory OLTP](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (que contém uma demonstração de benefícios de desempenho e passos para reproduzir estes resultados por si mesmo)
- [Vídeos OLTP em Memória: O que é e Quando/Como usá-lo](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/)

Existe uma forma programática de perceber se uma determinada base de dados suporta o OLTP in-memory. Pode executar a seguinte consulta Transact-SQL:

```sql
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```

Se a consulta devolver **1**, OLTP in-memory é suportado nesta base de dados. As seguintes consultas identificam todos os objetos que precisam de ser removidos antes de uma base de dados poder ser desclassificado para Final Geral, Padrão ou Básico:

```sql
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Tamanho de dados e tampa de armazenamento para OLTP de memória in-memory

O OLTP in-Memory inclui tabelas otimizadas para a memória, que são usadas para armazenar dados do utilizador. Estas mesas são necessárias para caber na memória. Como gere a memória diretamente na Base de Dados SQL, temos o conceito de quota para dados do utilizador. Esta ideia é referida como *armazenamento OLTP in-memory*.

Cada nível de preços de base de dados único suportado e cada nível elástico de preços da piscina inclui uma certa quantidade de armazenamento OLTP in-memory.

- [Limites de recursos baseados em DTU - base de dados única](database/resource-limits-dtu-single-databases.md)
- [Limites de recursos baseados em DTU - piscinas elásticas](database/resource-limits-dtu-elastic-pools.md)
- [limites de recursos baseados em vCore - bases de dados únicas](database/resource-limits-vcore-single-databases.md)
- [limites de recursos baseados em vCore - piscinas elásticas](database/resource-limits-vcore-elastic-pools.md)
- [limites de recursos baseados em vCore - instância gerida](managed-instance/resource-limits.md)

Os seguintes itens contam para a sua tampa de armazenamento OLTP in-memory:

- Linhas de dados de utilizadores ativas em tabelas e variáveis de tabela otimizadas pela memória. Note que as versões antigas não contam para a tampa.
- Índices em tabelas otimizadas pela memória.
- Sobrecarga operacional das operações da ALTER TABLE.

Se atingir a tampa, recebe um erro fora da quota e já não é capaz de inserir ou atualizar dados. Para mitigar este erro, elimine os dados ou aumente o nível de preços da base de dados ou do pool.

Para obter detalhes sobre a monitorização da utilização do armazenamento em memória OLTP e configurar alertas quando quase atingir a tampa, consulte o [armazenamento de memória do Monitor](in-memory-oltp-monitor-space.md).

#### <a name="about-elastic-pools"></a>Sobre piscinas elásticas

Com piscinas elásticas, o armazenamento OLTP in-memory é partilhado em todas as bases de dados da piscina. Portanto, a utilização numa base de dados pode potencialmente afetar outras bases de dados. Duas atenuações para isto são:

- Configurar uma `Max-eDTU` ou `MaxvCore` para bases de dados inferiores à contagem de eDTU ou vCore para a piscina como um todo. Este máximo cobre a utilização de armazenamento OLTP in-memory, em qualquer base de dados da piscina, ao tamanho que corresponde à contagem de eDTU.
- Configurar uma `Min-eDTU` ou `MinvCore` que seja maior que 0. Este mínimo garante que cada base de dados da piscina tem a quantidade de armazenamento OLTP disponível na memória que corresponde à configuração `Min-eDTU` ou `vCore` .

### <a name="changing-service-tiers-of-databases-that-use-in-memory-oltp-technologies"></a>Alteração dos níveis de serviço das bases de dados que utilizam tecnologias OLTP in-memory

Pode sempre atualizar a sua base de dados ou instância para um nível mais elevado, como de Propósito Geral a Crítica de Negócios (ou Standard a Premium). A funcionalidade e os recursos disponíveis só aumentam.

Mas a degradação do nível pode ter um impacto negativo na sua base de dados. O impacto é especialmente aparente quando se desclassifica de Business Critical para Finalidade Geral (ou Premium para Standard ou Basic) quando a sua base de dados contém objetos OLTP in-memory. As tabelas otimizadas pela memória não estão disponíveis após a degradação (mesmo que permaneçam visíveis). As mesmas considerações aplicam-se quando se está a baixar o nível de preços de uma piscina elástica, ou a mover uma base de dados com tecnologias de memória, para um pool elástico de finalidade geral, padrão ou básico.

> [!Important]
> OLTP in-Memory não é suportado no nível de Finalidade Geral, Standard ou Basic. Portanto, não é possível mover uma base de dados que tenha quaisquer objetos OLTP in-memory para um destes níveis.

Antes de desclassificar a base de dados para Finalidade Geral, Standard ou Básico, remova todas as tabelas e tipos de tabelas otimizados para a memória, bem como todos os módulos T-SQL compilados de forma nativa.

*Recursos de escala no nível Business Critical*: Os dados em tabelas otimizadas para a memória devem caber no armazenamento OLTP de memória que está associado ao nível da base de dados ou à instância gerida, ou está disponível na piscina elástica. Se tentar reduzir o nível ou mover a base de dados para uma piscina que não tenha armazenamento OLTP disponível suficiente, a operação falha.

## <a name="in-memory-columnstore"></a>Loja de colunas na memória

A tecnologia de loja de colunas na memória está a permitir-lhe armazenar e consultar uma grande quantidade de dados nas tabelas. A tecnologia Columnstore utiliza o formato de armazenamento de dados baseado em colunas e o processamento de consulta de lote para obter ganhos até 10 vezes o desempenho da consulta nas cargas de trabalho OLAP em relação ao armazenamento tradicional orientado para a linha. Também pode obter ganhos até 10 vezes a compressão de dados sobre o tamanho de dados não comprimidos.
Existem dois tipos de modelos de loja de colunas que pode utilizar para organizar os seus dados:

- **Loja de colunas agrupadas** onde todos os dados da tabela são organizados no formato colunar. Neste modelo, todas as linhas da tabela são colocadas em formato colunar que comprime os dados e permite executar consultas e relatórios analíticos rápidos em cima da mesa. Dependendo da natureza dos seus dados, o tamanho dos seus dados pode ser reduzido 10x-100x. O modelo de loja de colunas agrupado também permite a ingestão rápida de uma grande quantidade de dados (carga a granel), uma vez que grandes lotes de dados superiores a 100K são comprimidos antes de serem armazenados no disco. Este modelo é uma boa escolha para os cenários clássicos do armazém de dados.
- **Loja de colunas não agrupadas** onde os dados são armazenados na tabela tradicional de lojas de remo e há um índice no formato de loja de colunas que é usado para as consultas analíticas. Este modelo permite o Processamento Analítico Transacional Híbrido (HTAP): a capacidade de executar análises em tempo real numa carga de trabalho transacional. As consultas OLTP são executadas na tabela de lojas de linha que é otimizada para aceder a um pequeno conjunto de linhas, enquanto as consultas de OLAP são executadas no índice de loja de colunas que é melhor escolha para digitalizações e análises. O otimizador de consultas escolhe dinamicamente o formato de loja de linha ou de loja de colunas com base na consulta. Os índices de lojas de colunas não agrupados não diminuem o tamanho dos dados uma vez que o conjunto de dados original é mantido na tabela original da loja de linha sem qualquer alteração. No entanto, o tamanho do índice de loja de colunas adicional deve ser por ordem de magnitude inferior ao índice equivalente de árvore B.

> [!Note]
> A tecnologia de loja de colunas na memória guarda apenas os dados necessários para o processamento na memória, enquanto os dados que não podem caber na memória são armazenados no disco. Portanto, a quantidade de dados nas estruturas de colunas de memória pode exceder a quantidade de memória disponível.

Vídeo aprofundado sobre a tecnologia:

- [Índice de Colunas: Vídeos analíticos na memória da Ignite 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/)

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Tamanho de dados e armazenamento para índices de loja de colunas

Os índices de loja de colunas não são necessários para caber na memória. Portanto, o único limite para o tamanho dos índices é o tamanho máximo global da base de dados, que é documentado no modelo de compra baseado em [DTU](database/service-tiers-dtu.md) e artigos [de modelo de compra baseados em vCore.](database/service-tiers-vcore.md)

Quando utiliza índices de loja de colunas agrupados, é utilizada compressão colunar para o armazenamento da tabela base. Esta compressão pode reduzir significativamente a pegada de armazenamento dos dados dos seus utilizadores, o que significa que pode encaixar mais dados na base de dados. E a compressão pode ser aumentada com compressão de [arquivo colunar.](https://msdn.microsoft.com/library/cc280449.aspx#using-columnstore-and-columnstore-archive-compression) A quantidade de compressão que pode obter depende da natureza dos dados, mas 10 vezes a compressão não é incomum.

Por exemplo, se tiver uma base de dados com um tamanho máximo de 1 terabyte (TB) e conseguir 10 vezes a compressão utilizando índices de loja de colunas, pode encaixar um total de 10 TB de dados do utilizador na base de dados.

Quando utiliza índices de lojas de colunas não agrupados, a tabela base continua armazenada no formato tradicional da loja de linhas. Portanto, as poupanças de armazenamento não são tão grandes como com índices de lojas de colunas agrupadas. No entanto, se estiver a substituir uma série de índices tradicionais não agrupados por um único índice de loja de colunas, ainda pode ver uma poupança global na pegada de armazenamento da tabela.

### <a name="changing-service-tiers-of-databases-containing-columnstore-indexes"></a>Alteração dos níveis de serviço das bases de dados que contêm índices de lojas de colunas

*A degradação de uma única base de dados para Basic ou Standard* pode não ser possível se o seu nível-alvo estiver abaixo do S3. Os índices de loja de colunas são suportados apenas no nível de preços Business Critical/Premium e no nível Standard, S3 e acima, e não no nível Básico. Quando reduz a sua base de dados para um nível ou nível não suportado, o índice da loja de colunas fica indisponível. O sistema mantém o índice da loja de colunas, mas nunca alavanca o índice. Se posteriormente voltar a atualizar para um nível ou nível suportado, o seu índice de loja de colunas está imediatamente pronto para ser alavancado novamente.

Se tiver um índice de loja de colunas **agrupado,** toda a tabela fica indisponível após a desvalorização. Por isso, recomendamos que deixe cair todos os índices de loja de colunas *agrupados* antes de reduzir a sua base de dados para um nível ou nível não suportado.

> [!Note]
> SQL Managed Instance suporta índices ColumnStore em todos os níveis.

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

## <a name="next-steps"></a>Próximos passos

- [Quickstart 1: In-Memory OLTP Technologies para um desempenho T-SQL mais rápido](https://msdn.microsoft.com/library/mt694156.aspx)
- [Utilize o OLTP in-memory numa aplicação Azure SQL existente](in-memory-oltp-configure.md)
- [Monitor de armazenamento OLTP in-memory](in-memory-oltp-monitor-space.md) para OLTP in-memory
- [Experimentar as funcionalidades dentro da memória](in-memory-sample.md)

## <a name="additional-resources"></a>Recursos adicionais

### <a name="deeper-information"></a>Informação mais profunda

- [Saiba como o Qurum duplica a carga de trabalho da base de dados chave enquanto baixa o DTU em 70% com o OLTP in-memory na Base de Dados SQL](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)
- [In-Memory OLTP Blog Post](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)
- [Saiba mais sobre o OLTP in-memory](https://msdn.microsoft.com/library/dn133186.aspx)
- [Saiba mais sobre índices de lojas de colunas](https://msdn.microsoft.com/library/gg492088.aspx)
- [Conheça a análise operacional em tempo real](https://msdn.microsoft.com/library/dn817827.aspx)
- Ver [Padrões de Carga de Trabalho Comuns e Considerações de Migração](https://msdn.microsoft.com/library/dn673538.aspx) (que descreve padrões de carga de trabalho em que o OLTP in-memory geralmente proporciona ganhos significativos de desempenho)

### <a name="application-design"></a>Design da aplicação

- [OLTP in-memory (otimização na memória)](https://msdn.microsoft.com/library/dn133186.aspx)
- [Utilize o OLTP in-memory numa aplicação Azure SQL existente](in-memory-oltp-configure.md)

### <a name="tools"></a>Ferramentas

- [Portal do Azure](https://portal.azure.com/)
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
