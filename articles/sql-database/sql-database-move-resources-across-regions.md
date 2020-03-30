---
title: Como mover recursos para outra região
description: Saiba como mover a sua Base de Dados Azure SQL, piscina elástica Azure SQL ou Azure SQL gerido por exemplo para outra região.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 851ef49a5c066f12a95baa54daf5e267cb4278c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821439"
---
# <a name="how-to-move-azure-sql-resources-to-another-region"></a>Como mover os recursos Da Azure SQL para outra região

Este artigo ensina-lhe um fluxo de trabalho genérico para como mover a sua base de dados única Azure SQL, piscina elástica e instância gerida para uma nova região. 

## <a name="overview"></a>Descrição geral

Existem vários cenários em que você gostaria de mover os seus recursos Azure SQL existentes de uma região para outra. Por exemplo, expande o seu negócio para uma nova região e quer otimizá-lo para a nova base de clientes. Ou precisa de mudar as operações para outra região por razões de conformidade. Ou a Azure lançou uma nova região que proporciona uma melhor proximidade e melhora a experiência do cliente.  

Este artigo fornece um fluxo de trabalho geral para a deslocação de recursos para uma região diferente. O fluxo de trabalho consiste nos seguintes passos: 

- Verifique os pré-requisitos para a mudança 
- Prepare-se para mover os recursos no âmbito
- Monitorizar o processo de preparação
- Teste o processo de movimento
- Inicie o movimento real 
- Remover os recursos da região de origem 


> [!NOTE]
> Este artigo aplica-se às migrações dentro da nuvem pública de Azure, ou dentro da mesma nuvem soberana. 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-single-database"></a>Mover base de dados única

### <a name="verify-prerequisites"></a>Verificar os pré-requisitos 

1. Crie um servidor lógico alvo para cada servidor de origem. 
1. Configure a firewall com as exceções certas utilizando [o PowerShell](scripts/sql-database-create-and-configure-database-powershell.md).  
1. Configure os servidores lógicos com os logins corretos. Se não for o administrador de subscrição ou administrador de servidor SQL, trabalhe com o administrador para atribuir as permissões de que necessita. Para mais informações, consulte como gerir a segurança da base de [dados Azure SQL após a recuperação](sql-database-geo-replication-security-config.md)de desastres . 
1. Se as suas bases de dados estiverem encriptadas com TDE e utilizarem a sua própria chave de encriptação no cofre da chave Azure, certifique-se de que o material de encriptação correto está aprovisionado nas regiões-alvo. Para mais informações, consulte a [Encriptação de Dados Transparentes Azure SQL com chaves geridas pelo cliente no Cofre chave Azure](transparent-data-encryption-byok-azure-sql.md)
1. Se a auditoria ao nível da base de dados estiver ativada, desative-a e ative a auditoria ao nível do servidor. Após a falha, a auditoria ao nível da base de dados exigirá o tráfego transversal, que não é desejado nem possível após a mudança. 
1. Para auditorias ao nível do servidor, certifique-se de que:
   - O recipiente de armazenamento, Log Analytics, ou centro de eventos com os registos de auditoria existentes é transferido para a região alvo. 
   - A auditoria está configurada no servidor-alvo. Para mais informações, consulte Começar com a auditoria da base de [dados SQL](sql-database-auditing.md). 
1. Se a sua instância tiver uma política de retenção a longo prazo (LTR), as cópias de segurança LTR existentes permanecerão associadas ao servidor atual. Como o servidor alvo é diferente, poderá aceder às cópias de segurança LTR mais antigas da região de origem utilizando o servidor de origem, mesmo que o servidor seja eliminado. 

  > [!NOTE]
  > Isto será insuficiente para se deslocar entre a nuvem soberana e uma região pública. Tal migração exigirá a deslocação das cópias de segurança LTR para o servidor-alvo, que não é suportado atualmente. 

### <a name="prepare-resources"></a>Preparar recursos

1. Crie um grupo de [failover](sql-database-single-database-failover-group-tutorial.md#2---create-the-failover-group) entre o servidor lógico da fonte para o servidor lógico do alvo.  
1. Adicione as bases de dados que pretende mover para o grupo failover. 
    - A replicação de todas as bases de dados adicionadas será iniciada automaticamente. Para mais informações, consulte [as melhores práticas para utilizar grupos failover com bases de dados únicas](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). 
 
### <a name="monitor-the-preparation-process"></a>Monitorizar o processo de preparação

Pode ligar periodicamente para [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) para monitorizar a replicação das suas bases de dados da fonte para o alvo. O objeto `Get-AzSqlDatabaseFailoverGroup` de saída inclui uma propriedade para o Estado de **Replicação:** 
   - **ReplicaçãoEstado = 2** (CATCH_UP) indica que a base de dados está sincronizada e pode ser reprovada com segurança. 
   - **ReplicaçãoEstado = 0** (SEEDING) indica que a base de dados ainda não está semeada, e uma tentativa de falha falhar falhará. 

### <a name="test-synchronization"></a>Sincronização de teste

Uma vez que a **ReplicationState** seja `2`, ligue-se a cada `<fog-name>.secondary.database.windows.net` base de dados ou subconjunto de bases de dados utilizando o ponto final secundário e execute qualquer consulta contra as bases de dados para garantir conectividade, configuração de segurança adequada e replicação de dados. 

### <a name="initiate-the-move"></a>Inicie o movimento

1. Ligue-se ao servidor-alvo utilizando `<fog-name>.secondary.database.windows.net`o ponto final secundário .
1. Utilize o [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) para mudar a instância gerida secundária para ser a principal com sincronização completa. Esta operação terá sucesso, ou vai recuar. 
1. Verifique se o comando foi concluído `nslook up <fog-name>.secondary.database.windows.net` com sucesso utilizando para verificar se a entrada do DNS CNAME aponta para o endereço IP da região alvo. Se o comando do interruptor falhar, o CNAME não será atualizado. 

### <a name="remove-the-source-databases"></a>Remova as bases de dados de origem

Uma vez concluída a mudança, retire os recursos na região de origem para evitar encargos desnecessários. 

1. Elimine o grupo failover utilizando [o Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). 
1. Elimine cada base de dados de origem utilizando [a Base de Dados Remove-AzSql](/powershell/module/az.sql/remove-azsqldatabase) para cada uma das bases de dados do servidor de origem. Isto terminará automaticamente as ligações de geo-replicação. 
1. Elimine o servidor de origem utilizando [o Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver). 
1. Remova o cofre chave, recipientes de armazenamento de auditoria, centro de eventos, instância aAD e outros recursos dependentes para deixar de ser cobrado para eles. 

## <a name="move-elastic-pools"></a>Mova piscinas elásticas

### <a name="verify-prerequisites"></a>Verificar os pré-requisitos 

1. Crie um servidor lógico alvo para cada servidor de origem. 
1. Configure a firewall com as exceções certas utilizando [o PowerShell](scripts/sql-database-create-and-configure-database-powershell.md). 
1. Configure os servidores lógicos com os logins corretos. Se não for o administrador de subscrição ou administrador de servidor SQL, trabalhe com o administrador para atribuir as permissões de que necessita. Para mais informações, consulte como gerir a segurança da base de [dados Azure SQL após a recuperação](sql-database-geo-replication-security-config.md)de desastres . 
1. Se as suas bases de dados estiverem encriptadas com TDE e utilizarem a sua própria chave de encriptação no cofre da chave Azure, certifique-se de que o material de encriptação correto está aprovisionado na região alvo.
1. Crie uma piscina elástica-alvo para cada piscina elástica de origem, certificando-se de que a piscina é criada no mesmo nível de serviço, com o mesmo nome e o mesmo tamanho. 
1. Se uma auditoria ao nível da base de dados estiver ativada, desative-a e ative a auditoria ao nível do servidor. Após a falha, a auditoria ao nível da base de dados exigirá tráfego transversal, que não é desejado, ou possível após a mudança. 
1. Para auditorias ao nível do servidor, certifique-se de que:
    - O recipiente de armazenamento, Log Analytics, ou centro de eventos com os registos de auditoria existentes é transferido para a região alvo.
    - A configuração da auditoria está configurada no servidor-alvo. Para mais informações, consulte a auditoria da base de [dados SQL](sql-database-auditing.md).
1. Se a sua instância tiver uma política de retenção a longo prazo (LTR), as cópias de segurança LTR existentes permanecerão associadas ao servidor atual. Como o servidor alvo é diferente, poderá aceder às cópias de segurança LTR mais antigas da região de origem utilizando o servidor de origem, mesmo que o servidor seja eliminado. 

  > [!NOTE]
  > Isto será insuficiente para se deslocar entre a nuvem soberana e uma região pública. Tal migração exigirá a deslocação das cópias de segurança LTR para o servidor-alvo, que não é suportado atualmente. 

### <a name="prepare-to-move"></a>Prepare-se para se mover
 
1.  Crie um grupo de [failover](sql-database-elastic-pool-failover-group-tutorial.md#3---create-the-failover-group) separado entre cada piscina elástica no servidor lógico de origem e o seu conjunto elástico homólogo no servidor alvo. 
1.  Adicione todas as bases de dados da piscina ao grupo failover. 
    - A replicação das bases de dados adicionadas será iniciada automaticamente. Para mais informações, consulte [as melhores práticas para grupos de failover com piscinas elásticas](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). 

  > [!NOTE]
  > Embora seja possível criar um grupo de failover que inclua várias piscinas elásticas, recomendamos vivamente que crie um grupo de failover separado para cada piscina. Se tiver um grande número de bases de dados em várias piscinas elásticas que precisa de mover, pode executar os passos de preparação em paralelo e, em seguida, iniciar o passo de movimento em paralelo. Este processo irá escalar melhor e levará menos tempo em comparação com ter várias piscinas elásticas no mesmo grupo de failover. 

### <a name="monitor-the-preparation-process"></a>Monitorizar o processo de preparação

Pode ligar periodicamente para [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) para monitorizar a replicação das suas bases de dados da fonte para o alvo. O objeto `Get-AzSqlDatabaseFailoverGroup` de saída inclui uma propriedade para o Estado de **Replicação:** 
   - **ReplicaçãoEstado = 2** (CATCH_UP) indica que a base de dados está sincronizada e pode ser reprovada com segurança. 
   - **ReplicaçãoEstado = 0** (SEEDING) indica que a base de dados ainda não está semeada, e uma tentativa de falha falhar falhará. 

### <a name="test-synchronization"></a>Sincronização de teste
 
Uma vez que a **ReplicationState** seja `2`, ligue-se a cada `<fog-name>.secondary.database.windows.net` base de dados ou subconjunto de bases de dados utilizando o ponto final secundário e execute qualquer consulta contra as bases de dados para garantir conectividade, configuração de segurança adequada e replicação de dados. 

### <a name="initiate-the-move"></a>Inicie o movimento
 
1. Ligue-se ao servidor-alvo utilizando `<fog-name>.secondary.database.windows.net`o ponto final secundário .
1. Utilize o [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) para mudar a instância gerida secundária para ser a principal com sincronização completa. Esta operação terá sucesso, ou vai recuar. 
1. Verifique se o comando foi concluído `nslook up <fog-name>.secondary.database.windows.net` com sucesso utilizando para verificar se a entrada do DNS CNAME aponta para o endereço IP da região alvo. Se o comando do interruptor falhar, o CNAME não será atualizado. 

### <a name="remove-the-source-elastic-pools"></a>Remova as piscinas elásticas de origem
 
Uma vez concluída a mudança, retire os recursos na região de origem para evitar encargos desnecessários. 

1. Elimine o grupo failover utilizando [o Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup).
1. Elimine cada piscina elástica de origem no servidor de origem utilizando [remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool). 
1. Elimine o servidor de origem utilizando [o Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver). 
1. Remova o cofre chave, recipientes de armazenamento de auditoria, centro de eventos, instância aAD e outros recursos dependentes para deixar de ser cobrado para eles. 

## <a name="move-managed-instance"></a>Mover instância gerida

### <a name="verify-prerequisites"></a>Verificar os pré-requisitos
 
1. Para cada fonte gerida por exemplo, criar um alvo gerido da mesma dimensão na região alvo.  
1. Configure a rede para uma instância gerida. Para mais informações, consulte a [configuração da rede](sql-database-howto-managed-instance.md#network-configuration).
1. Configure a base de dados principal do alvo com os logins corretos. Se não for o administrador de subscrição ou administrador de servidor SQL, trabalhe com o administrador para atribuir as permissões de que necessita. 
1. Se as suas bases de dados estiverem encriptadas com TDE e utilizarem a sua própria chave de encriptação no cofre da chave Azure, certifique-se de que o AKV com chaves de encriptação idênticas existe tanto nas regiões de origem como nas regiões-alvo. Para mais informações, consulte [o TDE com as chaves geridas pelo cliente no Cofre de Chaves Azure](transparent-data-encryption-byok-azure-sql.md).
1. Se a auditoria estiver ativada, por exemplo, certifique-se de que:
    - O contentor de armazenamento ou centro de eventos com os troncos existentes é transferido para a região alvo. 
    - A auditoria está configurada na instância-alvo. Para mais informações, consulte [a auditoria com instância gerida](sql-database-managed-instance-auditing.md).
1. Se a sua instância tiver uma política de retenção a longo prazo (LTR), as cópias de segurança LTR existentes permanecerão associadas ao servidor atual. Como o servidor alvo é diferente, poderá aceder às cópias de segurança LTR mais antigas da região de origem utilizando o servidor de origem, mesmo que o servidor seja eliminado. 

  > [!NOTE]
  > Isto será insuficiente para se deslocar entre a nuvem soberana e uma região pública. Tal migração exigirá a deslocação das cópias de segurança LTR para o servidor-alvo, que não é suportado atualmente. 

### <a name="prepare-resources"></a>Preparar recursos

Crie um grupo de falhas entre cada instância de origem e a instância-alvo correspondente.
    - A replicação de todas as bases de dados de cada instância será iniciada automaticamente. Consulte os [grupos de auto-failover](sql-database-auto-failover-group.md) para obter mais informações.

 
### <a name="monitor-the-preparation-process"></a>Monitorizar o processo de preparação

Pode ligar periodicamente para [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup?view=azps-2.3.2) para monitorizar a replicação das suas bases de dados da fonte para o alvo. O objeto `Get-AzSqlDatabaseFailoverGroup` de saída inclui uma propriedade para o Estado de **Replicação:** 
   - **ReplicaçãoEstado = 2** (CATCH_UP) indica que a base de dados está sincronizada e pode ser reprovada com segurança. 
   - **ReplicaçãoEstado = 0** (SEEDING) indica que a base de dados ainda não está semeada, e uma tentativa de falha falhar falhará. 

### <a name="test-synchronization"></a>Sincronização de teste

Uma vez que a **ReplicationState** seja `2`, ligue-se a cada `<fog-name>.secondary.database.windows.net` base de dados ou subconjunto de bases de dados utilizando o ponto final secundário e execute qualquer consulta contra as bases de dados para garantir conectividade, configuração de segurança adequada e replicação de dados. 

### <a name="initiate-the-move"></a>Inicie o movimento 

1. Ligue-se ao servidor-alvo utilizando `<fog-name>.secondary.database.windows.net`o ponto final secundário .
1. Utilize o [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup?view=azps-2.3.2) para mudar a instância gerida secundária para ser a principal com sincronização completa. Esta operação terá sucesso, ou vai recuar. 
1. Verifique se o comando foi concluído `nslook up <fog-name>.secondary.database.windows.net` com sucesso utilizando para verificar se a entrada do DNS CNAME aponta para o endereço IP da região alvo. Se o comando do interruptor falhar, o CNAME não será atualizado. 

### <a name="remove-the-source-managed-instances"></a>Remova as instâncias geridas pela fonte
Uma vez concluída a mudança, retire os recursos na região de origem para evitar encargos desnecessários. 

1. Elimine o grupo failover utilizando [o Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). Isto deixará cair a configuração do grupo failover e terminará as ligações de geo-replicação entre as duas instâncias. 
1. Elimine a instância gerida pela fonte utilizando [remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance). 
1. Remova quaisquer recursos adicionais no grupo de recursos, tais como o cluster virtual, rede virtual e grupo de segurança. 

## <a name="next-steps"></a>Passos seguintes 

[Gerencie](sql-database-manage-after-migration.md) a sua Base de Dados Azure SQL uma vez migrada. 

