---
title: Sincronização de Dados
description: Esta visão geral introduz de sincronização de dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 08/20/2019
ms.openlocfilehash: 1ee2efbb8aebfc2f1a94c89edef6166898946d8a
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422520"
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>Sincronizar dados em várias bases de dados na cloud e no local com a sincronização de dados SQL

Sincronização de dados SQL é um serviço criado na base de dados do SQL do Azure que permite-lhe sincronizar os dados que selecionar bidirecionalmente em várias bases de dados SQL e instâncias do SQL Server.

> [!IMPORTANT]
> O Azure Sincronização de Dados SQL não oferece suporte a Instância Gerenciada do Banco de Dados SQL do Azure no momento.

## <a name="when-to-use-data-sync"></a>Quando utilizar a sincronização de dados

A sincronização de dados é útil nos casos em que os dados precisam ser mantidos atualizados em vários bancos de dados SQL do Azure ou em bancos de dado SQL Server. Aqui estão os casos de utilização principal para sincronização de dados:

- **Sincronização de dados híbridos:** Com a sincronização de dados, você pode manter os dados sincronizados entre os bancos de dados locais e os bancos de dado SQL do Azure para habilitar aplicativos híbridos. Esta capacidade pode ser atraente para os clientes que estão pensando em mudar para a nuvem e gostariam de colocar alguns dos seus aplicativos no Azure.
- **Aplicativos distribuídos:** Em muitos casos, é benéfico separar cargas de trabalho diferentes em bancos de dados diferentes. Por exemplo, se tiver uma base de dados de produção de grandes dimensões, mas também tem de executar uma carga de trabalho de relatórios ou análises sobre estes dados, é útil ter uma segunda base de dados para esta carga de trabalho adicional. Essa abordagem minimiza o impacto de desempenho na sua carga de trabalho de produção. Pode usar a sincronização de dados para manter esses dois bancos de dados sincronizados.
- **Aplicativos distribuídos globalmente:** Muitas empresas abrangem várias regiões e até mesmo vários países/regiões. Para minimizar a latência de rede, é melhor ter seus dados numa região perto de si. Sincronização de dados, pode facilmente manter bases de dados nas regiões em todo o mundo sincronizados.

A sincronização de dados não é a solução preferida para os seguintes cenários:

| Cenário | Algumas soluções recomendadas |
|----------|----------------------------|
| Recuperação Após Desastre | [Backups com redundância geográfica do Azure](sql-database-automated-backups.md) |
| Escala de leitura | [Usar réplicas somente leitura para balancear a carga de cargas de trabalho de consulta somente leitura (versão prévia)](sql-database-read-scale-out.md) |
| ETL (OLTP para OLAP) | [Azure data Factory](https://azure.microsoft.com/services/data-factory/) ou [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) |
| Migração do SQL Server no local para a base de dados SQL do Azure | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="overview-of-sql-data-sync"></a>Visão geral do Sincronização de Dados SQL

Sincronização de dados baseia-se em torno do conceito de um grupo de sincronização. Um grupo de sincronização é um grupo de bases de dados que pretende sincronizar.

Sincronização de dados utiliza uma topologia hub- and -spoke para sincronizar os dados. Define uma das bases de dados no grupo de sincronização da base de dados de Hub. O restante das bases de dados são bases de dados do membro. Sincronização ocorre apenas entre o Hub e membros individuais.

- O **banco de dados de Hub** deve ser um banco de dados SQL do Azure.
- Os **bancos de dados de membros** podem ser bancos de dados SQL, bancos de dados SQL Server locais ou instâncias de SQL Server em máquinas virtuais do Azure.
- O **banco** de dados de sincronização contém os metadados e o log para sincronização de data. O banco de dados de sincronização deve ser um banco de dados SQL do Azure localizado na mesma região que o banco de dados de Hub. A base de dados de sincronização é criado de cliente e cliente pertencentes à empresa.

> [!NOTE]
> Se você estiver usando um banco de dados local como um banco de dados membro, precisará [instalar e configurar um agente de sincronização local](sql-database-get-started-sql-data-sync.md#add-on-prem).

![Sincronizar dados entre bases de dados](media/sql-database-sync-data/sync-data-overview.png)

Um grupo de sincronização tem as seguintes propriedades:

- O **esquema de sincronização** descreve quais dados estão sendo sincronizados.
- A **direção de sincronização** pode ser bidirecional ou pode fluir em apenas uma direção. Ou seja, a direção de sincronização pode ser *Hub para membro*ou *membro para Hub*, ou ambos.
- O **intervalo de sincronização** descreve a frequência com que ocorre a sincronização.
- A **política de resolução de conflitos** é uma política de nível de grupo, que pode ser *Hub ganha* ou *membro ganha*.

## <a name="how-does-data-sync-work"></a>Como funciona a sincronização de dados

- **Acompanhamento de alterações de dados:** A sincronização de dados controla as alterações usando os gatilhos inserir, atualizar e excluir. As alterações são gravadas numa tabela do lado da base de dados do utilizador. Observe que BULK INSERT não dispara gatilhos por padrão. Se FIRE_TRIGGERS não for especificado, nenhum gatilho de inserção será executado. Adicione a opção de FIRE_TRIGGERS para que sincronização de dados possa acompanhar as inserções. 
- **Sincronizando dados:** A sincronização de dados é projetada em um modelo de Hub e spoke. O Hub sincroniza com cada membro individualmente. Alterações do Hub de são transferidas para o membro e, em seguida, as alterações a partir do membro são carregadas para o Hub.
- **Resolvendo conflitos:** A sincronização de dados fornece duas opções de resolução de conflitos, *Hub ganha* ou *membro ganha*.
  - Se você selecionar *Hub vence*, as alterações no Hub sempre substituirão as alterações no membro.
  - Se você selecionar *membro vence*, as alterações no membro substituirão as alterações no Hub. Se existir mais do que um membro, o valor final depende qual membro sincroniza pela primeira vez.

## <a name="compare-data-sync-with-transactional-replication"></a>Comparar a sincronização de dados com a replicação transacional

| | Sincronização de Dados | Replicação Transacional |
|---|---|---|
| Vantagens | -Suporte ativo-ativo<br/>-Bidirecional entre o local e o banco de dados SQL do Azure | -Latência mais baixa<br/>-Consistência transacional<br/>-Reutilizar a topologia existente após a migração |
| Desvantagens | -5 min ou mais latência<br/>-Sem consistência transacional<br/>-Maior impacto no desempenho | -Não é possível publicar no banco de dados de banco de dados SQL ou banco de dados em pool<br/>-Custo de manutenção alta |

## <a name="get-started-with-sql-data-sync"></a>Introdução à Sincronização de Dados do SQL

### <a name="set-up-data-sync-in-the-azure-portal"></a>Configurar a sincronização de dados no portal do Azure

- [Configurar a Sincronização de Dados SQL do Azure](sql-database-get-started-sql-data-sync.md)
- Agente de sincronização de dados- [agente de sincronização de dados para Azure sincronização de dados SQL](sql-database-data-sync-agent.md)

### <a name="set-up-data-sync-with-powershell"></a>Configurar a sincronização de dados com o PowerShell

- [Utilizar o PowerShell para sincronizar entre várias bases de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)
- [Utilizar o PowerShell para sincronizar entre uma Base de Dados SQL do Azure e uma base de dados do SQL Server no local](scripts/sql-database-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>Rever as melhores práticas para a sincronização de dados

- [Melhores práticas da Sincronização de Dados SQL do Azure](sql-database-best-practices-data-sync.md)

### <a name="did-something-go-wrong"></a>Algo deu errado

- [Resolver problemas da Sincronização de Dados SQL do Azure](sql-database-troubleshoot-data-sync.md)

## <a name="consistency-and-performance"></a>Consistência e desempenho

### <a name="eventual-consistency"></a>Consistência eventual

Como a sincronização de dados é baseada em gatilho, a consistência transacional não é garantida. A Microsoft garante que todas as alterações sejam feitas eventualmente e que a sincronização de dados não cause perda de dados.

### <a name="performance-impact"></a>Impacto no desempenho

Sincronização de dados utiliza insere, atualizar e eliminar acionadores para controlar as alterações. Ele cria tabelas do lado da base de dados do utilizador para controlo de alterações. Estas atividades de controlo de alteração tem um impacto na sua carga de trabalho de base de dados. Avaliar o seu escalão de serviço e atualizar se for necessário.

Aprovisionamento e desaprovisionamento durante a criação do grupo de sincronização, atualização e exclusão também podem impactar o desempenho da base de dados.

## <a name="sync-req-lim"></a>Requisitos e limitações

### <a name="general-requirements"></a>Requisitos gerais

- Cada tabela tem de ter uma chave primária. Não altere o valor da chave primária em qualquer linha. Se tiver de alterar um valor de chave primário, eliminar a linha e recriá-lo com o novo valor da chave primário.

> [!IMPORTANT]
> Alterar o valor de uma chave primária existente resultará no seguinte comportamento de falha:
> - Os dados entre o Hub e o membro podem ser perdidos, embora a sincronização não relate nenhum problema.
> - A sincronização pode falhar porque a tabela de rastreamento tem uma linha não existente da origem devido à alteração da chave primária.

- Isolamento de instantâneo tem de estar ativado. Para obter mais informações, veja [Snapshot Isolation in SQL Server (Isolamento de Instantâneo no SQL Server)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Limitações gerais

- Uma tabela não pode ter uma coluna de identidade que não seja a chave primária.
- Uma chave primária não pode ter os seguintes tipos de dados: sql_variant, binary, varbinary, Image, XML.
- Tenha cuidado ao utilizar os seguintes tipos de dados como uma chave primária, uma vez que a precisão suportada é apenas para o segundo: hora, datetime, datetime2, datetimeoffset.
- Os nomes de objetos (bancos de dados, tabelas e colunas) não podem conter o ponto de caracteres imprimíveis (.), colchete esquerdo ([) ou colchete direito (]).
- Não há suporte para a autenticação Azure Active Directory.
- Não há suporte para tabelas com o mesmo nome, mas com esquema diferente (por exemplo, dbo. Customers e Sales. Customers).
- Não há suporte para colunas com tipos de dados definidos pelo usuário

#### <a name="unsupported-data-types"></a>Tipos de dados não suportado

- FileStream
- SQL/CLR UDT
- XMLSchemaCollection (XML suportado)
- Período de tempo do cursor, RowVersion, Hierarchyid

#### <a name="unsupported-column-types"></a>Tipos de coluna não suportado

Sincronização de dados não é possível sincronizar as colunas de só de leitura ou gerados pelo sistema. Por exemplo:

- Colunas calculadas.
- Colunas gerados pelo sistema para tabelas temporais.

#### <a name="limitations-on-service-and-database-dimensions"></a>Limitações nas dimensões de serviço e da base de dados

| **As**                                                  | **Limite**              | **Solução**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Número máximo de grupos de sincronização a qualquer base de dados pode pertencer a.       | 5                      |                             |
| Número máximo de pontos de extremidade num grupo de sincronização único              | 30                     |                             |
| Número máximo de pontos de extremidade no local num grupo de sincronização único. | 5                      | Criar vários grupos de sincronização |
| Nomes de base de dados, de tabela, de esquema e de coluna                       | 50 carateres por nome |                             |
| Tabelas num grupo de sincronização                                          | 500                    | Criar vários grupos de sincronização |
| Colunas numa tabela num grupo de sincronização                              | 1000                   |                             |
| Tamanho de linha de dados numa tabela                                        | 24 Mb                  |                             |
| Intervalo de sincronização mínimo                                           | 5 Minutos              |                             |

> [!NOTE]
> Pode haver até 30 pontos de extremidade num grupo de sincronização único se houver apenas um grupo de sincronização. Se existir mais do que um grupo de sincronização, o número total de pontos de extremidade em todos os grupos de sincronização não pode exceder 30. Se uma base de dados pertencer a vários grupos de sincronização, é contabilizado como vários pontos de extremidade, não um.

## <a name="faq-about-sql-data-sync"></a>FAQ sobre a sincronização de dados SQL

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Quanto custa o serviço de Sincronização de Dados SQL

Não há encargos pelo próprio serviço de Sincronização de Dados SQL. No entanto, você ainda coleta cobranças de transferência de dados para movimentação de dados para dentro e para fora de sua instância do SQL Database. Para obter mais informações, consulte [preços do banco de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Quais regiões dão suporte à sincronização de dados

Sincronização de dados SQL está disponível em todas as regiões.

### <a name="is-a-sql-database-account-required"></a>É necessária uma conta do banco de dados SQL

Sim. Tem de ter uma conta de base de dados SQL para alojar a base de dados de Hub.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Posso usar a sincronização de dados para sincronizar somente os bancos de dado do SQL Server local

Não diretamente. Pode sincronizar entre bases de dados do SQL Server no local indiretamente, no entanto, ao criar uma base de dados de Hub no Azure e, em seguida, adicionar as bases de dados no local para o grupo de sincronização.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-subscriptions"></a>Posso usar a sincronização de dados para sincronizar entre bancos de dado SQL que pertencem a assinaturas diferentes

Sim. Pode sincronizar entre bases de dados do SQL que pertencem a grupos de recursos pertencentes a subscrições diferentes.

- Se as subscrições pertencem ao mesmo inquilino, e tem permissão para todas as subscrições, pode configurar o grupo de sincronização no portal do Azure.
- Caso contrário, terá de utilizar o PowerShell para adicionar os membros de sincronização que pertencem a subscrições diferentes.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china-21vianet"></a>Posso usar a sincronização de dados para sincronizar entre bancos de dado SQL que pertencem a nuvens diferentes (como a nuvem pública do Azure e o Azure China 21Vianet)

Sim. Pode sincronizar entre bases de dados do SQL que pertencem a nuvens diferentes, tem de utilizar o PowerShell para adicionar os membros de sincronização que pertencem a subscrições diferentes.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Posso usar a sincronização de dados para propagar dados do meu banco de dados de produção para um banco de dado vazio e, em seguida, sincronizá-los

Sim. Crie o esquema manualmente na nova base de dados, criação de scripts em relação ao original. Depois de criar o esquema e adicionar as tabelas para um grupo de sincronização para copiar os dados e manter sincronizados.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Devo usar Sincronização de Dados SQL para fazer backup e restaurar meus bancos de dados

Não é recomendável usar Sincronização de Dados SQL para criar um backup de seus dados. Não é possível fazer backup e restaurar para um ponto específico no tempo porque Sincronização de Dados SQL sincronizações não têm controle de versão. Além disso, o Sincronização de Dados SQL não faz backup de outros objetos SQL, como procedimentos armazenados, e não faz o equivalente de uma operação de restauração rapidamente.

Para obter uma técnica de backup recomendada, consulte [copiar um banco de dados SQL do Azure](sql-database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>A sincronização de dados pode sincronizar tabelas e colunas criptografadas

- Se um banco de dados usar Always Encrypted, você poderá sincronizar apenas as tabelas e colunas que *não* são criptografadas. Não é possível sincronizar colunas criptografadas, uma vez que a sincronização de dados não é possível desencriptar os dados.
- Se a uma coluna utiliza encriptação de nível de coluna (CLE), pode sincronizar a coluna, desde que o tamanho de linha é inferior ao tamanho máximo de 24 Mb. Sincronização de dados trata a coluna encriptada pela chave (CLE) como dados binários normais. Para descriptografar os dados em outros membros de sincronização, tem de ter o mesmo certificado.

### <a name="is-collation-supported-in-sql-data-sync"></a>Há suporte para agrupamento no Sincronização de Dados SQL

Sim. Sincronização de dados SQL suporta o agrupamento nos seguintes cenários:

- Se as tabelas de esquema de sincronização selecionadas ainda não estiverem em seus bancos de dados de Hub ou membro, quando você implantar o grupo de sincronização, o serviço criará automaticamente as tabelas e colunas correspondentes com as configurações de agrupamento selecionadas nos bancos de dados de destino vazios.
- Se as tabelas a ser sincronizada já existirem no seu hub e o membro de bases de dados, a sincronização de dados SQL requer que as colunas de chave primárias tem o mesmo agrupamento entre bases de dados de hub e member para implementar o grupo de sincronização com êxito. Não existem sem restrições de agrupamento em colunas, exceto as colunas de chave primárias.

### <a name="is-federation-supported-in-sql-data-sync"></a>Há suporte para federação no Sincronização de Dados SQL

Base de dados de raiz de Federação pode ser utilizado no serviço de sincronização de dados SQL sem qualquer limitação. Você não pode adicionar o ponto de extremidade do banco de dados federado à versão atual do Sincronização de Dados SQL.

## <a name="next-steps"></a>Passos seguintes

### <a name="update-the-schema-of-a-synced-database"></a>Atualizar o esquema de uma base de dados sincronizado

É necessário que atualizar o esquema de uma base de dados num grupo de sincronização? As alterações de esquema não são replicadas automaticamente. Para algumas soluções, veja os artigos seguintes:

- [Automatizar a replicação de alterações de esquema no Azure Sincronização de Dados SQL](sql-database-update-sync-schema.md)
- [Usar o PowerShell para atualizar o esquema de sincronização em um grupo de sincronização existente](scripts/sql-database-sync-update-schema.md)

### <a name="monitor-and-troubleshoot"></a>Monitorizar e resolver problemas

O Sincronização de Dados SQL está sendo feito conforme o esperado? Para monitorizar a atividade e resolver problemas, consulte os artigos seguintes:

- [Monitorar Sincronização de Dados SQL do Azure com logs de Azure Monitor](sql-database-sync-monitor-oms.md)
- [Resolver problemas da Sincronização de Dados SQL do Azure](sql-database-troubleshoot-data-sync.md)

### <a name="learn-more-about-azure-sql-database"></a>Obtenha mais informações sobre a Base de Dados SQL do Azure

Para mais informações sobre a base de dados SQL, veja os artigos seguintes:

- [Descrição Geral da Base de Dados SQL](sql-database-technical-overview.md)
- [Gestão do Ciclo de Vida da Base de Dados](https://msdn.microsoft.com/library/jj907294.aspx)
