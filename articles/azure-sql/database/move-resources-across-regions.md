---
title: Mover recursos para uma nova região
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Saiba como mover a sua base de dados ou gerir a sua área de dados para outra região.
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: ae6c87c9eabea837ba9c43676d4ca712caa385cb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94594169"
---
# <a name="move-resources-to-new-region---azure-sql-database--azure-sql-managed-instance"></a>Mover recursos para a nova região - Azure SQL Database & Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Este artigo ensina-lhe um fluxo de trabalho genérico para como mover a sua base de dados ou caso gerido para uma nova região.

## <a name="overview"></a>Descrição geral

Há vários cenários em que gostaria de mover a sua base de dados existente ou gerir o exemplo de uma região para outra. Por exemplo, está a expandir o seu negócio para uma nova região e quer otimizá-lo para a nova base de clientes. Ou precisa mover as operações para outra região por razões de conformidade. Ou a Azure lançou uma nova região que proporciona uma melhor proximidade e melhora a experiência do cliente.  

Este artigo fornece um fluxo de trabalho geral para a transferência de recursos para uma região diferente. O fluxo de trabalho consiste nos seguintes passos:

1. Verifique os pré-requisitos para a mudança.
1. Preparem-se para mover os recursos no âmbito.
1. Monitorize o processo de preparação.
1. Teste o processo de movimento.
1. Inicie o movimento real.
1. Retire os recursos da região de origem.

> [!NOTE]
> Este artigo aplica-se às migrações dentro da nuvem pública de Azure ou dentro da mesma nuvem soberana.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="move-a-database"></a>Mover uma base de dados

### <a name="verify-prerequisites"></a>Verificar os pré-requisitos

1. Crie um servidor alvo para cada servidor de origem.
1. Configure a firewall com as exceções certas utilizando [o PowerShell](scripts/create-and-configure-database-powershell.md).  
1. Configure os servidores com os logins corretos. Se não for o administrador de subscrição ou administrador do servidor SQL, trabalhe com o administrador para atribuir as permissões de que necessita. Para obter mais informações, consulte [Como gerir a segurança da Base de Dados Azure SQL após a recuperação de desastres](active-geo-replication-security-configure.md).
1. Se as suas bases de dados forem encriptadas com encriptação de dados transparentes e utilizar a sua própria chave de encriptação no Cofre de Chaves Azure, certifique-se de que o material de encriptação correto está a ser abastado nas regiões-alvo. Para obter mais informações, consulte [a encriptação de dados transparentes Azure SQL com chaves geridas pelo cliente no Cofre da Chave Azure](transparent-data-encryption-byok-overview.md).
1. Se a auditoria ao nível da base de dados estiver ativada, desative-a e ative a auditoria ao nível do servidor. Após o failover, a auditoria ao nível da base de dados exigirá o tráfego inter-região, o que não é desejado ou possível após a mudança.
1. Para auditorias ao nível do servidor, certifique-se de que:
   - O recipiente de armazenamento, Log Analytics ou centro de eventos com os registos de auditoria existentes é transferido para a região alvo.
   - A auditoria está configurada no servidor alvo. Para obter mais informações, consulte [Começar com a auditoria da SQL Database](../../azure-sql/database/auditing-overview.md).
1. Se o seu caso tiver uma política de retenção a longo prazo (LTR), as cópias de segurança LTR existentes continuarão associadas ao servidor atual. Como o servidor alvo é diferente, poderá aceder às cópias de segurança LTR mais antigas da região de origem utilizando o servidor de origem, mesmo que o servidor seja eliminado.

      > [!NOTE]
      > Isto será insuficiente para se deslocar entre a nuvem soberana e uma região pública. Tal migração exigirá a deslocação das cópias de segurança LTR para o servidor-alvo, que não é suportada atualmente.

### <a name="prepare-resources"></a>Preparar recursos

1. Crie um [grupo de failover](failover-group-add-single-database-tutorial.md#2---create-the-failover-group) entre o servidor da fonte e o servidor do alvo.  
1. Adicione as bases de dados que pretende mover para o grupo de failover.
  
    A replicação de todas as bases de dados adicionadas será iniciada automaticamente. Para obter mais informações, consulte [as melhores práticas para utilizar grupos de failover com bases de dados únicas.](auto-failover-group-overview.md#best-practices-for-sql-database)

### <a name="monitor-the-preparation-process"></a>Monitorizar o processo de preparação

Pode ligar periodicamente para [o Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) para monitorizar a replicação das suas bases de dados desde a fonte até ao alvo. O objeto de saída `Get-AzSqlDatabaseFailoverGroup` inclui uma propriedade para o Estado de **Replicação:**

- **ReplicationState = 2** (CATCH_UP) indica que a base de dados está sincronizada e pode ser falhada com segurança.
- **ReplicationState = 0** (SEEDING) indica que a base de dados ainda não está semeada, e uma tentativa de falhar falhará.

### <a name="test-synchronization"></a>Sincronização de testes

Após **replicação O Estado** é , `2` ligue-se a cada base de dados ou subconjunto de bases de dados utilizando o ponto final secundário `<fog-name>.secondary.database.windows.net` e efetue qualquer consulta com as bases de dados para garantir a conectividade, a configuração adequada da segurança e a replicação de dados.

### <a name="initiate-the-move"></a>Inicie o movimento

1. Ligue-se ao servidor alvo utilizando o ponto final `<fog-name>.secondary.database.windows.net` secundário.
1. Utilize [o Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) para mudar a instância gerida secundária para ser a principal com sincronização completa. Esta operação terá sucesso ou irá recuar.
1. Verifique se o comando foi concluído com sucesso utilizando `nslook up <fog-name>.secondary.database.windows.net` para verificar se a entrada do DNS CNAME aponta para o endereço IP da região alvo. Se o comando do interruptor falhar, o CNAME não será atualizado.

### <a name="remove-the-source-databases"></a>Remover as bases de dados de origem

Uma vez concluída a mudança, remova os recursos na região de origem para evitar encargos desnecessários.

1. Eliminar o grupo de failover utilizando [remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup).
1. Elimine cada base de dados de origem utilizando [a Base de Dados Remove-AzSql](/powershell/module/az.sql/remove-azsqldatabase) para cada uma das bases de dados no servidor de origem. Isto terminará automaticamente as ligações de geo-replicação.
1. Elimine o servidor de origem utilizando [remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver).
1. Remova o cofre-chave, os recipientes de armazenamento de auditoria, o centro de eventos, o exemplo do Azure Ative Directory (Azure AD) e outros recursos dependentes para deixar de ser cobrado por eles.

## <a name="move-elastic-pools"></a>Mover piscinas elásticas

### <a name="verify-prerequisites"></a>Verificar os pré-requisitos

1. Crie um servidor alvo para cada servidor de origem.
1. Configure a firewall com as exceções certas utilizando [o PowerShell](scripts/create-and-configure-database-powershell.md).
1. Configure os servidores com os logins corretos. Se não for o administrador de subscrição ou administrador do servidor, trabalhe com o administrador para atribuir as permissões de que necessita. Para obter mais informações, consulte [Como gerir a segurança da Base de Dados Azure SQL após a recuperação de desastres](active-geo-replication-security-configure.md).
1. Se as suas bases de dados estiverem encriptadas com encriptação de dados transparentes e utilizar a sua própria chave de encriptação no Cofre de Chaves Azure, certifique-se de que o material de encriptação correto está a ser abastado na região alvo.
1. Crie uma piscina elástica alvo para cada piscina elástica de origem, certificando-se de que a piscina é criada no mesmo nível de serviço, com o mesmo nome e o mesmo tamanho.
1. Se uma auditoria ao nível da base de dados estiver ativada, desative-a e ative a auditoria ao nível do servidor. Após o fracasso, a auditoria ao nível da base de dados exigirá tráfego inter-região, o que não é desejado, ou possível após a mudança.
1. Para auditorias ao nível do servidor, certifique-se de que:
    - O recipiente de armazenamento, Log Analytics ou centro de eventos com os registos de auditoria existentes é transferido para a região alvo.
    - A configuração da auditoria está configurada no servidor alvo. Para mais informações, consulte [a auditoria da SQL Database.](../../azure-sql/database/auditing-overview.md)
1. Se o seu caso tiver uma política de retenção a longo prazo (LTR), as cópias de segurança LTR existentes continuarão associadas ao servidor atual. Como o servidor alvo é diferente, poderá aceder às cópias de segurança LTR mais antigas da região de origem utilizando o servidor de origem, mesmo que o servidor seja eliminado.

      > [!NOTE]
      > Isto será insuficiente para se deslocar entre a nuvem soberana e uma região pública. Tal migração exigirá a deslocação das cópias de segurança LTR para o servidor-alvo, que não é suportada atualmente.

### <a name="prepare-to-move"></a>Preparar para mover

1. Crie um [grupo separado de failover](failover-group-add-elastic-pool-tutorial.md#3---create-the-failover-group) entre cada piscina elástica no servidor de origem e a sua piscina elástica de contrapartida no servidor alvo.
1. Adicione todas as bases de dados da piscina ao grupo de failover.

    A replicação das bases de dados adicionadas será iniciada automaticamente. Para obter mais informações, consulte [as melhores práticas para grupos de failover com piscinas elásticas.](auto-failover-group-overview.md#best-practices-for-sql-database)

      > [!NOTE]
      > Embora seja possível criar um grupo de failover que inclua múltiplas piscinas elásticas, recomendamos vivamente que crie um grupo separado de failover para cada piscina. Se tiver um grande número de bases de dados em várias piscinas elásticas que precisa de mover, pode executar os passos de preparação em paralelo e, em seguida, iniciar o passo de movimento em paralelo. Este processo irá escalar melhor e levará menos tempo em comparação com ter várias piscinas elásticas no mesmo grupo de failover.

### <a name="monitor-the-preparation-process"></a>Monitorizar o processo de preparação

Pode ligar periodicamente para [o Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) para monitorizar a replicação das suas bases de dados desde a fonte até ao alvo. O objeto de saída `Get-AzSqlDatabaseFailoverGroup` inclui uma propriedade para o Estado de **Replicação:**

- **ReplicationState = 2** (CATCH_UP) indica que a base de dados está sincronizada e pode ser falhada com segurança.
- **ReplicationState = 0** (SEEDING) indica que a base de dados ainda não está semeada, e uma tentativa de falhar falhará.

### <a name="test-synchronization"></a>Sincronização de testes

Uma vez **replicationState** é `2` , conecte-se a cada base de dados ou subconjunto de bases de dados usando o ponto final secundário `<fog-name>.secondary.database.windows.net` e efetue qualquer consulta com as bases de dados para garantir conectividade, configuração de segurança adequada e replicação de dados.

### <a name="initiate-the-move"></a>Inicie o movimento

1. Ligue-se ao servidor alvo utilizando o ponto final `<fog-name>.secondary.database.windows.net` secundário.
1. Utilize [o Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) para mudar a instância gerida secundária para ser a principal com sincronização completa. Esta operação ou vai ter sucesso, ou vai recuar.
1. Verifique se o comando foi concluído com sucesso utilizando `nslook up <fog-name>.secondary.database.windows.net` para verificar se a entrada do DNS CNAME aponta para o endereço IP da região alvo. Se o comando do interruptor falhar, o CNAME não será atualizado.

### <a name="remove-the-source-elastic-pools"></a>Retire as piscinas elásticas de origem

Uma vez concluída a mudança, remova os recursos na região de origem para evitar encargos desnecessários.

1. Eliminar o grupo de failover utilizando [remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup).
1. Elimine cada piscina elástica de origem no servidor de origem utilizando [remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool).
1. Elimine o servidor de origem utilizando [remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver).
1. Remova o cofre-chave, os recipientes de armazenamento de auditoria, o centro de eventos, a instância AD do Azure e outros recursos dependentes para deixar de ser cobrado por eles.

## <a name="move-a-managed-instance"></a>Mover um caso gerido

### <a name="verify-prerequisites"></a>Verificar os pré-requisitos

1. Para cada exemplo gerido por fonte, crie uma instância-alvo de SQL Managed Instance do mesmo tamanho na região alvo.  
1. Configurar a rede para um caso gerido. Para obter mais informações, consulte [a configuração da rede](../managed-instance/how-to-content-reference-guide.md#network-configuration).
1. Configure a base de dados principal do alvo com os logins corretos. Se não for a subscrição ou administrador da SQL Managed Instance, trabalhe com o administrador para atribuir as permissões de que necessita.
1. Se as suas bases de dados estiverem encriptadas com encriptação de dados transparentes e utilizar a sua própria chave de encriptação no Cofre de Chaves Azure, certifique-se de que o Cofre de Chaves Azure com chaves de encriptação idênticas existe tanto nas regiões de origem como nas regiões-alvo. Para obter mais informações, consulte [encriptação de dados transparentes com chaves geridas pelo cliente no Cofre de Chaves Azure](transparent-data-encryption-byok-overview.md).
1. Se a auditoria estiver ativada para a instância gerida, certifique-se de que:
    - O contentor de armazenamento ou centro de eventos com os troncos existentes é movido para a região alvo.
    - A auditoria está configurada na instância-alvo. Para obter mais informações, consulte [Auditing com SQL Managed Instance](../managed-instance/auditing-configure.md).
1. Se o seu caso tiver uma política de retenção a longo prazo (LTR), as cópias de segurança LTR existentes continuarão associadas à instância atual. Como a instância-alvo é diferente, poderá aceder às cópias de segurança LTR mais antigas da região de origem usando a instância de origem, mesmo que o caso seja apagado.

  > [!NOTE]
  > Isto será insuficiente para se deslocar entre a nuvem soberana e uma região pública. Essa migração exigirá a transferência dos backups do LTR para a instância-alvo, que não é atualmente suportada.

### <a name="prepare-resources"></a>Preparar recursos

Criar um grupo de failover entre cada instância gerida por fonte e a instância-alvo correspondente de SQL Managed Instance.

A replicação de todas as bases de dados em cada instância será iniciada automaticamente. Para obter mais informações, consulte [os grupos auto-failover](auto-failover-group-overview.md).

### <a name="monitor-the-preparation-process"></a>Monitorizar o processo de preparação

Pode ligar periodicamente para [o Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) para monitorizar a replicação das suas bases de dados desde a fonte até ao alvo. O objeto de saída `Get-AzSqlDatabaseFailoverGroup` inclui uma propriedade para o Estado de **Replicação:**

- **ReplicationState = 2** (CATCH_UP) indica que a base de dados está sincronizada e pode ser falhada com segurança.
- **ReplicationState = 0** (SEEDING) indica que a base de dados ainda não está semeada, e uma tentativa de falhar falhará.

### <a name="test-synchronization"></a>Sincronização de testes

Uma vez **replicationState** é `2` , conecte-se a cada base de dados, ou subconjunto de bases de dados usando o ponto final secundário `<fog-name>.secondary.database.windows.net` e efetue qualquer consulta com as bases de dados para garantir conectividade, configuração de segurança adequada e replicação de dados.

### <a name="initiate-the-move"></a>Inicie o movimento

1. Ligue-se à instância gerida pelo alvo utilizando o ponto final `<fog-name>.secondary.database.windows.net` secundário.
1. Utilize [o Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) para mudar a instância gerida secundária para ser a principal com sincronização completa. Esta operação terá sucesso, ou vai voltar para trás.
1. Verifique se o comando foi concluído com sucesso utilizando `nslook up <fog-name>.secondary.database.windows.net` para verificar se a entrada do DNS CNAME aponta para o endereço IP da região alvo. Se o comando do interruptor falhar, o CNAME não será atualizado.

### <a name="remove-the-source-managed-instances"></a>Remova as instâncias geridas pela fonte

Uma vez terminada a mudança, retire os recursos da região de origem para evitar cargas desnecessárias.

1. Eliminar o grupo de failover utilizando [remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). Isto irá deixar cair a configuração do grupo de failover e terminar as ligações de geo-replicação entre as duas instâncias.
1. Eliminar a instância gerida pela fonte utilizando [remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance).
1. Remova quaisquer recursos adicionais no grupo de recursos, tais como o cluster virtual, a rede virtual e o grupo de segurança.

## <a name="next-steps"></a>Passos seguintes

[Gerencie](manage-data-after-migrating-to-database.md) a sua base de dados depois de ter sido migrada.
