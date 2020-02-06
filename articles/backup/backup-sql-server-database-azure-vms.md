---
title: Fazer cópias de segurança de bases de dados do SQL Server em VMs do Azure
description: Neste artigo, saiba como fazer backup de bancos de dados SQL Server em máquinas virtuais do Azure com o backup do Azure.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 10f55bb4c5c488975f075aa0382296f808a9a5b1
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77029576"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Fazer cópias de segurança de bases de dados do SQL Server em VMs do Azure

Os bancos de dados do SQL Server são cargas de trabalho críticas que exigem um RPO (objetivo de ponto de recuperação) baixo e retenção de longo prazo. Pode fazer backup nas bases de dados do SQL Server em máquinas virtuais Azure (VMs) utilizando [o Azure Backup](backup-overview.md).

Este artigo mostra como fazer backup de um banco de dados SQL Server que está sendo executado em uma VM do Azure para um cofre dos serviços de recuperação de backup do Azure.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
>
> * Criar e configurar um cofre.
> * Descubra bancos de dados e configure backups.
> * Configure a proteção automática para bancos de dados.

>[!NOTE]
>**A eliminação suave para o servidor SQL em Azure VM e eliminação suave para SAP HANA em cargas de trabalho VM Azure** já está disponível na pré-visualização.<br>
>Para se inscrever na pré-estreia, escreva-nos na AskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Pré-requisitos

Antes de fazer backup de um banco de dados SQL Server, verifique os seguintes critérios:

1. Identifique ou crie um cofre de Serviços de [Recuperação](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) na mesma região e subscrição que o VM que acolhe a instância Do Servidor SQL.
2. Verifique se o VM tem conectividade de [rede.](backup-sql-server-database-azure-vms.md#establish-network-connectivity)
3. Certifique-se de que as bases de dados do SQL Server seguem as diretrizes de nomeação da base de [dados para a Cópia de Segurança Azure](#database-naming-guidelines-for-azure-backup).
4. Verifique se você não tem outras soluções de backup habilitadas para o banco de dados. Desabilite todos os outros backups de SQL Server antes de fazer backup do banco de dados.

> [!NOTE]
> Você pode habilitar o backup do Azure para uma VM do Azure e também para um banco de dados SQL Server em execução na VM sem conflito.

### <a name="establish-network-connectivity"></a>Estabelecer conectividade de rede

Para todas as operações, uma VM SQL Server requer conectividade com os endereços IP públicos do Azure. Operações de VM (descoberta de banco de dados, configurar backups, agendar backups, restaurar pontos de recuperação e assim por diante) falham sem conectividade com os endereços IP públicos do Azure.

Estabeleça a conectividade usando uma das seguintes opções:

#### <a name="allow-the-azure-datacenter-ip-ranges"></a>Permitir os intervalos de IP do datacenter do Azure

Esta opção permite que as [gamas IP](https://www.microsoft.com/download/details.aspx?id=41653) no ficheiro descarregado. Para acessar um NSG (grupo de segurança de rede), use o cmdlet Set-AzureNetworkSecurityRule. Se sua lista de destinatários confiáveis incluir apenas IPs específicos de região, você também precisará atualizar a lista de destinatários seguros a marca de serviço Azure Active Directory (AD do Azure) para habilitar a autenticação.

#### <a name="allow-access-using-nsg-tags"></a>Permitir acesso usando marcas NSG

Se você usar NSG para restringir a conectividade, deverá usar a marca de serviço AzureBackup para permitir o acesso de saída ao backup do Azure. Além disso, deve também permitir a conectividade para a autenticação e transferência de dados utilizando [regras](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) para o Azure AD e O Armazenamento Azure. Isso pode ser feito no portal do Azure ou por meio do PowerShell.

Para criar uma regra usando o portal:

  1. Em **Todos os Serviços,** vá a **grupos** de segurança da Rede e selecione o grupo de segurança da rede.
  2. Selecione regras de **segurança de saída** em **Definições**.
  3. Selecione **Adicionar**. Introduza todos os detalhes necessários para criar uma nova regra, conforme descrito nas [definições](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings)de regra de segurança . Certifique-se de que a opção **Destino** está definida para **etiqueta de serviço** e etiqueta de serviço **destino** está definida para **AzureBackup**.
  4. Clique em **Adicionar,** para salvar a regra de segurança de saída recém-criada.

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

**Permitir o acesso utilizando tags De firewall Azure**. Se estiver a utilizar o Firewall Azure, crie uma regra de aplicação utilizando a [etiqueta AzureBackup FQDN](https://docs.microsoft.com/azure/firewall/fqdn-tags). Isso permite o acesso de saída ao backup do Azure.

**Implemente um servidor proxy HTTP para encaminhar**o tráfego . Quando você faz backup de um banco de dados SQL Server em uma VM do Azure, a extensão de backup na VM usa as APIs HTTPS para enviar comandos de gerenciamento para o backup do Azure e dados para o armazenamento do Azure. A extensão de backup também usa o Azure AD para autenticação. Encaminhe o tráfego de extensão de backup para esses três serviços por meio do proxy HTTP. As extensões são o único componente que está configurado para acesso à Internet pública.

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
* Suportes quadrados de fecho (])
* Ponto e vírgula '; '
* Barra '/'

A alias está disponível para caracteres sem suporte, mas é recomendável evitá-los. Para obter mais informações, consulte [Noções Básicas sobre o Modelo de Dados do Serviço Tabela](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).

>[!NOTE]
>A operação **de proteção configurada** para bases de dados com caracteres especiais como "+" ou "&" em seu nome não é suportada. Pode alterar o nome da base de dados ou ativar a **Proteção Automática,** que pode proteger com sucesso estas bases de dados.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Descobrir bancos de dados SQL Server

Como descobrir bancos de dados em execução em uma VM:

1. No [portal Azure,](https://portal.azure.com)abra o cofre dos Serviços de Recuperação que usa para fazer o backup da base de dados.

2. No painel de **abóbadas** dos Serviços de Recuperação, selecione **Backup**.

   ![Selecione backup para abrir o menu meta de backup](./media/backup-azure-sql-database/open-backup-menu.png)

3. In **Backup Goal**, set Whereyour **workload running?**

4. No **que pretende fazer backup,** selecione **SQL Server em Azure VM**.

    ![Selecione SQL Server na VM do Azure para o backup](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. No **Backup Goal** > Discover **DBs em VMs**, selecione **Start Discovery** para procurar VMs desprotegidos na subscrição. Essa pesquisa pode demorar um pouco, dependendo do número de VMs não protegidas na assinatura.

   * As VMs desprotegidas devem aparecer na lista após a descoberta, listada por nome e grupo de recursos.
   * Se uma VM não estiver listada como esperado, veja se ela já foi submetida a backup em um cofre.
   * Várias VMs podem ter o mesmo nome, mas elas pertencerão a grupos de recursos diferentes.

     ![O backup está pendente durante a pesquisa de bancos de os em VMs](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Na lista VM, selecione o VM que executa a base de dados do SQL Server > **Discover DBs**.

7. Rastrear a descoberta da base de dados em **Notificações.** O tempo necessário para essa ação depende do número de bancos de dados de VM. Quando os bancos de dados selecionados são descobertos, uma mensagem de êxito é exibida.

    ![Mensagem de êxito na implantação](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. O backup do Azure descobre todos os bancos de dados SQL Server na VM. Durante a descoberta, os seguintes elementos ocorrem em segundo plano:

    * O backup do Azure registra a VM com o cofre para backup de carga de trabalho. Todos os bancos de dados na VM registrada podem ser submetidos a backup somente nesse cofre.
    * O backup do Azure instala a extensão AzureBackupWindowsWorkload na VM. Nenhum agente está instalado em um banco de dados SQL.
    * O backup do Azure cria a conta de serviço NT Service\AzureWLBackupPluginSvc na VM.
      * Todas as operações de backup e restauração usam a conta de serviço.
      * NT Service\AzureWLBackupPluginSvc requer permissões de sysadmin do SQL. Todas as VMs SQL Server criadas no Marketplace vêm com o SqlIaaSExtension instalado. A extensão AzureBackupWindowsWorkload usa o SQLIaaSExtension para obter automaticamente as permissões necessárias.
    * Se você não criou a VM do Marketplace ou se estiver no SQL 2008 e 2008 R2, a VM poderá não ter o SqlIaaSExtension instalado e a operação de descoberta falhará com a mensagem de erro UserErrorSQLNoSysAdminMembership. Para corrigir este problema, siga as instruções sob [permissões De set VM](backup-azure-sql-database.md#set-vm-permissions).

        ![Selecione a VM e o banco de dados](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Configurar a cópia de segurança  

1. No **Objetivo de Backup** > Passo **2: Configurar a cópia de segurança,** selecione **Configurar cópia de segurança**.

   ![Selecione configurar backup](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. Em **Selecione itens para backup,** consulte todos os grupos de disponibilidade registados e instâncias autónomas do Servidor SQL. Selecione a seta à esquerda de uma linha para expandir a lista de todos os bancos de dados desprotegidos nessa instância ou Always On grupo de disponibilidade.  

    ![Exibindo todas as instâncias de SQL Server com bancos de dados autônomos](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Escolha todas as bases de dados que pretende proteger e, em seguida, selecione **OK**.

   ![Protegendo o banco de dados](./media/backup-azure-sql-database/select-database-to-protect.png)

   Para otimizar as cargas de backup, o backup do Azure define um número máximo de bancos de dados em um trabalho de backup para 50.

     * Para proteger mais de 50 bancos de dados, configure vários backups.
     * Para [ativar](#enable-auto-protection) toda a instância ou o grupo Sempre On, na lista de drop-down **AUTOPROTECT,** selecione **ON**, e, em seguida, selecione **OK**.

    > [!NOTE]
    > A função [de proteção automática](#enable-auto-protection) não só permite proteção em todas as bases de dados existentes ao mesmo tempo, como também protege automaticamente quaisquer novas bases de dados adicionadas a essa instância ou ao grupo de disponibilidade.  

4. Selecione **OK** para abrir **a política**de backup .

    ![Habilitar a proteção automática para o grupo de disponibilidade Always On](./media/backup-azure-sql-database/enable-auto-protection.png)

5. Na **política de backup,** escolha uma apólice e, em seguida, selecione **OK**.

   * Selecione a política padrão como HourlyLogBackup.
   * Escolha uma política de backup existente criada anteriormente para SQL.
   * Defina uma nova política com base no RPO e no período de retenção.

     ![Selecionar política de backup](./media/backup-azure-sql-database/select-backup-policy.png)

6. Na **cópia de segurança,** selecione **'Ativar a cópia de segurança**' .

    ![Habilitar a política de backup escolhida](./media/backup-azure-sql-database/enable-backup-button.png)

7. Acompanhe o progresso da configuração na área de **Notificações** do portal.

    ![Área de notificação](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Criar uma política de backup

Uma política de backup define quando os backups são feitos e por quanto tempo eles são retidos.

* Uma política é criada no nível do cofre.
* Vários cofres podem usar a mesma política de backup, mas você deve aplicar a política de backup a cada cofre.
* Quando você cria uma política de backup, um backup completo diário é o padrão.
* Você pode adicionar um backup diferencial, mas somente se configurar backups completos para ocorrer semanalmente.
* Conheça [diferentes tipos de políticas de backup.](backup-architecture.md#sql-server-backup-types)

Para criar uma política de backup:

1. No cofre, selecione **as políticas** de backup > **Adicionar**.
2. Em **Adicionar**, selecione **SQL Server em VM Azure** para definir o tipo de política.

   ![Escolha um tipo de política para a nova política de backup](./media/backup-azure-sql-database/policy-type-details.png)

3. Em **nome de Política,** insira um nome para a nova política.
4. Na **política de backup completo,** selecione uma Frequência de **Backup**. Escolha **diariamente** ou **semanalmente.**

   * Para **o Daily,** selecione a hora e o fuso horário quando o trabalho de reserva começar.
   * Para **weekly**, selecione o dia da semana, hora e fuso horário quando o trabalho de reserva começar.
   * Faça uma cópia de segurança completa, porque não pode desligar a opção **Full Backup.**
   * Selecione **Backup Completo** para ver a apólice.
   * Não é possível criar backups diferenciais para backups diários completos.

     ![Novos campos de política de backup](./media/backup-azure-sql-database/full-backup-policy.png)  

5. No **RANGE DE RETENÇÃO,** todas as opções são selecionadas por padrão. Desmarque os limites de intervalo de retenção que você não deseja e defina os intervalos a serem usados.

    * O período de retenção mínimo para qualquer tipo de backup (completo, diferencial e log) é de sete dias.
    * Os pontos de recuperação são marcados para retenção com base em seu período de retenção. Por exemplo, se você selecionar um backup completo diário, apenas um backup completo será disparado todos os dias.
    * O backup de um dia específico é marcado e mantido com base no período de retenção semanal e na configuração de retenção semanal.
    * Os intervalos de retenção mensais e anuais se comportam de maneira semelhante.

       ![Configurações de intervalo do intervalo de retenção](./media/backup-azure-sql-database/retention-range-interval.png)

6. No menu de **política Full Backup,** selecione **OK** para aceitar as definições.
7. Para adicionar uma política de backup diferencial, selecione **Backup Diferencial**.

   definições de intervalo de intervalo de retenção de ![](./media/backup-azure-sql-database/retention-range-interval.png)
   ![Abrir o menu de política de backup diferencial](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. Na **política de backup diferencial,** selecione **Ativar** para abrir os controlos de frequência e retenção.

    * Você pode disparar apenas um backup diferencial por dia.
    * Backups diferenciais podem ser retidos por um máximo de 180 dias. Para maior retenção, use backups completos.

9. Selecione **OK** para salvar a apólice e volte ao menu principal de política de **backup.**

10. Para adicionar uma política de backup de registo transacional, selecione **Log Backup**.
11. No **Log Backup**, selecione **Enable**e, em seguida, detete os controlos de frequência e retenção. Os backups de log podem ocorrer com frequência a cada 15 minutos e podem ser retidos por até 35 dias.
12. Selecione **OK** para salvar a apólice e volte ao menu principal de política de **backup.**

    ![Editar a política de backup de log](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. No menu de **política de backup,** escolha se permite ou não ativar a Compressão de **Backup SQL.** Esta opção é desativada por defeito. Se habilitada, SQL Server enviará um fluxo de backup compactado para o VDI.  Por favor, note que a Backup Azure substitui os incumprimentos de nível de instância com a cláusula COMPRESSION/NO_COMPRESSION dependendo do valor deste controlo.

14. Depois de completar as edimas para a política de backup, selecione **OK**.

> [!NOTE]
> Cada backup de log é encadeado ao backup completo anterior para formar uma cadeia de recuperação. Esse backup completo será retido até que a retenção do último backup de log tenha expirado. Isso pode significar que o backup completo é mantido por um período extra para garantir que todos os logs possam ser recuperados. Vamos supor que o usuário tenha um backup completo semanal, os logs diferenciais diários e de 2 horas. Todos eles são mantidos por 30 dias. No entanto, a semana completa pode ser realmente limpa/excluída somente depois que o próximo backup completo estiver disponível, ou seja, após 30 a 7 dias. Digamos que um backup completo semanal ocorra em 16 de novembro. De acordo com a política de retenção, ela deve ser retida até 16 de dezembro. O último backup de log para esse total ocorre antes do próximo agendamento completo, em novembro de 22. Até que esse log esteja disponível até dec 22, o 16º total de novembro não poderá ser excluído. Portanto, o 16º 16 de novembro é mantido até dec 22.

## <a name="enable-auto-protection"></a>Habilitar proteção automática  

Você pode habilitar a proteção automática para fazer backup automático de todos os bancos de dados existentes e futuros para uma instância SQL Server autônoma ou para um grupo de disponibilidade Always On.

* Não há limite para o número de bancos de dados que você pode selecionar para proteção automática ao mesmo tempo.
* Você não pode proteger ou excluir seletivamente bancos de dados da proteção em uma instância no momento em que habilita a proteção automática.
* Se sua instância já inclui alguns bancos de dados protegidos, eles permanecerão protegidos em suas respectivas políticas mesmo depois de ativar a proteção automática. Todas as bases de dados desprotegidas adicionadas posteriormente terão apenas uma única política que definir no momento de permitir a auto-protecção, listada no âmbito da **Configuração Backup**. No entanto, você pode alterar a política associada a um banco de dados protegido automaticamente mais tarde.  

Para habilitar a proteção automática:

  1. Em **itens de backup,** selecione a instância para a qual pretende ativar a proteção automática.
  2. Selecione a lista de drop-down em **AutoPROTECT,** escolha **ON**, e, em seguida, selecione **OK**.

      ![Habilitar a proteção automática no grupo de disponibilidade](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. A cópia de segurança está configurada para todas as bases de dados em conjunto e pode ser rastreada em **Backup Jobs**.

Se precisar de desativar a proteção automática, selecione o nome da instância em **Configurar Backup**, e, em seguida, selecione **Desativar automaticamente,** por exemplo. Todos os bancos de dados continuarão a ser submetidos a backup, mas os bancos de dados futuros não serão automaticamente protegidos.

![Desabilitar a proteção automática nessa instância](./media/backup-azure-sql-database/disable-auto-protection.png)

## <a name="next-steps"></a>Passos seguintes

Aprenda a:

* [Restaurar as bases de dados do Servidor SQL apoiados](restore-sql-database-azure-vm.md)
* [Gerir bases de dados de servidorS SQL apoiadas](manage-monitor-sql-database-backup.md)
