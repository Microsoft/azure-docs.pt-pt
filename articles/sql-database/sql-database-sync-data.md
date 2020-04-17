---
title: Sincronização de Dados
description: Esta visão geral introduz o Azure SQL Data Sync
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 08/20/2019
ms.openlocfilehash: 8708c458e1064e4b9ea7dc67f1a4d4fbce1547b0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81481953"
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>Sync dados em várias bases de dados de nuvem e no local com SQL Data Sync

SQL Data Sync é um serviço construído na Base de Dados Azure SQL que permite sincronizar os dados que seleciona bidirecionalmente em várias bases de dados SQL e instâncias do Servidor SQL.

> [!IMPORTANT]
> O Azure SQL Data Sync não suporta a Instância Gerida pela Base de Dados Azure SQL neste momento.

## <a name="when-to-use-data-sync"></a>Quando utilizar o Data Sync

O Data Sync é útil nos casos em que os dados precisam de ser mantidos atualizados em várias bases de dados Do SQL do Azure ou nas bases de dados do SQL Server. Aqui estão os principais casos de utilização para Data Sync:

- **Sincronização de dados híbridos:** Com o Data Sync, pode manter os dados sincronizados entre as bases de dados no local e as bases de dados Azure SQL para permitir aplicações híbridas. Esta capacidade pode apelar aos clientes que estão a ponderar mudar-se para a nuvem e gostariam de colocar parte da sua aplicação no Azure.
- **Aplicações distribuídas:** Em muitos casos, é benéfico separar diferentes cargas de trabalho em diferentes bases de dados. Por exemplo, se tiver uma grande base de dados de produção, mas também precisa de executar uma carga de trabalho de reporte ou de análise sobre estes dados, é útil ter uma segunda base de dados para esta carga de trabalho adicional. Esta abordagem minimiza o impacto do desempenho na sua carga de trabalho de produção. Pode utilizar o Data Sync para manter estas duas bases de dados sincronizadas.
- **Aplicações distribuídas globalmente:** Muitas empresas abrangem várias regiões e até mesmo vários países/regiões. Para minimizar a latência da rede, o melhor é ter os seus dados numa região próxima de si. Com o Data Sync, pode facilmente manter bases de dados em regiões de todo o mundo sincronizadas.

Data Sync não é a solução preferida para os seguintes cenários:

| Cenário | Algumas soluções recomendadas |
|----------|----------------------------|
| Recuperação Após Desastre | [Backups geo-redundantes do Azure](sql-database-automated-backups.md) |
| Escala de leitura | [Utilize réplicas apenas de leitura para carregar cargas de trabalho de consulta de leitura de equilíbrio (pré-visualização)](sql-database-read-scale-out.md) |
| ETL (OLTP to OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) ou [Serviços de Integração de Servidores SQL](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) |
| Migração do Servidor SQL no local para a Base de Dados SQL Azure | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="overview-of-sql-data-sync"></a>Visão geral do SQL Data Sync

Data Sync baseia-se no conceito de um Sync Group. Um Sync Group é um grupo de bases de dados que pretende sincronizar.

Data Sync usa um hub e falou topologia para sincronizar dados. Define uma das bases de dados do grupo de sincronização como base de dados hub. O resto das bases de dados são bases de dados dos membros. A sincronização ocorre apenas entre o Hub e os membros individuais.

- A **Base de Dados Hub** deve ser uma Base de Dados Azure SQL.
- As bases de **dados dos membros** podem ser bases de dados SQL, bases de dados do Servidor SQL no local ou instâncias do Servidor SQL em máquinas virtuais Azure.
- A **Base de Dados de Sincronização** contém os metadados e o registo de Data Sync. A Base de Dados Sync tem de ser uma Base de Dados Azure SQL localizada na mesma região que a Base de Dados Hub. A Sync Database é criada pelo cliente e detida pelo cliente.

> [!NOTE]
> Se estiver a utilizar uma base de dados no local como base de dados de membros, tem de [instalar e configurar um agente de sincronização local](sql-database-get-started-sql-data-sync.md#add-on-prem).

![Sincronizar dados entre bases de dados](media/sql-database-sync-data/sync-data-overview.png)

Um Sync Group tem as seguintes propriedades:

- O **Sync Schema** descreve quais os dados que estão a ser sincronizados.
- A **Direção sincronizada** pode ser bidirecional ou pode fluir numa única direção. Ou seja, a Direção sincronizada pode ser *hub para membro*, ou membro do *Hub*, ou ambos.
- O **Sync Interval** descreve a frequência com que ocorre a sincronização.
- A Política de **Resolução de Conflitos** é uma política de nível de grupo, que pode ser *o Hub ganha* ou o deputado *ganha.*

## <a name="how-does-data-sync-work"></a>Como funciona o Data Sync

- **Rastrear alterações de dados:** O Data Sync rastreia as alterações utilizando a inserção, a atualização e a eliminação dos gatilhos. As alterações são registadas numa tabela lateral na base de dados do utilizador. Note que o INSERMA A GRANEL não dispara por defeito os gatilhos. Se FIRE_TRIGGERS não for especificado, não executam os gatilhos de inserção. Adicione a opção FIRE_TRIGGERS para que o Data Sync possa rastrear esses encaixes. 
- **Dados sincronizadores:** Data Sync é projetado num modelo Hub e Spoke. O Hub sincroniza-se individualmente com cada membro. As alterações do Hub são transferidas para o membro e, em seguida, as alterações do membro são enviadas para o Hub.
- **Resolução de conflitos:** Data Sync fornece duas opções para resolução de conflitos, *Hub ganha* ou o *membro ganha.*
  - Se selecionar *o Hub ganha,* as mudanças no hub substituem sempre as alterações no membro.
  - Se selecionar *O Membro ganha,* as alterações na sobreescrita do membro mudam no centro. Se houver mais de um membro, o valor final depende de qual membro sincroniza primeiro.

## <a name="compare-data-sync-with-transactional-replication"></a>Comparar sincronização de dados com replicação transacional

| | Sincronização de Dados | Replicação Transacional |
|---|---|---|
| Vantagens | - Apoio ativo<br/>- Bidirecional entre as instalações e a Base de Dados SQL Azure | - Latência mais baixa<br/>- Consistência transacional<br/>- Reutilizar a topologia existente após a migração |
| Desvantagens | - 5 min ou mais latência<br/>- Sem consistência transacional<br/>- Impacto de desempenho mais elevado | - Não pode publicar a partir da base de dados azure SQL única base de dados ou base de dados pooled<br/>- Elevado custo de manutenção |

## <a name="get-started-with-sql-data-sync"></a>Começar com SQL Data Sync

### <a name="set-up-data-sync-in-the-azure-portal"></a>Configurar data sync no portal Azure

- [Configurar a Sincronização de Dados SQL do Azure](sql-database-get-started-sql-data-sync.md)
- Data Sync Agent - [Data Sync Agent for Azure SQL Data Sync](sql-database-data-sync-agent.md)

### <a name="set-up-data-sync-with-powershell"></a>Configurar o Sincronizado de Dados com a PowerShell

- [Utilizar o PowerShell para sincronizar entre várias bases de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)
- [Utilizar o PowerShell para sincronizar entre uma Base de Dados SQL do Azure e uma base de dados do SQL Server no local](scripts/sql-database-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>Reveja as melhores práticas para Data Sync

- [Melhores práticas da Sincronização de Dados SQL do Azure](sql-database-best-practices-data-sync.md)

### <a name="did-something-go-wrong"></a>Aconteceu alguma coisa que correu mal.

- [Resolver problemas da Sincronização de Dados SQL do Azure](sql-database-troubleshoot-data-sync.md)

## <a name="consistency-and-performance"></a>Consistência e desempenho

### <a name="eventual-consistency"></a>Consistência eventual

Uma vez que o Data Sync é baseado no gatilho, a consistência transacional não está garantida. A Microsoft garante que todas as alterações são feitas eventualmente e que o Data Sync não causa perda de dados.

### <a name="performance-impact"></a>Impacto do desempenho

O Data Sync utiliza a inserção, atualização e elimina os gatilhos para rastrear alterações. Cria tabelas laterais na base de dados do utilizador para o rastreio de alterações. Estas atividades de rastreio de mudanças têm um impacto na sua carga de trabalho na base de dados. Avalie o seu nível de serviço e atualize se necessário.

O provisionamento e o desprovisionamento durante a criação, atualização e eliminação do grupo de sincronização podem igualmente ter impacto no desempenho da base de dados.

## <a name="requirements-and-limitations"></a><a name="sync-req-lim"></a>Requisitos e limitações

### <a name="general-requirements"></a>Requisitos gerais

- Cada mesa deve ter uma chave primária. Não mude o valor da chave primária em nenhuma linha. Se tiver de alterar um valor-chave primário, elimine a linha e recrie-a com o novo valor-chave primário.

> [!IMPORTANT]
> Alterar o valor de uma chave primária existente resultará no seguinte comportamento defeituoso:
> - Os dados entre o hub e o membro podem ser perdidos, mesmo que a sincronização não reporte qualquer problema.
> - O sync pode falhar porque a tabela de rastreio tem uma linha não existente de origem devido à mudança de chave primária.

- O isolamento de instantâneos tem de estar ativado. Para obter mais informações, veja [Snapshot Isolation in SQL Server (Isolamento de Instantâneo no SQL Server)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Limitações gerais

- Uma mesa não pode ter uma coluna de identidade que não seja a chave principal.
- Uma chave primária não pode ter os seguintes tipos de dados: sql_variant, binário, varbinary, imagem, xml.
- Tenha cuidado ao utilizar os seguintes tipos de dados como chave primária, porque a precisão suportada é apenas para a segunda: hora, data, data2, data compensada.
- Os nomes dos objetos (bases de dados, tabelas e colunas) não podem conter o período dos caracteres imprimíveis (.), suporte quadrado esquerdo ([), ou suporte quadrado direito (]).
- A autenticação do Diretório Ativo Azure não é suportada.
- As tabelas com o mesmo nome, mas diferentes esquemas (por exemplo, dbo.customers e sales.customers) não são suportadas.
- Colunas com tipos de dados definidos pelo utilizador não são suportadas
- Os servidores móveis entre diferentes subscrições não são suportados. 

#### <a name="unsupported-data-types"></a>Tipos de dados não suportados

- FileStream
- SQL/CLR UDT
- XMLSchemaCollection (XML suportado)
- Cursor, RowVersion, Timestamp, Hierarchyid

#### <a name="unsupported-column-types"></a>Tipos de colunas não suportados

O Data Sync não consegue sincronizar colunas apenas de leitura ou geradas pelo sistema. Por exemplo:

- Colunas computorizadas.
- Colunas geradas pelo sistema para tabelas temporais.

#### <a name="limitations-on-service-and-database-dimensions"></a>Limitações nas dimensões do serviço e da base de dados

| **Dimensões**                                                  | **Limite**              | **Supor**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Número máximo de grupos de sincronização a que qualquer base de dados pode pertencer.       | 5                      |                             |
| Número máximo de pontos finais num único grupo de sincronização              | 30                     |                             |
| Número máximo de pontos finais no local num único grupo de sincronização. | 5                      | Criar múltiplos grupos de sincronização |
| Bases de dados, tabela, esquema e nomes de colunas                       | 50 caracteres por nome |                             |
| Tabelas em um grupo de sincronização                                          | 500                    | Criar múltiplos grupos de sincronização |
| Colunas em uma mesa em um grupo de sincronização                              | 1000                   |                             |
| Tamanho da linha de dados em uma mesa                                        | 24 Mb                  |                             |
| Intervalo mínimo de sincronização                                           | 5 Minutos              |                             |

> [!NOTE]
> Pode haver até 30 pontos finais num único grupo de sincronização se houver apenas um grupo de sincronização. Se houver mais de um grupo de sincronização, o número total de pontos finais em todos os grupos de sincronização não pode exceder 30. Se uma base de dados pertence a múltiplos grupos de sincronização, é contado como múltiplos pontos finais, não um.

## <a name="faq-about-sql-data-sync"></a>FAQ sobre SQL Data Sync

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Quanto custa o serviço SQL Data Sync

Não há nenhum custo para o próprio serviço SQL Data Sync. No entanto, ainda recolhe encargos de transferência de dados para o movimento de dados dentro e fora da sua instância de Base de Dados SQL. Para mais informações, consulte os preços da Base de [Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Quais as regiões que suportam o Data Sync

O SQL Data Sync está disponível em todas as regiões.

### <a name="is-a-sql-database-account-required"></a>É necessária uma conta de base de dados SQL

Sim. Deve ter uma conta de Base de Dados SQL para acolher a Base de Dados Hub.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Posso usar o Data Sync para sincronizar apenas entre bases de dados do SQL Server no local

Não diretamente. No entanto, pode sincronizar indiretamente entre as bases de dados do SQL Server no local, através da criação de uma base de dados Hub em Azure e, em seguida, adicionando as bases de dados no local ao grupo de sincronização.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-subscriptions"></a>Posso usar o Data Sync para sincronizar entre bases de dados SQL que pertencem a diferentes subscrições

Sim. Pode sincronizar entre bases de dados SQL que pertencem a grupos de recursos pertencentes a diferentes subscrições.

- Se as subscrições pertencerem ao mesmo inquilino, e tiver permissão para todas as subscrições, pode configurar o grupo de sincronização no portal Azure.
- Caso contrário, tem de utilizar o PowerShell para adicionar os membros sincronizados que pertencem a diferentes subscrições.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china-21vianet"></a>Posso usar o Data Sync para sincronizar entre bases de dados SQL que pertencem a diferentes nuvens (como Azure Public Cloud e Azure China 21Vianet)

Sim. Pode sincronizar entre bases de dados SQL que pertencem a diferentes nuvens, tem de usar o PowerShell para adicionar os membros sincronizados que pertencem às diferentes subscrições.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Posso usar data Sync para sementes de dados da minha base de dados de produção para uma base de dados vazia, e depois sincronizá-los

Sim. Crie o esquema manualmente na nova base de dados, scriptndo-o a partir do original. Depois de criar o esquema, adicione as tabelas a um grupo de sincronização para copiar os dados e mantê-los sincronizados.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Devo usar o SQL Data Sync para fazer cópias de segurança e restaurar as minhas bases de dados

Não é aconselhável utilizar o SQL Data Sync para criar uma cópia de segurança dos seus dados. Não é possível recuar e restaurar a um ponto específico do tempo porque as sincronizações de Sincronização de Dados SQL não são versonizadas. Além disso, o SQL Data Sync não suporta outros objetos SQL, como procedimentos armazenados, e não faz o equivalente a uma operação de restauro rapidamente.

Para uma técnica de backup recomendada, consulte Copiar uma base de [dados Azure SQL](sql-database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Pode sincronizar tabelas e colunas encriptadas de Data Sync

- Se uma base de dados utilizar Sempre Encriptado, pode sincronizar apenas as tabelas e colunas que *não* estão encriptadas. Não é possível sincronizar as colunas encriptadas, porque o Data Sync não consegue desencriptar os dados.
- Se uma coluna utilizar encriptação de nível de coluna (CLE), pode sincronizar a coluna, desde que o tamanho da linha seja inferior ao tamanho máximo de 24 Mb. Data Sync trata a coluna encriptada pela chave (CLE) como dados binários normais. Para desencriptar os dados de outros membros da sincronização, é necessário ter o mesmo certificado.

### <a name="is-collation-supported-in-sql-data-sync"></a>É a colagem suportada no SQL Data Sync

Sim. SQL Data Sync suporta colagem nos seguintes cenários:

- Se as tabelas de esquemas de sincronização selecionadas já não estiverem no seu hub ou nas bases de dados dos membros, então quando implementa o grupo de sincronização, o serviço cria automaticamente as tabelas e colunas correspondentes com as definições de colagem selecionadas nas bases de dados de destino vazias.
- Se as tabelas a sincronizar já existirem tanto nas bases de dados do seu hub como dos membros, o SQL Data Sync exige que as colunas-chave primárias tenham a mesma colagem entre as bases de dados do hub e dos membros para implementar com sucesso o grupo de sincronização. Não existem restrições de colagem em colunas que não sejam as colunas-chave primárias.

### <a name="is-federation-supported-in-sql-data-sync"></a>A federação é apoiada no SQL Data Sync

A Base de Dados de Raiz da Federação pode ser utilizada no Serviço de Sincronização de Dados SQL sem qualquer limitação. Não é possível adicionar o ponto final da Base de Dados Federada à versão atual do SQL Data Sync.

## <a name="next-steps"></a>Passos seguintes

### <a name="update-the-schema-of-a-synced-database"></a>Atualizar o esquema de uma base de dados sincronizada

Tem de atualizar o esquema de uma base de dados num grupo de sincronização? As alterações de esquema não são automaticamente replicadas. Para obter algumas soluções, consulte os seguintes artigos:

- [Automatizar a replicação de alterações de esquemas no SQL Data Sync do Azure](sql-database-update-sync-schema.md)
- [Utilizar o PowerShell para atualizar o esquema de sincronização num grupo de sincronização existente](scripts/sql-database-sync-update-schema.md)

### <a name="monitor-and-troubleshoot"></a>Monitorizar e resolver problemas

O SQL Data Sync está a fazer o que se esperava? Para monitorizar a atividade e problemas de resolução de problemas, consulte os seguintes artigos:

- [Monitor Azure SQL Data Sync com registos do Monitor Azure](sql-database-sync-monitor-oms.md)
- [Resolver problemas da Sincronização de Dados SQL do Azure](sql-database-troubleshoot-data-sync.md)

### <a name="learn-more-about-azure-sql-database"></a>Saiba mais sobre a Base de Dados Azure SQL

Para mais informações sobre a Base de Dados SQL, consulte os seguintes artigos:

- [Descrição Geral da Base de Dados SQL](sql-database-technical-overview.md)
- [Gestão do Ciclo de Vida da Base de Dados](https://msdn.microsoft.com/library/jj907294.aspx)
