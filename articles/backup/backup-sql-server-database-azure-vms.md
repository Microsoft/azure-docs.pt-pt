---
title: Cópia de segurança de bases de dados do SQL Server em VMs do Azure | Documentos da Microsoft
description: Saiba como fazer cópias de segurança de bases de dados do SQL Server em VMs do Azure
services: backup
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: sachdevaswati
ms.openlocfilehash: 2fba8b0056c80a62837682a6820b68f71fba9ea8
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65952946"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Fazer cópias de segurança de bases de dados do SQL Server em VMs do Azure

Bases de dados do SQL Server são cargas de trabalho críticas que exigem um objetivo de baixa de ponto de recuperação (RPO) e a retenção de longa duração. Pode fazer backup de bancos de dados do SQL Server em execução em máquinas virtuais do Azure (VMs) usando [Azure Backup](backup-overview.md).

Este artigo mostra como fazer cópias de segurança de uma base de dados do SQL Server que está em execução numa VM do Azure para um cofre dos serviços de recuperação de cópia de segurança do Azure.

Neste artigo, ficará a saber como:

> [!div class="checklist"]
> * Criar e configurar um cofre.
> * Detetar as bases de dados e configurar cópias de segurança.
> * Configure a proteção automática para bases de dados.


## <a name="prerequisites"></a>Pré-requisitos

Antes de fazer backup de uma base de dados do SQL Server, verifique os seguintes critérios:

1. Identificar ou criar um [cofre dos serviços de recuperação](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) na mesma região ou região do que a VM que aloja a instância do SQL Server.
2. Verifique os [permissões de VM necessário](backup-azure-sql-database.md#fix-sql-sysadmin-permissions) para fazer cópias de segurança de bases de dados SQL.
3. Certifique-se de que a VM tem [conectividade de rede](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
4. Certifique-se de que as bases de dados do SQL Server, siga os [diretrizes de nomenclatura de base de dados para o Azure Backup](#database-naming-guidelines-for-azure-backup).
5. Certifique-se de que não tem quaisquer outras soluções de cópia de segurança ativadas para a base de dados. Desative todas as outras cópias de segurança do SQL Server antes de fazer cópias de segurança da base de dados.

> [!NOTE]
> Pode ativar a cópia de segurança do Azure para uma VM do Azure e também para uma base de dados do SQL Server em execução na VM sem conflitos.


### <a name="establish-network-connectivity"></a>Estabelecer conectividade de rede

Para todas as operações, uma VM do SQL Server necessitam de conectividade para os endereços IP públicos do Azure. Operações de VM (deteção de base de dados, configurar cópias de segurança, agendar cópias de segurança, restaurar pontos de recuperação e assim por diante) falharem sem conectividade com os endereços IP públicos do Azure.

Estabelecer conectividade ao utilizar uma das seguintes opções:

- **Permitir que os intervalos IP do datacenter do Azure**. Esta opção permite [intervalos de IP](https://www.microsoft.com/download/details.aspx?id=41653) no download. Para aceder a um grupo de segurança de rede (NSG), utilize o cmdlet Set-AzureNetworkSecurityRule. Se tiver a lista de permissões apenas específico da região IPs, irá também precisar à lista de permissões do Azure Active Directory (Azure AD) etiqueta de serviço para ativar a autenticação.

- **Permitir o acesso utilizando as etiquetas de NSG**. Se utilizar NSGs para restringir a conectividade, esta opção acrescenta uma regra para o seu NSG que permita o acesso de saída para o Azure Backup usando a marca de AzureBackup. Para além desta etiqueta, também precisará correspondente [regras](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) para o Azure AD e o armazenamento do Azure para permitir a ligação para a autenticação e transferência de dados. A etiqueta de AzureBackup só está atualmente disponível no PowerShell. Para criar uma regra ao utilizar a tag de AzureBackup:

    - Adicionar credenciais de conta do Azure e atualizar as nuvens nacionais<br/>
    `Add-AzureRmAccount`

    - Selecione a subscrição de NSG<br/>
    `Select-AzureRmSubscription "<Subscription Id>"`

     - Selecione o NSG<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

    - Adicionar permitir regra de saída para a etiqueta de serviço de cópia de segurança do Azure<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

  - Guardar o NSG<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`
- **Permitir o acesso através de etiquetas de Firewall do Azure**. Se estiver usando o Firewall do Azure, criar uma regra de aplicação utilizando o AzureBackup [etiqueta do FQDN](https://docs.microsoft.com/azure/firewall/fqdn-tags). Isso permite o acesso de saída para o Azure Backup.
- **Implementar um servidor proxy HTTP para encaminhar o tráfego**. Quando copia em segurança um banco de dados do SQL Server numa VM do Azure, a extensão de cópia de segurança na VM utiliza as APIs de HTTPS para enviar comandos de gestão para a cópia de segurança do Azure e os dados ao armazenamento do Azure. A extensão de cópia de segurança também utiliza o Azure AD para autenticação. Encaminhe o tráfego de extensão de cópia de segurança para estes três serviços através do proxy HTTP. As extensões são o único componente que está configurado para acesso à internet pública.

Opções de conectividade incluem as seguintes vantagens e desvantagens:

**Opção** | **Vantagens** | **Desvantagens**
--- | --- | ---
Permitir intervalos de IP | Não existem custos adicionais | Complexo de gerir porque os intervalos de endereços IP mudam ao longo do tempo <br/><br/> Fornece acesso a todo do Azure, não apenas o armazenamento do Azure
Utilizar etiquetas de serviço do NSG | Mais fácil de gerenciar como as alterações de intervalo são mescladas automaticamente <br/><br/> Não existem custos adicionais <br/><br/> | Pode ser utilizado apenas com NSGs <br/><br/> Fornece acesso a todo o serviço
Utilizar etiquetas de FQDN de Firewall do Azure | Mais fácil de gerenciar como os FQDNs necessários são geridos automaticamente | Pode ser utilizado apenas com o Firewall do Azure
Utilizar um proxy de HTTP | URLs do controle granular no proxy sobre o armazenamento é permitido <br/><br/> Acesso de ponto único de internet às VMs <br/><br/> Não sujeitas a alterações ao endereço IP do Azure | Custos adicionais para executar uma VM com o software de proxy

### <a name="set-vm-permissions"></a>Definir permissões de VM

Ao configurar uma cópia de segurança para uma base de dados do SQL Server, faz o seguinte cópia de segurança do Azure:

- Adiciona a extensão de AzureBackupWindowsWorkload.
- Cria uma conta NT SERVICE\AzureWLBackupPluginSvc para detetar as bases de dados na máquina virtual. Esta conta é utilizada para uma cópia de segurança e restaurar e necessita de permissões de administrador do sistema do SQL.
- Deteta as bases de dados que estão em execução numa VM, o Azure Backup utiliza a conta NT AUTHORITY\SYSTEM. Esta conta tem de ser um público início de sessão no SQL.

Se não criar a VM do SQL Server no Azure Marketplace, poderá receber um erro de UserErrorSQLNoSysadminMembership. Para obter mais informações, consulte a secção de considerações e limitações de recursos encontrados na [sobre o SQL Server Backup em VMs do Azure](backup-azure-sql-database.md#fix-sql-sysadmin-permissions).

### <a name="database-naming-guidelines-for-azure-backup"></a>Diretrizes de nomenclatura para o Azure Backup do banco de dados

Evite a utilizar os seguintes elementos em nomes de base de dados:

  * À direita e espaços à esquerda
  * Marcas de exclamação (!!!) à direita
  * Fechar Parênteses Retos (])
  * A partir do F:\

Aliasing está disponível para carateres não suportados, mas recomendamos que evite-los. Para obter mais informações, consulte [Noções Básicas sobre o Modelo de Dados do Serviço Tabela](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).


[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Detetar as bases de dados do SQL Server

Como detetar as bases de dados em execução numa VM:

1. Na [portal do Azure](https://portal.azure.com), abra o Cofre de serviços de recuperação que utilizar para criar cópias de segurança da base de dados.

2. Na **cofre dos serviços de recuperação** dashboard, selecione **cópia de segurança**.

   ![Selecionar cópia de segurança para abrir o menu de objetivo de cópia de segurança](./media/backup-azure-sql-database/open-backup-menu.png)

3. Na **objetivo de cópia de segurança**, defina **em que a sua carga de trabalho é executado?** para **Azure**.

4. Na **o que fazer quiser a cópia de segurança**, selecione **do SQL Server na VM do Azure**.

    ![Selecione o SQL Server na VM do Azure para a cópia de segurança](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. Na **objetivo de cópia de segurança** > **detetar DBs em VMs**, selecione **Iniciar deteção** para procurar as VMs não protegidas na subscrição. Esta pesquisa pode demorar algum tempo, dependendo do número de VMs não protegidas na subscrição.

   - VMs não protegidas devem aparecer na lista após a deteção, listada por nome e grupo de recursos.
   - Se uma VM não estiver listada como esperado, ver se ele já foi efetuado num cofre.
   - Várias VMs podem ter o mesmo nome, mas irá pertencem a diferentes grupos de recursos.

     ![Cópia de segurança está pendente durante a pesquisa do DBs em VMs](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Na lista de VM, selecione a VM em execução a base de dados do SQL Server > **detetar bds**.

7. Deteção de base de dados de controle no **notificações**. O tempo necessário para esta ação depende o número de bases de dados da VM. Quando forem detetadas as bases de dados selecionadas, é apresentada uma mensagem de êxito.

    ![Mensagem de êxito da implementação](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Cópia de segurança do Azure Deteta todas as bases de dados do SQL Server na VM. Durante a deteção, os elementos seguintes ocorrem em segundo plano:

    - O Azure Backup registra a VM com o Cofre de cópia de segurança da carga de trabalho. Todas as bases de dados na VM registada podem ser uma cópia de segurança neste cofre apenas.
    - O Azure Backup instala a extensão de AzureBackupWindowsWorkload na VM. Nenhum agente está instalado num banco de dados SQL.
    - Cópia de segurança do Azure cria a conta de serviço NT Service\AzureWLBackupPluginSvc na VM.
      - Todas as operações de cópia de segurança e restauro, utilize a conta de serviço.
      - NT Service\AzureWLBackupPluginSvc requer permissões de administrador do sistema do SQL. Todas as VMs do SQL Server criado no Marketplace são fornecidos com o SqlIaaSExtension instalado. A extensão de AzureBackupWindowsWorkload utiliza o SQLIaaSExtension para obter automaticamente as permissões necessárias.
    - Se não criar a VM no Marketplace, a VM não terá SqlIaaSExtension instalado e a operação de deteção falha com a mensagem de erro UserErrorSQLNoSysAdminMembership. Para corrigir este problema, siga os [instruções](backup-azure-sql-database.md#fix-sql-sysadmin-permissions).

        ![Selecione a VM e a base de dados](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Configurar a cópia de segurança  

1. Na **objetivo de cópia de segurança** > **passo 2: Configurar cópia de segurança**, selecione **configurar a cópia de segurança**.

   ![Selecione Configurar cópia de segurança](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. Na **selecionar itens para cópia de segurança**, verá todos os grupos de disponibilidade registados e instâncias do SQL Server autónomo. Selecione a seta à esquerda de uma linha para expandir a lista de todas as bases de dados não protegidas desse instância ou grupo de disponibilidade Always On.  

    ![Exibir todas as instâncias do SQL Server com bases de dados autónomas](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Escolha todas as bases de dados que pretende proteger e, em seguida, selecione **OK**.

   ![Proteger a base de dados](./media/backup-azure-sql-database/select-database-to-protect.png)

   Para otimizar cargas de cópia de segurança, cópia de segurança do Azure define um número máximo de bases de dados numa tarefa de cópia de segurança como 50.

     * Para proteger mais de 50 bases de dados, configure várias cópias de segurança.
     * Para habilitar [ ](#enable-auto-protection) toda a instância ou o grupo de disponibilidade Always On. Na **AUTOPROTECT** na lista pendente, selecione **ON**e, em seguida, selecione **OK**.
     
    > [!NOTE]
    > O [proteção automática](#enable-auto-protection) funcionalidade não só permite a proteção em todos os bancos de dados existentes ao mesmo tempo, mas também automaticamente protege bases de dados novas adicionados a essa instância ou o grupo de disponibilidade.  

4. Selecione **OK** para abrir **política de cópia de segurança**.

    ![Ativar a proteção automática para o grupo de disponibilidade Always On](./media/backup-azure-sql-database/enable-auto-protection.png)

5. Na **política de cópia de segurança**, escolha uma política e, em seguida, selecione **OK**.

   - Selecione a política predefinida como HourlyLogBackup.
   - Escolha uma política de cópia de segurança existente criada anteriormente para SQL.
   - Defina uma nova política baseada no período de retenção e RPO.

     ![Selecione a política de cópia de segurança](./media/backup-azure-sql-database/select-backup-policy.png)

6. Na **cópia de segurança**, selecione **ativar cópia de segurança**.

    ![Ativar a política de cópia de segurança escolhida](./media/backup-azure-sql-database/enable-backup-button.png)

7. Controlar o progresso da configuração no **notificações** área do portal.

    ![Área de notificação](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Criar uma política de cópia de segurança

Uma política de cópia de segurança define quando os backups são feitos e o período de tempo são retidos.

- É criada uma política ao nível do cofre.
- Vários cofres podem utilizar a mesma política de cópia de segurança, mas tem de aplicar a política de cópia de segurança para cada cofre.
- Quando cria uma política de cópia de segurança, um backup completo diário é o padrão.
- Pode adicionar um backup diferencial, mas apenas se configurar cópias de segurança completas para ocorrer semanalmente.
- Saiba mais sobre [diferentes tipos de políticas de cópia de segurança](backup-architecture.md#sql-server-backup-types).

Para criar uma política de cópia de segurança:

1. No cofre, selecione **políticas de cópia de segurança** > **Add**.
2. Na **Add**, selecione **do SQL Server na VM do Azure** para definir o tipo de política.

   ![Escolha um tipo de política para a nova política de cópia de segurança](./media/backup-azure-sql-database/policy-type-details.png)

3. Na **nome da política**, introduza um nome para a nova política.
4. Na **política de cópia de segurança completa**, selecione um **frequência de cópia de segurança**. Escolhem **diária** ou **semanal**.

   - Para **diária**, selecione a hora e fuso horário quando começa a tarefa de cópia de segurança.
   - Para **semanal**, selecione o dia da semana, hora e fuso horário quando começa a tarefa de cópia de segurança.
   - Executar um backup completo, porque não é possível desativar a **cópia de segurança completa** opção.
   - Selecione **cópia de segurança completa** para ver a política.
   - Não é possível criar cópias de segurança diferenciais para cópias de segurança completas diárias.

     ![novos campos de política de cópia de segurança](./media/backup-azure-sql-database/full-backup-policy.png)  

5. Na **período de retenção**, todas as opções estão selecionadas por predefinição. Limpar qualquer período de retenção limita-se que não pretende e, em seguida, defina os intervalos de usar.

    - Período de retenção mínimo para qualquer tipo de cópia de segurança (completas, diferenciais e log) é de sete dias.
    - Pontos de recuperação são marcados para retenção com base no seu período de retenção. Por exemplo, se selecionar uma cópia de segurança completa diária, apenas uma cópia de segurança completa é acionada por dia.
    - A cópia de segurança para um dia específico é marcado e mantido com base no período de retenção semanais e a definição de retenção semanal.
    - Mensal e anual períodos de retenção se comportam de maneira semelhante.

       ![Definições de intervalo de intervalo de retenção](./media/backup-azure-sql-database/retention-range-interval.png)

6. Na **política de cópia de segurança completa** menu, selecione **OK** para aceitar as configurações.
7. Para adicionar uma política de cópia de segurança diferencial, selecione **cópia de segurança diferencial**.

   ![Definições de intervalo de intervalo de retenção](./media/backup-azure-sql-database/retention-range-interval.png)
   ![abrir o menu de política de cópia de segurança diferencial](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. Na **política de cópia de segurança diferencial**, selecione **ativar** para abrir os controles de frequência e retenção.

    - Pode acionar apenas uma cópia de segurança diferencial por dia.
    - As cópias de segurança diferenciais podem ser mantidas para um máximo de 180 dias. Para retenção mais longa, utilize cópias de segurança completas.

9. Selecione **OK** para guardar a política e retornar o Main **política de cópia de segurança** menu.

10. Para adicionar uma política de cópia de segurança do registo transacional, selecione **cópia de segurança do registo**.
11. Na **registo de cópia de segurança**, selecione **ativar**e, em seguida, defina os controlos de frequência e retenção. Backups de log podem ocorrer com a frequência de 15 minutos e podem ser mantidos até 35 dias.
12. Selecione **OK** para guardar a política e retornar o Main **política de cópia de segurança** menu.

    ![Editar a política de cópia de segurança do registo](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. Sobre o **política de cópia de segurança** menu, escolha se pretende ativar **compressão de cópia de segurança de SQL**.
    - Compressão está desativada por predefinição.
    - No back-end, o Azure Backup utiliza compressão de cópias de segurança nativa SQL.

14. Depois de concluir as edições para a política de cópia de segurança, selecione **OK**.


### <a name="modify-policy"></a>Modificar política
Modificar a política para alterar o intervalo de frequência ou de retenção de cópia de segurança.

> [!NOTE]
> Qualquer alteração no período de retenção será aplicada retrospectively para todos os pontos de recuperação antigos além das novas.

No dashboard do cofre, aceda a **Manage** > **políticas de cópia de segurança** e escolha a política que pretende editar.

  ![Gerir a política de cópia de segurança](./media/backup-azure-sql-database/modify-backup-policy.png)


## <a name="enable-auto-protection"></a>Ativar a proteção automática  

Pode ativar a proteção automática de backup automaticamente todas as bases de dados existentes e futuros para uma instância do SQL Server autónomo ou a um grupo de disponibilidade Always On.

- Não existe nenhum limite no número de bases de dados que pode selecionar para a proteção automática de uma só vez.
- Não é possível proteger ou excluir as bases de dados da proteção numa instância ao tempo que ativar a proteção automática seletivamente.
- Se a sua instância já inclui algumas bases de dados protegidos, será permanecem protegidos sob seus respectivas diretivas, mesmo depois de ativar a proteção automática. Todos os bancos de dados desprotegidos adicionados mais tarde terão apenas uma única política que definir no momento da ativação da proteção automática, listada na **configurar a cópia de segurança**. No entanto, pode alterar a política associada uma base de dados automaticamente protegida mais tarde.  

Para ativar a proteção automática:

  1. Na **itens para cópia de segurança**, selecione a instância para o qual pretende ativar a proteção automática.
  2. Selecione a lista pendente sob **AUTOPROTECT**, escolha **ON**e, em seguida, selecione **OK**.

      ![Ativar a proteção automática no grupo de disponibilidade](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. Cópia de segurança está configurada para todas as bases de dados em conjunto e pode ser controlada no **tarefas de cópia de segurança**.

Se tiver de desativar a proteção automática, selecione o nome da instância sob **configurar a cópia de segurança**e, em seguida, selecione **desativar Autoprotect** para a instância. Todas as bases de dados continuará a ter uma cópia de segurança, mas futuros bases de dados não protegidos automaticamente.

![Desative a proteção automática nessa instância](./media/backup-azure-sql-database/disable-auto-protection.png)

 
## <a name="next-steps"></a>Passos Seguintes

Aprenda a:

- [Restaurar bases de dados do backup do SQL Server](restore-sql-database-azure-vm.md)
- [Gerir bases de dados do backup do SQL Server](manage-monitor-sql-database-backup.md)
