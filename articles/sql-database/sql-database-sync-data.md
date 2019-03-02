---
title: Sincronização de dados SQL do Azure | Documentos da Microsoft
description: Esta visão geral introduz de sincronização de dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 5e8e634bfb69d7c274322d34d26ff614f75905af
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57215932"
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>Sincronizar dados em várias bases de dados na cloud e no local com a sincronização de dados SQL

Sincronização de dados SQL é um serviço criado na base de dados do SQL do Azure que permite-lhe sincronizar os dados que selecionar bidirecionalmente em várias bases de dados SQL e instâncias do SQL Server.

> [!IMPORTANT]
> Sincronização de dados SQL do Azure faz **não** suporta a instância gerida da base de dados SQL do Azure neste momento.

## <a name="when-to-use-data-sync"></a>Quando utilizar a sincronização de dados

Sincronização de dados é útil em casos onde os dados têm de ser mantidos atualizados em várias bases de dados SQL do Azure ou bases de dados do SQL Server. Aqui estão os casos de utilização principal para sincronização de dados:

- **Sincronização de dados híbrida:** Sincronização de dados, pode manter os dados sincronizados entre as suas bases de dados no local e bases de dados SQL do Azure para ativar aplicações híbridas. Esta capacidade pode ser atraente para os clientes que estão pensando em mudar para a nuvem e gostariam de colocar alguns dos seus aplicativos no Azure.
- **Aplicações distribuídas:** Em muitos casos, é vantajoso separar cargas de trabalho diferentes em diferentes bases de dados. Por exemplo, se tiver uma base de dados de produção de grandes dimensões, mas também tem de executar uma carga de trabalho de relatórios ou análises sobre estes dados, é útil ter uma segunda base de dados para esta carga de trabalho adicional. Essa abordagem minimiza o impacto de desempenho na sua carga de trabalho de produção. Pode usar a sincronização de dados para manter esses dois bancos de dados sincronizados.
- **Aplicações distribuídas globalmente:** Muitas empresas abrangem várias regiões e até mesmo vários países. Para minimizar a latência de rede, é melhor ter seus dados numa região perto de si. Sincronização de dados, pode facilmente manter bases de dados nas regiões em todo o mundo sincronizados.

Sincronização de dados não é a solução preferencial para os seguintes cenários:

| Cenário | Algumas soluções recomendadas |
|----------|----------------------------|
| Recuperação Após Desastre | [Cópias de segurança do Azure com redundância geográfica](sql-database-automated-backups.md) |
| Escala de leitura | [Utilizar réplicas só de leitura para carregar saldo consulta só de leitura cargas de trabalho (pré-visualização)](sql-database-read-scale-out.md) |
| ETL (OLTP para OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) ou [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services?view=sql-server-2017) |
| Migração do SQL Server no local para a base de dados SQL do Azure | [Serviço de migração de base de dados do Azure](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="overview-of-sql-data-sync"></a>Descrição geral da sincronização de dados SQL

Sincronização de dados baseia-se em torno do conceito de um grupo de sincronização. Um grupo de sincronização é um grupo de bases de dados que pretende sincronizar.

Sincronização de dados utiliza uma topologia hub- and -spoke para sincronizar os dados. Define uma das bases de dados no grupo de sincronização da base de dados de Hub. O restante das bases de dados são bases de dados do membro. Sincronização ocorre apenas entre o Hub e membros individuais.

- O **base de dados de Hub** tem de ser uma base de dados do SQL do Azure.
- O **bases de dados do membro** pode ser bases de dados SQL, bases de dados do SQL Server no local ou instâncias do SQL Server em máquinas virtuais do Azure.
- O **base de dados de sincronização** contém os metadados e o log de sincronização de dados. A base de dados de sincronização tem de ser que uma base de dados do SQL do Azure localizados na mesma região que a base de dados de Hub. A base de dados de sincronização é criado de cliente e cliente pertencentes à empresa.

> [!NOTE]
> Se estiver a utilizar uma base de dados no local como uma base de dados do membro, precisa [instalar e configurar um agente de sincronização local](sql-database-get-started-sql-data-sync.md#add-on-prem).

![Sincronizar dados entre bases de dados](media/sql-database-sync-data/sync-data-overview.png)

Um grupo de sincronização tem as seguintes propriedades:

- O **esquema de sincronização** descreve quais os dados que está a ser sincronizados.
- O **direção de sincronização** pode ser bidirecional ou podem fluir em apenas uma direção. Ou seja, a direção de sincronização pode ser *Hub para membro*, ou *membro para o Hub*, ou ambos.
- O **intervalo de sincronização** descreve com que frequência ocorre a sincronização.
- O **política de resolução de conflito** é uma política de nível de grupo, que pode ser *Hub wins* ou *wins membro*.

## <a name="how-does-data-sync-work"></a>Como funciona a sincronização de dados

- **O controle de alterações de dados:** Sincronização de dados controla as alterações usando inserir, atualizar e eliminar acionadores. As alterações são gravadas numa tabela do lado da base de dados do utilizador. Tenha em atenção que inserção em massa não é disparado acionadores por predefinição. Não se não for especificado FIRE_TRIGGERS, executar nenhuma acionador insert. Adicione a opção de FIRE_TRIGGERS para que sincronização de dados possa acompanhar as inserções. 
- **Sincronização de dados:** Sincronização de dados foi criada num modelo de Hub- and -Spoke. O Hub sincroniza com cada membro individualmente. Alterações do Hub de são transferidas para o membro e, em seguida, as alterações a partir do membro são carregadas para o Hub.
- **Resolver conflitos:** Sincronização de dados fornece duas opções para resolução de conflitos *Hub wins* ou *wins membro*.
  - Se selecionou *Hub wins*, as alterações no hub substitui sempre as alterações no membro.
  - Se selecionou *wins membro*, as alterações nas alterações de substituição de membro no hub. Se existir mais do que um membro, o valor final depende qual membro sincroniza pela primeira vez.

## <a name="compare-data-sync-with-transactional-replication"></a>Comparar a sincronização de dados com a replicação transacional

| | Sincronização de Dados | Replicação Transacional |
|---|---|---|
| Vantagens | -Suporte de ativo-ativo<br/>Bidirecional entre o local e a base de dados SQL do Azure | -Menor latência<br/>-Consistência transacional<br/>-Reutilizar topologia existente após a migração |
| Desvantagens | -5 min ou mais latência<br/>-Sem uma consistência transacional<br/>-Maior impacto no desempenho | -Não é possível publicar a partir da base de dados única base de dados do Azure SQL ou base de dados agrupada<br/>-Custo de manutenção elevada |
| | | |

## <a name="get-started-with-sql-data-sync"></a>Introdução à Sincronização de Dados do SQL

### <a name="set-up-data-sync-in-the-azure-portal"></a>Configurar a sincronização de dados no portal do Azure

- [Configurar a Sincronização de Dados SQL do Azure](sql-database-get-started-sql-data-sync.md)
- Agente de - de sincronização de dados [agente de sincronização de dados SQL do Azure de sincronização de dados](sql-database-data-sync-agent.md)

### <a name="set-up-data-sync-with-powershell"></a>Configurar a sincronização de dados com o PowerShell

- [Utilizar o PowerShell para sincronizar entre várias bases de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)
- [Utilizar o PowerShell para sincronizar entre uma Base de Dados SQL do Azure e uma base de dados do SQL Server no local](scripts/sql-database-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>Rever as melhores práticas para a sincronização de dados

- [Melhores práticas da Sincronização de Dados SQL do Azure](sql-database-best-practices-data-sync.md)

### <a name="did-something-go-wrong"></a>Algo errado

- [Resolver problemas da Sincronização de Dados SQL do Azure](sql-database-troubleshoot-data-sync.md)

## <a name="consistency-and-performance"></a>Consistência e desempenho

#### <a name="eventual-consistency"></a>Consistência eventual

Uma vez que a sincronização de dados é baseado no acionador, consistência transacional não é garantida. Microsoft garante que todas as alterações são feitas, eventualmente, e que a sincronização de dados não cause a perda de dados.

#### <a name="performance-impact"></a>Impacto no desempenho

Sincronização de dados utiliza insere, atualizar e eliminar acionadores para controlar as alterações. Ele cria tabelas do lado da base de dados do utilizador para controlo de alterações. Estas atividades de controlo de alteração tem um impacto na sua carga de trabalho de base de dados. Avaliar o seu escalão de serviço e atualizar se for necessário.

Aprovisionamento e desaprovisionamento durante a criação do grupo de sincronização, atualização e exclusão também podem impactar o desempenho da base de dados. 

## <a name="sync-req-lim"></a> Requisitos e limitações

### <a name="general-requirements"></a>Requisitos gerais

- Cada tabela tem de ter uma chave primária. Não altere o valor da chave primária em qualquer linha. Se tiver de alterar um valor de chave primário, eliminar a linha e recriá-lo com o novo valor da chave primário. 
- Isolamento de instantâneo tem de estar ativado. Para mais informações, veja [isolamento do instantâneo no SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Limitações gerais

- Uma tabela não pode ter uma coluna de identidade que não é a chave primária.
- Uma chave primária não pode ter os seguintes tipos de dados: sql_variant, binary, varbinary, imagem, xml. 
- Tenha cuidado ao utilizar os seguintes tipos de dados como uma chave primária, uma vez que a precisão suportada é apenas para o segundo: hora, datetime, datetime2, datetimeoffset.
- Os nomes dos objetos (bases de dados, tabelas e colunas) não podem conter o período de caracteres imprimíveis (.), à esquerda parêntesis Reto ([), ou Reto direito square (]).
- Não é suportada a autenticação do Azure Active Directory.
- As tabelas com o mesmo nome, mas um esquema diferente (por exemplo, dbo.customers e sales.customers) não são suportadas.

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

| **Dimensões**                                                      | **Limite**              | **Solução**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Número máximo de grupos de sincronização a qualquer base de dados pode pertencer a.       | 5                      |                             |
| Número máximo de pontos de extremidade num grupo de sincronização único              | 30                     |                             |
| Número máximo de pontos de extremidade no local num grupo de sincronização único. | 5                      | Criar vários grupos de sincronização |
| Nomes de base de dados, de tabela, de esquema e de coluna                       | 50 carateres por nome |                             |
| Tabelas num grupo de sincronização                                          | 500                    | Criar vários grupos de sincronização |
| Colunas numa tabela num grupo de sincronização                              | 1000                   |                             |
| Tamanho de linha de dados numa tabela                                        | 24 Mb                  |                             |
| Intervalo de sincronização mínimo                                           | 5 Minutos              |                             |
|||
> [!NOTE]
> Pode haver até 30 pontos de extremidade num grupo de sincronização único se houver apenas um grupo de sincronização. Se existir mais do que um grupo de sincronização, o número total de pontos de extremidade em todos os grupos de sincronização não pode exceder 30. Se uma base de dados pertencer a vários grupos de sincronização, é contabilizado como vários pontos de extremidade, não um.

## <a name="faq-about-sql-data-sync"></a>FAQ sobre a sincronização de dados SQL

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Quanto custa o serviço de sincronização de dados SQL

Não existe nenhum custo associado para o próprio serviço de sincronização de dados SQL.  No entanto, ainda acumular os custos de transferência de dados de movimento de dados dentro e fora de sua instância de base de dados SQL. Para mais informações, veja [preços de base de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Que regiões suportam a sincronização de dados

Sincronização de dados SQL está disponível em todas as regiões.

### <a name="is-a-sql-database-account-required"></a>É necessária uma conta de base de dados SQL

Sim. Tem de ter uma conta de base de dados SQL para alojar a base de dados de Hub.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Pode utilizar a sincronização de dados para sincronizar entre o SQL Server no local bases de dados apenas

Não diretamente. Pode sincronizar entre bases de dados do SQL Server no local indiretamente, no entanto, ao criar uma base de dados de Hub no Azure e, em seguida, adicionar as bases de dados no local para o grupo de sincronização.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-subscriptions"></a>Pode utilizar a sincronização de dados para sincronizar entre bases de dados do SQL que pertencem a subscrições diferentes

Sim. Pode sincronizar entre bases de dados do SQL que pertencem a grupos de recursos pertencentes a subscrições diferentes.

- Se as subscrições pertencem ao mesmo inquilino, e tem permissão para todas as subscrições, pode configurar o grupo de sincronização no portal do Azure.
- Caso contrário, terá de utilizar o PowerShell para adicionar os membros de sincronização que pertencem a subscrições diferentes.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china"></a>Pode utilizar a sincronização de dados para sincronizar entre bases de dados do SQL que pertencem a nuvens diferentes (como nuvem pública do Azure e Azure China)

Sim. Pode sincronizar entre bases de dados do SQL que pertencem a nuvens diferentes, tem de utilizar o PowerShell para adicionar os membros de sincronização que pertencem a subscrições diferentes.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Posso utilizar a sincronização de dados para dados de seed da minha base de dados de produção para uma base de dados vazia e, em seguida, sincronizá-los

Sim. Crie o esquema manualmente na nova base de dados, criação de scripts em relação ao original. Depois de criar o esquema e adicionar as tabelas para um grupo de sincronização para copiar os dados e manter sincronizados.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Deve utilizar a sincronização de dados SQL para criar cópias de segurança e restaurar meus bancos de dados

Não é recomendado utilizar a sincronização de dados do SQL para criar uma cópia de segurança dos seus dados. Não é possível criar cópias de segurança e restaurar para um ponto específico no tempo, porque não têm versões sincronizações de sincronização de dados SQL. Além disso, a sincronização de dados SQL não faz backup outros objetos SQL, tais como procedimentos armazenados e não faz o equivalente a uma operação de restauro rapidamente.

Para um recomendado técnica de cópia de segurança, consulte [copiar uma base de dados SQL do Azure](sql-database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Sincronização de dados podem sincronizar encriptadas tabelas e colunas

- Se a uma base de dados utiliza o Always Encrypted, pode sincronizar apenas as tabelas e colunas que estão *não* encriptados. Não é possível sincronizar colunas criptografadas, uma vez que a sincronização de dados não é possível desencriptar os dados.
- Se a uma coluna utiliza encriptação de nível de coluna (CLE), pode sincronizar a coluna, desde que o tamanho de linha é inferior ao tamanho máximo de 24 Mb. Sincronização de dados trata a coluna encriptada pela chave (CLE) como dados binários normais. Para descriptografar os dados em outros membros de sincronização, tem de ter o mesmo certificado.

### <a name="is-collation-supported-in-sql-data-sync"></a>O agrupamento é suportado na sincronização de dados SQL

Sim. Sincronização de dados SQL suporta o agrupamento nos seguintes cenários:

- Se as tabelas do esquema de sincronização selecionados não estiverem já em seus bancos de dados de hub ou membro, em seguida, ao implementar o grupo de sincronização, o serviço cria automaticamente as correspondentes de tabelas e colunas com as definições de agrupamento selecionadas nas bases de dados de destino vazia.
- Se as tabelas a ser sincronizada já existirem no seu hub e o membro de bases de dados, a sincronização de dados SQL requer que as colunas de chave primárias tem o mesmo agrupamento entre bases de dados de hub e member para implementar o grupo de sincronização com êxito. Não existem sem restrições de agrupamento em colunas, exceto as colunas de chave primárias.

### <a name="is-federation-supported-in-sql-data-sync"></a>Federação é suportada na sincronização de dados SQL

Base de dados de raiz de Federação pode ser utilizado no serviço de sincronização de dados SQL sem qualquer limitação. Não é possível adicionar o ponto final federado a base de dados para a versão atual da sincronização de dados SQL.

## <a name="next-steps"></a>Passos Seguintes

### <a name="update-the-schema-of-a-synced-database"></a>Atualizar o esquema de uma base de dados sincronizado

É necessário que atualizar o esquema de uma base de dados num grupo de sincronização? Alterações de esquema não são replicadas automaticamente. Para algumas soluções, veja os artigos seguintes:

- [Automatizar a replicação das alterações de esquema na sincronização de dados SQL do Azure](sql-database-update-sync-schema.md)
- [Utilize o PowerShell para atualizar o esquema de sincronização num grupo de sincronização existente](scripts/sql-database-sync-update-schema.md)

### <a name="monitor-and-troubleshoot"></a>Monitorizar e resolver problemas

Sincronização de dados SQL está executando como esperado? Para monitorizar a atividade e resolver problemas, consulte os artigos seguintes:

- [Monitorizar a sincronização de dados SQL do Azure com os registos do Azure Monitor](sql-database-sync-monitor-oms.md)
- [Resolver problemas da Sincronização de Dados SQL do Azure](sql-database-troubleshoot-data-sync.md)

### <a name="learn-more-about-azure-sql-database"></a>Obtenha mais informações sobre a Base de Dados SQL do Azure

Para mais informações sobre a base de dados SQL, veja os artigos seguintes:

- [Descrição Geral da Base de Dados SQL](sql-database-technical-overview.md)
- [Gestão do Ciclo de Vida da Base de Dados](https://msdn.microsoft.com/library/jj907294.aspx)
