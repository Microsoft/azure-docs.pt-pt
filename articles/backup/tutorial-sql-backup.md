---
title: Tutorial - Restruda as bases de dados do SQL Server para O Azure
description: Neste tutorial, aprenda a fazer backup de uma base de dados do SQL Server que funciona num Azure VM para um cofre dos Serviços de Recuperação de Backup Azure.
ms.topic: tutorial
ms.date: 06/18/2019
ms.openlocfilehash: 17a8472da2595c08cb198baaf853faf110a619fa
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88612457"
---
# <a name="back-up-a-sql-server-database-in-an-azure-vm"></a>Ressarça uma base de dados do SQL Server num VM Azure

Este tutorial mostra-lhe como fazer backup de uma base de dados do SQL Server que funciona num Azure VM para um cofre dos Serviços de Recuperação de Backup Azure. Neste artigo, vai aprender a:

> [!div class="checklist"]
>
> * Criar e configurar um cofre.
> * Descubra bases de dados e crie backups.
> * Configurar a proteção automática para bases de dados.
> * Faça uma cópia de segurança a pedido.

## <a name="prerequisites"></a>Pré-requisitos

Antes de fazer uma reserva de back-up na base de dados do SQL Server, verifique as seguintes condições:

1. Identifique ou [crie](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) um cofre de Serviços de Recuperação na mesma região ou local que o VM que hospeda a instância do SQL Server.
2. [Verifique as permissões VM necessárias](backup-azure-sql-database.md#set-vm-permissions) para fazer o back up das bases de dados SQL.
3. Verifique se o VM tem [conectividade de rede.](backup-sql-server-database-azure-vms.md#establish-network-connectivity)
4. Verifique se as bases de dados do SQL Server estão nomeadas de acordo com [as diretrizes](#verify-database-naming-guidelines-for-azure-backup) de nomeação para Azure Backup.
5. Verifique se não tem outras soluções de backup ativadas para a base de dados. Desative todas as cópias de segurança do SQL Server antes de configurar este cenário. Pode ativar o Azure Backup para um Azure VM juntamente com a Azure Backup para uma base de dados SQL Server que funciona no VM sem qualquer conflito.

### <a name="establish-network-connectivity"></a>Estabelecer conectividade de rede

Para todas as operações, a máquina virtual SQL Server VM necessita de conectividade com endereços IP públicos Azure. As operações VM (descoberta de base de dados, configuração de backups, cópias de segurança de agenda, restaurar pontos de recuperação, e assim por diante) falham sem conectividade com os endereços IP públicos. Estabelecer conectividade com uma destas opções:

* **Permitir os intervalos IP do datacenter Azure**: Permitir as [gamas IP](https://www.microsoft.com/download/details.aspx?id=41653) no download. Para aceder ao grupo de segurança da rede (NSG), utilize o **cmdlet Set-AzureNetworkSecurityRule.**
* **Implementar um servidor de procuração HTTP para encaminhar o tráfego**: Quando faz cópia de segurança de uma base de dados do SQL Server num Azure VM, a extensão de backup no VM utiliza as APIs HTTPS para enviar comandos de gestão para Azure Backup e dados para O Azure Storage. A extensão de backup também utiliza o Azure Ative Directory (Azure AD) para autenticação. Encaminhe o tráfego de extensão de backup para estes três serviços através do representante HTTP. As extensões são o único componente configurado para acesso à internet pública.

Cada opção tem vantagens e desvantagens

**Opção** | **Vantagens** | **Desvantagens**
--- | --- | ---
Permitir gamas IP | Sem custos adicionais. | Complexo para gerir porque os intervalos de endereço IP mudam ao longo do tempo. <br/><br/> Fornece acesso a todo o Azure, não apenas ao Azure Storage.
Use um representante HTTP   | É permitido o controlo granular no proxy sobre os URLs de armazenamento. <br/><br/> Único ponto de acesso à Internet aos VMs. <br/><br/> Não sujeito a alterações no endereço IP do Azure. | Custos adicionais para executar um VM com o software proxy.

### <a name="set-vm-permissions"></a>Definir permissões VM

O Azure Backup faz uma série de coisas quando configura a cópia de segurança para uma base de dados do SQL Server:

* Adiciona a extensão **AzureBackUpWindowsWorkload.**
* Para descobrir bases de dados na máquina virtual, o Azure Backup cria a conta **NT SERVICE\AzureWLBackupPluginSvc**. Esta conta é usada para cópia de segurança e restauro, e requer permissões de sysadmin SQL.
* O Azure Backup aproveita a conta **NT AUTHORITY\SYSTEM** para a descoberta/inquérito da base de dados, pelo que esta conta tem de ser um login público no SQL.

Se não criou o SQL Server VM a partir do Azure Marketplace, poderá receber um erro **UserErrorSQLNoSysadminMembership**. Se isto [ocorrer, siga estas instruções](backup-azure-sql-database.md#set-vm-permissions).

### <a name="verify-database-naming-guidelines-for-azure-backup"></a>Verifique as diretrizes de nomeação da base de dados para a Azure Backup

Evite os seguintes nomes de bases de dados:

* Espaços de trailing/leading
* Trailing '!
* Fechar o suporte quadrado ']'
* Nomes de bases de dados a começar por 'F:\'

Temos um pseudónimo para caracteres não apoiados da mesa Azure, mas recomendamos que os evitem. [Saiba mais](/rest/api/storageservices/understanding-the-table-service-data-model).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Descobrir bases de dados do SQL Server

Descubra bases de dados em execução no VM.

1. No [portal Azure,](https://portal.azure.com)abra o cofre dos Serviços de Recuperação que utiliza para fazer o back-up da base de dados.

2. No painel de **abóbada dos Serviços de Recuperação,** selecione **Backup**.

   ![Selecione Backup para abrir o menu Backup Goal](./media/backup-azure-sql-database/open-backup-menu.png)

3. Em **Backup Goal**, set Where is your workload **running** to **Azure** (o padrão).

4. No **que pretende fazer cópia de segurança**, selecione **SQL Server em Azure VM**.

    ![Selecione o SQL Server em Azure VM para a cópia de segurança](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. Em **Backup Goal**Discover  >  **DBs em VMs,** selecione Start **Discovery** para procurar VMs desprotegidos na subscrição. Pode demorar um pouco, dependendo do número de máquinas virtuais desprotegidas na subscrição.

   * Os VMs desprotegidos devem aparecer na lista após a descoberta, listados pelo nome e pelo grupo de recursos.
   * Se um VM não está listado como espera, verifique se já está num cofre.
   * Vários VMs podem ter o mesmo nome, mas pertencerão a diferentes grupos de recursos.

     ![A cópia de segurança está pendente durante a procura de DBs em VMs](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Na lista VM, selecione o VM que executa a base de dados do SQL Server > **Discover DBs**.

7. Rastreie a descoberta da base de dados na área **de Notificações.** Pode levar algum tempo até que o trabalho esteja concluído, dependendo de quantas bases de dados estão no VM. Quando as bases de dados selecionadas são descobertas, aparece uma mensagem de sucesso.

    ![Mensagem de sucesso de implantação](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. O Azure Backup descobre todas as bases de dados do SQL Server no VM. Durante a descoberta, o seguinte ocorre em segundo plano:

    * Azure Backup regista o VM com o cofre para cópia de segurança de carga de trabalho. Todas as bases de dados do VM registado só podem ser apoiadas até este cofre.
    * A Azure Backup instala a extensão **AzureBackupWindowsWorkload** na VM. Nenhum agente está instalado na base de dados SQL.
    * A Azure Backup cria a conta de serviço **NT Service\AzureWLBackupPluginSvc** no VM.
      * Todas as operações de backup e restauro utilizam a conta de serviço.
      * **O serviço NT\AzureWLBackupPluginSvc** necessita de permissões sysadmin SQL. Todos os VMs do SQL Server criados no Azure Marketplace vêm com a **instalação sqlIaaSExtension.** A extensão **AzureBackupWindowsWorkload** utiliza a **extensão SQLIaaSExtension** para obter automaticamente as permissões necessárias.
    * Se não criou o VM a partir do mercado, então o VM não tem a **SqlIaaSExtension** instalada, e a operação de descoberta falha com a mensagem de erro **UserErrorSQLNoSysAdminMembership**. Siga as [instruções](backup-azure-sql-database.md#set-vm-permissions) para corrigir este problema.

        ![Selecione o VM e a base de dados](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Configurar a cópia de segurança  

Configure a cópia de segurança da seguinte forma:

1. In **Backup Goal**, selecione **Configure Backup**.

   ![Selecione Configurar Backup](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. Selecione **Configurar a cópia de segurança,** aparecem os **itens selecionador** para o painel de segurança. Isto lista todos os grupos de disponibilidade registados e servidores SQL autónomos. Expanda o chevron para a esquerda da fila para ver todas as bases de dados desprotegidas nesse caso ou sempre em AG.  

    ![Exibindo todas as instâncias do SQL Server com bases de dados autónomas](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Selecione todas as bases de dados que pretende proteger > **OK**.

   ![Proteger a base de dados](./media/backup-azure-sql-database/select-database-to-protect.png)

   Para otimizar as cargas de backup, o Azure Backup define um número máximo de bases de dados num trabalho de backup para 50.

     * Em alternativa, pode ativar a proteção automática em todo o caso ou grupo Always On Availability selecionando a opção **ON** na correspondente redução na coluna **AUTOPROTECT.** A funcionalidade de proteção automática não só permite a proteção em todas as bases de dados existentes de uma só vez, como também protege automaticamente quaisquer novas bases de dados que venham a ser adicionadas a esse caso ou ao grupo de disponibilidade no futuro.  

4. Selecione **OK** para abrir o painel **de política de backup.**

    ![Ativar a proteção automática no grupo de disponibilidade Always On](./media/backup-azure-sql-database/enable-auto-protection.png)

5. Na **política de backup,** selecione uma política e, em seguida, selecione **OK**.

   * Selecione a política predefinitiva: HourlyLogBackup.
   * Escolha uma política de backup existente previamente criada para o SQL.
   * Defina uma nova política com base no seu RPO e na gama de retenção.

     ![Selecione a política de backup](./media/backup-azure-sql-database/select-backup-policy.png)

6. No menu **backup,** **selecione Ative backup**.

    ![Ativar a política de backup escolhida](./media/backup-azure-sql-database/enable-backup-button.png)

7. Acompanhe o progresso da configuração na área de **Notificações** do portal.

    ![Área de notificação](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Criar uma política de backup

Uma política de backup define quando as cópias de segurança são tomadas e quanto tempo são mantidas.

* Uma política é criada ao nível do cofre.
* Vários cofres podem usar a mesma política de reserva, mas tens de aplicar a política de reserva em cada cofre.
* Quando cria uma política de backup, uma cópia de segurança diária é o padrão.
* Pode adicionar uma cópia de segurança diferencial, mas apenas se configurar cópias de segurança completas para ocorrer semanalmente.
* [Conheça](backup-architecture.md#sql-server-backup-types) diferentes tipos de políticas de backup.

Para criar uma política de backup:

1. No cofre, selecione **políticas de backup**  >  **Adicionar**.
2. No menu **Adicionar,** selecione **SQL Server em Azure VM** para definir o tipo de política.

   ![Escolha um tipo de política para a nova política de backup](./media/backup-azure-sql-database/policy-type-details.png)

3. Em **nome da Política,** insira um nome para a nova política.
4. Na **política de cópia de segurança completa**, selecione uma frequência de **backup**, escolha **diariamente** ou **semanalmente**.

   * Para **diariamente,** selecione o fuso horário e hora quando o trabalho de reserva começar.
   * Tem de executar uma cópia de segurança completa, uma vez que não pode desligar a opção **Full Backup.**
   * Selecione **Full Backup** para ver a política.
   * Não é possível criar cópias de segurança diferenciais para cópias de segurança diárias.
   * Para **o Weekly**, selecione o dia da semana, hora e fuso horário quando o trabalho de reserva começar.

     ![Novos campos de política de backup](./media/backup-azure-sql-database/full-backup-policy.png)  

5. Para **o Intervalo de Retenção,** por predefinição todas as opções são selecionadas. Limpe quaisquer limites de alcance de retenção não utilizados que não queira utilizar e desemprova os intervalos a utilizar.

    * O período mínimo de retenção para qualquer tipo de backup (completo/diferencial/log) é de sete dias.
    * Os pontos de recuperação são marcados para retenção com base no seu alcance de retenção. Por exemplo, se selecionar uma cópia de segurança diária completa, apenas uma cópia de segurança completa é ativada todos os dias.
    * A cópia de segurança para um dia específico é marcada e mantida com base na gama de retenção semanal e na sua definição de retenção semanal.
    * As gamas de retenção mensais e anual comportam-se de forma semelhante.

   ![Definições de intervalo de intervalo de retenção](./media/backup-azure-sql-database/retention-range-interval.png)

6. No menu **de política de cópia de segurança completa,** selecione **OK** para aceitar as definições.
7. Para adicionar uma política de backup diferencial, selecione **Backup Diferencial**.

   ![Definições de intervalo de intervalo de retenção ](./media/backup-azure-sql-database/retention-range-interval.png)
    ![ Abra o menu de política de backup diferencial](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. Na **política de backup diferencial,** selecione **Ativar** para abrir os controlos de frequência e retenção.

    * No máximo, pode acionar uma cópia de segurança diferencial por dia.
    * As cópias de segurança diferenciais podem ser mantidas por um máximo de 180 dias. Se precisar de uma retenção mais longa, deve utilizar cópias de segurança completas.

9. Selecione **OK** para guardar a política e volte ao menu de política de **backup** principal.

10. Para adicionar uma política de backup de registo de transações, **selecione Registar Backup**.
11. In **Log Backup**, selecione **Ativar**e, em seguida, definir os controlos de frequência e retenção. As cópias de segurança podem ocorrer com a maior frequência a cada 15 minutos, podendo ser mantidas até 35 dias.
12. Selecione **OK** para guardar a política e volte ao menu de política de **backup** principal.

    ![Editar a política de backup de registo](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. No menu **de política de cópia de segurança,** escolha se ativa a **compressão de backup SQL**.
    * A compressão é desativada por defeito.
    * Na parte traseira, o Azure Backup utiliza a compressão de backup nativa SQL.

14. Depois de completar as edições para a política de backup, selecione **OK**.

## <a name="run-an-on-demand-backup"></a>Faça uma cópia de segurança a pedido

1. No cofre dos Serviços de Recuperação, escolha itens de reserva.
2. Selecione "SQL in Azure VM".
3. Clique com o direito numa base de dados e escolha "Backup now".
4. Escolha o tipo de cópia de segurança (Full/Diferencial/Log/Copy Only Full) e Compressão (Ativar/Desativar)
5. Selecione OK para iniciar a cópia de segurança.
6. Monitorize o trabalho de backup indo ao cofre dos Serviços de Recuperação e escolhendo "Backup Jobs".

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, utilizou o portal do Azure para:

> [!div class="checklist"]
>
> * Criar e configurar um cofre.
> * Descubra bases de dados e crie backups.
> * Configurar a proteção automática para bases de dados.
> * Faça uma cópia de segurança a pedido.

Avance para o próximo tutorial para restaurar uma máquina virtual do Azure a partir do disco.

> [!div class="nextstepaction"]
> [Restaurar bases de dados do SQL Server em VMs Azure](./restore-sql-database-azure-vm.md)
