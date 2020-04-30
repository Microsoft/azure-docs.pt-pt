---
title: Fazer cópias de segurança de bases de dados do SQL Server em VMs do Azure
description: Neste artigo, aprenda a apoiar as bases de dados do SQL Server em máquinas virtuais Azure com Backup Azure.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 887f15deed74330cf132e0574d166c074d2c7cad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81685717"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Fazer cópias de segurança de bases de dados do SQL Server em VMs do Azure

As bases de dados do SQL Server são cargas de trabalho críticas que requerem um objetivo de ponto de recuperação baixo (RPO) e retenção a longo prazo. Pode fazer backup nas bases de dados do SQL Server em máquinas virtuais Azure (VMs) utilizando [o Azure Backup](backup-overview.md).

Este artigo mostra como fazer backup numa base de dados do SQL Server que está a funcionar num Azure VM para um cofre dos Serviços de Recuperação de Backup Azure.

Neste artigo, aprenderá a:

> [!div class="checklist"]
>
> * Crie e configure um cofre.
> * Descubra bases de dados e instale cópias de segurança.
> * Criar proteção automática para bases de dados.

>[!NOTE]
>**A eliminação suave para o servidor SQL em Azure VM e eliminação suave para SAP HANA em cargas de trabalho VM Azure** já está disponível na pré-visualização.<br>
>Para se inscrever para a pré-visualização, escreva-nos emAskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Pré-requisitos

Antes de fazer o check-up numa base de dados do SQL Server, verifique os seguintes critérios:

1. Identifique ou crie um cofre de Serviços de [Recuperação](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) na mesma região e subscrição que o VM que acolhe a instância Do Servidor SQL.
2. Verifique se o VM tem conectividade de [rede.](backup-sql-server-database-azure-vms.md#establish-network-connectivity)
3. Certifique-se de que as bases de dados do SQL Server seguem as diretrizes de nomeação da base de [dados para a Cópia de Segurança Azure](#database-naming-guidelines-for-azure-backup).
4. Verifique se não tem outras soluções de backup ativadas para a base de dados. Desative todas as outras cópias de segurança do SQL Server antes de fazer o backup da base de dados.

> [!NOTE]
> Pode ativar o Backup Azure para um VM Azure e também para uma base de dados do SQL Server que funciona no VM sem conflito.

### <a name="establish-network-connectivity"></a>Estabelecer conectividade de rede

Para todas as operações, um VM de servidor SQL requer conectividade com endereços IP públicos do Azure. As operações vM (descoberta de bases de dados, configurar backups, agendar backups, restaurar pontos de recuperação, e assim por diante) falham sem conectividade com endereços IP públicos do Azure.

Estabelecer conectividade utilizando uma das seguintes opções:

#### <a name="allow-the-azure-datacenter-ip-ranges"></a>Permitir as gamas IP do datacenter Azure

Esta opção permite que as [gamas IP](https://www.microsoft.com/download/details.aspx?id=41653) no ficheiro descarregado. Para aceder a um grupo de segurança de rede (NSG), utilize o cmdlet Set-AzureNetworkSecurityRule. Se a sua lista de destinatários seguros incluir apenas IPs específicos da região, também terá de atualizar a lista de destinatários seguros da etiqueta de serviço Azure Ative Directory (Azure AD) para permitir a autenticação.

#### <a name="allow-access-using-nsg-tags"></a>Permitir o acesso utilizando tags NSG

Se utilizar o NSG para restringir a conectividade, deverá utilizar a etiqueta de serviço AzureBackup para permitir o acesso de saída ao Azure Backup. Além disso, deve também permitir a conectividade para a autenticação e transferência de dados utilizando [regras](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) para o Azure AD e O Armazenamento Azure. Isto pode ser feito a partir do portal Azure ou via PowerShell.

Para criar uma regra usando o portal:

  1. Em **Todos os Serviços,** vá a **grupos** de segurança da Rede e selecione o grupo de segurança da rede.
  2. Selecione regras de **segurança de saída** em **Definições**.
  3. Selecione **Adicionar**. Introduza todos os detalhes necessários para criar uma nova regra, conforme descrito nas [definições](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings)de regra de segurança . Certifique-se de que a opção **Destino** está definida para **etiqueta de serviço** e etiqueta de serviço **destino** está definida para **AzureBackup**.
  4. Clique em **Adicionar,** para salvar a regra de segurança de saída recém-criada.

Para criar uma regra usando o PowerShell:

 1. Adicione credenciais de conta Azure e atualize as nuvens nacionais<br/>
      `Add-AzureRmAccount`<br/>

 2. Selecione a subscrição NSG<br/>
      `Select-AzureRmSubscription "<Subscription Id>"`

 3. Selecione o NSG<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

 4. Adicione a regra de saída para a etiqueta de serviço de backup Azure<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 5. Adicione a regra de saída para a etiqueta de serviço de armazenamento<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "StorageAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "Storage" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 6. Adicione a regra de saída para a etiqueta de serviço AzureActiveDirectory<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureActiveDirectoryAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureActiveDirectory" -DestinationPortRange 443 -Description "Allow outbound traffic to AzureActiveDirectory service"`

 7. Salve o NSG<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`

**Permitir o acesso utilizando tags De firewall Azure**. Se estiver a utilizar o Firewall Azure, crie uma regra de aplicação utilizando a [etiqueta AzureBackup FQDN](https://docs.microsoft.com/azure/firewall/fqdn-tags). Isto permite o acesso de saída ao Azure Backup.

**Implemente um servidor proxy HTTP para encaminhar**o tráfego . Quando faz o backup de uma base de dados do SQL Server num VM Azure, a extensão de cópia de segurança no VM utiliza as APIs HTTPS para enviar comandos de gestão para Backup Azure e dados para o Armazenamento Azure. A extensão de reserva também utiliza AD Azure para autenticação. Encaminhe o tráfego de extensão de reserva para estes três serviços através do proxy HTTP. Não existem domínios wildcard em uso com O Backup Azure para adicionar à lista de espera para as suas regras de procuração. Você precisará usar as gamas públicas de IP para estes serviços prestados pelo Azure. As extensões são o único componente configurado para o acesso à internet pública.

As opções de conectividade incluem as seguintes vantagens e desvantagens:

**Opção** | **Vantagens** | **Desvantagens**
--- | --- | ---
Permitir gamas IP | Sem custos adicionais | Complexo de gerir porque as gamas de endereços IP mudam ao longo do tempo <br/><br/> Dá acesso a todo o Azure, e não apenas ao Armazenamento Azure
Utilize etiquetas de serviço NSG | Mais fácil de gerir à medida que as alterações de gama são automaticamente fundidas <br/><br/> Sem custos adicionais <br/><br/> | Pode ser usado apenas com NSGs <br/><br/> Fornece acesso a todo o serviço
Utilize tags FQDN de firewall Azure | Mais fácil de gerir à medida que as FQDNs necessárias são geridas automaticamente | Pode ser usado apenas com Firewall Azure
Use um proxy HTTP | Ponto único de acesso à Internet aos VMs <br/> | Custos adicionais para executar um VM com o software proxy <br/> Sem endereços FQDN publicados, permitir regras estarão sujeitas a alterações de endereço IP Do Azure

#### <a name="private-endpoints"></a>Pontos Finais Privados

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

### <a name="database-naming-guidelines-for-azure-backup"></a>Base de dados de diretrizes de nomeação para Backup Azure

Evite utilizar os seguintes elementos em nomes de bases de dados:

* Espaços de trailing e principais
* Marcas de exclamação de rasto (!)
* Suportes quadrados de fecho (])
* Ponto-e-vírgula ';'
* Barra para a frente '/'

O aliasing está disponível para caracteres não suportados, mas recomendamos evitá-los. Para obter mais informações, consulte [Noções Básicas sobre o Modelo de Dados do Serviço Tabela](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).

>[!NOTE]
>A operação **de proteção de configuração** para bases de dados com caracteres especiais como "+" ou "&" em seu nome não é suportada. Pode alterar o nome da base de dados ou ativar a **Proteção Automática,** que pode proteger com sucesso estas bases de dados.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Descubra as bases de dados do Servidor SQL

Como descobrir bases de dados em execução num VM:

1. No [portal Azure,](https://portal.azure.com)abra o cofre dos Serviços de Recuperação que usa para fazer o backup da base de dados.

2. No painel de **abóbadas** dos Serviços de Recuperação, selecione **Backup**.

   ![Selecione Backup para abrir o menu 'Backup Goal'](./media/backup-azure-sql-database/open-backup-menu.png)

3. In **Backup Goal**, set Where **Azure**your **workload running?**

4. No **que pretende fazer backup,** selecione **SQL Server em Azure VM**.

    ![Selecione SQL Server em Azure VM para a cópia de segurança](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. Em **Backup Goal** > **Discover DBs em VMs**, selecione **Start Discovery** para procurar VMs desprotegidos na subscrição. Esta pesquisa pode demorar algum tempo, dependendo do número de VMs desprotegidos na subscrição.

   * VMs desprotegidos devem aparecer na lista após descoberta, listadas pelo nome e pelo grupo de recursos.
   * Se um VM não está listado como espera, veja se já está num cofre.
   * Vários VMs podem ter o mesmo nome, mas pertencerão a diferentes grupos de recursos.

     ![Backup está pendente durante pesquisa de DBs em VMs](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Na lista VM, selecione o VM que executa a base de dados do Servidor SQL > **Discover DBs**.

7. Rastrear a descoberta da base de dados em **Notificações.** O tempo necessário para esta ação depende do número de bases de dados vm. Quando as bases de dados selecionadas são descobertas, aparece uma mensagem de sucesso.

    ![Mensagem de sucesso de implantação](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. O Azure Backup descobre todas as bases de dados do SQL Server no VM. Durante a descoberta, os seguintes elementos ocorrem em segundo plano:

    * O Azure Backup regista o VM com o cofre para cópia de trabalho. Todas as bases de dados do VM registado só podem ser apoiadas neste cofre.
    * A Cópia de Segurança Azure instala a extensão AzureBackupWindowsWorkload no VM. Nenhum agente está instalado numa base de dados SQL.
    * A Azure Backup cria a conta de serviço NT Service\AzureWLBackupPluginSvc no VM.
      * Todas as operações de backup e restauro utilizam a conta de serviço.
      * Serviço NT\AzureWLBackupPluginSvc requer permissões de sysadmins SQL. Todos os VMs do Servidor SQL criados no Mercado vêm com a Extensão SqlIaS instalada. A extensão AzureBackupWindowsWorkload utiliza a Extensão SQLIaS para obter automaticamente as permissões necessárias.
    * Se não criou o VM a partir do Marketplace ou se estiver no SQL 2008 e 2008 R2, o VM pode não ter a Extensão SqlIaS instalada, e a operação de descoberta falha com a mensagem de erro UserErrorSQLNoSysAdminMembership. Para corrigir este problema, siga as instruções sob [permissões De set VM](backup-azure-sql-database.md#set-vm-permissions).

        ![Selecione o VM e a base de dados](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Configurar a cópia de segurança  

1. No Passo de **Objetivo** > de Backup**2: Configurar a cópia de segurança,** selecione **Configurar cópia de segurança**.

   ![Selecione Configurar cópia de segurança](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. Em **Selecione itens para backup,** consulte todos os grupos de disponibilidade registados e instâncias autónomas do Servidor SQL. Selecione a seta à esquerda de uma linha para expandir a lista de todas as bases de dados desprotegidas nesse caso ou sempre em grupo de disponibilidade.  

    ![Exibindo todas as instâncias do SQL Server com bases de dados autónomas](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Escolha todas as bases de dados que pretende proteger e, em seguida, selecione **OK**.

   ![Proteger a base de dados](./media/backup-azure-sql-database/select-database-to-protect.png)

   Para otimizar as cargas de backup, o Azure Backup define um número máximo de bases de dados num trabalho de backup para 50.

     * Para proteger mais de 50 bases de dados, configure várias cópias de segurança.
     * Para [ativar](#enable-auto-protection) toda a instância ou o grupo Sempre On, na lista de drop-down **AUTOPROTECT,** selecione **ON**, e, em seguida, selecione **OK**.

    > [!NOTE]
    > A função [de proteção automática](#enable-auto-protection) não só permite proteção em todas as bases de dados existentes ao mesmo tempo, como também protege automaticamente quaisquer novas bases de dados adicionadas a essa instância ou ao grupo de disponibilidade.  

4. Selecione **OK** para abrir **a política**de backup .

    ![Ativar proteção automática para o grupo sempre em funcionação](./media/backup-azure-sql-database/enable-auto-protection.png)

5. Na **política de backup,** escolha uma apólice e, em seguida, selecione **OK**.

   * Selecione a política predefinida como HoradoLogBackup.
   * Escolha uma política de backup existente anteriormente criada para o SQL.
   * Defina uma nova política com base no seu intervalo de RPO e retenção.

     ![Selecione política de backup](./media/backup-azure-sql-database/select-backup-policy.png)

6. Na **cópia de segurança,** selecione **'Ativar a cópia de segurança**' .

    ![Ativar a política de backup escolhida](./media/backup-azure-sql-database/enable-backup-button.png)

7. Acompanhe o progresso da configuração na área de **Notificações** do portal.

    ![Área de notificação](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Criar uma política de backup

Uma política de reserva define quando os backups são tomados e quanto tempo são retidos.

* Uma apólice é criada ao nível do cofre.
* Vários cofres podem usar a mesma política de reserva, mas tens de aplicar a política de reserva a cada cofre.
* Quando se cria uma política de backup, um backup completo diário é o padrão.
* Pode adicionar uma cópia de segurança diferencial, mas apenas se configurar cópias de segurança completas para ocorrer semanalmente.
* Conheça [diferentes tipos de políticas de backup.](backup-architecture.md#sql-server-backup-types)

Para criar uma política de backup:

1. No cofre, selecione **Políticas** > de Backup**Adicionar**.
2. Em **Adicionar**, selecione **SQL Server em VM Azure** para definir o tipo de política.

   ![Escolha um tipo de política para a nova política de backup](./media/backup-azure-sql-database/policy-type-details.png)

3. Em **nome de Política,** insira um nome para a nova política.
4. Na **política de backup completo,** selecione uma Frequência de **Backup**. Escolha **diariamente** ou **semanalmente.**

   * Para **o Daily,** selecione a hora e o fuso horário quando o trabalho de reserva começar.
   * Para **weekly**, selecione o dia da semana, hora e fuso horário quando o trabalho de reserva começar.
   * Faça uma cópia de segurança completa, porque não pode desligar a opção **Full Backup.**
   * Selecione **Backup Completo** para ver a apólice.
   * Não se pode criar backups diferenciais para cópias de segurança diárias completas.

     ![Novos campos de política de backup](./media/backup-azure-sql-database/full-backup-policy.png)  

5. No **RANGE DE RETENÇÃO,** todas as opções são selecionadas por padrão. Limpe os limites de alcance de retenção que não quiser e, em seguida, detete os intervalos a utilizar.

    * O período mínimo de retenção para qualquer tipo de cópia de segurança (completa, diferencial e registo) é de sete dias.
    * Os pontos de recuperação estão marcados para retenção com base na sua gama de retenção. Por exemplo, se selecionar uma cópia de segurança completa diária, apenas um backup completo é acionado todos os dias.
    * A cópia de segurança para um dia específico é marcada e mantida com base na gama semanal de retenção e na definição semanal de retenção.
    * As gamas de retenção mensais e anuais comportam-se de forma semelhante.

       ![Definições de intervalo de intervalo de retenção](./media/backup-azure-sql-database/retention-range-interval.png)

6. No menu de **política Full Backup,** selecione **OK** para aceitar as definições.
7. Para adicionar uma política de backup diferencial, selecione **Backup Diferencial**.

   ![Definições de](./media/backup-azure-sql-database/retention-range-interval.png)
   ![intervalo de intervalo de retenção Abrir o menu de política de backup diferencial](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. Na **política de backup diferencial,** selecione **Ativar** para abrir os controlos de frequência e retenção.

    * Só pode acionar uma cópia de segurança diferencial por dia.
    * As cópias de segurança diferenciais podem ser retidas por um máximo de 180 dias. Para uma retenção mais longa, utilize cópias de segurança completas.

9. Selecione **OK** para salvar a apólice e volte ao menu principal de política de **backup.**

10. Para adicionar uma política de backup de registo transacional, selecione **Log Backup**.
11. No **Log Backup**, selecione **Enable**e, em seguida, detete os controlos de frequência e retenção. As cópias de segurança de log podem ocorrer com a frequência de cada 15 minutos e podem ser retidas até 35 dias.
12. Selecione **OK** para salvar a apólice e volte ao menu principal de política de **backup.**

    ![Editar a política de backup de registo](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. No menu de **política de backup,** escolha se permite ou não ativar a Compressão de **Backup SQL.** Esta opção está desativada por predefinição. Se ativado, o SQL Server enviará um fluxo de reserva comprimido para o VDI.  Por favor, note que a Backup Azure substitui os incumprimentos de nível de instância com a cláusula COMPRESSION/NO_COMPRESSION dependendo do valor deste controlo.

14. Depois de completar as edimas para a política de backup, selecione **OK**.

> [!NOTE]
> Cada cópia de segurança de registo é acorrentada à cópia de segurança completa anterior para formar uma cadeia de recuperação. Esta cópia de segurança completa será retida até que a retenção da última cópia de segurança tenha expirado. Isto pode significar que a cópia de segurança completa é retida por um período extra para se certificar de que todos os registos podem ser recuperados. Vamos assumir que o utilizador tem uma cópia de segurança semanal completa, diferencial diário e registos de 2 horas. Todos estão retidos por 30 dias. Mas, o todo semanal só pode ser limpo/apagado após a próxima cópia de segurança completa estar disponível, ou seja, após 30 + 7 dias. Digamos, um reforço semanal acontece no dia 16 de novembro. De acordo com a política de retenção, deve ser mantida até 16 de dezembro. O último registo de reserva para este completo acontece antes do próximo programado completo, no dia 22 de novembro. Até que este registo esteja disponível até 22 de dezembro, o 16 de novembro completo não pode ser apagado. Assim, o 16º de novembro completo é mantido até 22 de dezembro.

## <a name="enable-auto-protection"></a>Ativar a proteção automática  

Pode ativar a auto-protecção para fazer o reexame automático de todas as bases de dados existentes e futuras para uma instância autónoma do SQL Server ou para um grupo de disponibilidade Always On.

* Não há limite para o número de bases de dados que pode selecionar para proteção automática de uma só vez.
* Não é possível proteger ou excluir seletivamente bases de dados de proteção num caso no momento em que permite a auto-protecção.
* Se a sua instância já incluir algumas bases de dados protegidas, elas permanecerão protegidas sob as respetivas políticas mesmo depois de ligar a auto-proteção. Todas as bases de dados desprotegidas adicionadas posteriormente terão apenas uma única política que definir no momento de permitir a auto-protecção, listada no âmbito da **Configuração Backup**. No entanto, pode alterar a política associada a uma base de dados protegida automaticamente mais tarde.  

Para permitir a proteção automática:

  1. Em **itens de backup,** selecione a instância para a qual pretende ativar a proteção automática.
  2. Selecione a lista de drop-down em **AutoPROTECT,** escolha **ON**, e, em seguida, selecione **OK**.

      ![Ativar a proteção automática no grupo de disponibilidade](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. A cópia de segurança está configurada para todas as bases de dados em conjunto e pode ser rastreada em **Backup Jobs**.

Se precisar de desativar a proteção automática, selecione o nome da instância em **Configurar Backup**, e, em seguida, selecione **Desativar automaticamente,** por exemplo. Todas as bases de dados continuarão a ser apoiadas, mas as futuras bases de dados não serão protegidas automaticamente.

![Desativar a proteção automática neste caso](./media/backup-azure-sql-database/disable-auto-protection.png)

## <a name="next-steps"></a>Passos seguintes

Aprenda a:

* [Restaurar as bases de dados do Servidor SQL apoiados](restore-sql-database-azure-vm.md)
* [Gerir bases de dados de servidorS SQL apoiadas](manage-monitor-sql-database-backup.md)
