---
title: O que é o nível de serviço de Hiperescala?
description: Este artigo descreve o nível de serviço hyperscale no modelo de compra baseado em vCore na Base de Dados Azure SQL e explica como é diferente dos níveis de serviço de Finalidade Geral e Crítica de Negócios.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/03/2020
ms.openlocfilehash: 3455503570d09daedc5e34cba0bf36d71ddcdcbc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90988113"
---
# <a name="hyperscale-service-tier"></a>Camada de serviços do Hyperscale

O Azure SQL Database é baseado na arquitetura do motor de base de dados do SQL Server que é ajustada para o ambiente em nuvem de forma a garantir uma disponibilidade de 99,99% mesmo em caso de falhas de infraestrutura. Existem três modelos arquitetónicos que são usados na Base de Dados Azure SQL:

- Finalidade Geral/Padrão
- Hyperscale
- Negócio Crítico/Premium

O nível de serviço de hiperescala na Base de Dados Azure SQL é o mais recente nível de serviço no modelo de compra baseado em vCore. Este nível de serviço é um nível de desempenho de armazenamento e cálculo altamente escalável que aproveita a arquitetura Azure para escalar os recursos de armazenamento e cálculo para uma Base de Dados Azure SQL substancialmente além dos limites disponíveis para os níveis de serviço De finalidade geral e business critical.

> [!NOTE]
>
> - Para mais detalhes sobre os níveis de serviço De Finalidade Geral e Business Critical no modelo de compra baseado em vCore, consulte os níveis de serviço [De Finalidade Geral](service-tier-general-purpose.md) e Business [Critical.](service-tier-business-critical.md) Para comparar o modelo de compra baseado em vCore com o modelo de compra baseado em DTU, consulte modelos e recursos de [compra da Base de Dados Azure SQL](purchasing-models.md).
> - O nível de serviço de hiperescala está atualmente disponível apenas para Azure SQL Database, e não Azure SQL Managed Instance.

## <a name="what-are-the-hyperscale-capabilities"></a>Quais são as capacidades de Hiperescala

O nível de serviço de hiperescala na Base de Dados Azure SQL fornece as seguintes capacidades adicionais:

- Suporte para até 100 TB do tamanho da base de dados
- Backups de bases de dados quase instantâneos (baseados em instantâneos armazenados no armazenamento de Azure Blob) independentemente do tamanho sem impacto IO nos recursos computacional  
- A base de dados rápida restaura (com base em instantâneos de ficheiros) em minutos e não em horas ou dias (não um tamanho de operação de dados)
- maior desempenho global devido ao aumento da produção de registo e tempos de transação mais rápidos, independentemente dos volumes de dados
- Escala rápida para fora - você pode providenciar um ou mais nódoas apenas de leitura para descarregar a sua carga de trabalho de leitura e para ser usado como hot-standbys
- Rapid Scale up - você pode, em tempo constante, aumentar os seus recursos de computação para acomodar cargas de trabalho pesadas quando necessário, e, em seguida, escalar os recursos de cálculo de volta quando não é necessário.

O nível de serviço Hyperscale remove muitos dos limites práticos tradicionalmente vistos nas bases de dados em nuvem. Quando a maioria das outras bases de dados são limitadas pelos recursos disponíveis num único nó, as bases de dados no nível de serviço hyperscale não têm esses limites. Com a sua arquitetura de armazenamento flexível, o armazenamento cresce conforme necessário. Na verdade, as bases de dados de hiperescala não são criadas com um tamanho máximo definido. Uma base de dados de hiperescala cresce conforme necessário - e é cobrado apenas pela capacidade que usa. Para cargas de trabalho intensivas de leitura, o nível de serviço de Hiperescala proporciona uma escala rápida, fornecendo réplicas de leitura adicionais, conforme necessário para descarregar cargas de trabalho de leitura.

Além disso, o tempo necessário para criar cópias de segurança na base de dados ou para escalar para cima ou para baixo já não está ligado ao volume de dados na base de dados. As bases de dados de hiperescala podem ser apoiadas instantaneamente. Também pode escalar uma base de dados nas dezenas de terabytes para cima ou para baixo em minutos. Esta capacidade liberta-o das preocupações de ser encurralado pelas suas escolhas iniciais de configuração.

Para obter mais informações sobre os tamanhos de cálculo para o nível de serviço Hyperscale, consulte as [características do nível de serviço](service-tiers-vcore.md#service-tiers).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Quem deve considerar o nível de serviço de Hiperescala

O nível de serviço de Hiperescala destina-se à maioria das cargas de trabalho do negócio, uma vez que proporciona grande flexibilidade e alto desempenho com recursos de computação e armazenamento escaláveis independentemente. Com a capacidade de autodimensionar o armazenamento até 100 TB, é uma ótima escolha para clientes que:

- Têm grandes bases de dados no local e querem modernizar as suas aplicações movendo-se para a nuvem
- Já estão na nuvem e estão limitados pelas restrições máximas de tamanho da base de dados de outros níveis de serviço (1-4 TB)
- Têm bases de dados mais pequenas, mas requerem uma escala rápida de cálculo vertical e horizontal, alto desempenho, cópia de segurança instantânea e restauro rápido da base de dados.

O nível de serviço de Hiperescala suporta uma ampla gama de cargas de trabalho do SQL Server, desde o OLTP puro à análise pura, mas é principalmente otimizado para o OLTP e transações híbridas e cargas de processamento analítico (HTAP).

> [!IMPORTANT]
> As piscinas elásticas não suportam o nível de serviço Hyperscale.

## <a name="hyperscale-pricing-model"></a>Modelo de preços em hiperescala

O nível de serviço de hiperescala só está disponível no [modelo vCore.](service-tiers-vcore.md) Para alinhar com a nova arquitetura, o modelo de preços é ligeiramente diferente dos níveis de serviço de Propósito Geral ou Business Critical:

- **Cálculo:**

  O preço unitário do cálculo de Hiperescala é por réplica. O preço [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) é aplicado para ler réplicas de escala automaticamente. Criamos uma réplica primária e uma réplica apenas de leitura por base de dados de Hiperescala por padrão.  Os utilizadores podem ajustar o número total de réplicas, incluindo a primária de 1-5.

- **Armazenamento:**

  Não é necessário especificar o tamanho máximo dos dados ao configurar uma base de dados hyperscale. No nível de hiperescala, é cobrado para armazenamento para a sua base de dados com base na alocação real. O armazenamento é automaticamente atribuído entre 40 GB e 100 TB, em incrementos de 10-GB. Vários ficheiros de dados podem crescer ao mesmo tempo, se necessário. Uma base de dados de hiperescala é criada com um tamanho inicial de 10 GB e começa a crescer 10 GB a cada 10 minutos, até atingir o tamanho de 40 GB.

Para obter mais informações sobre preços em escala, consulte preços [da base de dados Azure SQL](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Arquitetura de funções distribuídas

Ao contrário dos motores de base de dados tradicionais que centralizaram todas as funções de gestão de dados num só local/processo (mesmo as chamadas bases de dados distribuídas em produção têm hoje múltiplas cópias de um motor de dados monolítico), uma base de dados hyperscale separa o motor de processamento de consultas, onde a semântica de vários motores de dados diverge, dos componentes que fornecem armazenamento e durabilidade a longo prazo para os dados. Desta forma, a capacidade de armazenamento pode ser escalonada suavemente na medida do necessário (o objetivo inicial é 100 TB). As réplicas apenas de leitura partilham os mesmos componentes de armazenamento para que nenhuma cópia de dados seja necessária para girar uma nova réplica legível.

O diagrama a seguir ilustra os diferentes tipos de nó numa base de dados de hiperescala:

![arquitetura](./media/service-tier-hyperscale/hyperscale-architecture.png)

Uma base de dados de hiperescala contém os seguintes tipos diferentes de componentes:

### <a name="compute"></a>Computação

O nó computacional é onde o motor relacional vive, assim todos os elementos linguísticos, processamento de consulta, e assim por diante, ocorrem. Todas as interações do utilizador com uma base de dados hyperscale acontecem através destes nós de computação. Os nós computacionais têm caches baseados em SSD (labeled RBPEX - Resilient Buffer Pool Extension no diagrama anterior) para minimizar o número de viagens redondas de rede necessárias para obter uma página de dados. Há um nó de computação primário onde todas as cargas de trabalho e transações de leitura são processadas. Existem um ou mais nódoas de computação secundária que funcionam como nódos de espera quentes para efeitos de failover, bem como agir como nó de computação apenas de leitura para descarregar cargas de trabalho de leitura (se esta funcionalidade for desejada).

### <a name="page-server"></a>Servidor de página

Os servidores de página são sistemas que representam um motor de armazenamento em escala.  Cada servidor de página é responsável por um subconjunto das páginas na base de dados.  Nominalmente, cada servidor de página controla entre 128 GB e 1 TB de dados. Nenhum dado é partilhado em mais de uma página do servidor (fora das réplicas que são mantidas para redundância e disponibilidade). O trabalho de um servidor de página é servir páginas de base de dados para os nós de computação a pedido, e manter as páginas atualizadas à medida que as transações atualizam dados. Os servidores de página são mantidos atualizados reproduzindo registos do serviço de registo. Os servidores de página também mantêm caches baseados em SSD para melhorar o desempenho. O armazenamento a longo prazo das páginas de dados é mantido no Azure Storage para uma fiabilidade adicional.

### <a name="log-service"></a>Serviço de registo

O serviço de registo aceita registos da réplica do cálculo primário, persiste numa cache durável e encaminha os registos de registo para o resto das réplicas computacional (para que possam atualizar os seus caches) bem como os servidores de página relevantes, para que os dados possam ser atualizados lá. Desta forma, todas as alterações de dados a partir da réplica do cálculo primário são propagadas através do serviço de registo para todas as réplicas de computação secundária e servidores de página. Finalmente, os registos de registo são empurrados para o armazenamento a longo prazo no Azure Storage, que é um repositório de armazenamento virtualmente infinito. Este mecanismo elimina a necessidade de uma truncação frequente de registos. O serviço de registo também tem cache local para acelerar o acesso aos registos de registos.

### <a name="azure-storage"></a>Storage do Azure

O Azure Storage contém todos os ficheiros de dados numa base de dados. Os servidores de página mantêm os ficheiros de dados no Azure Storage atualizados. Este armazenamento é utilizado para fins de backup, bem como para replicação entre regiões de Azure. As cópias de segurança são implementadas utilizando imagens de armazenamento de ficheiros de dados. As operações de restauro utilizando instantâneos são rápidas, independentemente do tamanho dos dados. Os dados podem ser restaurados a qualquer ponto no tempo dentro do período de retenção de backup da base de dados.

## <a name="backup-and-restore"></a>Cópia de segurança e restauro

As cópias de segurança são baseadas em ficheiros e, portanto, são quase instantâneas. A separação de armazenamento e computação permite empurrar a operação de backup/restauro para a camada de armazenamento para reduzir o fardo de processamento na réplica do cálculo primário. Como resultado, a cópia de segurança da base de dados não afeta o desempenho do nó de computação primária. Da mesma forma, a recuperação de pontos no tempo (PITR) é feita revertendo para arquivar instantâneos, e como tal não é um tamanho de operação de dados. A restauração de uma base de dados de Hiperescala na mesma região de Azure é uma operação de tempo constante, e mesmo bases de dados de múltiplos terabytes podem ser restauradas em minutos em vez de horas ou dias. A criação de novas bases de dados através da restauração de uma cópia de segurança existente também tira partido desta funcionalidade: a criação de cópias de bases de dados para fins de desenvolvimento ou testes, mesmo de bases de dados de tamanho terabyte, é exequível em minutos.

Para restaurar geo-restaurar as bases de dados de Hiperescala, consulte [restaurar uma base de dados de hiperescala para uma região diferente.](#restoring-a-hyperscale-database-to-a-different-region)

## <a name="scale-and-performance-advantages"></a>Vantagens de escala e desempenho

Com a capacidade de girar rapidamente para cima/para baixo nos nós de computação adicionais apenas de leitura, a arquitetura Hyperscale permite capacidades significativas de escala de leitura e também pode libertar o nó de computação primária para servir mais pedidos de escrita. Além disso, os nós computacional podem ser escalados para cima/para baixo rapidamente devido à arquitetura de armazenamento partilhado da arquitetura Hyperscale.

## <a name="create-a-hyperscale-database"></a>Criar uma base de dados de hiperescala

Uma base de dados de hiperescala pode ser criada utilizando o [portal Azure](https://portal.azure.com), [T-SQL,](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current) [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase)ou [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create). As bases de dados de hiperescala só estão disponíveis utilizando o [modelo de compra baseado em vCore.](service-tiers-vcore.md)

O seguinte comando T-SQL cria uma base de dados de hiperescala. Deve especificar tanto a edição como o objetivo de serviço no `CREATE DATABASE` comunicado. Consulte os [limites](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases#hyperscale---provisioned-compute---gen4) de recursos para obter uma lista de objetivos de serviço válidos.

```sql
-- Create a Hyperscale Database
CREATE DATABASE [HyperscaleDB1] (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

Isto criará uma base de dados de hiperescala no hardware da Gen5 com quatro núcleos.

## <a name="upgrade-existing-database-to-hyperscale"></a>Atualizar a base de dados existente para Hyperscale

Pode mover as bases de dados existentes na Base de Dados Azure SQL para Hyperscale utilizando o [portal Azure](https://portal.azure.com), [T-SQL,](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase)ou [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update). Neste momento, esta é uma migração unidirecciona. Não é possível mover bases de dados de Hyperscale para outro nível de serviço, a não ser exportando e importando dados. Para provas de conceito (POCs), recomendamos fazer uma cópia das suas bases de dados de produção e migrar a cópia para Hyperscale. Migrar uma base de dados existente na Base de Dados Azure SQL para o nível de Hiperescala é um tamanho de operação de dados.

O seguinte comando T-SQL move uma base de dados para o nível de serviço de Hiperescala. Deve especificar tanto a edição como o objetivo de serviço no `ALTER DATABASE` comunicado.

```sql
-- Alter a database to make it a Hyperscale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Ligue-se a uma réplica em escala de leitura de uma base de dados de hiperescala

Nas bases de dados hyperscale, o `ApplicationIntent` argumento na cadeia de ligação fornecida pelo cliente dita se a ligação é encaminhada para a réplica de escrita ou para uma réplica secundária apenas de leitura. Se o `ApplicationIntent` conjunto e a base de `READONLY` dados não tiverem uma réplica secundária, a ligação será encaminhada para a réplica primária e predefinições ao `ReadWrite` comportamento.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

As réplicas secundárias de hiperescala são todas idênticas, utilizando o mesmo Objetivo de Nível de Serviço que a réplica primária. Se houver mais de uma réplica secundária, a carga de trabalho é distribuída por todos os secundários disponíveis. Cada réplica secundária é atualizada de forma independente. Assim, diferentes réplicas poderiam ter latência de dados diferentes em relação à réplica primária.

## <a name="database-high-availability-in-hyperscale"></a>Alta disponibilidade da base de dados em Hiperescala

Como em todos os outros níveis de serviço, a Hyperscale garante a durabilidade dos dados para transações comprometidas, independentemente da disponibilidade de réplicas computacionais. A extensão do tempo de inatividade devido à inserção da réplica primária depende do tipo de falha (planeada vs. não planeada), e da presença de pelo menos uma réplica secundária. Numa falha planeada (isto é, um evento de manutenção), o sistema cria a nova réplica primária antes de iniciar uma falha, ou utiliza uma réplica secundária existente como alvo de falha. Numa falha não planeada (isto é, uma falha de hardware na réplica primária), o sistema utiliza uma réplica secundária como alvo de falha, caso exista, ou cria uma nova réplica primária a partir do pool de capacidade de computação disponível. Neste último caso, a duração do tempo de inatividade é mais longa devido aos passos adicionais necessários para criar a nova réplica primária.

Para hiperescala SLA, consulte [sLA para a base de dados Azure SQL](https://azure.microsoft.com/support/legal/sla/sql-database/).

## <a name="disaster-recovery-for-hyperscale-databases"></a>Recuperação de desastres para bases de dados de hiperescala

### <a name="restoring-a-hyperscale-database-to-a-different-region"></a>Restaurar uma base de dados de hiperescala para uma região diferente

Se precisar de restaurar uma base de dados de Hiperescala na Base de Dados Azure SQL para uma região diferente daquela em que está atualmente hospedada, como parte de uma operação de recuperação de desastres ou perfuração, deslocalização ou qualquer outra razão, o método principal é fazer um geo-restauro da base de dados. Isto envolve exatamente os mesmos passos que você usaria para restaurar qualquer outra base de dados na Base de Dados SQL para uma região diferente:

1. Crie um [servidor](logical-servers.md) na região alvo se ainda não tiver um servidor apropriado lá.  Este servidor deve ser propriedade da mesma subscrição que o servidor original (fonte).
2. Siga as instruções no tópico [de geo-restauro](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore) da página sobre a restauração de uma base de dados na Base de Dados Azure SQL a partir de cópias de segurança automáticas.

> [!NOTE]
> Uma vez que a fonte e o alvo se encontram em regiões distintas, a base de dados não pode partilhar o armazenamento instantâneo com a base de dados de origem como em restauros não geo geo, que se completam muito rapidamente. No caso de um geo-restauro de uma base de dados de hiperescala, será uma operação de tamanho de dados, mesmo que o alvo esteja na região emparelhada do armazenamento geo-replicado.  Isto significa que fazer um geo-restauro levará tempo proporcional ao tamanho da base de dados que está a ser restaurada.  Se o alvo estiver na região emparelhada, a cópia será dentro de uma região, que será significativamente mais rápida do que uma cópia inter-região, mas continuará a ser uma operação de tamanho de dados.

## <a name="available-regions"></a><a name=regions></a>Regiões disponíveis

O nível de hiperescala de base de dados Azure SQL está disponível em todas as regiões, mas ativado por padrão nas seguintes regiões listadas abaixo.
Se quiser criar uma base de dados hyperscale numa região que não esteja listada como suportada, pode enviar um pedido de embarque através do portal Azure. Para obter instruções, consulte [os aumentos de quota de pedido para a Base de Dados Azure SQL](quota-increase-request.md) para obter instruções. Ao submeter o seu pedido, utilize as seguintes orientações:

- Utilize o tipo de quota de quota de base de dados SQL de [acesso à Região.](quota-increase-request.md#region)
- Nos detalhes de texto, adicione os núcleos SKU/total do cálculo, incluindo réplicas legíveis.
- Especificar também a tuberculose estimada.

Regiões Habilitados:
- Leste da Austrália
- Austrália Sudeste
- Austrália Central
- Sul do Brasil
- Canadá Central
- E.U.A. Central
- China Leste 2
- China Norte 2
- Ásia Leste
- E.U.A. Leste
- Leste de Nós 2
- França Central
- Alemanha Centro-Oeste
- Leste do Japão
- Oeste do Japão
- Coreia do Sul Central
- Sul da Coreia do Sul
- E.U.A. Centro-Norte
- Europa do Norte
- Leste da Noruega
- Oeste da Noruega
- África do Sul Norte
- E.U.A. Centro-Sul
- Sudeste Asiático
- Suíça Oeste
- Sul do Reino Unido
- Oeste do Reino Unido
- US DoD Centro
- US DoD - Leste
- Eua Govt Arizona
- Eua Govt Texas
- E.U.A. Centro-Oeste
- Europa Ocidental
- E.U.A. Oeste
- E.U.A. Oeste 2

## <a name="known-limitations"></a>Limitações conhecidas

Estas são as limitações atuais para o nível de serviço de Hiperescala a partir de GA.  Estamos a trabalhar ativamente para remover o maior número possível destas limitações.

| Problema | Descrição |
| :---- | :--------- |
| O painel de backups de gestão para um servidor não mostra bases de dados de hiperescala. Estes serão filtrados da vista.  | A Hiperescala tem um método separado para gerir backups, por isso as definições de retenção de retenção de retenção de retenção de retenção de retenção de retenção de retenção de retenção de retenção de retenção de retenção de retenção de retenção de retenção de retenção de retenção de retenção de retenção de retenção de Long-Term e pontual não se aplicam. Assim, as bases de dados de hiperescala não aparecem no painel de backup de gestão.<br><br>Para as bases de dados migradas para Hyperscale a partir de outros níveis de serviço de base de dados Azure SQL, as cópias de segurança pré-migração são mantidas durante o período de [retenção](automated-backups-overview.md#backup-retention) de backup da base de dados de origem. Estas cópias de segurança podem ser usadas para [restaurar](recovery-using-backups.md#programmatic-recovery-using-automated-backups) a base de dados de origem a um ponto no tempo antes da migração.|
| Restauro para um ponto anterior no tempo | Uma base de dados não-Hyperscale não pode ser restaurada como uma base de dados de hiperescala, e uma base de dados de hiperescala não pode ser restaurada como uma base de dados não-Hyperscale. Para uma base de dados não-Hyperscale que tenha sido migrada para Hyperscale alterando o seu nível de serviço, restaure a um ponto no tempo antes da migração e dentro do período de retenção de backup da base de dados é possível [programáticamente](recovery-using-backups.md#programmatic-recovery-using-automated-backups). A base de dados restaurada não será de Hiperescala. |
| Se uma base de dados tiver um ou mais ficheiros de dados maiores do que 1 TB, a migração falha | Em alguns casos, pode ser possível contornar esta questão reduzindo os grandes ficheiros para menos de 1 TB. Se migrar uma base de dados que está a ser utilizada durante o processo de migração, certifique-se de que nenhum ficheiro fica maior que 1 TB. Utilize a seguinte consulta para determinar o tamanho dos ficheiros de base de dados. `SELECT *, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| Instância Gerida do SQL | A azure SQL Managed Instance não é suportado atualmente com bases de dados de hiperescala. |
| Conjuntos Elásticos |  As Piscinas Elásticas não são suportadas atualmente com a Hyperscale.|
| A migração para a Hiperescala é atualmente uma operação unidireccionar | Uma vez que uma base de dados é migrada para Hyperscale, não pode ser migrada diretamente para um nível de serviço não-Hyperscale. Atualmente, a única forma de migrar uma base de dados de Hyperscale para não-Hyperscale é exportar/importar usando um ficheiro bacpac ou outras tecnologias de movimento de dados (Cópia a granel, Fábrica de Dados Azure, Azure Databricks, SSIS, etc.) A exportação/importação bacpac do portal Azure, da PowerShell utilizando [a New-AzSqlDatabaseExport](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaseexport) ou [a New-AzSqlDatabaseImport,](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaseimport)da Azure CLI utilizando [a exportação de az sql db](https://docs.microsoft.com/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-export) e a importação de [az sql db](https://docs.microsoft.com/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-import), e da [REST API](https://docs.microsoft.com/rest/api/sql/databases%20-%20import%20export) não é suportada. A importação/exportação de Bacpac para bases de dados de hiperescala mais pequenas (até 200 GB) é suportada utilizando a versão SSMS e [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) 18.4 e posterior. No que diz as bases de dados de maiores dimensões, a exportação/importação de bacpac pode demorar muito tempo, podendo falhar por várias razões.|
| Migração de bases de dados com objetos OLTP In-Memory | A hiperescala suporta um subconjunto de objetos OLTP In-Memory, incluindo tipos de mesa otimizados na memória, variáveis de tabela e módulos compilados de forma nativa. No entanto, quando qualquer tipo de In-Memory objetos OLTP estão presentes na base de dados em migração, a migração dos níveis de serviço Premium e Business Critical para Hyperscale não é suportada. Para migrar tal base de dados para Hyperscale, todos os In-Memory objetos OLTP e suas dependências devem ser largados. Após a migração da base de dados, estes objetos podem ser recriados. As tabelas otimizadas para memória duráveis e não duradouras não são atualmente suportadas em Hiperescala e devem ser recriadas como tabelas de discos.|
| Georreplicação  | Ainda não é possível configurar a geo-replicação para a hiperescala da base de dados Azure SQL. |
| Cópia da base de dados | A cópia da base de dados em Hyperscale está agora em pré-visualização pública. |
| Integração TDE/AKV | A encriptação transparente da base de dados utilizando o Cofre de Chaves Azure (vulgarmente designado por Bring-Your-Your-Own-Key ou BYOK) está atualmente em pré-visualização. |
| Funcionalidades inteligentes da base de dados | Com exceção da opção "Plano de Força", todas as outras opções de Sintonização Automática ainda não são suportadas na Hyperscale: as opções podem parecer ativadas, mas não haverá recomendações ou ações feitas. |
| Query Performance Insight | A Consulta Performance Insights não é suportada atualmente para bases de dados de hiperescala. |
| Base de Dados de Encolher | DBCC SHRINKDATABASE ou DBCC SHRINKFILE não é suportado atualmente para bases de dados de hiperescala. |
| Verificação de integridade da base de dados | O DBCC CHECKDB não é suportado atualmente para bases de dados de hiperescala. DBCC CHECKFILEGROUP e DBCC CHECKTABLE podem ser usados como uma solução alternativa. Consulte [a Integridade dos Dados na Base de Dados Azure SQL](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) para obter mais informações sobre a gestão da integridade dos dados na Base de Dados Azure SQL. |

## <a name="next-steps"></a>Passos seguintes

- Para uma FAQ em Hyperscale, consulte [perguntas frequentes sobre Hiperescala.](service-tier-hyperscale-frequently-asked-questions-faq.md)
- Para obter informações sobre os níveis de serviço, consulte [os níveis de serviço](purchasing-models.md)
- Consulte [a visão geral dos limites de recursos num servidor](resource-limits-logical-server.md) para obter informações sobre limites nos níveis de servidor e subscrição.
- Para comprar limites de modelos para uma única base de dados, consulte [os limites do modelo de compra baseado em Azure SQL Database vCore para uma única base de dados](resource-limits-vcore-single-databases.md).
- Para obter uma lista de funcionalidades e comparação, consulte [as características comuns sql](features-comparison.md).
 
