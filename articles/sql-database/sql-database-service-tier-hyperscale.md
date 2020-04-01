---
title: Visão geral de hiperescala de hiperescala da base de dados Azure SQL [ Microsoft Docs
description: Este artigo descreve o nível de serviço de hiperescala no modelo de compra baseado em vCore na Base de Dados Azure SQL e explica como é diferente dos níveis de serviço De propósito Geral e Business Critical.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/01/2019
ms.openlocfilehash: 5a9917010b7301bf70c3bebf68c35d82f4839e0f
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409051"
---
# <a name="hyperscale-service-tier"></a>Camada de serviços do Hyperscale

A Base de Dados Azure SQL baseia-se na arquitetura do motor de base de dados do Servidor SQL que é ajustada para o ambiente em nuvem de forma a garantir uma disponibilidade de 99,99%, mesmo nos casos de falhas de infraestrutura. Existem três modelos arquitetônicos que são usados na Base de Dados Azure SQL:
- Finalidade Geral/Norma 
-  Hyperscale
-  Business Critical/Premium

O nível de serviço Hyperscale na Base de Dados Azure SQL é o mais recente nível de serviço no modelo de compra baseado em vCore. Este nível de serviço é um nível de desempenho altamente escalável de armazenamento e cálculo que aproveita a arquitetura Azure para dimensionar os recursos de armazenamento e computação para uma Base de Dados Azure SQL substancialmente para além dos limites disponíveis para os níveis de serviço de Propósito Geral e Business Critical.

> 
> [!NOTE]
> Para mais detalhes sobre os níveis de serviço de Propósito Geral e Business Critical no modelo de compra baseado em vCore, consulte os níveis de serviço [De Propósito Geral](sql-database-service-tier-general-purpose.md) e Business [Critical.](sql-database-service-tier-business-critical.md) Para uma comparação do modelo de compra baseado em vCore com o modelo de compra baseado em DTU, consulte os modelos e recursos de compra da Base de [Dados Azure SQL.](sql-database-service-tiers.md)


## <a name="what-are-the-hyperscale-capabilities"></a>Quais são as capacidades de Hiperescala

O nível de serviço de hiperescala na Base de Dados Azure SQL fornece as seguintes capacidades adicionais:

- Suporte para até 100 TB de tamanho de base de dados
- Backups de base de dados quase instantâneas (com base em instantâneos armazenados no armazenamento de Blob Azure) independentemente do tamanho sem impacto io nos recursos computacionais  
- A base de dados rápida restaura (com base em instantâneos de ficheiros) em minutos em vez de horas ou dias (não um tamanho de operação de dados)
- Maior desempenho global devido a maior produção de log e tempos de compromisso de transação mais rápido, independentemente dos volumes de dados
- Escala rápida para fora - você pode fornecer um ou mais nós de leitura apenas para descarregar a sua carga de trabalho de leitura e para uso como espera quente
- Escala rápida - pode, em tempo constante, aumentar os seus recursos computacionais para acomodar cargas de trabalho pesadas conforme e quando necessário, e depois escalar os recursos computacionais para baixo quando não necessário.

O nível de serviço de Hiperescala remove muitos dos limites práticos tradicionalmente vistos nas bases de dados em nuvem. Quando a maioria das outras bases de dados são limitadas pelos recursos disponíveis num único nó, as bases de dados do nível de serviço de Hiperescala não têm tais limites. Com a sua arquitetura de armazenamento flexível, o armazenamento cresce conforme necessário. Na verdade, as bases de dados de Hiperescala não são criadas com um tamanho máximo definido. Uma base de dados de hiperescala cresce conforme necessário - e você é cobrado apenas pela capacidade que você usa. Para cargas de trabalho intensivas de leitura, o nível de serviço de hiperescala fornece uma escala rápida, fornecendo réplicas de leitura adicionais necessárias para descarregar cargas de trabalho de leitura.

Além disso, o tempo necessário para criar cópias de segurança da base de dados ou para escalar para cima ou para baixo já não está ligado ao volume de dados na base de dados. As bases de dados de hiperescala podem ser apoiadas virtualmente instantaneamente. Pode também escalar uma base de dados nas dezenas de terabytes para cima ou para baixo em minutos. Esta capacidade liberta-o de preocupações sobre ser encaixotado pelas suas escolhas iniciais de configuração.

Para obter mais informações sobre os tamanhos da computação para o nível de serviço de hiperescala, consulte [as características do nível](sql-database-service-tiers-vcore.md#service-tiers)de serviço .

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Quem deve considerar o nível de serviço de hiperescala

O nível de serviço de Hiperescala destina-se à maioria das cargas de trabalho empresariais, uma vez que proporciona uma grande flexibilidade e alto desempenho com recursos de computação e armazenamento independentes escaláveis. Com a capacidade de armazenamento em escala automática até 100 TB, é uma ótima escolha para os clientes que:

- Tenha grandes bases de dados no local e queira modernizar as suas aplicações movendo-se para a nuvem
- Já estão na nuvem e estão limitados pelas restrições máximas de tamanho da base de dados de outros níveis de serviço (1-4 TB)
- Tenha bases de dados mais pequenas, mas requeira uma escala rápida vertical e horizontal da computação, alto desempenho, backup instantâneo e restauração rápida da base de dados.

O nível de serviço Hyperscale suporta uma ampla gama de cargas de trabalho do SQL Server, desde o OLTP puro até à análise pura, mas é otimizado principalmente para cargas de trabalho de transação oLTP e híbrida e processamento analítico (HTAP).

> [!IMPORTANT]
> As piscinas elásticas não suportam o nível de serviço de hiperescala.

## <a name="hyperscale-pricing-model"></a>Modelo de preços de hiperescala

O nível de serviço de hiperescala só está disponível no [modelo vCore.](sql-database-service-tiers-vcore.md) Para alinhar com a nova arquitetura, o modelo de preços é ligeiramente diferente dos níveis de serviço De Propósito Geral ou Business Critical:

- **Computação:**

  O preço unitário da computação hyperscale é por réplica. O preço do [Benefício Híbrido Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) é aplicado para ler automaticamente as réplicas de escala. Criamos uma réplica primária e uma réplica só de leitura por base de dados de Hiperescala por padrão.  Os utilizadores podem ajustar o número total de réplicas, incluindo a primária de 1 a 5.

- **Armazenamento:**

  Não é necessário especificar o tamanho máximo de dados ao configurar uma base de dados de hiperescala. No nível de hiperescala, é cobrado o armazenamento da sua base de dados com base na atribuição real. O armazenamento é automaticamente atribuído entre 40 GB e 100 TB, em incrementos de 10 GB de 10 GB. Vários ficheiros de dados podem crescer ao mesmo tempo, se necessário. Uma base de dados de hiperescala é criada com um tamanho inicial de 10 GB e começa a crescer em 10 GB a cada 10 minutos, até atingir o tamanho de 40 GB.

Para mais informações sobre preços de hiperescala, consulte preços de base de [dados Azure SQL](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Arquitetura de funções distribuídas

Ao contrário dos motores tradicionais de base de dados que centralizaram todas as funções de gestão de dados num só local /processo (mesmo as chamadas bases de dados distribuídas em produção hoje em dia têm várias cópias de um motor de dados monolítico), uma base de dados de Hiperescala separa o motor de processamento de consultas, onde a semântica de vários motores de dados divergem, dos componentes que fornecem armazenamento e durabilidade a longo prazo para os dados. Desta forma, a capacidade de armazenamento pode ser dimensionada sem problemas na medida do necessário (o objetivo inicial é de 100 TB). As réplicas apenas de leitura partilham os mesmos componentes de armazenamento, pelo que não é necessária nenhuma cópia de dados para girar uma nova réplica legível. 

O diagrama que se segue ilustra os diferentes tipos de nó numa base de dados de hiperescala:

![arquitetura](./media/sql-database-hyperscale/hyperscale-architecture.png)

Uma base de dados de hiperescala contém os seguintes tipos de componentes:

### <a name="compute"></a>Computação

O nó computacional é onde o motor relacional vive, então todos os elementos linguísticos, processamento de consulta, e assim por diante, ocorrem. Todas as interações do utilizador com uma base de dados hyperscale acontecem através destes nós de computação. Os nós de computação têm caches baseados em SSD (com a etiqueta RBPEX - Extensão do Pool Buffer Resiliente no diagrama anterior) para minimizar o número de viagens redondas de rede necessárias para obter uma página de dados. Há um nó de cálculo primário onde todas as cargas de trabalho e transações de leitura são processadas. Existem um ou mais nós de cálculo secundário que funcionam como nós de espera quentes para fins de failover, bem como funcionam como nós computacionais de leitura apenas para descarregar cargas de trabalho de leitura (se esta funcionalidade for desejada).

### <a name="page-server"></a>Servidor de página

Os servidores de página são sistemas que representam um motor de armazenamento escalonado.  Cada servidor de página é responsável por um subconjunto das páginas na base de dados.  Nominalmente, cada servidor de página controla entre 128 GB e 1 TB de dados. Nenhum dado é partilhado em mais de um servidor de página (fora das réplicas que são mantidas para redundância e disponibilidade). O trabalho de um servidor de página é servir páginas de bases de dados para os nós de computação a pedido, e manter as páginas atualizadas à medida que as transações atualizam os dados. Os servidores de página são mantidos atualizados reproduzindo registos de registo saque a partir do serviço de registo. Os servidores de página também mantêm caches baseados em SSD para melhorar o desempenho. O armazenamento a longo prazo das páginas de dados é mantido no Armazenamento Azure para fiabilidade adicional.

### <a name="log-service"></a>Serviço de registo

O serviço de registo aceita registos de registos da réplica do cálculo primário, persiste-os numa cache durável e reencaminha os registos de registo para o resto das réplicas computacionais (para que possam atualizar as suas caches) bem como os servidores de página relevantes, para que os dados possam ser atualizados lá. Desta forma, todas as alterações de dados da réplica do cálculo primário são propagadas através do serviço de registo para todas as réplicas de cálculo secundário e servidores de página. Finalmente, os registos de registo são empurrados para o armazenamento a longo prazo no Armazenamento Azure, que é um repositório de armazenamento praticamente infinito. Este mecanismo elimina a necessidade de uma truncação frequente de log. O serviço de registo também tem cache local para acelerar o acesso aos registos de registos.

### <a name="azure-storage"></a>Storage do Azure

O Azure Storage contém todos os ficheiros de dados numa base de dados. Os servidores de página mantêm os ficheiros de dados no Armazenamento Azure atualizados. Este armazenamento é utilizado para fins de backup, bem como para replicação entre regiões de Azure. As cópias de segurança são implementadas utilizando instantâneos de armazenamento de ficheiros de dados. Restaurar as operações utilizando instantâneos são rápidos, independentemente do tamanho dos dados. Os dados podem ser restaurados a qualquer momento no período de retenção de cópia de segurança da base de dados.

## <a name="backup-and-restore"></a>Cópia de segurança e restauro

As cópias de segurança são baseadas em instantâneos e, portanto, são quase instantâneas. A separação de armazenamento e cálculo permite empurrar para baixo a operação de backup/restauro para a camada de armazenamento para reduzir o peso de processamento na réplica do cálculo primário. Como resultado, a cópia de segurança da base de dados não afeta o desempenho do nó de cálculo primário; da mesma forma, os restauros são feitos revertendo para instantâneos de ficheiros, e como tal não são uma dimensão de operação de dados. Restaurar é uma operação em tempo constante, e mesmo bases de dados de vários terabytes podem ser restauradas em minutos em vez de horas ou dias. A criação de novas bases de dados, restaurando uma cópia de segurança existente, também tira partido desta funcionalidade: a criação de cópias de base de dados para fins de desenvolvimento ou de teste, mesmo de bases de dados de tamanho terabyte, é possível em minutos.

## <a name="scale-and-performance-advantages"></a>Vantagens de escala e desempenho

Com a capacidade de girar rapidamente para cima/para baixo nós de cálculo adicionais apenas de leitura, a arquitetura Hyperscale permite capacidades de leitura significativas de escala e também pode libertar o nó de computação primária para servir mais pedidos de escrita. Além disso, os nós computacionais podem ser dimensionados para cima/para baixo rapidamente devido à arquitetura de armazenamento partilhado da arquitetura hyperscale.

## <a name="create-a-hyperscale-database"></a>Criar uma base de dados de hiperescala

Uma base de dados de hiperescala pode ser criada utilizando o [portal Azure,](https://portal.azure.com) [T-SQL,](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current) [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase) ou [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create). As bases de dados de hiperescala estão disponíveis apenas utilizando o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).

O seguinte comando T-SQL cria uma base de dados de hiperescala. Deve especificar tanto a edição `CREATE DATABASE` como o objetivo de serviço no comunicado. Consulte os [limites](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases#hyperscale---provisioned-compute---gen4) de recursos para uma lista de objetivos de serviço válidos.

```sql
-- Create a Hyperscale Database
CREATE DATABASE [HyperscaleDB1] (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```
Isto criará uma base de dados de Hiperescala no hardware Gen5 com 4 núcleos.

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>Migrar uma base de dados Azure SQL existente para o nível de serviço de hiperescala

Pode mover as bases de dados Azure SQL existentes para Hyperscale utilizando o [portal Azure,](https://portal.azure.com) [T-SQL,](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) ou [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update). Neste momento, trata-se de uma migração de sentido único. Não é possível mover bases de dados da Hiperscale para outro nível de serviço, a não ser exportando e importando dados. Para provas de conceito (POCs), recomendamos fazer uma cópia das suas bases de dados de produção e migrar a cópia para Hyperscale. Migrar uma base de dados Azure SQL existente para o nível de Hiperescala é um tamanho de operação de dados.

O comando T-SQL seguinte move uma base de dados para o nível de serviço de hiperescala. Deve especificar tanto a edição `ALTER DATABASE` como o objetivo de serviço no comunicado.

```sql
-- Alter a database to make it a Hyperscale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Ligue-se a uma réplica em escala de leitura de uma base de dados de hiperescala

Nas bases de dados `ApplicationIntent` de Hiperescala, o argumento na cadeia de ligação fornecida pelo cliente dita se a ligação é encaminhada para a réplica de escrita ou para uma réplica secundária apenas de leitura. Se `ApplicationIntent` o `READONLY` conjunto e a base de dados não tiverem uma réplica secundária, a `ReadWrite` ligação será encaminhada para a réplica primária e incumprimentos para o comportamento.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

As réplicas secundárias de hiperescala são todas idênticas, utilizando o mesmo Objetivo de Nível de Serviço que a réplica primária. Se estiver presente mais de uma réplica secundária, a carga de trabalho é distribuída por todos os secundários disponíveis. Cada réplica secundária é atualizada de forma independente, pelo que réplicas diferentes podem ter diferentes latências de dados em relação à réplica primária.

## <a name="database-high-availability-in-hyperscale"></a>Base de Dados Alta Disponibilidade em Hiperescala

Tal como em todos os outros níveis de serviço, a Hyperscale garante a durabilidade dos dados para transações comprometidas, independentemente da disponibilidade de réplicas computacionais. A extensão do tempo de inatividade devido à indisponibilidade da réplica primária depende do tipo de falha (planeada vs. não planeada) e da presença de pelo menos uma réplica secundária. Num failover planeado (isto é, um evento de manutenção), o sistema ou cria a nova réplica primária antes de começar uma falha, ou usa uma réplica secundária existente como alvo de failover. Numa falha não planeada (isto é, uma falha de hardware na réplica primária), o sistema utiliza uma réplica secundária como alvo de failover se existir, ou cria uma nova réplica primária a partir do conjunto de capacidade computacional disponível. Neste último caso, a duração do tempo de inatividade é mais longa devido aos passos adicionais necessários para criar a nova réplica primária.

Para Hyperscale SLA, consulte [SLA para Azure SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database/).

## <a name="disaster-recovery-for-hyperscale-databases"></a>Recuperação de desastres para bases de dados de hiperescala

### <a name="restoring-a-hyperscale-database-to-a-different-geography"></a>Restaurar uma base de dados de hiperescala para uma geografia diferente
Se precisar de restaurar um Db de Hiperescala de Base de Dados Azure SQL para outra região que não a que está atualmente hospedada, como parte de uma operação de recuperação de desastres, deslocalização ou qualquer outra razão, o método principal é fazer uma geo-restauração da base de dados.  Isto envolve exatamente os mesmos passos que você usaria para restaurar qualquer outro AZURE SQL DB para uma região diferente:
1. Crie um servidor de base de dados SQL na região alvo se ainda não tiver um servidor apropriado.  Este servidor deve ser propriedade da mesma subscrição que o servidor original (fonte).
2. Siga as instruções no tópico de [georestauro](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore) da página sobre a restauração das bases de dados Azure SQL a partir de cópias de segurança automáticas.

> [!NOTE]
> Uma vez que a fonte e o alvo estão em regiões separadas, a base de dados não pode partilhar o armazenamento de instantâneos com a base de dados de origem como em restauros não geográficos, que completam muito rapidamente. No caso de um geo-restauro de uma base de dados de hiperescala, será uma operação de tamanho de dados, mesmo que o alvo esteja na região emparelhada do armazenamento geo-replicado.  Isto significa que fazer um geo-restauro levará tempo proporcional ao tamanho da base de dados que está a ser restaurada.  Se o objetivo estiver na região emparelhada, a cópia será significativamente mais rápida do que uma cópia transversal, mas continuará a ser uma operação de dimensão de dados.

## <a name="available-regions"></a><a name=regions></a>Regiões disponíveis

O nível de hiperescala de base de dados Azure SQL está atualmente disponível nas seguintes regiões:

- Leste da Austrália
- Austrália Sudeste
- Sul do Brasil
- Canadá Central
- E.U.A. Central
- China Leste 2
- China Norte 2
- Ásia Leste
- E.U.A. Leste
- Leste de Nós 2
- França Central
- Leste do Japão
- Oeste do Japão
- Coreia do Sul Central
- Sul da Coreia do Sul
- E.U.A. Centro-Norte
- Europa do Norte
- África do Sul Norte
- E.U.A. Centro-Sul
- Ásia Sudeste
- Sul do Reino Unido
- Oeste do Reino Unido
- Europa ocidental
- E.U.A. Oeste
- E.U.A.Oeste 2

Se quiser criar uma base de dados hyperscale numa região que não esteja listada como suportada, pode enviar um pedido de embarque através do portal Azure. Para obter instruções, consulte O pedido aumenta para a Base de [Dados Azure SQL](quota-increase-request.md) para obter instruções. Ao submeter o seu pedido, utilize as seguintes orientações:

- Utilize o outro tipo de quota de dados SQL pedido de quota de dados de pedido de [quota.](quota-increase-request.md#other)
- Nos detalhes do texto, adicione o computado SKU/núcleos totais, incluindo réplicas legíveis.
- Especifique também a tuberculose estimada.

## <a name="known-limitations"></a>Limitações conhecidas

Estas são as limitações atuais para o nível de serviço de hiperescala a partir de GA.  Estamos a trabalhar ativamente para eliminar o maior número possível destas limitações.

| Problema | Descrição |
| :---- | :--------- |
| O painel 'Gerir backups' para um servidor lógico não mostra que as bases de dados de Hiperescala serão filtradas do servidor SQL  | A Hiperscale tem um método separado para gerir backups, e como tal as definições de retenção a longo prazo e ponto no tempo de retenção de reserva não se aplicam/são invalidadas. Assim, as bases de dados de hiperescala não aparecem no painel 'Gerir backup'. |
| Restauro para um ponto anterior no tempo | Pode restaurar uma base de dados de hiperescala numa base de dados não hiperescala, dentro do período de retenção de bases de dados não hiperescala. Não é possível restaurar uma base de dados não hiperescala numa base de dados de Hiperescala.|
| Se uma base de dados tiver um ou mais ficheiros de dados maiores do que 1 TB, a migração falha | Em alguns casos, pode ser possível contornar esta questão, reduzindo os ficheiros grandes para menos de 1 TB. Se migrar uma base de dados que está a ser utilizada durante o processo de migração, certifique-se de que nenhum ficheiro é superior a 1 TB. Utilize a seguinte consulta para determinar o tamanho dos ficheiros de base de dados. `SELECT *, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| Instância Gerida | A Instância Gerida pela Base de Dados Azure SQL não é suportada atualmente com bases de dados de hiperescala. |
| Conjuntos Elásticos |  As Piscinas Elásticas não são suportadas atualmente com hiperescala de base de dados SQL.|
| Migração para Hiperescala é atualmente uma operação de sentido único | Uma vez que uma base de dados é migrada para Hyperscale, não pode ser migrada diretamente para um nível de serviço não hiperescala. Atualmente, a única forma de migrar uma base de dados de Hiperescala para não-Hiperescala é exportar/importar utilizando um ficheiro BACPAC ou outras tecnologias de movimento de dados (Copy Bulk, Azure Data Factory, Azure Databricks, SSIS, etc.)|
| Migração de bases de dados com objetos persistentes na memória | A hiperescala suporta apenas objetos in-memory não persistentes (tipos de tabela, SPs nativos e funções).  As tabelas persistentes de Memória e outros objetos devem ser largadas e recriadas como objetos não-in-Memory antes de migraruma base de dados para o nível de serviço de hiperescala.|
| Monitorização de Alterações | O Change Tracking encontra-se atualmente em pré-visualização pública e pode ser ativado em bases de dados de Hiperescala novas ou existentes. |
| Geo Replicação  | Ainda não é possível configurar a geo-replicação para a hiperescala de base de dados Azure SQL. |
| Cópia da base de dados | Ainda não é possível utilizar a Database Copy para criar uma nova base de dados em Hiperescala Azure SQL. |
| Integração TDE/AKV | A encriptação transparente da base de dados utilizando o Cofre de Chaves Azure (vulgarmente referido como Bring-Your-Own-Key ou BYOK) ainda não é suportada para a Hiperescala de Base de Dados Azure SQL, no entanto o TDE com Chaves Geridas pelo Serviço é totalmente suportado. |
|Funcionalidades de base de dados inteligentes | Com exceção da opção "Plano de Força", todas as outras opções de afinação automática ainda não são suportadas em Hiperescala: as opções podem parecer ativadas, mas não haverá recomendações ou ações feitas. |
|Query Performance Insight | A Consulta Performance Insights não é atualmente suportada para bases de dados de hiperescala. |
| Base de Dados Encolher | O DBCC SHRINKDATABASE ou o DBCC SHRINKFILE não são atualmente suportados para bases de dados de hiperescala. |
| Verificação da integridade da base de dados | O DBCC CHECKDB não é atualmente suportado para bases de dados de hiperescala. Consulte a Integridade dos Dados na Base de [Dados Azure SQL](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) para obter detalhes sobre a gestão da integridade de dados na Base de Dados Azure SQL. |

## <a name="next-steps"></a>Passos seguintes

- Para um FAQ em Hiperescala, consulte [frequentemente perguntas sobre hiperescala](sql-database-service-tier-hyperscale-faq.md).
- Para obter informações sobre os níveis de serviço, consulte [os níveis de serviço](sql-database-service-tiers.md)
- Consulte a [visão geral dos limites de recursos num servidor lógico](sql-database-resource-limits-logical-server.md) para obter informações sobre limites nos níveis de servidor e subscrição.
- Para obter limites de modelo sumo para uma única base de dados, consulte os limites do [modelo de compra baseado em Azure SQL Base de Dados vCore para uma única base](sql-database-vcore-resource-limits-single-databases.md)de dados .
- Para obter uma lista de funcionalidades e comparação, consulte [as características comuns da SQL](sql-database-features.md).
