---
title: Tecnologias na memória
description: As tecnologias na memória do banco de dados SQL do Azure melhoram muito o desempenho de cargas de trabalho transacionais e de análise.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/19/2019
ms.openlocfilehash: de60712451d4c2e8a7d931f7a09352f55be05694
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73810270"
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-sql-database"></a>Otimizar o desempenho usando tecnologias na memória no banco de dados SQL

As tecnologias na memória no banco de dados SQL do Azure permitem melhorar o desempenho do seu aplicativo e, potencialmente, reduzir o custo do seu banco de dados. 

## <a name="when-to-use-in-memory-technologies"></a>Quando usar tecnologias na memória

Usando tecnologias na memória no banco de dados SQL do Azure, você pode obter melhorias de desempenho com várias cargas de trabalho:

- **Transacional** (OLTP (processamento transacional online)) em que a maioria das solicitações lê ou atualiza um conjunto menor de dados (por exemplo, operações CRUD).
- **Análise** (OLAP (processamento analítico online), em que a maioria das consultas tem cálculos complexos para os fins de relatório, com um determinado número de consultas que carregam e anexam dados às tabelas existentes (portanto, chamadas de carregamento em massa) ou excluem os dados de As tabelas. 
- **Misto** (processamento analítico/de transações híbridas (HTAP), em que as consultas OLTP e OLAP são executadas no mesmo conjunto de dados.

As tecnologias na memória podem melhorar o desempenho dessas cargas de trabalho, mantendo os dados que devem ser processados na memória, usando a compilação nativa das consultas ou processamento avançado, como processamento em lotes e instruções SIMD disponíveis no hardware subjacente. 

## <a name="overview"></a>Descrição geral

O banco de dados SQL do Azure tem as seguintes tecnologias na memória:
- O *[OLTP na memória](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)* aumenta o número de transações por segundo e reduz a latência para processamento de transações. Os cenários que se beneficiam do OLTP na memória são: processamento de transações de alta taxa de transferência, como comércio e jogos, ingestão de dados de eventos ou dispositivos IoT, cache, carregamento de dados e cenários de variáveis de tabela e tabelas temporárias.
- Os *índices Columnstore clusterizados* reduzem seu espaço de armazenamento (até 10 vezes) e melhoram o desempenho para consultas de análise e relatório. Você pode usá-lo com tabelas de fatos em seu marts de dados para ajustar mais dados em seu banco e melhorar o desempenho. Além disso, você pode usá-lo com dados históricos em seu banco de dado operacional para arquivar e ser capaz de consultar até 10 vezes mais dados.
- Os *índices columnstore não clusterizados* para HTAP ajudam você a obter informações em tempo real sobre seus negócios por meio da consulta do banco de dados operacional diretamente, sem a necessidade de executar um processo de ETL (extração, transformação e carregamento) caro e aguardar por eles o depósito a ser populado. Os índices columnstore não clusterizados permitem a execução rápida de consultas de análise no banco de dados OLTP, ao mesmo tempo que reduz o impacto na carga de trabalho operacional.
- Os *índices columnstore clusterizados com otimização de memória* para HTAP permitem que você execute o processamento rápido de transações e execute *simultaneamente* consultas de análise com rapidez nos mesmos dados.

Tanto os índices columnstore quanto o OLTP na memória fazem parte do produto SQL Server desde 2012 e 2014, respectivamente. O banco de dados SQL do Azure e SQL Server compartilham a mesma implementação de tecnologias na memória. No futuro, novos recursos para essas tecnologias são lançados primeiro no banco de dados SQL do Azure, antes de serem lançados em SQL Server.

## <a name="benefits-of-in-memory-technology"></a>Benefícios da tecnologia na memória

Devido ao processamento mais eficiente de consulta e transação, as tecnologias na memória também ajudam a reduzir o custo. Normalmente, você não precisa atualizar o tipo de preço do banco de dados para obter ganhos de desempenho. Em alguns casos, você pode até mesmo ser capaz de reduzir o tipo de preço e ainda ver melhorias de desempenho com tecnologias na memória.

Aqui estão dois exemplos de como o OLTP na memória ajudou a melhorar significativamente o desempenho:

- Ao usar o OLTP na memória, [as soluções comerciais de quorum podiam dobrar sua carga de trabalho e, ao mesmo tempo, melhorar as DTUs em 70%](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database).

  - DTU significa *unidade de transação de banco de dados*e inclui uma medida de consumo de recursos.
- O vídeo a seguir demonstra uma melhoria significativa no consumo de recursos com uma carga de trabalho de exemplo: [OLTP na memória no vídeo do banco de dados SQL do Azure](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB).
  - Para obter mais informações, consulte a postagem do blog: [OLTP na memória na postagem do blog do banco de dados SQL do Azure](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

> [!NOTE]  
> As tecnologias na memória estão disponíveis em bancos de dados SQL do Azure de camada Premium e Comercialmente Crítico e pools elásticos Premium.

O vídeo a seguir explica possíveis ganhos de desempenho com tecnologias na memória no banco de dados SQL do Azure. Lembre-se de que o benefício de desempenho que você vê sempre depende de muitos fatores, incluindo a natureza da carga de trabalho e dos dados, o padrão de acesso do banco e assim por diante.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>
>

Este artigo descreve aspectos de OLTP na memória e índices columnstore que são específicos para o banco de dados SQL do Azure e também inclui exemplos:

- Você verá o impacto dessas tecnologias em limites de armazenamento e de tamanho de dados.
- Você verá como gerenciar a movimentação de bancos de dados que usam essas tecnologias entre os diferentes tipos de preço.
- Você verá dois exemplos que ilustram o uso de OLTP na memória, bem como índices columnstore no banco de dados SQL do Azure.

Para obter mais informações, consulte:

- [Visão geral e cenários de uso do OLTP na memória](https://msdn.microsoft.com/library/mt774593.aspx) (inclui referências a estudos de caso de cliente e informações para começar)
- [Documentação para OLTP na memória](https://msdn.microsoft.com/library/dn133186.aspx)
- [Guia de índices Columnstore](https://msdn.microsoft.com/library/gg492088.aspx)
- HTAP (processamento transacional/analítico híbrido), também conhecido como [análise operacional em tempo real](https://msdn.microsoft.com/library/dn817827.aspx)

## <a name="in-memory-oltp"></a>OLTP na memória

A tecnologia OLTP na memória fornece operações de acesso a dados extremamente rápidas, mantendo todos os dados na memória. Ele também usa índices especializados, compilação nativa de consultas e acesso a dados sem trava para melhorar o desempenho da carga de trabalho OLTP. Há duas maneiras de organizar seus dados OLTP na memória:

- Formato **de armazenamento com otimização de memória,** em que cada linha é um objeto de memória separado. Este é um formato OLTP na memória clássico otimizado para cargas de trabalho OLTP de alto desempenho. Há dois tipos de tabelas com otimização de memória que podem ser usadas no formato de armazenamento com otimização de memória:
  - As *tabelas duráveis* (SCHEMA_AND_DATA) em que as linhas colocadas na memória são preservadas após a reinicialização do servidor. Esse tipo de tabelas se comporta como uma tabela de armazenamento tradicional de lojas com os benefícios adicionais das otimizações na memória.
  - *Tabelas não duráveis* (SCHEMA_ONLY) em que as linhas não são preservadas após a reinicialização. Esse tipo de tabela é projetado para dados temporários (por exemplo, substituição de tabelas temporárias) ou tabelas em que você precisa carregar rapidamente os dados antes de movê-los para algumas tabelas persistentes (portanto, chamadas de tabelas de preparo).
- Formato **columnstore com otimização de memória** em que os dados são organizados em um formato de coluna. Essa estrutura é projetada para cenários HTAP em que você precisa executar consultas analíticas na mesma estrutura de dados em que a carga de trabalho OLTP está em execução.

> [!Note]
> A tecnologia OLTP na memória é projetada para as estruturas de dados que podem residir totalmente na memória. Como os dados na memória não podem ser descarregados para o disco, certifique-se de que você está usando um banco de dado com memória suficiente. Consulte [tamanho dos dados e limite de armazenamento para OLTP na memória](#data-size-and-storage-cap-for-in-memory-oltp) para obter mais detalhes.

Uma rápida introdução sobre OLTP na memória: início rápido [1: tecnologias OLTP em memória para um desempenho mais rápido do T-SQL](https://msdn.microsoft.com/library/mt694156.aspx) (outro artigo para ajudá-lo a começar)

Vídeos detalhados sobre as tecnologias:

- [OLTP na memória no banco de dados SQL do Azure](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (que contém uma demonstração dos benefícios de desempenho e as etapas para reproduzir esses resultados por conta própria)
- [Vídeos OLTP na memória: o que é e quando/como usá-lo](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/)

Há uma maneira programática de entender se um determinado banco de dados dá suporte a OLTP na memória. Você pode executar a seguinte consulta Transact-SQL:
```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```
Se a consulta retornar **1**, o OLTP na memória terá suporte neste banco de dados. As consultas a seguir identificam todos os objetos que precisam ser removidos antes que um banco de dados possa ser desatualizado para Standard/Basic:
```
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Tamanho dos dados e limite de armazenamento para OLTP na memória

O OLTP na memória inclui tabelas com otimização de memória, que são usadas para armazenar dados do usuário. Essas tabelas são necessárias para caber na memória. Como você gerencia a memória diretamente no serviço do banco de dados SQL, temos o conceito de uma cota para o usuário. Essa ideia é conhecida como *armazenamento OLTP na memória*.

Cada tipo de preço de banco de dados único com suporte e cada tipo de preço de pool elástico inclui uma determinada quantidade de armazenamento OLTP na memória. Confira [limites de recursos baseados em DTU-banco de dados individual](sql-database-dtu-resource-limits-single-databases.md), [limites de recursos baseados em DTU-pools elásticos](sql-database-dtu-resource-limits-elastic-pools.md),[limites de recursos baseados em VCORE-bancos de dados individuais](sql-database-vcore-resource-limits-single-databases.md) e [limites de recursos baseados em VCORE-pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md).

Os itens a seguir contam para o limite de armazenamento OLTP na memória:

- Linhas de dados de usuário ativas em tabelas com otimização de memória e variáveis de tabela. Observe que as versões de linha antigas não contam para o limite.
- Índices em tabelas com otimização de memória.
- Sobrecarga operacional de operações ALTER TABLE.

Se você atingir o limite, receberá um erro de falta de cota e não poderá mais inserir ou atualizar os dados. Para atenuar esse erro, exclua dados ou aumente o tipo de preço do banco de dados ou do pool.

Para obter detalhes sobre como monitorar a utilização do armazenamento OLTP na memória e configurar alertas quando você quase atinge o limite, consulte [monitorar o armazenamento na memória](sql-database-in-memory-oltp-monitoring.md).

#### <a name="about-elastic-pools"></a>Sobre pools elásticos

Com pools elásticos, o armazenamento OLTP na memória é compartilhado entre todos os bancos de dados no pool. Portanto, o uso em um banco de dados pode potencialmente afetar outros bancos. Duas atenuações para isso são:

- Configure um `Max-eDTU` ou `MaxvCore` para bancos de dados inferiores à contagem de eDTU ou vCore do pool como um todo. Esse máximo limita a utilização do armazenamento OLTP na memória, em qualquer banco de dados no pool, para o tamanho que corresponde à contagem de eDTU.
- Configure um `Min-eDTU` ou `MinvCore` maior que 0. Esse mínimo garante que cada banco de dados no pool tenha a quantidade de armazenamento OLTP na memória disponível que corresponde ao `Min-eDTU` ou `vCore`configurado.

### <a name="changing-service-tiers-of-databases-that-use-in-memory-oltp-technologies"></a>Alterando as camadas de serviço de bancos de dados que usam tecnologias OLTP na memória

Você sempre pode atualizar seu banco de dados ou instância para uma camada superior, como de Uso Geral para Comercialmente Crítico (ou Standard para Premium). A funcionalidade e os recursos disponíveis só aumentam.

Mas o downgrade da camada pode afetar negativamente o banco de dados. O impacto é especialmente aparente quando você faz o downgrade de Comercialmente Crítico para Uso Geral (ou Premium para Standard ou Basic) quando seu banco de dados contém objetos OLTP na memória. As tabelas com otimização de memória não estão disponíveis após o downgrade (mesmo se permanecerem visíveis). As mesmas considerações se aplicam quando você está reduzindo o tipo de preço de um pool elástico ou movendo um banco de dados com tecnologias na memória para um pool elástico Standard ou básico.

> [!Important]
> O OLTP na memória não tem suporte na camada Uso Geral, Standard ou Basic. Portanto, não é possível mover um banco de dados que tenha objetos OLTP na memória para a camada Standard ou Basic.

Antes de fazer o downgrade do banco de dados para Standard/Basic, remova todas as tabelas com otimização de memória e os tipos de tabela, bem como todos os módulos T-SQL compilados nativamente. 

*Recursos de dimensionamento na camada de comercialmente crítico*: os dados em tabelas com otimização de memória devem se ajustar ao armazenamento OLTP na memória que está associado à camada do banco de dados ou instância gerenciada, ou está disponível no pool elástico. Se você tentar reduzir verticalmente a camada ou mover o banco de dados para um pool que não tem armazenamento OLTP disponível na memória suficiente, a operação falhará.

## <a name="in-memory-columnstore"></a>Columnstore na memória

A tecnologia columnstore na memória está permitindo que você armazene e consulte uma grande quantidade de dados nas tabelas. A tecnologia Columnstore usa o formato de armazenamento de dados baseado em coluna e o processamento de consulta em lote para obter até 10 vezes o desempenho da consulta em cargas de trabalho OLAP em relação ao armazenamento tradicional orientado por linha. Você também pode obter ganhos de até 10 vezes a compactação de dados em relação ao tamanho de dados descompactado.
Há dois tipos de modelos columnstore que você pode usar para organizar seus dados:

- **Columnstore clusterizado** onde todos os dados na tabela são organizados no formato colunar. Nesse modelo, todas as linhas da tabela são colocadas no formato de coluna que compacta os dados e permite que você execute consultas e relatórios analíticos rápidos na tabela. Dependendo da natureza dos seus dados, o tamanho dos dados pode ser reduzido com 10x-100x. O modelo columnstore clusterizado também permite a ingestão rápida de grandes quantidades de dados (carregamento em massa), já que grandes lotes de dados com mais de 100 mil linhas são compactados antes de serem armazenados no disco. Esse modelo é uma boa opção para os cenários de data warehouse clássicas. 
- **Columnstore não clusterizado** em que os dados são armazenados na tabela de armazenamento tradicional do grupo e há um índice no formato columnstore que é usado para as consultas analíticas. Esse modelo habilita o processamento de análise transacional híbrida (HTAP): a capacidade de executar análise em tempo real de desempenho em uma carga de trabalho transacional. As consultas OLTP são executadas na tabela do repositório de linhas que é otimizada para acessar um pequeno conjunto de filas, enquanto as consultas OLAP são executadas no índice columnstore que é melhor escolha para verificações e análises. O otimizador de consulta do banco de dados SQL do Azure escolhe dinamicamente o formato de repositório ou de columnstore com base na consulta. Os índices columnstore não clusterizados não diminuem o tamanho dos dados, uma vez que o conjunto de dados original é mantido na tabela de armazenamento original sem nenhuma alteração. No entanto, o tamanho do índice columnstore adicional deve estar em ordem de magnitude menor do que o índice equivalente da árvore B.

> [!Note]
> A tecnologia columnstore na memória mantém apenas os dados necessários para o processamento na memória, enquanto os dados que não se ajustam à memória são armazenados em disco. Portanto, a quantidade de dados em estruturas columnstore na memória pode exceder a quantidade de memória disponível. 

Vídeo detalhado sobre a tecnologia:

- [Índice Columnstore: vídeos de análise na memória do Ignite 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/)

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Tamanho dos dados e armazenamento para índices columnstore

Os índices Columnstore não são necessários para caber na memória. Portanto, a única limitação no tamanho dos índices é o tamanho máximo do banco de dados geral, que está documentado no [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) e nos artigos do [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md) .

Quando você usa índices columnstore clusterizados, a compactação colunar é usada para o armazenamento de tabela base. Essa compactação pode reduzir significativamente o volume de armazenamento dos dados do usuário, o que significa que você pode ajustar mais dados no banco de dado. E a compactação pode aumentar ainda mais com a [compactação de arquivamento de coluna](https://msdn.microsoft.com/library/cc280449.aspx#using-columnstore-and-columnstore-archive-compression). A quantidade de compactação que você pode obter depende da natureza dos dados, mas 10 vezes a compactação não é incomum.

Por exemplo, se você tiver um banco de dados com um tamanho máximo de 1 terabyte (TB) e atingir 10 vezes a compactação usando índices columnstore, você poderá se ajustar a um total de 10 TB de dados do usuário no banco de dado.

Quando você usa índices columnstore não clusterizados, a tabela base ainda é armazenada no formato de armazenamento tradicional. Portanto, as economias de armazenamento não são tão grandes quanto com índices columnstore clusterizados. No entanto, se você estiver substituindo vários índices não clusterizados tradicionais por um único índice columnstore, ainda poderá ver uma economia geral na superfície de armazenamento da tabela.

### <a name="changing-service-tiers-of-databases-containing-columnstore-indexes"></a>Alterando as camadas de serviço de bancos de dados que contêm índices Columnstore

O *downgrade de banco de dados individual para básico ou Standard* pode não ser possível se a camada de destino estiver abaixo do S3. Os índices Columnstore têm suporte apenas no tipo de preço Comercialmente Crítico/Premium e na camada Standard, S3 e acima, e não na camada básica. Quando você faz downgrade do banco de dados para uma camada ou nível sem suporte, seu índice columnstore fica indisponível. O sistema mantém o índice columnstore, mas nunca aproveita o índice. Se, mais tarde, você atualizar de volta para uma camada ou nível com suporte, seu índice columnstore estará imediatamente pronto para ser utilizado novamente.

Se você tiver um índice columnstore **clusterizado** , a tabela inteira ficará indisponível após o downgrade. Portanto, recomendamos que você remova todos os índices columnstore *clusterizados* antes de fazer o downgrade de seu banco de dados para uma camada ou nível sem suporte.

> [!Note]
> Instância Gerenciada dá suporte a índices ColumnStore em todas as camadas.

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

## <a name="next-steps"></a>Passos seguintes

- [Início rápido 1: tecnologias OLTP em memória para um desempenho mais rápido do T-SQL](https://msdn.microsoft.com/library/mt694156.aspx)
- [Usar o OLTP na memória em um aplicativo SQL do Azure existente](sql-database-in-memory-oltp-migration.md)
- [Monitorar o armazenamento OLTP na memória](sql-database-in-memory-oltp-monitoring.md) para OLTP na memória
- [Experimente os recursos na memória no banco de dados SQL do Azure](sql-database-in-memory-sample.md)

## <a name="additional-resources"></a>Recursos adicionais

### <a name="deeper-information"></a>Informações mais profundas

- [Saiba como o quorum duplica a carga de trabalho do banco de dados principal ao reduzir a DTU de 70% com OLTP na memória no banco de dados SQL](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)
- [OLTP na memória na postagem do blog do banco de dados SQL do Azure](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)
- [Saiba mais sobre o OLTP na memória](https://msdn.microsoft.com/library/dn133186.aspx)
- [Saiba mais sobre índices columnstore](https://msdn.microsoft.com/library/gg492088.aspx)
- [Saiba mais sobre a análise operacional em tempo real](https://msdn.microsoft.com/library/dn817827.aspx)
- Consulte [padrões comuns de carga de trabalho e considerações de migração](https://msdn.microsoft.com/library/dn673538.aspx) (que descreve os padrões de carga de trabalho em que o OLTP na memória normalmente fornece ganhos de desempenho significativos)

### <a name="application-design"></a>Design da aplicação

- [OLTP na memória (otimização na memória)](https://msdn.microsoft.com/library/dn133186.aspx)
- [Usar o OLTP na memória em um aplicativo SQL do Azure existente](sql-database-in-memory-oltp-migration.md)

### <a name="tools"></a>Ferramentas

- [Portal do Azure](https://portal.azure.com/)
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
