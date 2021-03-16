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
ms.reviewer: ''
ms.date: 08/20/2019
ms.openlocfilehash: c38e4681c76fb0dd52d77c7dc1438b87a9571a80
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103562064"
---
# <a name="what-is-sql-data-sync-for-azure"></a>O que é SQL Data Sync para Azure?

SQL Data Sync é um serviço construído na Base de Dados Azure SQL que permite sincronizar os dados que seleciona bidireccionalmente através de várias bases de dados, tanto no local como na nuvem. 

> [!IMPORTANT]
> O Azure SQL Data Sync não suporta a Azure SQL Managed Instance neste momento.


## <a name="overview"></a>Descrição Geral 

O Data Sync baseia-se no conceito de um grupo de sincronização. Um grupo de sincronização é um grupo de bases de dados que pretende sincronizar.

Data Sync usa um hub e falou topologia para sincronizar dados. Define uma das bases de dados do grupo de sincronização como base de dados do hub. O resto das bases de dados são bases de dados de membros. O sincronização ocorre apenas entre o hub e os membros individuais.

- A **Base de Dados do Hub** deve ser uma Base de Dados Azure SQL.
- As **bases de dados dos membros** podem ser bases de dados na Base de Dados Azure SQL ou em casos de SQL Server.
- A **Base de Dados de Metadados de Sincronização** contém os metadados e o registo para o Data Sync. A Base de Dados de Metadados de Sincronização tem de ser uma Base de Dados Azure SQL localizada na mesma região que a Base de Dados do Hub. A Sync Metadata Database é criada pelo cliente e detida pelo cliente. Só pode ter uma Base de Dados de Metadados de Sincronização por região e subscrição. Sync Medata Database não pode ser eliminado ou renomeado enquanto existem grupos de sincronização ou agentes de sincronização. A Microsoft recomenda criar uma nova base de dados vazia para utilização como Base de Dados de Metadados de Sincronização. O Data Sync cria tabelas nesta base de dados e executa uma carga de trabalho frequente.

> [!NOTE]
> Se estiver a utilizar uma base de dados no local como base de dados de membros, tem de [instalar e configurar um agente de sincronização local.](sql-data-sync-sql-server-configure.md#add-on-prem)

![Sincronizar dados entre bases de dados](./media/sql-data-sync-data-sql-server-sql-database/sync-data-overview.png)

Um grupo de sincronização tem as seguintes propriedades:

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

| Scenario | Algumas soluções recomendadas |
|----------|----------------------------|
| Recuperação Após Desastre | [Backups geo-redundantes do Azure](automated-backups-overview.md) |
| Escala de leitura | [Utilize réplicas apenas de leitura para carregar cargas de trabalho de consulta apenas de leitura (pré-visualização)](read-scale-out.md) |
| ETL (OLTP a OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) ou [Serviços de Integração de Servidores SQL](/sql/integration-services/sql-server-integration-services) |
| Migração do SQL Server para Azure SQL Database. No entanto, o SQL Data Sync pode ser utilizado após a conclusão da migração, para garantir que a origem e o alvo sejam mantidos em sincronização.  | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="how-it-works"></a>Como funciona

- **Alterações de dados de rastreio:** Data Sync rastreia alterações utilizando inserção, atualização e eliminação de gatilhos. As alterações são registadas numa tabela lateral na base de dados do utilizador. Note que o BULK INSERT não dispara disparos por defeito. Se FIRE_TRIGGERS não for especificado, nenhum gatilho de inserção executa. Adicione a opção FIRE_TRIGGERS para que o Data Sync possa rastrear esses inserções. 
- **Sincronizar dados:** Data Sync é projetado num hub e modelo de fala. O hub sincroniza-se com cada membro individualmente. As mudanças do hub são transferidas para o membro e, em seguida, as alterações do membro são enviadas para o centro.
- **Resolução de conflitos:** O Data Sync oferece duas opções para a resolução de conflitos, *o Hub vence* ou o Membro *ganha.*
  - Se selecionar *o Hub ganha,* as mudanças no hub substituem sempre as alterações no membro.
  - Se selecionar *o Membro ganha,* as alterações no membro substituem as alterações no centro. Se houver mais de um membro, o valor final depende de qual membro sincroniza primeiro.

## <a name="compare-with-transactional-replication"></a>Compare com replicação transacional

| | Sincronização de Dados | Replicação Transacional |
|---|---|---|
| **Vantagens** | - Suporte ativo<br/>- Biducional entre as instalações e a Base de Dados Azure SQL | - Menor latência<br/>- Consistência transacional<br/>- Reutilização da topologia existente após a migração <br/>-Suporte a exemplos geridos Azure SQL |
| **Desvantagens** | - Sem consistência transacional<br/>- Maior impacto no desempenho | - Não pode publicar na Base de Dados Azure SQL <br/>- Alto custo de manutenção |

## <a name="private-link-for-data-sync-preview"></a>Link privado para Data Sync (pré-visualização)
A nova funcionalidade de ligação privada (pré-visualização) permite-lhe escolher um ponto final privado gerido por um serviço para estabelecer uma ligação segura entre o serviço de sincronização e as bases de dados do seu membro/hub durante o processo de sincronização de dados. Um ponto final privado gerido por um serviço é um endereço IP privado dentro de uma rede virtual específica e sub-rede. Dentro do Data Sync, o ponto final privado gerido pelo serviço é criado pela Microsoft e é exclusivamente utilizado pelo serviço Data Sync para uma determinada operação de sincronização. Antes de configurar o link privado, leia os [requisitos gerais](sql-data-sync-data-sql-server-sql-database.md#general-requirements) para a funcionalidade. 

![Link privado para Data Sync](./media/sql-data-sync-data-sql-server-sql-database/sync-private-link-overview.png)

> [!NOTE]
> Tem de aprovar manualmente o ponto final privado gerido pelo serviço na página de **ligações de ponto final privado** do portal Azure durante a implementação do grupo de sincronização ou utilizando o PowerShell.

## <a name="get-started"></a>Introdução 

### <a name="set-up-data-sync-in-the-azure-portal"></a>Configurar o Data Sync no portal Azure

- [Configurar a Sincronização de Dados SQL do Azure](sql-data-sync-sql-server-configure.md)
- Data Sync Agent - [Data Sync Agent for Azure SQL Data Sync](sql-data-sync-agent-overview.md)

### <a name="set-up-data-sync-with-powershell"></a>Configurar o Data Sync com o PowerShell

- [Utilize o PowerShell para sincronizar entre várias bases de dados na Base de Dados Azure SQL](scripts/sql-data-sync-sync-data-between-sql-databases.md)
- [Utilize o PowerShell para sincronizar entre uma base de dados na Base de Dados Azure SQL e uma base de dados num caso de Servidor SQL](scripts/sql-data-sync-sync-data-between-azure-onprem.md)

### <a name="set-up-data-sync-with-rest-api"></a>Configurar o Data Sync com REST API
- [Utilize a API REST para sincronizar entre várias bases de dados na Base de Dados Azure SQL](scripts/sql-data-sync-sync-data-between-sql-databases-rest-api.md)

### <a name="review-the-best-practices-for-data-sync"></a>Reveja as melhores práticas para Data Sync

- [Melhores práticas da Sincronização de Dados SQL do Azure](sql-data-sync-best-practices.md)

### <a name="did-something-go-wrong"></a>Fez alguma coisa correr mal

- [Resolver problemas da Sincronização de Dados SQL do Azure](./sql-data-sync-troubleshoot.md)

## <a name="consistency-and-performance"></a>Consistência e desempenho

### <a name="eventual-consistency"></a>Consistência eventual

Uma vez que o Data Sync é baseado no gatilho, a consistência transacional não é garantida. A Microsoft garante que todas as alterações são feitas eventualmente e que o Data Sync não causa perda de dados.

### <a name="performance-impact"></a>Impacto no desempenho

Data Sync utiliza inserção, atualização e elimina os gatilhos para rastrear as alterações. Cria tabelas laterais na base de dados do utilizador para o rastreio de alterações. Estas atividades de rastreio de alterações têm impacto na carga de trabalho da sua base de dados. Avalie o seu nível de serviço e atualize se necessário.

O fornecimento e desprovisionamento durante a criação, atualização e eliminação do grupo de sincronização também podem ter impacto no desempenho da base de dados.

## <a name="requirements-and-limitations"></a><a name="sync-req-lim"></a> Requisitos e limitações

### <a name="general-requirements"></a>Requisitos gerais

- Cada mesa deve ter uma chave primária. Não mude o valor da chave primária em nenhuma linha. Se tiver de alterar um valor de chave primária, elimine a linha e recrie-a com o novo valor da chave primária.

> [!IMPORTANT]
> Alterar o valor de uma chave primária existente resultará no seguinte comportamento defeituoso:
> - Os dados entre o hub e o membro podem ser perdidos, mesmo que a sincronização não reporte qualquer problema.
> - O Sync pode falhar porque a tabela de rastreio tem uma linha não existente a partir da fonte devido à mudança de tecla primária.

- O isolamento instantâneo deve ser ativado tanto para os membros do Sync como para o hub. Para obter mais informações, veja [Snapshot Isolation in SQL Server (Isolamento de Instantâneo no SQL Server)](/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

- Para utilizar a ligação privada com o Data Sync, tanto as bases de dados dos membros como dos hubs devem ser alojadas em Azure (regiões iguais ou diferentes), no mesmo tipo de nuvem (por exemplo, tanto na nuvem pública como em ambos na nuvem governamental). Além disso, para utilizar o link privado, os fornecedores de recursos da Microsoft.Network devem estar registados para as subscrições que acolhem o hub e os servidores dos membros. Por último, tem de aprovar manualmente o link privado para O Sincronização de Dados durante a configuração de sincronização, dentro da secção "Ligações de ponto final privado" no portal Azure ou através do PowerShell. Para obter mais detalhes sobre como aprovar o link privado, consulte [Configurar o SQL Data Sync](./sql-data-sync-sql-server-configure.md). Assim que aprovar o ponto final privado gerido pelo serviço, todas as comunicações entre o serviço de sincronização e as bases de dados membro/hub irão ocorrer sobre o link privado. Os grupos de sincronização existentes podem ser atualizados para ter esta funcionalidade ativada.

### <a name="general-limitations"></a>Limitações gerais

- Uma mesa não pode ter uma coluna de identidade que não seja a chave principal.
- Uma tabela deve ter um índice agrupado para utilizar a sincronização de dados.
- Uma chave primária não pode ter os seguintes tipos de dados: sql_variant, binário, varbinário, imagem, xml.
- Tenha cuidado quando utilizar os seguintes tipos de dados como chave primária, porque a precisão suportada é apenas para a segunda: hora, data, data 2, datatimeoff.
- Os nomes dos objetos (bases de dados, tabelas e colunas) não podem conter o período de caracteres imprimíveis (.), suporte quadrado esquerdo ([) ou suporte quadrado direito (]).
- Um nome de mesa não pode conter caracteres imprimíveis: ! " # $ % ' ( ) + - espaço
- A autenticação do Diretório Ativo Azure não é suportada.
- Se houver tabelas com o mesmo nome mas esquemas diferentes (por exemplo, dbo.clientes e vendas.clientes) apenas uma das tabelas pode ser adicionada em sincronização.
- Colunas com User-Defined Tipos de Dados não são suportadas
- A movimentação de servidores entre diferentes subscrições não é suportada. 

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

| **Dimensões**                                                  | **Limite**              | **Solução**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Número máximo de grupos de sincronização a que qualquer base de dados pode pertencer.       | 5                      |                             |
| Número máximo de pontos finais num único grupo de sincronização              | 30                     |                             |
| Número máximo de pontos finais no local num único grupo de sincronização. | 5                      | Criar vários grupos de sincronização |
| Bases de dados, tabelas, esquemas e nomes de colunas                       | 50 caracteres por nome |                             |
| Tabelas em um grupo de sincronização                                          | 500                    | Criar vários grupos de sincronização |
| Colunas em uma mesa em um grupo de sincronização                              | 1000                   |                             |
| Tamanho da linha de dados em uma mesa                                        | 24 Mb                  |                             |

> [!NOTE]
> Pode haver até 30 pontos finais num único grupo de sincronização se houver apenas um grupo de sincronização. Se houver mais de um grupo de sincronização, o número total de pontos finais em todos os grupos de sincronização não pode exceder 30. Se uma base de dados pertence a vários grupos de sincronização, é contada como vários pontos finais, não um.

### <a name="network-requirements"></a>Requisitos de rede

> [!NOTE]
> Se utilizar um link privado, estes requisitos de rede não se aplicam. 

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

Sim. Deve ter uma conta SQL Database para hospedar a base de dados do hub.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-databases-only"></a>Posso usar o Data Sync para sincronizar apenas entre bases de dados do SQL Server

Não diretamente. No entanto, pode sincronizar indiretamente entre bases de dados do SQL Server, criando uma base de dados Hub em Azure e, em seguida, adicionando as bases de dados no local ao grupo de sincronização.

### <a name="can-i-use-data-sync-to-sync-between-databases-in-sql-database-that-belong-to-different-subscriptions"></a>Posso utilizar o Data Sync para sincronizar entre bases de dados na Base de Dados SQL que pertencem a diferentes subscrições

Sim. Pode sincronizar entre bases de dados pertencentes a grupos de recursos pertencentes a diferentes subscrições.

- Se as subscrições pertencerem ao mesmo inquilino, e tiver permissão para todas as subscrições, pode configurar o grupo de sincronização no portal Azure.
- Caso contrário, terá de utilizar o PowerShell para adicionar os membros sincronizados que pertencem a diferentes subscrições.

### <a name="can-i-use-data-sync-to-sync-between-databases-in-sql-database-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china-21vianet"></a>Posso utilizar o Data Sync para sincronizar entre bases de dados na Base de Dados SQL que pertencem a diferentes nuvens (como Azure Public Cloud e Azure China 21Vianet)

Sim. Pode sincronizar entre bases de dados que pertencem a diferentes nuvens. Tem de utilizar o PowerShell para adicionar os membros sincronizados que pertencem às diferentes subscrições.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Posso usar o Data Sync para sementerar dados da minha base de dados de produção para uma base de dados vazia, e depois sincronizá-los

Sim. Crie o esquema manualmente na nova base de dados, scriptando-o a partir do original. Depois de criar o esquema, adicione as tabelas a um grupo de sincronização para copiar os dados e mantê-lo sincronizado.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Devo usar o SQL Data Sync para fazer o back up e restaurar as minhas bases de dados

Não é aconselhável utilizar o SQL Data Sync para criar uma cópia de segurança dos seus dados. Não é possível fazer back up e restaurar a um ponto específico no tempo porque as sincronizações sql Data Sync não são versadas. Além disso, o SQL Data Sync não faz o back up de outros objetos SQL, como procedimentos armazenados, e não faz o equivalente a uma operação de restauro rapidamente.

Para obter uma técnica de backup recomendada, consulte [copiar uma base de dados na Base de Dados Azure SQL](database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Pode sincronizar tabelas e colunas encriptadas de dados

- Se uma base de dados utilizar Sempre Encriptado, pode sincronizar apenas as tabelas e colunas que *não* estão encriptadas. Não é possível sincronizar as colunas encriptadas, porque o Data Sync não consegue desencriptar os dados.
- Se uma coluna utilizar Column-Level Encriptação (CLE), pode sincronizar a coluna, desde que o tamanho da linha seja inferior ao tamanho máximo de 24 Mb. O Data Sync trata a coluna encriptada por chave (CLE) como dados binários normais. Para desencriptar os dados de outros membros sincronizados, precisa de ter o mesmo certificado.

### <a name="is-collation-supported-in-sql-data-sync"></a>É colagem suportada no SQL Data Sync

Sim. SQL Data Sync suporta a colagem nos seguintes cenários:

- Se as tabelas de esquemas de sincronização selecionadas já não estiverem no seu centro ou bases de dados dos membros, então quando implementa o grupo de sincronização, o serviço cria automaticamente as tabelas e colunas correspondentes com as definições de colisão selecionadas nas bases de dados de destino vazias.
- Se as tabelas a serem sincronizadas já existirem tanto no seu hub como nas bases de dados dos membros, o SQL Data Sync requer que as colunas-chave primárias tenham a mesma colagem entre bases de dados do hub e dos membros para implementar com sucesso o grupo de sincronização. Não existem restrições de colagem em colunas que não as colunas-chave primárias.

### <a name="is-federation-supported-in-sql-data-sync"></a>É a federação apoiada no SQL Data Sync

A Federation Root Database pode ser utilizada no Serviço de Sincronização de Dados SQL sem qualquer limitação. Não é possível adicionar o ponto final da Base de Dados Federada à versão atual do SQL Data Sync.

### <a name="can-i-use-data-sync-to-sync-data-exported-from-dynamics-365-using-bring-your-own-database-byod-feature"></a>Posso utilizar o Data Sync para sincronizar os dados exportados da Dynamics 365 utilizando a sua própria base de dados (BYOD) ?

O Dynamics 365 traz a sua própria funcionalidade de base de dados permite aos administradores exportar entidades de dados da aplicação para a sua própria base de dados Microsoft Azure SQL. O Data Sync pode ser utilizado para sincronizar estes dados noutras bases de dados se os dados forem exportados através de **um impulso incremental** (o impulso total não é suportado) e permitir que os **gatilhos na base de dados-alvo** sejam definidos como **sim**.

## <a name="next-steps"></a>Passos seguintes

### <a name="update-the-schema-of-a-synced-database"></a>Atualizar o esquema de uma base de dados sincronizada

Tem de atualizar o esquema de uma base de dados num grupo de sincronização? As alterações de esquema não são replicadas automaticamente. Para algumas soluções, consulte os seguintes artigos:

- [Automatizar a replicação de alterações de esquema com SQL Data Sync em Azure](./sql-data-sync-update-sync-schema.md)
- [Utilizar o PowerShell para atualizar o esquema de sincronização num grupo de sincronização existente](scripts/update-sync-schema-in-sync-group.md)

### <a name="monitor-and-troubleshoot"></a>Monitorizar e resolver problemas

O SQL Data Sync está a fazer o que se esperava? Para monitorizar a atividade e resolver problemas, consulte os seguintes artigos:

- [Monitor SQL Data Sync com registos do Monitor Azure](./monitor-tune-overview.md)
- [Resolver problemas da Sincronização de Dados SQL do Azure](./sql-data-sync-troubleshoot.md)

### <a name="learn-more-about-azure-sql-database"></a>Saiba mais sobre a Base de Dados Azure SQL

Para obter mais informações sobre a Base de Dados Azure SQL, consulte os seguintes artigos:

- [Descrição Geral da Base de Dados SQL](sql-database-paas-overview.md)
- [Gestão do Ciclo de Vida da Base de Dados](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
