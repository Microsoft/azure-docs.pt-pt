---
title: Sincronização de Dados
description: Esta visão geral apresenta o Azure Sincronização de Dados SQL
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
ms.openlocfilehash: d69378b2e791732fb478a66f226c6269e2c515f3
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820825"
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>Sincronizar dados entre vários bancos de dados locais e de nuvem com Sincronização de Dados SQL

O Sincronização de Dados SQL é um serviço criado no banco de dados SQL do Azure que permite sincronizar o bi-Directional em vários bancos de dado SQL e SQL Server instâncias.

> [!IMPORTANT]
> O Azure Sincronização de Dados SQL **não oferece suporte a** instância gerenciada do banco de dados SQL do Azure no momento.

## <a name="when-to-use-data-sync"></a>Quando usar a sincronização de dados

A sincronização de dados é útil nos casos em que os dados precisam ser mantidos atualizados em vários bancos de dados SQL do Azure ou em bancos de dado SQL Server. Aqui estão os principais casos de uso para sincronização de dados:

- **Sincronização de dados híbridos:** Com a sincronização de dados, você pode manter os dados sincronizados entre os bancos de dados locais e os bancos de dado SQL do Azure para habilitar aplicativos híbridos. Esse recurso pode ser atraente para clientes que estão considerando a mudança para a nuvem e desejam colocar alguns de seus aplicativos no Azure.
- **Aplicativos distribuídos:** Em muitos casos, é benéfico separar cargas de trabalho diferentes em bancos de dados diferentes. Por exemplo, se você tiver um banco de dados de produção grande, mas também precisar executar uma carga de trabalho de relatório ou análise nesses dados, será útil ter um segundo banco de dados para essa carga de trabalho adicional. Essa abordagem minimiza o impacto no desempenho na carga de trabalho de produção. Você pode usar a sincronização de dados para manter esses dois bancos de dados sincronizados.
- **Aplicativos distribuídos globalmente:** Muitas empresas abrangem várias regiões e até mesmo vários países/regiões. Para minimizar a latência de rede, é melhor ter seus dados em uma região perto de você. Com a sincronização de dados, você pode facilmente manter os bancos de dados em regiões em todo o mundo sincronizados.

A sincronização de dados não é a solução preferida para os seguintes cenários:

| Cenário | Algumas soluções recomendadas |
|----------|----------------------------|
| Recuperação Após Desastre | [Backups com redundância geográfica do Azure](sql-database-automated-backups.md) |
| Escala de leitura | [Usar réplicas somente leitura para balancear a carga de cargas de trabalho de consulta somente leitura (versão prévia)](sql-database-read-scale-out.md) |
| ETL (OLTP para OLAP) | [Azure data Factory](https://azure.microsoft.com/services/data-factory/) ou [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) |
| Migração do SQL Server local para o banco de dados SQL do Azure | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="overview-of-sql-data-sync"></a>Visão geral do Sincronização de Dados SQL

A sincronização de dados é baseada em relação ao conceito de um grupo de sincronização. Um grupo de sincronização é um grupo de bancos de dados que você deseja sincronizar.

A sincronização de dados usa uma topologia hub e spoke para sincronizar dados. Você define um dos bancos de dados no grupo de sincronização como o banco de dados de Hub. O restante dos bancos de dados são bancos de dados de membros. A sincronização ocorre somente entre o Hub e os membros individuais.

- O **banco de dados de Hub** deve ser um banco de dados SQL do Azure.
- Os **bancos de dados de membros** podem ser bancos de dados SQL, bancos de dados SQL Server locais ou instâncias de SQL Server em máquinas virtuais do Azure.
- O **banco** de dados de sincronização contém os metadados e o log para sincronização de data. O banco de dados de sincronização deve ser um banco de dados SQL do Azure localizado na mesma região que o banco de dados de Hub. O banco de dados de sincronização é criado pelo cliente e pertence ao cliente.

> [!NOTE]
> Se você estiver usando um banco de dados local como um banco de dados membro, precisará [instalar e configurar um agente de sincronização local](sql-database-get-started-sql-data-sync.md#add-on-prem).

![Sincronizar dados entre bancos](media/sql-database-sync-data/sync-data-overview.png)

Um grupo de sincronização tem as seguintes propriedades:

- O **esquema de sincronização** descreve quais dados estão sendo sincronizados.
- A **direção de sincronização** pode ser bidirecional ou pode fluir em apenas uma direção. Ou seja, a direção de sincronização pode ser *Hub para membro*ou *membro para Hub*, ou ambos.
- O **intervalo de sincronização** descreve a frequência com que ocorre a sincronização.
- A **política de resolução de conflitos** é uma política de nível de grupo, que pode ser *Hub ganha* ou *membro ganha*.

## <a name="how-does-data-sync-work"></a>Como funciona a sincronização de dados

- **Acompanhamento de alterações de dados:** A sincronização de dados controla as alterações usando os gatilhos inserir, atualizar e excluir. As alterações são registradas em uma tabela lateral no banco de dados do usuário. Observe que o BULK INSERT não aciona gatilhos por padrão. Se FIRE_TRIGGERS não for especificado, nenhum gatilho de inserção será executado. Adicione a opção FIRE_TRIGGERS para que a sincronização de dados possa rastrear essas inserções. 
- **Sincronizando dados:** A sincronização de dados é projetada em um modelo de Hub e spoke. O Hub é sincronizado com cada membro individualmente. As alterações do hub são baixadas para o membro e, em seguida, as alterações do membro são carregadas no Hub.
- **Resolvendo conflitos:** A sincronização de dados fornece duas opções de resolução de conflitos, *Hub ganha* ou *membro ganha*.
  - Se você selecionar *Hub vence*, as alterações no Hub sempre substituirão as alterações no membro.
  - Se você selecionar *membro vence*, as alterações no membro substituirão as alterações no Hub. Se houver mais de um membro, o valor final dependerá de qual membro será sincronizado primeiro.

## <a name="compare-data-sync-with-transactional-replication"></a>Comparar a sincronização de dados com a replicação transacional

| | Sincronização de Dados | Replicação Transacional |
|---|---|---|
| Principais | -Suporte ativo-ativo<br/>-Bidirecional entre o local e o banco de dados SQL do Azure | -Latência mais baixa<br/>-Consistência transacional<br/>-Reutilizar a topologia existente após a migração |
| Desvantagens | -5 min ou mais latência<br/>-Sem consistência transacional<br/>-Maior impacto no desempenho | -Não é possível publicar no banco de dados de banco de dados SQL ou banco de dados em pool<br/>-Custo de manutenção alta |
| | | |

## <a name="get-started-with-sql-data-sync"></a>Introdução à Sincronização de Dados do SQL

### <a name="set-up-data-sync-in-the-azure-portal"></a>Configurar a sincronização de dados no portal do Azure

- [Configurar a Sincronização de Dados SQL do Azure](sql-database-get-started-sql-data-sync.md)
- Agente de sincronização de dados- [agente de sincronização de dados para Azure sincronização de dados SQL](sql-database-data-sync-agent.md)

### <a name="set-up-data-sync-with-powershell"></a>Configurar a sincronização de dados com o PowerShell

- [Utilizar o PowerShell para sincronizar entre várias bases de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)
- [Utilizar o PowerShell para sincronizar entre uma Base de Dados SQL do Azure e uma base de dados do SQL Server no local](scripts/sql-database-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>Examine as práticas recomendadas para a sincronização de dados

- [Melhores práticas da Sincronização de Dados SQL do Azure](sql-database-best-practices-data-sync.md)

### <a name="did-something-go-wrong"></a>Algo deu errado

- [Resolver problemas da Sincronização de Dados SQL do Azure](sql-database-troubleshoot-data-sync.md)

## <a name="consistency-and-performance"></a>Consistência e desempenho

#### <a name="eventual-consistency"></a>Consistência eventual

Como a sincronização de dados é baseada em gatilho, a consistência transacional não é garantida. A Microsoft garante que todas as alterações sejam feitas eventualmente e que a sincronização de dados não cause perda de dados.

#### <a name="performance-impact"></a>Impacto no desempenho

A sincronização de dados usa gatilhos INSERT, Update e Delete para controlar as alterações. Ele cria tabelas laterais no banco de dados do usuário para controle de alterações. Essas atividades de controle de alterações têm um impacto na carga de trabalho do banco de dados. Avalie sua camada de serviço e atualize se necessário.

O provisionamento e o desprovisionamento durante a criação, atualização e exclusão do grupo de sincronização também podem afetar o desempenho do banco de dados. 

## <a name="sync-req-lim"></a>Requisitos e limitações

### <a name="general-requirements"></a>Requisitos gerais

- Cada tabela deve ter uma chave primária. Não altere o valor da chave primária em nenhuma linha. Se você precisar alterar um valor de chave primária, exclua a linha e recrie-a com o novo valor de chave primária. 

> [!IMPORTANT]
> Alterar o valor de uma chave primária existente resultará no seguinte comportamento de falha:   
>   - Os dados entre o Hub e o membro podem ser perdidos, embora a sincronização não relate nenhum problema.
> - A sincronização pode falhar porque a tabela de rastreamento tem uma linha não existente da origem devido à alteração da chave primária.

- O isolamento de instantâneos tem de estar ativado. Para obter mais informações, veja [Snapshot Isolation in SQL Server (Isolamento de Instantâneo no SQL Server)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Limitações gerais

- Uma tabela não pode ter uma coluna de identidade que não seja a chave primária.
- Uma chave primária não pode ter os seguintes tipos de dados: sql_variant, binary, varbinary, Image, XML. 
- Tenha cuidado ao usar os seguintes tipos de dados como chave primária, porque a precisão com suporte é apenas para o segundo: time, DateTime, datetime2, DateTimeOffset.
- Os nomes de objetos (bancos de dados, tabelas e colunas) não podem conter o ponto de caracteres imprimíveis (.), colchete esquerdo ([) ou colchete direito (]).
- Não há suporte para a autenticação Azure Active Directory.
- Não há suporte para tabelas com o mesmo nome, mas com esquema diferente (por exemplo, dbo. Customers e Sales. Customers).
- Não há suporte para colunas com tipos de dados definidos pelo usuário

#### <a name="unsupported-data-types"></a>Tipos de dados sem suporte

- FileStream
- SQL/CLR UDT
- XMLSchemaCollection (XML com suporte)
- Cursor, de IsVersion, carimbo de data/hora, hierarchyid

#### <a name="unsupported-column-types"></a>Tipos de coluna sem suporte

A sincronização de dados não pode sincronizar colunas somente leitura ou geradas pelo sistema. Por exemplo:

- Colunas computadas.
- Colunas geradas pelo sistema para tabelas temporais.

#### <a name="limitations-on-service-and-database-dimensions"></a>Limitações nas dimensões de serviço e banco de dados

| **As**                                                      | **Limite**              | **Solução**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Número máximo de grupos de sincronização aos quais qualquer banco de dados pode pertencer.       | 5                      |                             |
| Número máximo de pontos de extremidade em um único grupo de sincronização              | 30                     |                             |
| Número máximo de pontos de extremidade locais em um único grupo de sincronização. | 5                      | Criar vários grupos de sincronização |
| Nomes de banco de dados, tabela, esquema e coluna                       | 50 caracteres por nome |                             |
| Tabelas em um grupo de sincronização                                          | 500                    | Criar vários grupos de sincronização |
| Colunas em uma tabela em um grupo de sincronização                              | 1000                   |                             |
| Tamanho da linha de dados em uma tabela                                        | 24 MB                  |                             |
| Intervalo de sincronização mínimo                                           | 5 Minutos              |                             |
|||
> [!NOTE]
> Pode haver até 30 pontos de extremidade em um único grupo de sincronização se houver apenas um grupo de sincronização. Se houver mais de um grupo de sincronização, o número total de pontos de extremidade em todos os grupos de sincronização não poderá exceder 30. Se um banco de dados pertencer a vários grupos de sincronização, ele será contado como vários pontos de extremidade, não um.

## <a name="faq-about-sql-data-sync"></a>Perguntas frequentes sobre Sincronização de Dados SQL

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Quanto custa o serviço de Sincronização de Dados SQL

Não há nenhum encargo para o próprio serviço de Sincronização de Dados SQL.  No entanto, você ainda acumula cobranças de transferência de dados para movimentação de dados para dentro e para fora da instância do banco de dado SQL. Para obter mais informações, consulte [preços do banco de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Quais regiões dão suporte à sincronização de dados

Sincronização de Dados SQL está disponível em todas as regiões.

### <a name="is-a-sql-database-account-required"></a>É necessária uma conta do banco de dados SQL

Sim. Você deve ter uma conta do banco de dados SQL para hospedar o banco de dados Hub.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Posso usar a sincronização de dados para sincronizar somente os bancos de dado do SQL Server local

Não diretamente. No entanto, você pode sincronizar entre SQL Server bancos de dados locais indiretamente, criando um banco de dados de Hub no Azure e, em seguida, adicionando os bancos de dados locais ao grupo de sincronização.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-subscriptions"></a>Posso usar a sincronização de dados para sincronizar entre bancos de dado SQL que pertencem a assinaturas diferentes

Sim. Você pode sincronizar entre bancos de dados SQL que pertencem a grupos de recursos de propriedade de assinaturas diferentes.

- Se as assinaturas pertencerem ao mesmo locatário e você tiver permissão para todas as assinaturas, você poderá configurar o grupo de sincronização no portal do Azure.
- Caso contrário, você precisa usar o PowerShell para adicionar os membros de sincronização que pertencem a assinaturas diferentes.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china"></a>Posso usar a sincronização de dados para sincronizar entre bancos de dado SQL que pertencem a nuvens diferentes (como a nuvem pública do Azure e o Azure China)

Sim. Você pode sincronizar entre bancos de dados SQL que pertencem a nuvens diferentes, você precisa usar o PowerShell para adicionar os membros de sincronização que pertencem às diferentes assinaturas.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Posso usar a sincronização de dados para propagar dados do meu banco de dados de produção para um banco de dado vazio e, em seguida, sincronizá-los

Sim. Crie o esquema manualmente no novo banco de dados criando scripts do original. Depois de criar o esquema, adicione as tabelas a um grupo de sincronização para copiar os dados e mantê-los sincronizados.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Devo usar Sincronização de Dados SQL para fazer backup e restaurar meus bancos de dados

Não é recomendável usar Sincronização de Dados SQL para criar um backup de seus dados. Você não pode fazer backup e restaurar para um ponto específico no tempo porque Sincronização de Dados SQL sincronizações não têm controle de versão. Além disso, o Sincronização de Dados SQL não faz backup de outros objetos SQL, como procedimentos armazenados, e não faz o equivalente de uma operação de restauração rapidamente.

Para obter uma técnica de backup recomendada, consulte [copiar um banco de dados SQL do Azure](sql-database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>A sincronização de dados pode sincronizar tabelas e colunas criptografadas

- Se um banco de dados usar Always Encrypted, você poderá sincronizar apenas as tabelas e colunas que *não* são criptografadas. Não é possível sincronizar as colunas criptografadas, pois a sincronização de dados não pode descriptografar os dados.
- Se uma coluna usar criptografia em nível de coluna (CLE), você poderá sincronizar a coluna, desde que o tamanho da linha seja menor que o tamanho máximo de 24 MB. A sincronização de dados trata a coluna criptografada por chave (CLE) como dados binários normais. Para descriptografar os dados em outros membros de sincronização, você precisa ter o mesmo certificado.

### <a name="is-collation-supported-in-sql-data-sync"></a>Há suporte para agrupamento no Sincronização de Dados SQL

Sim. O Sincronização de Dados SQL dá suporte ao agrupamento nos seguintes cenários:

- Se as tabelas de esquema de sincronização selecionadas ainda não estiverem em seus bancos de dados de Hub ou membro, quando você implantar o grupo de sincronização, o serviço criará automaticamente as tabelas e colunas correspondentes com as configurações de agrupamento selecionadas nos bancos de dados de destino vazios.
- Se as tabelas a serem sincronizadas já existirem nos bancos de dados de Hub e membro, Sincronização de Dados SQL exigirá que as colunas de chave primária tenham o mesmo agrupamento entre os bancos de dados de Hub e membro para implantar o grupo de sincronização com êxito. Não há restrições de agrupamento em colunas que não sejam as colunas de chave primária.

### <a name="is-federation-supported-in-sql-data-sync"></a>Há suporte para federação no Sincronização de Dados SQL

O banco de dados raiz da Federação pode ser usado no serviço de Sincronização de Dados SQL sem nenhuma limitação. Não é possível adicionar o ponto de extremidade do banco de dados federado à versão atual do Sincronização de Dados SQL.

## <a name="next-steps"></a>Passos seguintes

### <a name="update-the-schema-of-a-synced-database"></a>Atualizar o esquema de um banco de dados sincronizado

Você precisa atualizar o esquema de um banco de dados em um grupo de sincronização? As alterações de esquema não são replicadas automaticamente. Para algumas soluções, consulte os seguintes artigos:

- [Automatizar a replicação de alterações de esquema no Azure Sincronização de Dados SQL](sql-database-update-sync-schema.md)
- [Usar o PowerShell para atualizar o esquema de sincronização em um grupo de sincronização existente](scripts/sql-database-sync-update-schema.md)

### <a name="monitor-and-troubleshoot"></a>Monitorizar e resolver problemas

O Sincronização de Dados SQL está sendo executado conforme o esperado? Para monitorizar a atividade e resolver problemas, consulte os artigos seguintes:

- [Monitorar Sincronização de Dados SQL do Azure com logs de Azure Monitor](sql-database-sync-monitor-oms.md)
- [Resolver problemas da Sincronização de Dados SQL do Azure](sql-database-troubleshoot-data-sync.md)

### <a name="learn-more-about-azure-sql-database"></a>Saiba mais sobre a Base de Dados SQL do Azure

Para obter mais informações sobre o banco de dados SQL, consulte os seguintes artigos:

- [Descrição Geral da Base de Dados SQL](sql-database-technical-overview.md)
- [Gestão do Ciclo de Vida da Base de Dados](https://msdn.microsoft.com/library/jj907294.aspx)
