---
title: Tecnologias de SQL da base de dados dentro da memória do Azure | Documentos da Microsoft
description: Tecnologias de SQL da base de dados dentro da memória do Azure aumentam muito o desempenho do transacional e cargas de trabalho de análise.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jodebrui
ms.author: jodebrui
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: c873587a640bb36e9fa43e314bf789a207956ae0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57854845"
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-sql-database"></a>Otimizar o desempenho ao utilizar tecnologias dentro da memória na base de dados SQL

Tecnologias dentro da memória na SQL Database do Azure permitem-lhe melhorar o desempenho da sua aplicação e potencialmente reduzem os custos da base de dados. 

## <a name="when-to-use-in-memory-technologies"></a>Quando utilizar tecnologias dentro da memória

Usando tecnologias dentro da memória da base de dados do Azure SQL, pode obter aperfeiçoamentos de desempenho com várias cargas de trabalho:

- **Transacional** (processamento transacional online (OLTP)) em que a maioria das solicitações ler ou atualizar o menor conjunto de dados (por exemplo, operações de CRUD).
- **Análise** (processamento analítico online (OLAP)) em que a maioria das consultas possui cálculos complexos para os relatórios fins, com um determinado número de consultas que carregar e acrescentar dados para as tabelas existentes (então, a chamada de carregamento em massa) ou para eliminar o dados das tabelas. 
- **Misto** (processamento analítico/transação do híbrida (HTAP)) em que as consultas OLTP e OLAP são executadas no mesmo conjunto de dados.

Tecnologias dentro da memória podem melhorar o desempenho destas cargas de trabalho, mantendo os dados que devem ser processados na memória, usando a compilação nativo das consultas, ou avançada de processamento, tal como lotes processamento e instruções de SIMD que estão disponíveis no hardware subjacente. 

## <a name="overview"></a>Descrição geral

Base de dados SQL do Azure tem as seguintes tecnologias dentro da memória:
- *[OLTP dentro da memória](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)*  aumenta o número de transações por segundo e reduz a latência para processamento de transações. Os cenários que tiram partido de OLTP dentro da memória são: processamento, como de negociação e de jogos, ingestão de dados de eventos ou dispositivos de IoT, colocação em cache, o carregamento de dados e a tabela temporária e cenários de variável de tabela de transações de alto débito.
- *Colocar em cluster os índices columnstore* reduzir sua pegada de armazenamento (até 10 vezes) e melhore o desempenho de consultas de análise e relatórios. Pode utilizá-lo com tabelas de fatos em seus data marts para se ajustar mais dados na base de dados e melhorar o desempenho. Além disso, pode utilizá-lo com dados históricos na sua base de dados operacional para arquivar e ser capaz de consultar até 10 vezes mais dados.
- *Os índices columnstore não em cluster* para HTAP ajudam-para obter informações em tempo real sobre o seu negócio através de consultar a base de dados operacional diretamente, sem a necessidade de executar uma extração cara, transformar e carregar (ETL) processar e espere até que o armazém de dados deve ser preenchida. Índices columnstore não em cluster permitem a rápida execução das consultas de análise na base de dados OLTP, reduzindo o impacto sobre a carga de trabalho operacional.
- *Com otimização de memória em cluster os índices columnstore* para HTAP permite-lhe efetuar o processamento de transações rápido e a *simultaneamente* executar consultas de análise muito rapidamente nos mesmos dados.

Os índices columnstore e OLTP dentro da memória desde parte do produto SQL Server 2012 e de 2014, respectivamente. Base de dados SQL do Azure e o SQL Server compartilham a mesma implementação de tecnologias dentro da memória. Daqui em diante, novas capacidades para essas tecnologias são lançadas na base de dados do Azure SQL em primeiro lugar, antes de serem liberadas no SQL Server.

## <a name="benefits-of-in-memory-technology"></a>Benefícios da tecnologia na memória

Devido à consulta mais eficiente e processamento de transações, tecnologias dentro da memória também ajudá-lo a reduzir os custos. Normalmente, não terá de atualizar o escalão de preço da base de dados para atingir ganhos de desempenho. Em alguns casos, pode até mesmo ser capazes de reduzir o escalão de preço, enquanto ainda veem melhorias de desempenho com tecnologias dentro da memória.

Aqui estão dois exemplos de como o OLTP dentro da memória ajudou a melhorar significativamente o desempenho:

- Com o OLTP dentro da memória, [soluções de negócios de quórum foi capaz de duplicar a sua carga de trabalho enquanto melhora DTUs 70%](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database).

  - DTU significa *unidade de transação de base de dados*, e inclui uma medida de consumo de recursos.
- O vídeo seguinte demonstra uma melhoria significativa no consumo de recursos com uma carga de trabalho de exemplo: [OLTP dentro da memória no vídeo de base de dados SQL do Azure](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB).
  - Para obter mais informações, consulte a mensagem de blogue: [OLTP dentro da memória na mensagem de blogue de base de dados SQL do Azure](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

> [!NOTE]  
> Tecnologias dentro da memória estão disponíveis no Premium e crítico para a empresa camada SQL do Azure bases de dados e conjuntos elásticos Premium.

O vídeo seguinte explica os ganhos de desempenho possíveis com tecnologias dentro da memória na base de dados do Azure SQL. Lembre-se de que o ganho de desempenho que verá sempre depende de vários fatores, incluindo a natureza da carga de trabalho e os dados, o padrão de acesso da base de dados e assim por diante.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>
>

Este artigo descreve aspectos do OLTP dentro da memória e columnstore índices que são específicos para a base de dados do Azure SQL e também inclui exemplos:

- Verá o impacto dessas tecnologias em limites de tamanho de armazenamento e dados.
- Verá como gerir o movimento de bases de dados usar essas tecnologias entre os escalões de preços diferentes.
- Verá dois exemplos que ilustram o uso de OLTP na memória, bem como os índices columnstore na base de dados do Azure SQL.

Para obter mais informações, consulte:

- [Descrição geral de OLTP na memória e cenários de uso](https://msdn.microsoft.com/library/mt774593.aspx) (inclui referências a casos práticos de clientes e informações para começar a trabalhar)
- [Documentação para OLTP dentro da memória](https://msdn.microsoft.com/library/dn133186.aspx)
- [Guia de índices Columnstore](https://msdn.microsoft.com/library/gg492088.aspx)
- Híbrido transacional/analítico de processamento (HTAP), também conhecido como [análises operacionais em tempo real](https://msdn.microsoft.com/library/dn817827.aspx)

## <a name="in-memory-oltp"></a>OLTP dentro da memória

Tecnologia OLTP na memória fornece operações de acesso de dados de resposta extremamente rápidos, manter todos os dados na memória. Ele também usa índices especializados, compilação nativo de consultas e acesso a dados sem bloqueios temporários para melhorar o desempenho da carga de trabalho OLTP. Existem duas formas de organizar os dados de OLTP dentro da memória:

- **Com otimização de memória rowstore** formato em que cada linha é um objeto de memória em separado. Este é um formato de OLTP dentro da memória clássico otimizado para cargas de trabalho OLTP de alto desempenho. Existem dois tipos de tabelas com otimização de memória que podem ser utilizados no formato rowstore com otimização de memória:
  - *Tabelas duráveis* (SCHEMA_AND_DATA) em que as linhas colocadas na memória são mantidas depois de reiniciar o servidor. Este tipo de tabelas se comporte como uma tabela de rowstore tradicional com as vantagens adicionais das otimizações na memória.
  - *Duradoura não tabelas* (SCEMA_ONLY) onde as linhas são preservados para não após o reinício. Este tipo de tabela foi concebido para dados temporários (por exemplo, a substituição de tabelas temporárias) ou tabelas em que precisa rapidamente carregar os dados antes de movê-lo para alguma tabela persistente (então, a chamada de tabelas de testes).
- **Com otimização de memória de columnstore** formato em que os dados são organizados num formato em colunas. Esta estrutura destina-se a cenários HTAP em que precisa para executar consultas de análise sobre a mesma estrutura de dados onde está a executar a carga de trabalho OLTP.

> [!Note]
> Tecnologia de OLTP dentro da memória foi concebida para as estruturas de dados que podem residir totalmente na memória. Uma vez que os dados dentro da memória não podem ser descarregados para o disco, certifique-se de que está a utilizar base de dados que tenha memória suficiente. Ver [limite de tamanho e o armazenamento de dados para OLTP dentro da memória](#data-size-and-storage-cap-for-in-memory-oltp) para obter mais detalhes.

Uma palavra em OLTP na memória: [Início rápido 1: Tecnologias de OLTP dentro da memória para um desempenho de T-SQL com mais rapidez](https://msdn.microsoft.com/library/mt694156.aspx) (a outro artigo para ajudá-lo a começar)

Vídeos detalhados sobre as tecnologias:

- [OLTP dentro da memória na base de dados do Azure SQL](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (que contém uma demonstração dos benefícios de desempenho e passos para reproduzir esses resultados por conta própria)
- [Vídeos OLTP dentro da memória: O que é e quando/como usá-lo](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/)

Existe uma forma programática para compreender se um determinado banco de dados suporta o OLTP dentro da memória. Pode executar a consulta de Transact-SQL seguinte:
```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```
Se a consulta devolve **1**, OLTP dentro da memória é suportado nesta base de dados. As seguintes consultas de identificam todos os objetos que tenham de ser removidas antes de uma base de dados pode ser desatualizada para básico/Standard:
```
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Limite de tamanho e o armazenamento de dados para OLTP dentro da memória

OLTP dentro da memória inclui tabelas com otimização de memória, que são utilizadas para armazenar dados de utilizador. Essas tabelas são necessários para caber na memória. Como gerencia a memória diretamente no serviço de base de dados SQL, temos o conceito de uma quota para dados de utilizador. Essa idéia é referida como *armazenamento OLTP dentro da memória*.

Cada base de dados única suportado e cada conjunto elástico escalão de preço do escalão de preço inclui uma certa quantidade de armazenamento OLTP dentro da memória. Ver [limites de recursos baseados em DTU - base de dados individual](sql-database-dtu-resource-limits-single-databases.md), [limites de recursos baseados em DTU - conjuntos elásticos](sql-database-dtu-resource-limits-elastic-pools.md),[limites de recursos baseados em vCore - bases de dados individuais](sql-database-vcore-resource-limits-single-databases.md) e [limites de recursos baseados em vCore - conjuntos elásticos](sql-database-vcore-resource-limits-elastic-pools.md).

Os seguintes itens contam para o limite de armazenamento OLTP dentro da memória:

- Linhas de dados de utilizador ativo em tabelas com otimização de memória e as variáveis de tabela. Tenha em atenção que as versões de linha antiga não contam para o limite máximo.
- Índices em tabelas com otimização de memória.
- Sobrecarga operacional de operações de ALTER TABLE.

Se atingir o limite, receberá um erro de limite de quota e já não é possível inserir ou atualizar os dados. Para mitigar este erro, eliminar dados ou aumentar o escalão de preço da base de dados ou do conjunto.

Para obter detalhes sobre como monitorizar a utilização do armazenamento OLTP dentro da memória e configurar alertas quando quase a atingir o limite, consulte [armazenamento de monitorizar dentro da memória](sql-database-in-memory-oltp-monitoring.md).

#### <a name="about-elastic-pools"></a>Sobre conjuntos elásticos

Com os conjuntos elásticos, o armazenamento OLTP dentro da memória é partilhado entre todas as bases de dados no conjunto. Por conseguinte, a utilização numa base de dados pode potencialmente afetar outras bases de dados. São dois mitigações para isso:

- Configurar uma `Max-eDTU` ou `MaxvCore` para bases de dados que é menor do que a contagem de eDTU ou vCore para o conjunto como um todo. Este valor máximo caps a utilização de armazenamento OLTP dentro da memória, em qualquer base de dados no conjunto, o tamanho que corresponde à contagem de eDTU.
- Configurar uma `Min-eDTU` ou `MinvCore` superior a 0. Este mínimo garante que cada base de dados no conjunto tem a quantidade de armazenamento OLTP dentro da memória disponível que corresponde à configurada `Min-eDTU` ou `vCore`.

### <a name="changing-service-tiers-of-databases-that-use-in-memory-oltp-technologies"></a>Alterar escalões de serviço de bases de dados que utilizam tecnologias de OLTP dentro da memória

Pode sempre atualizar sua base de dados ou uma instância para um escalão superior, como de fins gerais para crítico para a empresa (ou Standard para Premium). Apenas aumentam os recursos e funcionalidade disponível.

Mas fazer downgrade a camada pode afetar negativamente o seu banco de dados. O impacto é especialmente evidente quando mudar a versão de negócio crítico para fins gerais (ou de Premium para Standard ou Basic) quando a sua base de dados contém objetos de OLTP na memória. Tabelas com otimização de memória não estão disponíveis após a mudança para versão anterior (mesmo que eles permanecem visíveis). As mesmas considerações aplicam-se quando está reduzindo o escalão de preço de um conjunto elástico ou mover uma base de dados com tecnologias dentro da memória, para um Standard ou Basic conjunto elástico.

> [!Important]
> OLTP dentro da memória não é suportado no escalão fins gerais, Standard ou Basic. Por conseguinte, não é possível mover uma base de dados com quaisquer objetos de OLTP na memória para o escalão Standard ou Basic.

Antes de mudar a versão da base de dados para o Standard/Basic, remova todas as tabelas com otimização de memória e tipos de tabela, bem como todos os módulos de T-SQL compilados nativamente. 

*Recursos de dimensionamento para baixo no escalão crítico para a empresa*: Dados em tabelas com otimização de memória têm ajustadas o armazenamento OLTP dentro da memória que está associado a camada da base de dados ou a instância gerida, ou está disponível no conjunto elástico. Se tentar reduzir verticalmente a camada ou mover a base de dados para um conjunto que não tem armazenamento OLTP dentro da memória suficiente disponível, a operação falhar.

## <a name="in-memory-columnstore"></a>Columnstore na memória

Tecnologia columnstore dentro da memória é de que lhe permite armazenar e consultar uma grande quantidade de dados nas tabelas. Formato de armazenamento de dados baseado em colunas de utiliza a tecnologia Columnstore e processamento de consultas de batch para conseguir obter até 10 vezes o desempenho de consulta em cargas de trabalho OLAP sobre o armazenamento orientado por linhas tradicional. Também pode obter ganhos de até 10 vezes a compressão de dados sobre o tamanho de dados não comprimidos.
Existem dois tipos de modelos de columnstore, que pode utilizar para organizar os dados:

- **Columnstore em cluster** onde todos os dados na tabela é organizado no formato em colunas. Nesse modelo, todas as linhas na tabela são colocadas no formato colunar que altamente comprime os dados e permite-lhe executar consultas rápidas analíticas e relatórios na tabela. Dependendo da natureza dos seus dados, o tamanho dos dados pode ser reduzido 10 x-100 x. Modelo de columnstore em cluster também permite a rápida ingestão de grande quantidade de dados (carregamento em massa) desde lotes grandes de dados maiores do que 100 mil linhas são compactadas antes de serem armazenadas no disco. Este modelo é uma boa escolha para os cenários de armazém de dados clássicos. 
- **Columnstore em cluster não** onde os dados são armazenados na tabela de rowstore tradicional e existe um índice no formato columnstore que é utilizado para as consultas de análise. Este modelo permite o processamento transacional análise híbrida (HTAP): a capacidade de executar análises em tempo real de alto desempenho em cargas de trabalho transacionais. Consultas OLTP são executadas na tabela de rowstore otimizada para aceder a um pequeno conjunto de linhas, enquanto as consultas OLAP são executadas num índice columnstore, que é a melhor escolha para análises e da análise. Otimizador de consulta de base de dados SQL do Azure escolhe dinamicamente rowstore ou columnstore formato com base na consulta. Os índices columnstore não em cluster não diminuir o tamanho dos dados, uma vez que o conjunto de dados original é mantido na tabela original rowstore sem qualquer alteração. No entanto, o tamanho do índice columnstore adicionais deve estar na ordem de magnitude menor do que o índice de árvore B equivalente.

> [!Note]
> Tecnologia columnstore dentro da memória mantém apenas os dados que é necessário para processamento na memória, enquanto os dados que não podem caber na memória são armazenados no disco. Por conseguinte, a quantidade de dados em estruturas de columnstore dentro da memória pode exceder a quantidade de memória disponível. 

Vídeo aprofundado sobre a tecnologia:

- [Índice Columnstore: Vídeos de análise dentro da memória do Ignite 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/)

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Tamanho dos dados e armazenamento para os índices columnstore

Os índices Columnstore não são necessárias para caber na memória. Por conseguinte, o único limite no tamanho dos índices é o máximo da base de dados tamanho geral, que está documentado na [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) e [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md) artigos.

Quando utiliza os índices columnstore em cluster, é utilizada compressão em colunas para o armazenamento de tabela base. Esta compressão pode reduzir significativamente os requisitos de espaço de armazenamento dos seus dados de utilizador, o que significa que pode se encaixar mais dados na base de dados. E a compactação é possível aumentar ainda mais ao [compactação de arquivo em colunas](https://msdn.microsoft.com/library/cc280449.aspx#using-columnstore-and-columnstore-archive-compression). A quantidade de compressão que pode alcançar depende da natureza dos dados, mas 10 vezes a compressão não é incomum.

Por exemplo, se tiver uma base de dados com um tamanho máximo de 1 terabyte (TB) e obter a compactação de 10 vezes, utilizando os índices columnstore, pode se encaixar um total de 10 TB de dados de utilizador na base de dados.

Quando utiliza os índices columnstore não em cluster, a tabela base ainda é armazenada no formato rowstore tradicional. Por conseguinte, a economia de armazenamento não é tão grande como com índices columnstore em cluster. No entanto, se a substituição de um número de índices não agrupados tradicionais com um índice columnstore único, ainda poderá ver uma redução geral nos requisitos de espaço de armazenamento para a tabela.

### <a name="changing-service-tiers-of-databases-containing-columnstore-indexes"></a>Alterar escalões de serviço de bases de dados que contém os índices Columnstore

*Desatualização de base de dados individual para básico ou Standard* pode não ser possível se a sua camada de destino não abaixo S3. Os índices Columnstore são suportados apenas no escalão de preço comercial crítico/Premium e no escalão Standard S3 e acima e não no escalão básico. Quando mudar a sua base de dados para um escalão não suportado ou o nível de versão, o índice columnstore fica indisponível. O sistema mantém o índice columnstore, mas nunca utiliza o índice. Se atualizar mais tarde para uma camada de suporte ou o nível, o índice columnstore é imediatamente pronto para ser aproveitadas novamente.

Se tiver um **em cluster** índice columnstore, a tabela inteira fica indisponível após a mudança para versão anterior. Por conseguinte, recomendamos que remova todos *em cluster* columnstore indexa antes de mudar a versão de base de dados para um nível ou camada não suportada.

> [!Note]
> Gerida suporta a instância os índices ColumnStore em todas as camadas.

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

## <a name="next-steps"></a>Passos Seguintes

- [Início rápido 1: Tecnologias de OLTP dentro da memória para acelerar o desempenho de T-SQL](https://msdn.microsoft.com/library/mt694156.aspx)
- [Utilize o OLTP na memória num aplicativo existente do SQL do Azure](sql-database-in-memory-oltp-migration.md)
- [Armazenamento OLTP dentro da memória de monitor](sql-database-in-memory-oltp-monitoring.md) para OLTP dentro da memória
- [Experimentar as funcionalidades de dentro da memória na base de dados do Azure SQL](sql-database-in-memory-sample.md)

## <a name="additional-resources"></a>Recursos adicionais

### <a name="deeper-information"></a>Informações mais detalhadas

- [Saiba como o quórum duplica a carga de trabalho de chave da base de dados, reduzindo assim a DTU por 70% com o OLTP dentro da memória na base de dados SQL](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)
- [OLTP dentro da memória na mensagem de blogue de base de dados SQL do Azure](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)
- [Saiba mais sobre o OLTP dentro da memória](https://msdn.microsoft.com/library/dn133186.aspx)
- [Saiba mais sobre os índices columnstore](https://msdn.microsoft.com/library/gg492088.aspx)
- [Saiba mais sobre análise operacional em tempo real](https://msdn.microsoft.com/library/dn817827.aspx)
- Ver [padrões de carga de trabalho comuns e considerações sobre a migração](https://msdn.microsoft.com/library/dn673538.aspx) (que descreve padrões de carga de trabalho onde o OLTP dentro da memória fornece mais ganhos significativos de desempenho)

### <a name="application-design"></a>Design do aplicativo

- [(Otimização de memória) de OLTP na memória](https://msdn.microsoft.com/library/dn133186.aspx)
- [Utilize o OLTP na memória num aplicativo existente do SQL do Azure](sql-database-in-memory-oltp-migration.md)

### <a name="tools"></a>Ferramentas

- [Portal do Azure](https://portal.azure.com/)
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
