---
title: O que é SQL Data Sync para Azure?
description: Esta visão geral introduz o SQL Data Sync for Azure, que permite sincronizar dados através de várias bases de dados de nuvem e no local.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync, sqldbrb=1, fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 08/20/2019
ms.openlocfilehash: c2c0e6d1d3ffd9ec3091e92530ec5c191f3f7ca6
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/02/2020
ms.locfileid: "84297960"
---
# <a name="what-is-sql-data-sync-for-azure"></a>O que é SQL Data Sync para Azure?
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

SQL Data Sync é um serviço construído na Base de Dados Azure SQL que permite sincronizar os dados que seleciona bidireccionalmente através de várias bases de dados, tanto no local como na nuvem. 

> [!IMPORTANT]
> O Azure SQL Data Sync não suporta a Azure SQL Managed Instance neste momento.


## <a name="overview"></a>Descrição geral 

O Data Sync baseia-se no conceito de um Grupo Sync. Um Sync Group é um grupo de bases de dados que pretende sincronizar.

Data Sync usa um hub e falou topologia para sincronizar dados. Define uma das bases de dados do grupo de sincronização como base de dados do Hub. O resto das bases de dados são bases de dados de membros. O sincronização ocorre apenas entre o Hub e os membros individuais.

- A **Base de Dados do Hub** deve ser uma Base de Dados Azure SQL.
- As **bases de dados dos membros** podem ser bases de dados na Base de Dados Azure SQL ou em casos de SQL Server.
- A **Base de Dados de Sincronização** contém os metadados e o registo para o Data Sync. A Base de Dados de Sincronização tem de ser uma Base de Dados Azure SQL localizada na mesma região que a Base de Dados do Hub. A Base de Dados de Sincronização é criada pelo cliente e detida pelo cliente.

> [!NOTE]
> Se estiver a utilizar uma base de dados no local como base de dados de membros, tem de [instalar e configurar um agente de sincronização local.](sql-data-sync-sql-server-configure.md#add-on-prem)

![Sincronizar dados entre bases de dados](./media/sql-data-sync-data-sql-server-sql-database/sync-data-overview.png)

Um Grupo Sync tem as seguintes propriedades:

- O **Sync Schema** descreve quais os dados que estão a ser sincronizados.
- A **Direção de Sincronização** pode ser bidis ou fluir numa só direção. Ou seja, a Direção de Sincronização pode ser *Hub para Membro*, ou Membro do *Hub*, ou ambos.
- O **Intervalo de Sincronização** descreve a frequência da sincronização.
- A **Política de Resolução de Conflitos** é uma política de nível de grupo, que pode ser *o Hub ganha* ou o *membro ganha.*

## <a name="when-to-use"></a>Quando utilizar

O Data Sync é útil nos casos em que os dados precisam de ser mantidos atualizados em várias bases de dados na Base de Dados Azure SQL ou no SQL Server. Aqui estão os principais casos de utilização para Data Sync:

- **Sincronização de dados híbridos:** Com o Data Sync, pode manter os dados sincronizados entre as suas bases de dados no SQL Server e na Base de Dados Azure SQL para permitir aplicações híbridas. Esta capacidade pode apelar aos clientes que estão a considerar mudar-se para a nuvem e gostariam de colocar algumas das suas aplicações no Azure.
- **Aplicações Distribuídas:** Em muitos casos, é benéfico separar diferentes cargas de trabalho em diferentes bases de dados. Por exemplo, se tiver uma grande base de dados de produção, mas também precisar de executar uma carga de trabalho de reporte ou análise nestes dados, é útil ter uma segunda base de dados para esta carga de trabalho adicional. Esta abordagem minimiza o impacto do desempenho na carga de trabalho da sua produção. Pode utilizar o Data Sync para manter estas duas bases de dados sincronizadas.
- **Aplicações distribuídas globalmente:** Muitas empresas abrangem várias regiões e até vários países/regiões. Para minimizar a latência da rede, o melhor é ter os seus dados numa região próxima. Com o Data Sync, pode facilmente manter bases de dados em regiões de todo o mundo sincronizadas.

Data Sync não é a solução preferida para os seguintes cenários:

| Cenário | Algumas soluções recomendadas |
|----------|----------------------------|
| Recuperação Após Desastre | [Backups geo-redundantes do Azure](automated-backups-overview.md) |
| Escala de leitura | [Utilize réplicas apenas de leitura para carregar cargas de trabalho de consulta apenas de leitura (pré-visualização)](read-scale-out.md) |
| ETL (OLTP a OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) ou [Serviços de Integração de Servidores SQL](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) |
| Migração do SQL Server para Azure SQL Database | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||



## <a name="how-it-works"></a>Como funciona

- **Alterações de dados de rastreio:** Data Sync rastreia alterações utilizando inserção, atualização e eliminação de gatilhos. As alterações são registadas numa tabela lateral na base de dados do utilizador. Note que o BULK INSERT não dispara disparos por defeito. Se FIRE_TRIGGERS não for especificado, nenhum gatilho de inserção executa. Adicione a opção FIRE_TRIGGERS para que o Data Sync possa rastrear esses inserções. 
- **Sincronizar dados:** Data Sync é projetado num modelo Hub e Spoke. O Hub sincroniza-se com cada membro individualmente. As alterações do Hub são transferidas para o membro e, em seguida, as alterações do membro são enviadas para o Hub.
- **Resolução de conflitos:** O Data Sync oferece duas opções para a resolução de conflitos, *o Hub vence* ou o Membro *ganha.*
  - Se selecionar *o Hub ganha,* as mudanças no hub substituem sempre as alterações no membro.
  - Se selecionar *o Membro ganha,* as alterações no membro substituem as alterações no centro. Se houver mais de um membro, o valor final depende de qual membro sincroniza primeiro.

## <a name="compare-with-transactional-replication"></a>Compare com replicação transacional

| | Sincronização de Dados | Replicação Transacional |
|---|---|---|
| Vantagens | - Suporte ativo<br/>- Biducional entre as instalações e a Base de Dados Azure SQL | - Menor latência<br/>- Consistência transacional<br/>- Reutilização da topologia existente após a migração <br/>-Suporte a exemplos geridos Azure SQL |
| Desvantagens | - 5 min ou mais de latência<br/>- Sem consistência transacional<br/>- Maior impacto no desempenho | - Não pode publicar na Base de Dados Azure SQL <br/>- Alto custo de manutenção |

## <a name="get-started"></a>Introdução 

### <a name="set-up-data-sync-in-the-azure-portal"></a>Configurar o Data Sync no portal Azure

- [Configurar a Sincronização de Dados SQL do Azure](sql-data-sync-sql-server-configure.md)
- Data Sync Agent - [Data Sync Agent for Azure SQL Data Sync](sql-data-sync-agent-overview.md)

### <a name="set-up-data-sync-with-powershell"></a>Configurar o Data Sync com o PowerShell

- [Utilize o PowerShell para sincronizar entre várias bases de dados na Base de Dados Azure SQL](scripts/sql-data-sync-sync-data-between-sql-databases.md)
- [Utilize o PowerShell para sincronizar entre uma base de dados na Base de Dados Azure SQL e uma base de dados num caso de Servidor SQL](scripts/sql-data-sync-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>Reveja as melhores práticas para Data Sync

- [Melhores práticas da Sincronização de Dados SQL do Azure](sql-data-sync-best-practices.md)

### <a name="did-something-go-wrong"></a>Fez alguma coisa correr mal

- [Resolver problemas da Sincronização de Dados SQL do Azure](../../sql-database/sql-database-troubleshoot-data-sync.md)

## <a name="consistency-and-performance"></a>Consistência e desempenho

### <a name="eventual-consistency"></a>Consistência eventual

Uma vez que o Data Sync é baseado no gatilho, a consistência transacional não é garantida. A Microsoft garante que todas as alterações são feitas eventualmente e que o Data Sync não causa perda de dados.

### <a name="performance-impact"></a>Impacto de desempenho

Data Sync utiliza inserção, atualização e elimina os gatilhos para rastrear as alterações. Cria tabelas laterais na base de dados do utilizador para o rastreio de alterações. Estas atividades de rastreio de alterações têm impacto na carga de trabalho da sua base de dados. Avalie o seu nível de serviço e atualize se necessário.

O fornecimento e desprovisionamento durante a criação, atualização e eliminação do grupo de sincronização também podem ter impacto no desempenho da base de dados.

## <a name="requirements-and-limitations"></a><a name="sync-req-lim"></a>Requisitos e limitações

### <a name="general-requirements"></a>Requisitos gerais

- Cada mesa deve ter uma chave primária. Não mude o valor da chave primária em nenhuma linha. Se tiver de alterar um valor de chave primária, elimine a linha e recrie-a com o novo valor da chave primária.

> [!IMPORTANT]
> Alterar o valor de uma chave primária existente resultará no seguinte comportamento defeituoso:
> - Os dados entre o hub e o membro podem ser perdidos, mesmo que a sincronização não reporte qualquer problema.
> - O Sync pode falhar porque a tabela de rastreio tem uma linha não existente a partir da fonte devido à mudança de tecla primária.

- O isolamento de instantâneos tem de estar ativado. Para obter mais informações, veja [Snapshot Isolation in SQL Server (Isolamento de Instantâneo no SQL Server)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Limitações gerais

- Uma mesa não pode ter uma coluna de identidade que não seja a chave principal.
- Uma chave primária não pode ter os seguintes tipos de dados: sql_variant, binário, varbinário, imagem, xml.
- Tenha cuidado quando utilizar os seguintes tipos de dados como chave primária, porque a precisão suportada é apenas para a segunda: hora, data, data 2, datatimeoff.
- Os nomes dos objetos (bases de dados, tabelas e colunas) não podem conter o período de caracteres imprimíveis (.), suporte quadrado esquerdo ([) ou suporte quadrado direito (]).
- A autenticação do Diretório Ativo Azure não é suportada.
- As tabelas com o mesmo nome mas esquemas diferentes (por exemplo, dbo.clientes e vendas.clientes) não são suportados.
- Colunas com tipos de dados definidos pelo utilizador não são suportadas
- A deslocação de servidores entre diferentes subscrições não é suportada. 

#### <a name="unsupported-data-types"></a>Tipos de dados não suportados

- FileStream
- SQL/CLR UDT
- XMLSchemaCollection (suporte a XML)
- Cursor, RowVersion, Timetamp, Hierarchyid

#### <a name="unsupported-column-types"></a>Tipos de colunas não suportados

O Data Sync não consegue sincronizar apenas colunas de leitura ou geradas pelo sistema. Por exemplo:

- Colunas computadas.
- Colunas geradas pelo sistema para tabelas temporais.

#### <a name="limitations-on-service-and-database-dimensions"></a>Limitações nas dimensões do serviço e da base de dados

| **Dimensões**                                                  | **Limite**              | **Solução alternativa**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Número máximo de grupos de sincronização a que qualquer base de dados pode pertencer.       | 5                      |                             |
| Número máximo de pontos finais num único grupo de sincronização              | 30                     |                             |
| Número máximo de pontos finais no local num único grupo de sincronização. | 5                      | Criar vários grupos de sincronização |
| Bases de dados, tabelas, esquemas e nomes de colunas                       | 50 caracteres por nome |                             |
| Tabelas em um grupo de sincronização                                          | 500                    | Criar vários grupos de sincronização |
| Colunas em uma mesa em um grupo de sincronização                              | 1000                   |                             |
| Tamanho da linha de dados em uma mesa                                        | 24 Mb                  |                             |
| Intervalo de sincronização mínimo                                           | 5 Minutos              |                             |

> [!NOTE]
> Pode haver até 30 pontos finais num único grupo de sincronização se houver apenas um grupo de sincronização. Se houver mais de um grupo de sincronização, o número total de pontos finais em todos os grupos de sincronização não pode exceder 30. Se uma base de dados pertence a vários grupos de sincronização, é contada como vários pontos finais, não um.

### <a name="network-requirements"></a>Requisitos de rede

Quando o grupo de sincronização é estabelecido, o serviço Data Sync precisa de se ligar à base de dados do hub. No momento em que estabelecer o grupo de sincronização, o servidor Azure SQL deve ter a seguinte configuração nas suas `Firewalls and virtual networks` definições:

 * *Negar o acesso* à rede pública deve ser definido para *Off*.
 * *Permitir que os serviços e recursos do Azure acedam a este servidor* tem de ser definidos para *Sim,* ou tem de criar regras IP para os [endereços IP utilizados pelo serviço Data Sync](network-access-controls-overview.md#data-sync).

Uma vez criado e provisionado o grupo de sincronização, pode desativar estas definições. O agente de sincronização ligar-se-á diretamente à base de dados do hub, e pode utilizar [as regras IP](firewall-configure.md) de firewall do servidor ou [pontos finais privados](private-endpoint-overview.md) para permitir que o agente aceda ao servidor do hub.

> [!NOTE]
> Se alterar as definições de esquema do grupo de sincronização, terá de permitir que o serviço Data Sync volte a aceder ao servidor para que a base de dados do hub possa ser re-a provisionada.

## <a name="faq-about-sql-data-sync"></a>FAQ sobre SQL Data Sync

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Quanto custa o serviço SQL Data Sync

Não há qualquer custo para o próprio serviço SQL Data Sync. No entanto, ainda recolhe taxas de transferência de dados para movimento de dados dentro e fora da sua instância SQL Database. Para mais informações, consulte [os preços da Base de Dados SQL.](https://azure.microsoft.com/pricing/details/sql-database/)

### <a name="what-regions-support-data-sync"></a>Que regiões suportam o Data Sync

O SQL Data Sync está disponível em todas as regiões.

### <a name="is-a-sql-database-account-required"></a>É necessária uma conta SQL Database

Yes. Deve ter uma conta SQL Database para hospedar a Base de Dados do Hub.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-databases-only"></a>Posso usar o Data Sync para sincronizar apenas entre bases de dados do SQL Server

Não diretamente. No entanto, pode sincronizar indiretamente entre bases de dados do SQL Server, criando uma base de dados Hub em Azure e, em seguida, adicionando as bases de dados no local ao grupo de sincronização.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-subscriptions"></a>Posso utilizar o Data Sync para sincronizar entre bases de dados SQL que pertencem a diferentes subscrições

Yes. Pode sincronizar entre bases de dados SQL que pertencem a grupos de recursos pertencentes a diferentes subscrições.

- Se as subscrições pertencerem ao mesmo inquilino, e tiver permissão para todas as subscrições, pode configurar o grupo de sincronização no portal Azure.
- Caso contrário, terá de utilizar o PowerShell para adicionar os membros sincronizados que pertencem a diferentes subscrições.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china-21vianet"></a>Posso utilizar o Data Sync para sincronizar entre bases de dados SQL que pertencem a diferentes nuvens (como Azure Public Cloud e Azure China 21Vianet)

Yes. Pode sincronizar entre bases de dados SQL que pertencem a diferentes nuvens, tem de usar o PowerShell para adicionar os membros sincronizados que pertencem às diferentes subscrições.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Posso usar o Data Sync para sementerar dados da minha base de dados de produção para uma base de dados vazia, e depois sincronizá-los

Yes. Crie o esquema manualmente na nova base de dados, scriptando-o a partir do original. Depois de criar o esquema, adicione as tabelas a um grupo de sincronização para copiar os dados e mantê-lo sincronizado.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Devo usar o SQL Data Sync para fazer o back up e restaurar as minhas bases de dados

Não é aconselhável utilizar o SQL Data Sync para criar uma cópia de segurança dos seus dados. Não é possível fazer back up e restaurar a um ponto específico no tempo porque as sincronizações sql Data Sync não são versadas. Além disso, o SQL Data Sync não faz o back up de outros objetos SQL, como procedimentos armazenados, e não faz o equivalente a uma operação de restauro rapidamente.

Para obter uma técnica de backup recomendada, consulte [copiar uma base de dados Azure SQL](database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Pode sincronizar tabelas e colunas encriptadas de dados

- Se uma base de dados utilizar Sempre Encriptado, pode sincronizar apenas as tabelas e colunas que *não* estão encriptadas. Não é possível sincronizar as colunas encriptadas, porque o Data Sync não consegue desencriptar os dados.
- Se uma coluna utilizar encriptação de nível de coluna (CLE), pode sincronizar a coluna, desde que o tamanho da linha seja inferior ao tamanho máximo de 24 Mb. O Data Sync trata a coluna encriptada por chave (CLE) como dados binários normais. Para desencriptar os dados de outros membros sincronizados, precisa de ter o mesmo certificado.

### <a name="is-collation-supported-in-sql-data-sync"></a>É colagem suportada no SQL Data Sync

Yes. SQL Data Sync suporta a colagem nos seguintes cenários:

- Se as tabelas de esquemas de sincronização selecionadas já não estiverem no seu centro ou bases de dados dos membros, então quando implementa o grupo de sincronização, o serviço cria automaticamente as tabelas e colunas correspondentes com as definições de colisão selecionadas nas bases de dados de destino vazias.
- Se as tabelas a serem sincronizadas já existirem tanto no seu hub como nas bases de dados dos membros, o SQL Data Sync requer que as colunas-chave primárias tenham a mesma colagem entre bases de dados do hub e dos membros para implementar com sucesso o grupo de sincronização. Não existem restrições de colagem em colunas que não as colunas-chave primárias.

### <a name="is-federation-supported-in-sql-data-sync"></a>É a federação apoiada no SQL Data Sync

A Federation Root Database pode ser utilizada no Serviço de Sincronização de Dados SQL sem qualquer limitação. Não é possível adicionar o ponto final da Base de Dados Federada à versão atual do SQL Data Sync.

## <a name="next-steps"></a>Passos seguintes

### <a name="update-the-schema-of-a-synced-database"></a>Atualizar o esquema de uma base de dados sincronizada

Tem de atualizar o esquema de uma base de dados num grupo de sincronização? As alterações de esquema não são replicadas automaticamente. Para algumas soluções, consulte os seguintes artigos:

- [Automatizar a replicação de alterações de esquema com SQL Data Sync em Azure](../../sql-database/sql-database-update-sync-schema.md)
- [Utilizar o PowerShell para atualizar o esquema de sincronização num grupo de sincronização existente](scripts/update-sync-schema-in-sync-group.md)

### <a name="monitor-and-troubleshoot"></a>Monitorizar e resolver problemas

O SQL Data Sync está a fazer o que se esperava? Para monitorizar a atividade e resolver problemas, consulte os seguintes artigos:

- [Monitor SQL Data Sync com registos do Monitor Azure](../../sql-database/sql-database-sync-monitor-oms.md)
- [Resolver problemas da Sincronização de Dados SQL do Azure](../../sql-database/sql-database-troubleshoot-data-sync.md)

### <a name="learn-more-about-azure-sql-database"></a>Saiba mais sobre a Base de Dados Azure SQL

Para obter mais informações sobre a Base de Dados Azure SQL, consulte os seguintes artigos:

- [Descrição Geral da Base de Dados SQL](sql-database-paas-overview.md)
- [Gestão do Ciclo de Vida da Base de Dados](https://msdn.microsoft.com/library/jj907294.aspx)
