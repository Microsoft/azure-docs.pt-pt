---
title: Fazer cópias de segurança de bases de dados do SQL Server em VMs do Azure
description: Neste artigo, aprenda a fazer backup das bases de dados do SQL Server em máquinas virtuais Azure com Cópia de Segurança Azure.
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 16e24ed94d8017d9fb922193bb16a33ec7a9cdfd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84817541"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Fazer cópias de segurança de bases de dados do SQL Server em VMs do Azure

As bases de dados do SQL Server são cargas de trabalho críticas que requerem um objetivo de baixo ponto de recuperação (RPO) e retenção a longo prazo. Pode fazer backup das bases de dados do SQL Server em funcionamento em máquinas virtuais Azure (VMs) utilizando [o Azure Backup](backup-overview.md).

Este artigo mostra como fazer backup de uma base de dados do SQL Server que está a funcionar num Azure VM para um cofre dos Serviços de Recuperação de Backup Azure.

Neste artigo, aprenderá a:

> [!div class="checklist"]
>
> * Criar e configurar um cofre.
> * Descubra bases de dados e crie backups.
> * Configurar a proteção automática para bases de dados.

>[!NOTE]
>**A exclusão suave para o servidor SQL em Azure VM e a eliminação suave para as cargas de trabalho SAP HANA em Azure VM** já está disponível na pré-visualização.<br>
>Para se inscrever para a pré-visualização, escreva-nos emAskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Pré-requisitos

Antes de fazer uma versão de base de dados do SQL Server, verifique os seguintes critérios:

1. Identifique ou crie um [cofre de Serviços de Recuperação](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) na mesma região e subscrição que o VM que hospeda a instância do SQL Server.
1. Verifique se o VM tem [conectividade de rede.](backup-sql-server-database-azure-vms.md#establish-network-connectivity)
1. Certifique-se de que as bases de dados do SQL Server seguem [as diretrizes de nomeação da base de dados para a Azure Backup](#database-naming-guidelines-for-azure-backup).
1. Certifique-se de que o comprimento combinado do nome VM do SQL Server e o nome do grupo de recursos não excedem 84 caracteres para VMs (ARM) Resource Manager (ou 77 caracteres para VMs clássicos). Esta limitação é porque alguns caracteres são reservados pelo serviço.
1. Verifique se não tem outras soluções de backup ativadas para a base de dados. Desative todas as outras cópias de segurança do SQL Server antes de fazer cópia de segurança na base de dados.

> [!NOTE]
> Pode ativar o Azure Backup para um Azure VM e também para uma base de dados SQL Server que esteja a decorrer no VM sem conflitos.

### <a name="establish-network-connectivity"></a>Estabelecer conectividade de rede

Para todas as operações, um SQL Server VM requer conectividade ao serviço de Backup Azure, Azure Storage e Azure Ative Directory. Isto pode ser conseguido utilizando pontos finais privados ou permitindo o acesso aos endereços IP públicos necessários ou FQDNs. Não permitir a conectividade adequada aos serviços Azure necessários pode levar a falhas em operações como a descoberta de bases de dados, configurar backup, realizar backups e restaurar dados.

A tabela a seguir enumera as várias alternativas que pode utilizar para estabelecer conectividade:

| **Opção**                        | **Vantagens**                                               | **Desvantagens**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Pontos finais privados                 | Permitir cópias de segurança sobre iPs privados dentro da rede virtual  <br><br>   Fornecer controlo granular no lado da rede e do cofre | Incorre [nos custos](https://azure.microsoft.com/pricing/details/private-link/) padrão do ponto final privado |
| Etiquetas de serviço NSG                  | Mais fácil de gerir à medida que as mudanças de alcance são automaticamente fundidas   <br><br>   Sem custos adicionais | Pode ser usado apenas com NSGs  <br><br>    Fornece acesso a todo o serviço |
| Tags FQDN de Firewall Azure Firewall          | Mais fácil de gerir uma vez que os FQDNs necessários são geridos automaticamente | Pode ser usado apenas com Azure Firewall                         |
| Permitir o acesso ao serviço FQDNs/IPs | Sem custos adicionais   <br><br>  Funciona com todos os aparelhos de segurança da rede e firewalls | Um conjunto alargado de IPs ou FQDNs pode ser obrigado a ser acedido   |
| Use um representante HTTP                 | Ponto único de acesso à Internet aos VMs                       | Custos adicionais para executar um VM com o software proxy         |

Mais detalhes sobre a utilização destas opções são partilhados abaixo:

#### <a name="private-endpoints"></a>Pontos finais privados

Os pontos finais privados permitem-lhe ligar-se de forma segura a partir de servidores dentro de uma rede virtual ao cofre dos Serviços de Recuperação. O ponto final privado utiliza um IP a partir do espaço de endereço VNET para o seu cofre. O tráfego de rede entre os seus recursos dentro da rede virtual e o cofre viaja pela sua rede virtual e uma ligação privada na rede de espinha dorsal da Microsoft. Isto elimina a exposição da internet pública. Leia mais sobre os pontos finais privados para o Azure Backup [aqui](https://docs.microsoft.com/azure/backup/private-endpoints).

#### <a name="nsg-tags"></a>Etiquetas NSG

Se utilizar grupos de segurança de rede (NSG), utilize a etiqueta de serviço *AzureBackup* para permitir o acesso de saída ao Azure Backup. Além da etiqueta Azure Backup, também precisa de permitir a conectividade para a autenticação e transferência de dados, criando [regras NSG semelhantes](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) para *Azure AD* e *Azure Storage*.  Os seguintes passos descrevem o processo para criar uma regra para a etiqueta de backup Azure:

1. Em **Todos os Serviços,** vá aos **grupos de segurança da Rede** e selecione o grupo de segurança da rede.

1. Selecione **regras de segurança de saída** em **Definições**.

1. Selecione **Adicionar**. Introduza todos os detalhes necessários para a criação de uma nova regra, conforme descrito nas [definições de regras de segurança](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings). Certifique-se de que a opção **Destino** está definida para tag de serviço *de serviço* e de **destino** está definida para *AzureBackup*.

1. Clique **em Adicionar** para salvar a regra de segurança de saída recém-criada.

Pode igualmente criar regras de segurança de saída NSG para Azure Storage e Azure AD.

#### <a name="azure-firewall-tags"></a>Tags Azure Firewall

Se estiver a utilizar o Azure Firewall, crie uma regra de aplicação utilizando a [etiqueta FQDN Azure Firewall AzureBackup .](https://docs.microsoft.com/azure/firewall/fqdn-tags) *AzureBackup* Isto permite que todo o acesso de saída ao Azure Backup.

#### <a name="allow-access-to-service-ip-ranges"></a>Permitir o acesso às gamas IP de serviço

Se optar por permitir iPs de serviço de acesso, consulte os intervalos IP no ficheiro JSON disponível [aqui](https://www.microsoft.com/download/confirmation.aspx?id=56519). Terá de permitir o acesso aos IPs correspondentes ao Azure Backup, Azure Storage e Azure Ative Directory.

#### <a name="allow-access-to-service-fqdns"></a>Permitir o acesso ao serviço FQDNs

Também pode utilizar os seguintes FQDNs para permitir o acesso aos serviços necessários a partir dos seus servidores:

| Serviço    | Nomes de domínio a serem acedidos                             |
| -------------- | ------------------------------------------------------------ |
| Reserva Azure  | `*.backup.windowsazure.com`                             |
| Armazenamento Azure | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Azure AD      | Permitir o acesso às FQDNs nos termos das secções 56 e 59 de acordo com [este artigo](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) |

#### <a name="use-an-http-proxy-server-to-route-traffic"></a>Use um servidor de procuração HTTP para encaminhar o tráfego

Quando faz uma cópia de segurança de uma base de dados do SQL Server num Azure VM, a extensão de backup no VM utiliza as APIs HTTPS para enviar comandos de gestão para a Azure Backup e dados para o Azure Storage. A extensão de backup também utiliza Azure AD para autenticação. Encaminhe o tráfego de extensão de backup para estes três serviços através do representante HTTP. Utilize a lista de IPs e FQDNs acima mencionadas para permitir o acesso aos serviços necessários. Os servidores de procuração autenticados não são suportados.

### <a name="database-naming-guidelines-for-azure-backup"></a>Diretrizes de nomeação de base de dados para backup Azure

Evite utilizar os seguintes elementos em nomes de bases de dados:

* Espaços de trailing e de liderança
* Marcas de exclamação em fuga (!)
* Fecho dos suportes quadrados (])
* Ponto e vírgula';
* Corte para a frente '/'

O aliasing está disponível para caracteres não suportados, mas recomendamos que os evitem. Para obter mais informações, consulte [Noções Básicas sobre o Modelo de Dados do Serviço Tabela](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).

>[!NOTE]
>A **operação de Proteção de Configuração** para bases de dados com caracteres especiais como "+" ou "&" em seu nome não é suportada. Pode alterar o nome da base de dados ou ativar a **Proteção Automática,** que pode proteger com sucesso estas bases de dados.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Descobrir bases de dados do SQL Server

Como descobrir bases de dados em execução num VM:

1. No [portal Azure,](https://portal.azure.com)abra o cofre dos Serviços de Recuperação que utiliza para fazer o back-up da base de dados.

2. No painel de **abóbada dos Serviços de Recuperação,** selecione **Backup**.

   ![Selecione Backup para abrir o menu Backup Goal](./media/backup-azure-sql-database/open-backup-menu.png)

3. In **Backup Goal**, set Where **Azure**is your **workload running?**

4. No **que pretende fazer cópia de segurança**, selecione **SQL Server em Azure VM**.

    ![Selecione o SQL Server em Azure VM para a cópia de segurança](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. Em **Backup Goal**Discover  >  **DBs em VMs,** selecione Start **Discovery** para procurar VMs desprotegidos na subscrição. Esta pesquisa pode demorar algum tempo, dependendo do número de VMs desprotegidos na subscrição.

   * Os VMs desprotegidos devem aparecer na lista após a descoberta, listados pelo nome e pelo grupo de recursos.
   * Se um VM não está listado como espera, veja se já está num cofre.
   * Vários VMs podem ter o mesmo nome, mas pertencerão a diferentes grupos de recursos.

     ![A cópia de segurança está pendente durante a procura de DBs em VMs](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Na lista VM, selecione o VM que executa a base de dados do SQL Server > **Discover DBs**.

7. Rastreio de descoberta de bases de dados em **Notificações.** O tempo necessário para esta ação depende do número de bases de dados VM. Quando as bases de dados selecionadas são descobertas, aparece uma mensagem de sucesso.

    ![Mensagem de sucesso de implantação](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. O Azure Backup descobre todas as bases de dados do SQL Server no VM. Durante a descoberta, os seguintes elementos ocorrem em segundo plano:

    * A Azure Backup regista o VM com o cofre para cópia de segurança da carga de trabalho. Todas as bases de dados do VM registado só podem ser registadas neste cofre.
    * A Azure Backup instala a extensão AzureBackupWindowsWorkload na VM. Nenhum agente está instalado numa base de dados SQL.
    * A Azure Backup cria a conta de serviço NT Service\AzureWLBackupPluginSvc no VM.
      * Todas as operações de backup e restauro utilizam a conta de serviço.
      * O serviço NT\AzureWLBackupPluginSvc requer permissões de sysadmin SQL. Todos os VMs do Servidor SQL criados no Marketplace vêm com a instalação sqlIaaSExtension. A extensão AzureBackupWindowsWorkload utiliza a extensão SQLIaaSExtension para obter automaticamente as permissões necessárias.
    * Se não criou o VM a partir do Marketplace ou se estiver no SQL 2008 e 2008 R2, o VM pode não ter a SqlIaaSExtension instalada, e a operação de descoberta falha com a mensagem de erro UserErrorSQLNoSysAdminMembership. Para corrigir este problema, siga as instruções sob [as permissões set VM](backup-azure-sql-database.md#set-vm-permissions).

        ![Selecione o VM e a base de dados](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Configurar a cópia de segurança  

1. In **Backup Goal**Step  >  **2: Configure Backup**, selecione **Configure Backup**.

   ![Selecione Configurar Backup](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. Em **Selecionar itens para backup,** consulte todos os grupos de disponibilidade registados e instâncias autónomas do SQL Server. Selecione a seta à esquerda de uma linha para expandir a lista de todas as bases de dados desprotegidas nesse caso ou grupo de disponibilidade Always On.  

    ![Exibindo todas as instâncias do SQL Server com bases de dados autónomas](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Escolha todas as bases de dados que pretende proteger e, em seguida, selecione **OK**.

   ![Proteger a base de dados](./media/backup-azure-sql-database/select-database-to-protect.png)

   Para otimizar as cargas de backup, o Azure Backup define um número máximo de bases de dados num trabalho de backup para 50.

     * Para proteger mais de 50 bases de dados, configuure várias cópias de segurança.
     * Para [ativar](#enable-auto-protection) toda a instância ou o grupo de disponibilidade Always On, na lista de drop-down **AUTOPROTECT,** selecione **ON**e, em seguida, selecione **OK**.

    > [!NOTE]
    > A funcionalidade [de proteção automática](#enable-auto-protection) não só permite a proteção em todas as bases de dados existentes de uma só vez, como também protege automaticamente quaisquer novas bases de dados adicionadas a esse caso ou ao grupo de disponibilidade.  

4. Selecione **OK** para abrir a **política de backup**.

    ![Ativar a proteção automática para o grupo de disponibilidade Always On](./media/backup-azure-sql-database/enable-auto-protection.png)

5. Na **política de backup,** escolha uma política e, em seguida, selecione **OK**.

   * Selecione a política predefinitiva como HourlyLogBackup.
   * Escolha uma política de backup existente previamente criada para o SQL.
   * Defina uma nova política com base no seu RPO e na gama de retenção.

     ![Selecione a política de backup](./media/backup-azure-sql-database/select-backup-policy.png)

6. Em **Cópia de Segurança**, selecione Ative **backup**.

    ![Ativar a política de backup escolhida](./media/backup-azure-sql-database/enable-backup-button.png)

7. Acompanhe o progresso da configuração na área de **Notificações** do portal.

    ![Área de notificação](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Criar uma política de backup

Uma política de backup define quando as cópias de segurança são tomadas e quanto tempo são mantidas.

* Uma política é criada ao nível do cofre.
* Vários cofres podem usar a mesma política de reserva, mas tens de aplicar a política de reserva em cada cofre.
* Quando cria uma política de backup, uma cópia de segurança diária é o padrão.
* Pode adicionar uma cópia de segurança diferencial, mas apenas se configurar cópias de segurança completas para ocorrer semanalmente.
* Conheça [diferentes tipos de políticas de backup.](backup-architecture.md#sql-server-backup-types)

Para criar uma política de backup:

1. No cofre, selecione **políticas de backup**  >  **Adicionar**.
2. Em **Adicionar**, selecione **SQL Server em Azure VM** para definir o tipo de política.

   ![Escolha um tipo de política para a nova política de backup](./media/backup-azure-sql-database/policy-type-details.png)

3. Em **nome da Política,** insira um nome para a nova política.
4. Na **política de cópia de segurança completa,** selecione uma frequência de **backup**. Escolha **o Daily** ou **o Weekly**.

   * Para **diariamente,** selecione o fuso horário e hora quando o trabalho de reserva começar.
   * Para **o Weekly**, selecione o dia da semana, hora e fuso horário quando o trabalho de reserva começar.
   * Faça uma cópia de segurança completa, porque não pode desligar a opção **Full Backup.**
   * Selecione **Full Backup** para ver a política.
   * Não é possível criar cópias de segurança diferenciais para cópias de segurança diárias.

     ![Novos campos de política de backup](./media/backup-azure-sql-database/full-backup-policy.png)  

5. No **INTERVALO DE RETENÇÃO,** todas as opções são selecionadas por predefinição. Limpe os limites de alcance de retenção que não deseja e, em seguida, desa um pouco dos intervalos a utilizar.

    * O período mínimo de retenção para qualquer tipo de backup (completo, diferencial e log) é de sete dias.
    * Os pontos de recuperação são marcados para retenção com base no seu alcance de retenção. Por exemplo, se selecionar uma cópia de segurança diária completa, apenas uma cópia de segurança completa é ativada todos os dias.
    * A cópia de segurança para um dia específico é marcada e mantida com base na gama de retenção semanal e na definição de retenção semanal.
    * As gamas de retenção mensais e anual comportam-se de forma semelhante.

       ![Definições de intervalo de intervalo de retenção](./media/backup-azure-sql-database/retention-range-interval.png)

6. No menu **de política de cópia de segurança completa,** selecione **OK** para aceitar as definições.
7. Para adicionar uma política de backup diferencial, selecione **Backup Diferencial**.

   ![Definições de intervalo de intervalo de retenção ](./media/backup-azure-sql-database/retention-range-interval.png)
    ![ Abra o menu de política de backup diferencial](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. Na **política de backup diferencial,** selecione **Ativar** para abrir os controlos de frequência e retenção.

    * Só pode acionar uma cópia de segurança diferencial por dia.
    * As cópias de segurança diferenciais podem ser mantidas por um máximo de 180 dias. Para uma retenção mais longa, utilize cópias de segurança completas.

9. Selecione **OK** para guardar a política e volte ao menu de política de **backup** principal.

10. Para adicionar uma política de backup de registo de transações, **selecione Registar Backup**.
11. In **Log Backup**, selecione **Ativar**e, em seguida, definir os controlos de frequência e retenção. As cópias de segurança podem ocorrer com a maior frequência a cada 15 minutos e podem ser mantidas até 35 dias.
12. Selecione **OK** para guardar a política e volte ao menu de política de **backup** principal.

    ![Editar a política de backup de registo](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. No menu **de política de cópia de segurança,** escolha se ativa ou não a **compressão de backup SQL.** Esta opção está desativada por predefinição. Se estiver ativado, o SQL Server enviará um fluxo de backup comprimido para o VDI.  Por favor, note que o Azure Backup substitui os padrãos de nível de instância com compression /NO_COMPRESSION cláusula dependendo do valor deste controlo.

14. Depois de completar as edições para a política de backup, selecione **OK**.

> [!NOTE]
> Cada cópia de segurança de registo está acorrentada à cópia de segurança completa anterior para formar uma cadeia de recuperação. Este backup completo será mantido até que a retenção da última cópia de segurança de registo tenha expirado. Isto pode significar que a cópia de segurança completa é retida por um período extra para garantir que todos os registos podem ser recuperados. Vamos assumir que o utilizador tem uma cópia de segurança semanal, diferencial diário e registos de 2 horas. Todos estão retidos por 30 dias. Mas, a totalidade semanal só pode ser limpa/eliminada após a próxima cópia de segurança completa disponível, ou seja, após 30 + 7 dias. Digamos, um reforço semanal completo acontece no dia 16 de novembro. De acordo com a política de retenção, deve ser mantida até 16 de dezembro. O último registo para este registo completo acontece antes do próximo dia 22 de novembro. Até que este registo esteja disponível até 22 de dezembro, o 16 de novembro completo não pode ser apagado. Assim, o 16º de novembro completo é mantido até 22 de dezembro.

## <a name="enable-auto-protection"></a>Ativar a proteção automática  

Pode permitir que a proteção automática faça o back-up automaticamente de todas as bases de dados existentes e futuras para uma instância autónoma do SQL Server ou para um grupo de disponibilidade Always On.

* Não há limite para o número de bases de dados que pode selecionar para proteção automática de cada vez. A descoberta normalmente corre a cada oito horas. No entanto, pode descobrir e proteger novas bases de dados imediatamente se executar manualmente uma descoberta selecionando a opção **Redescobrir DBs.**
* Não é possível proteger ou excluir seletivamente bases de dados de proteção, numa instância no momento em que ativa a proteção automática.
* Se o seu caso já inclui algumas bases de dados protegidas, elas permanecerão protegidas sob as respetivas políticas, mesmo depois de ligar a auto-protecção. Todas as bases de dados desprotegidas adicionadas posteriormente terão apenas uma política que definir no momento de permitir a auto-protecção, listada no âmbito do **Configure Backup**. No entanto, pode alterar a política associada a uma base de dados auto-protegida mais tarde.  

Para permitir a auto-protecção:

  1. Em **Itens para fazer cópia de segurança,** selecione a instância para a qual pretende ativar a proteção automática.
  2. Selecione a lista de drop-down em **AUTOPROTECT,** escolha **ON**e, em seguida, selecione **OK**.

      ![Permitir a proteção automática no grupo de disponibilidade](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. A cópia de segurança está configurada para todas as bases de dados em conjunto e pode ser rastreada em **Backup Jobs**.

Se precisar de desativar a proteção automática, selecione o nome da instância em **"Configurar Backup"** e, em seguida, selecione **Desativar** o Protecto Automático para o caso. Todas as bases de dados continuarão a ser apoiadas, mas as futuras bases de dados não serão automaticamente protegidas.

![Desativar a proteção automática nesse caso](./media/backup-azure-sql-database/disable-auto-protection.png)

## <a name="next-steps"></a>Próximos passos

Aprenda a:

* [Restaurar bases de dados de servidores SQL com ressarçamento](restore-sql-database-azure-vm.md)
* [Gerir bases de dados de servidores SQL com apoio](manage-monitor-sql-database-backup.md)
