---
title: Fazer cópias de segurança de bases de dados do SQL Server em VMs do Azure
description: Neste artigo, saiba como fazer backup de bancos de dados SQL Server em máquinas virtuais do Azure com o backup do Azure.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: fc0c3127594fe3ca90b0a66ce548f471c55f4e5f
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156476"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Fazer cópias de segurança de bases de dados do SQL Server em VMs do Azure

Os bancos de dados do SQL Server são cargas de trabalho críticas que exigem um RPO (objetivo de ponto de recuperação) baixo e retenção de longo prazo. Você pode fazer backup de bancos de dados SQL Server em execução em VMs (máquinas virtuais) do Azure usando o [backup do Azure](backup-overview.md).

Este artigo mostra como fazer backup de um banco de dados SQL Server que está sendo executado em uma VM do Azure para um cofre dos serviços de recuperação de backup do Azure.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
>
> * Criar e configurar um cofre.
> * Descubra bancos de dados e configure backups.
> * Configure a proteção automática para bancos de dados.

## <a name="prerequisites"></a>Pré-requisitos

Antes de fazer backup de um banco de dados SQL Server, verifique os seguintes critérios:

1. Identifique ou crie um [cofre dos serviços de recuperação](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) na mesma região e assinatura que a VM que hospeda a instância de SQL Server.
2. Verifique se a VM tem [conectividade de rede](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
3. Certifique-se de que os bancos de dados do SQL Server seguem as [diretrizes de nomenclatura para o backup do Azure](#database-naming-guidelines-for-azure-backup).
4. Verifique se você não tem outras soluções de backup habilitadas para o banco de dados. Desabilite todos os outros backups de SQL Server antes de fazer backup do banco de dados.

> [!NOTE]
> Você pode habilitar o backup do Azure para uma VM do Azure e também para um banco de dados SQL Server em execução na VM sem conflito.

### <a name="establish-network-connectivity"></a>Estabelecer conectividade de rede

Para todas as operações, uma VM SQL Server requer conectividade com os endereços IP públicos do Azure. Operações de VM (descoberta de banco de dados, configurar backups, agendar backups, restaurar pontos de recuperação e assim por diante) falham sem conectividade com os endereços IP públicos do Azure.

Estabeleça a conectividade usando uma das seguintes opções:

#### <a name="allow-the-azure-datacenter-ip-ranges"></a>Permitir os intervalos de IP do datacenter do Azure

Essa opção permite os [intervalos de IP](https://www.microsoft.com/download/details.aspx?id=41653) no arquivo baixado. Para acessar um NSG (grupo de segurança de rede), use o cmdlet Set-AzureNetworkSecurityRule. Se sua lista de destinatários confiáveis incluir apenas IPs específicos de região, você também precisará atualizar a lista de destinatários seguros a marca de serviço Azure Active Directory (AD do Azure) para habilitar a autenticação.

#### <a name="allow-access-using-nsg-tags"></a>Permitir acesso usando marcas NSG

Se você usar NSG para restringir a conectividade, deverá usar a marca de serviço AzureBackup para permitir o acesso de saída ao backup do Azure. Além disso, você também deve permitir a conectividade para autenticação e transferência de dados usando [regras](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) para o Azure AD e o armazenamento do Azure. Isso pode ser feito no portal do Azure ou por meio do PowerShell.

Para criar uma regra usando o portal:

  1. Em **todos os serviços**, vá para **grupos de segurança de rede** e selecione o grupo de segurança de rede.
  2. Selecione **regras de segurança de saída** em **configurações**.
  3. Selecione **Adicionar**. Insira todos os detalhes necessários para criar uma nova regra, conforme descrito em [configurações de regra de segurança](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings). Verifique se a opção **destino** está definida como **marca de serviço** e se a **marca serviço de destino** está definida como **AzureBackup**.
  4. Clique em **Adicionar**para salvar a regra de segurança de saída recém-criada.

Para criar uma regra usando o PowerShell:

 1. Adicionar credenciais de conta do Azure e atualizar as nuvens nacionais<br/>
      `Add-AzureRmAccount`<br/>

 2. Selecione a assinatura do NSG<br/>
      `Select-AzureRmSubscription "<Subscription Id>"`

 3. Selecione o NSG<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

 4. Adicionar regra de permissão de saída para a marca do serviço de backup do Azure<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 5. Adicionar regra de permissão de saída para a marca de serviço de armazenamento<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "StorageAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "Storage" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 6. Adicionar regra de permissão de saída para a marca de serviço AzureActiveDirectory<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureActiveDirectoryAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureActiveDirectory" -DestinationPortRange 443 -Description "Allow outbound traffic to AzureActiveDirectory service"`

 7. Salve o NSG<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`

**Permitir acesso usando marcas de firewall do Azure**. Se você estiver usando o Firewall do Azure, crie uma regra de aplicativo usando a [marca de FQDN](https://docs.microsoft.com/azure/firewall/fqdn-tags)AzureBackup. Isso permite o acesso de saída ao backup do Azure.

**Implante um servidor proxy http para rotear o tráfego**. Quando você faz backup de um banco de dados SQL Server em uma VM do Azure, a extensão de backup na VM usa as APIs HTTPS para enviar comandos de gerenciamento para o backup do Azure e dados para o armazenamento do Azure. A extensão de backup também usa o Azure AD para autenticação. Encaminhe o tráfego de extensão de backup para esses três serviços por meio do proxy HTTP. As extensões são o único componente que está configurado para acesso à Internet pública.

As opções de conectividade incluem as seguintes vantagens e desvantagens:

**Opção** | **Vantagens** | **Desvantagens**
--- | --- | ---
Permitir intervalos de IP | Sem custos adicionais | Complexo para gerenciar porque os intervalos de endereços IP mudam ao longo do tempo <br/><br/> Fornece acesso ao todo o Azure, não apenas ao armazenamento do Azure
Usar marcas de serviço NSG | Mais fácil de gerenciar como as alterações de intervalo são mescladas automaticamente <br/><br/> Sem custos adicionais <br/><br/> | Pode ser usado somente com NSGs <br/><br/> Fornece acesso ao serviço inteiro
Usar marcas de FQDN do firewall do Azure | Mais fácil de gerenciar, pois os FQDNs necessários são gerenciados automaticamente | Pode ser usado somente com o Firewall do Azure
Usar um proxy HTTP | O controle granular no proxy sobre as URLs de armazenamento é permitido <br/><br/> Ponto único de acesso à Internet para VMs <br/><br/> Não está sujeito às alterações de endereço IP do Azure | Custos adicionais para executar uma VM com o software proxy

### <a name="database-naming-guidelines-for-azure-backup"></a>Diretrizes de nomenclatura de banco de dados para o backup do Azure

Evite usar os seguintes elementos em nomes de banco de dados:

* Espaços à direita e à esquerda
* Pontos de exclamação à direita (!)
* Colchetes de fechamento (])
* Ponto e vírgula '; '
* Barra '/'

A alias está disponível para caracteres sem suporte, mas é recomendável evitá-los. Para obter mais informações, consulte [Noções Básicas sobre o Modelo de Dados do Serviço Tabela](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Descobrir bancos de dados SQL Server

Como descobrir bancos de dados em execução em uma VM:

1. No [portal do Azure](https://portal.azure.com), abra o cofre dos serviços de recuperação que você usa para fazer backup do banco de dados.

2. No painel do **cofre dos serviços de recuperação** , selecione **backup**.

   ![Selecione backup para abrir o menu meta de backup](./media/backup-azure-sql-database/open-backup-menu.png)

3. Em **meta de backup**, defina **onde sua carga de trabalho está em execução? para o** **Azure**.

4. Em **o que você deseja fazer backup**, selecione **SQL Server na VM do Azure**.

    ![Selecione SQL Server na VM do Azure para o backup](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. Em **meta de Backup** > **descobrir bancos de os em VMs**, selecione **Iniciar descoberta** para procurar VMs desprotegidas na assinatura. Essa pesquisa pode demorar um pouco, dependendo do número de VMs não protegidas na assinatura.

   * As VMs desprotegidas devem aparecer na lista após a descoberta, listada por nome e grupo de recursos.
   * Se uma VM não estiver listada como esperado, veja se ela já foi submetida a backup em um cofre.
   * Várias VMs podem ter o mesmo nome, mas elas pertencerão a grupos de recursos diferentes.

     ![O backup está pendente durante a pesquisa de bancos de os em VMs](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Na lista VM, selecione a VM que está executando o banco de dados SQL Server > **descobrir bancos**.

7. Acompanhe a descoberta de banco de dados em **notificações**. O tempo necessário para essa ação depende do número de bancos de dados de VM. Quando os bancos de dados selecionados são descobertos, uma mensagem de êxito é exibida.

    ![Mensagem de êxito na implantação](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. O backup do Azure descobre todos os bancos de dados SQL Server na VM. Durante a descoberta, os seguintes elementos ocorrem em segundo plano:

    * O backup do Azure registra a VM com o cofre para backup de carga de trabalho. Todos os bancos de dados na VM registrada podem ser submetidos a backup somente nesse cofre.
    * O backup do Azure instala a extensão AzureBackupWindowsWorkload na VM. Nenhum agente está instalado em um banco de dados SQL.
    * O backup do Azure cria a conta de serviço NT Service\AzureWLBackupPluginSvc na VM.
      * Todas as operações de backup e restauração usam a conta de serviço.
      * NT Service\AzureWLBackupPluginSvc requer permissões de sysadmin do SQL. Todas as VMs SQL Server criadas no Marketplace vêm com o SqlIaaSExtension instalado. A extensão AzureBackupWindowsWorkload usa o SQLIaaSExtension para obter automaticamente as permissões necessárias.
    * Se você não criou a VM do Marketplace ou se estiver no SQL 2008 e 2008 R2, a VM poderá não ter o SqlIaaSExtension instalado e a operação de descoberta falhará com a mensagem de erro UserErrorSQLNoSysAdminMembership. Para corrigir esse problema, siga as instruções em [definir permissões de VM](backup-azure-sql-database.md#set-vm-permissions).

        ![Selecione a VM e o banco de dados](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Configurar a cópia de segurança  

1. Em **meta de Backup** > **etapa 2: configurar backup**, selecione **Configurar backup**.

   ![Selecione configurar backup](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. Em **selecionar itens para backup**, você verá todos os grupos de disponibilidade registrados e instâncias de SQL Server autônomas. Selecione a seta à esquerda de uma linha para expandir a lista de todos os bancos de dados desprotegidos nessa instância ou Always On grupo de disponibilidade.  

    ![Exibindo todas as instâncias de SQL Server com bancos de dados autônomos](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Escolha todos os bancos de dados que você deseja proteger e, em seguida, selecione **OK**.

   ![Protegendo o banco de dados](./media/backup-azure-sql-database/select-database-to-protect.png)

   Para otimizar as cargas de backup, o backup do Azure define um número máximo de bancos de dados em um trabalho de backup para 50.

     * Para proteger mais de 50 bancos de dados, configure vários backups.
     * Para [habilitar](#enable-auto-protection) a instância inteira ou o grupo de disponibilidade Always on, na lista suspensa **autoproteger** , selecione **ativado**e, em seguida, selecione **OK**.

    > [!NOTE]
    > O recurso de [proteção automática](#enable-auto-protection) não só habilita a proteção em todos os bancos de dados existentes de uma vez, mas também protege automaticamente quaisquer novos bancos de dados adicionados a essa instância ou ao grupo de disponibilidade.  

4. Selecione **OK** para abrir a **política de backup**.

    ![Habilitar a proteção automática para o grupo de disponibilidade Always On](./media/backup-azure-sql-database/enable-auto-protection.png)

5. Em **política de backup**, escolha uma política e, em seguida, selecione **OK**.

   * Selecione a política padrão como HourlyLogBackup.
   * Escolha uma política de backup existente criada anteriormente para SQL.
   * Defina uma nova política com base no RPO e no período de retenção.

     ![Selecionar política de backup](./media/backup-azure-sql-database/select-backup-policy.png)

6. Em **backup**, selecione **habilitar backup**.

    ![Habilitar a política de backup escolhida](./media/backup-azure-sql-database/enable-backup-button.png)

7. Acompanhe o progresso da configuração na área **notificações** do Portal.

    ![Área de notificação](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Criar uma política de backup

Uma política de backup define quando os backups são feitos e por quanto tempo eles são retidos.

* Uma política é criada no nível do cofre.
* Vários cofres podem usar a mesma política de backup, mas você deve aplicar a política de backup a cada cofre.
* Quando você cria uma política de backup, um backup completo diário é o padrão.
* Você pode adicionar um backup diferencial, mas somente se configurar backups completos para ocorrer semanalmente.
* Saiba mais sobre os [diferentes tipos de políticas de backup](backup-architecture.md#sql-server-backup-types).

Para criar uma política de backup:

1. No cofre, selecione **políticas de Backup** > **Adicionar**.
2. Em **Adicionar**, selecione **SQL Server na VM do Azure** para definir o tipo de política.

   ![Escolha um tipo de política para a nova política de backup](./media/backup-azure-sql-database/policy-type-details.png)

3. Em **nome da política**, insira um nome para a nova política.
4. Em **política de backup completo**, selecione uma **frequência de backup**. Escolha **diária** ou **semanal**.

   * Para **diário**, selecione a hora e o fuso horário em que o trabalho de backup começa.
   * Para **semana**, selecione o dia da semana, hora e fuso horário em que o trabalho de backup começa.
   * Execute um backup completo, porque você não pode desativar a opção de **backup completo** .
   * Selecione **backup completo** para exibir a política.
   * Não é possível criar backups diferenciais para backups diários completos.

     ![Novos campos de política de backup](./media/backup-azure-sql-database/full-backup-policy.png)  

5. Em **período de retenção**, todas as opções são selecionadas por padrão. Desmarque os limites de intervalo de retenção que você não deseja e defina os intervalos a serem usados.

    * O período de retenção mínimo para qualquer tipo de backup (completo, diferencial e log) é de sete dias.
    * Os pontos de recuperação são marcados para retenção com base em seu período de retenção. Por exemplo, se você selecionar um backup completo diário, apenas um backup completo será disparado todos os dias.
    * O backup de um dia específico é marcado e mantido com base no período de retenção semanal e na configuração de retenção semanal.
    * Os intervalos de retenção mensais e anuais se comportam de maneira semelhante.

       ![Configurações de intervalo do intervalo de retenção](./media/backup-azure-sql-database/retention-range-interval.png)

6. No menu **política de backup completo** , selecione **OK** para aceitar as configurações.
7. Para adicionar uma política de backup diferencial, selecione **backup diferencial**.

   ![configurações de intervalo de intervalo de retenção](./media/backup-azure-sql-database/retention-range-interval.png)
   ![abrir o menu de política de backup diferencial](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. Em **política de backup diferencial**, selecione **habilitar** para abrir os controles de frequência e retenção.

    * Você pode disparar apenas um backup diferencial por dia.
    * Backups diferenciais podem ser retidos por um máximo de 180 dias. Para maior retenção, use backups completos.

9. Selecione **OK** para salvar a política e retornar para o menu principal da **política de backup** .

10. Para adicionar uma política de backup de log transacional, selecione **backup de log**.
11. Em **backup de log**, selecione **habilitar**e defina a frequência e os controles de retenção. Os backups de log podem ocorrer com frequência a cada 15 minutos e podem ser retidos por até 35 dias.
12. Selecione **OK** para salvar a política e retornar para o menu principal da **política de backup** .

    ![Editar a política de backup de log](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. No menu **política de backup** , escolha se deseja habilitar a **compactação de backup do SQL** ou não. Esta opção está desativada por predefinição. Se habilitada, SQL Server enviará um fluxo de backup compactado para o VDI.  Observe que o backup do Azure substitui os padrões de nível de instância pela cláusula COMPRESSION/NO_COMPRESSION dependendo do valor desse controle.

14. Depois de concluir as edições na política de backup, selecione **OK**.

> [!NOTE]
> Cada backup de log é encadeado ao backup completo anterior para formar uma cadeia de recuperação. Esse backup completo será retido até que a retenção do último backup de log tenha expirado. Isso pode significar que o backup completo é mantido por um período extra para garantir que todos os logs possam ser recuperados. Vamos supor que o usuário tenha um backup completo semanal, os logs diferenciais diários e de 2 horas. Todos eles são mantidos por 30 dias. No entanto, a semana completa pode ser realmente limpa/excluída somente depois que o próximo backup completo estiver disponível, ou seja, após 30 a 7 dias. Digamos que um backup completo semanal ocorra em 16 de novembro. De acordo com a política de retenção, ela deve ser retida até 16 de dezembro. O último backup de log para esse total ocorre antes do próximo agendamento completo, em novembro de 22. Até que esse log esteja disponível até dec 22, o 16º total de novembro não poderá ser excluído. Portanto, o 16º 16 de novembro é mantido até dec 22.

## <a name="enable-auto-protection"></a>Habilitar proteção automática  

Você pode habilitar a proteção automática para fazer backup automático de todos os bancos de dados existentes e futuros para uma instância SQL Server autônoma ou para um grupo de disponibilidade Always On.

* Não há limite para o número de bancos de dados que você pode selecionar para proteção automática ao mesmo tempo.
* Você não pode proteger ou excluir seletivamente bancos de dados da proteção em uma instância no momento em que habilita a proteção automática.
* Se sua instância já inclui alguns bancos de dados protegidos, eles permanecerão protegidos em suas respectivas políticas mesmo depois de ativar a proteção automática. Todos os bancos de dados desprotegidos adicionados posteriormente terão apenas uma única política que você definir no momento da habilitação da proteção automática, listada em **Configurar backup**. No entanto, você pode alterar a política associada a um banco de dados protegido automaticamente mais tarde.  

Para habilitar a proteção automática:

  1. Em **itens para backup**, selecione a instância para a qual você deseja habilitar a proteção automática.
  2. Selecione a lista suspensa em **AutoProtect**, escolha **ativado**e, em seguida, selecione **OK**.

      ![Habilitar a proteção automática no grupo de disponibilidade](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. O backup é configurado para todos os bancos de dados juntos e pode ser acompanhado em **trabalhos de backup**.

Se você precisar desabilitar a proteção automática, selecione o nome da instância em **Configurar backup**e, em seguida, selecione **desabilitar autoproteção** para a instância. Todos os bancos de dados continuarão a ser submetidos a backup, mas os bancos de dados futuros não serão automaticamente protegidos.

![Desabilitar a proteção automática nessa instância](./media/backup-azure-sql-database/disable-auto-protection.png)

## <a name="next-steps"></a>Passos seguintes

Saiba como:

* [Restaurar bancos de dados SQL Server de backup](restore-sql-database-azure-vm.md)
* [Gerenciar bancos de dados SQL Server com backup](manage-monitor-sql-database-backup.md)
