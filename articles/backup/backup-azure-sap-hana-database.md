---
title: Faça uma base de dados SAP HANA para Azure com Backup Azure
description: Neste artigo, aprenda a fazer backup de uma base de dados SAP HANA para máquinas virtuais Azure com o serviço De backup Azure.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: deedd4d2553b3b06f76f698fdb2425a8d3878d23
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79248063"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>Fazer cópias de segurança de bases de dados SAP HANA nas VMs do Azure

As bases de dados SAP HANA são cargas de trabalho críticas que requerem um objetivo de ponto de recuperação baixo (RPO) e retenção a longo prazo. Pode fazer backup nas bases de dados SAP HANA em execução em máquinas virtuais Azure (VMs) utilizando [o Azure Backup](backup-overview.md).

Este artigo mostra como apoiar as bases de dados DaAP HANA que estão a funcionar em VMs Azure para um cofre dos Serviços de Recuperação de Backup Azure.

Neste artigo, aprenderá a:
> [!div class="checklist"]
>
> * Criar e configurar um cofre
> * Descubra bases de dados
> * Configurar cópias de segurança
> * Faça um trabalho de reserva a pedido

>[!NOTE]
>**A eliminação suave para o servidor SQL em Azure VM e eliminação suave para SAP HANA em cargas de trabalho VM Azure** já está disponível na pré-visualização.<br>
>Para se inscrever para a pré-visualização, escreva-nos emAskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Pré-requisitos

Consulte os [pré-requisitos](tutorial-backup-sap-hana-db.md#prerequisites) e o [que o script de pré-registo faz](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) secções para configurar a base de dados para cópia de segurança.

### <a name="set-up-network-connectivity"></a>Configurar a conectividade da rede

Para todas as operações, o VM SAP HANA requer conectividade com endereços IP públicos do Azure. As operações vM (descoberta de bases de dados, configurar backups, agendar backups, restaurar pontos de recuperação, e assim por diante) falham sem conectividade com endereços IP públicos do Azure.

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

**Implemente um servidor proxy HTTP para encaminhar**o tráfego . Quando faz o backup de uma base de dados SAP HANA num VM Azure, a extensão de backup do VM utiliza as APIs HTTPS para enviar comandos de gestão para Backup Azure e dados para o Armazenamento Azure. A extensão de reserva também utiliza AD Azure para autenticação. Encaminhe o tráfego de extensão de reserva para estes três serviços através do proxy HTTP. As extensões são o único componente configurado para o acesso à internet pública.

As opções de conectividade incluem as seguintes vantagens e desvantagens:

**Opção** | **Vantagens** | **Desvantagens**
--- | --- | ---
Permitir gamas IP | Sem custos adicionais | Complexo de gerir porque as gamas de endereços IP mudam ao longo do tempo <br/><br/> Dá acesso a todo o Azure, e não apenas ao Armazenamento Azure
Utilize etiquetas de serviço NSG | Mais fácil de gerir à medida que as alterações de gama são automaticamente fundidas <br/><br/> Sem custos adicionais <br/><br/> | Pode ser usado apenas com NSGs <br/><br/> Fornece acesso a todo o serviço
Utilize tags FQDN de firewall Azure | Mais fácil de gerir à medida que as FQDNs necessárias são geridas automaticamente | Pode ser usado apenas com Firewall Azure
Use um proxy HTTP | É permitido o controlo granular no proxy sobre os URLs de armazenamento <br/><br/> Ponto único de acesso à Internet aos VMs <br/><br/> Não sujeito a alterações de endereço IP do Azure | Custos adicionais para executar um VM com o software proxy

#### <a name="private-endpoints"></a>Pontos Finais Privados

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Descubra as bases de dados

1. No cofre, em **Getting Started,** clique em **Backup**. Em Onde está a funcionar **SAP HANA in Azure VM**a **sua carga de trabalho?**
2. Clique em **Iniciar Descoberta**. Isto inicia a descoberta de VMs linux desprotegidos na região do cofre.

   * Após a descoberta, VMs desprotegidos aparecem no portal, listados pelo nome e pelo grupo de recursos.
   * Se um VM não está listado como esperado, verifique se já está apoiado num cofre.
   * Vários VMs podem ter o mesmo nome, mas pertencem a diferentes grupos de recursos.

3. Em **Select Virtual Machines,** clique no link para descarregar o script que fornece permissões para o serviço de backup Azure aceder aos VMs SAP HANA para descoberta de base de dados.
4. Execute o script em cada VM que aloja as bases de dados SAP HANA que pretende fazer o apoio.
5. Depois de executar o script nos VMs, em **Select Virtual Machines,** selecione os VMs. Em seguida, clique em **Descobrir DBs**.
6. O Azure Backup descobre todas as bases de dados da SAP HANA no VM. Durante a descoberta, o Azure Backup regista o VM com o cofre e instala uma extensão no VM. Nenhum agente está instalado na base de dados.

    ![Descubra as bases de dados SAP HANA](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Configurar a cópia de segurança  

Agora, ative a reserva.

1. No passo 2, clique em **Configurar Cópia de Segurança**.

    ![Configurar a Cópia de Segurança](./media/backup-azure-sap-hana-database/configure-backup.png)
2. Em **itens Selecionados para fazer o seu trabalho de remarcação,** selecione todas as bases de dados que pretende proteger > **OK**.

    ![Selecione itens para fazer back-up](./media/backup-azure-sap-hana-database/select-items.png)
3. Na **Política** > de Backup**Escolha a política**de backup , crie uma nova política de backup para as bases de dados, de acordo com as instruções abaixo.

    ![Escolha a política de backup](./media/backup-azure-sap-hana-database/backup-policy.png)
4. Depois de criar a política, no menu **'Backup',** clique em **ativar a cópia de segurança**.

    ![Ativar a cópia de segurança](./media/backup-azure-sap-hana-database/enable-backup.png)
5. Acompanhe o progresso da configuração de backup na área de **Notificações** do portal.

### <a name="create-a-backup-policy"></a>Criar uma política de backup

Uma política de reserva define quando os backups são tomados, e quanto tempo são retidos.

* Uma apólice é criada ao nível do cofre.
* Vários cofres podem usar a mesma política de reserva, mas tens de aplicar a política de reserva a cada cofre.

Especifique as definições de política da seguinte forma:

1. Em **nome de Política,** insira um nome para a nova política.

   ![Insira o nome da política](./media/backup-azure-sap-hana-database/policy-name.png)
2. Na **política de backup completo,** selecione uma Frequência de **Backup,** escolha **Diariamente** ou **Semanalmente**.
   * **Diariamente:** Selecione a hora e o fuso horário no qual começa o trabalho de reserva.
       * Tens de fazer um reforço completo. Não pode desligar esta opção.
       * Clique em **Backup Completo** para ver a política.
       * Não se pode criar backups diferenciais para cópias de segurança diárias completas.
   * **Semanalmente**: Selecione o dia da semana, hora e fuso horário no qual o trabalho de reserva funciona.

   ![Selecione frequência de backup](./media/backup-azure-sap-hana-database/backup-frequency.png)

3. No **Intervalo de Retenção,** configure as definições de retenção para a cópia de segurança completa.
    * Por padrão, todas as opções são selecionadas. Limpe os limites de alcance de retenção que não queira usar e desloque os que faz.
    * O período mínimo de retenção para qualquer tipo de cópia de segurança (full/diferencial/registo) é de sete dias.
    * Os pontos de recuperação estão marcados para retenção com base na sua gama de retenção. Por exemplo, se selecionar uma cópia de segurança completa diária, apenas um backup completo é acionado todos os dias.
    * A cópia de segurança para um dia específico é marcada e mantida com base na gama e configuração semanais de retenção.
    * As gamas de retenção mensal e anual comportam-se de forma semelhante.

4. No menu de **política de backup completo,** clique em **OK** para aceitar as definições.
5. Selecione **Backup Diferencial** para adicionar uma política diferencial.
6. Na **política de backup diferencial,** selecione **Ativar** para abrir os controlos de frequência e retenção.
    * No máximo, pode desencadear uma cópia de segurança diferencial por dia.
    * As cópias de segurança diferenciais podem ser retidas por um máximo de 180 dias. Se necessitar de retenção mais longa, deve utilizar cópias de segurança completas.

    ![Política de backup diferencial](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > As cópias de segurança incrementais não são suportadas atualmente.

7. Clique em **OK** para salvar a apólice e volte ao menu principal de política de **backup.**
8. Selecione **'Log Backup'** para adicionar uma política de backup de registo transacional,
    * No **Log Backup,** selecione **Ativar**.  Isto não pode ser desativado uma vez que o SAP HANA gere todas as cópias de segurança de registo.
    * Detete os controlos de frequência e retenção.

    > [!NOTE]
    > As cópias de segurança de log só começam a fluir depois de uma cópia de segurança completa bem sucedida ser concluída.

9. Clique em **OK** para salvar a apólice e volte ao menu principal de política de **backup.**
10. Depois de terminar de definir a política de backup, **clique**ok .

> [!NOTE]
> Cada cópia de segurança de registo é acorrentada à cópia de segurança completa anterior para formar uma cadeia de recuperação. Esta cópia de segurança completa será retida até que a retenção da última cópia de segurança tenha expirado. Isto pode significar que a cópia de segurança completa é retida por um período extra para se certificar de que todos os registos podem ser recuperados. Vamos assumir que o utilizador tem uma cópia de segurança semanal completa, diferencial diário e registos de 2 horas. Todos estão retidos por 30 dias. Mas, o todo semanal só pode ser limpo/apagado após a próxima cópia de segurança completa estar disponível, ou seja, após 30 + 7 dias. Digamos, um reforço semanal acontece no dia 16 de novembro. De acordo com a política de retenção, deve ser mantida até 16 de dezembro. O último registo de reserva para este completo acontece antes do próximo programado completo, no dia 22 de novembro. Até que este registo esteja disponível até 22 de dezembro, o 16 de novembro completo não pode ser apagado. Assim, o 16º de novembro completo é mantido até 22 de dezembro.

## <a name="run-an-on-demand-backup"></a>Faça um backup a pedido

Os backups funcionam de acordo com o calendário de apólices. Pode executar um backup a pedido da seguinte forma:

1. No menu do cofre, clique em **itens de backup**.
2. Em Itens de **Backup,** selecione o VM que executa a base de dados SAP HANA e, em seguida, clique em **Backup agora**.
3. Em **Backup Now,** utilize o controlo do calendário para selecionar o último dia em que o ponto de recuperação deve ser mantido. Em seguida, clique em **OK**.
4. Monitorize as notificações do portal. Você pode monitorizar o progresso do trabalho no painel de instrumentos do cofre > **Backup Jobs** > **Em andamento.** Dependendo do tamanho da sua base de dados, a criação da cópia de segurança inicial pode demorar algum tempo.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Executar backup do Estúdio SAP HANA numa base de dados com backup Azure ativado

Se quiser obter uma cópia de segurança local (usando o HANA Studio) de uma base de dados que está a ser apoiada com o Azure Backup, faça o seguinte:

1. Aguarde quaisquer cópias de segurança completas ou de registo para que a base de dados termine. Verifique o estado no Estúdio SAP HANA / Cockpit.
2. Desative as cópias de segurança e desative as cópias de segurança e desative o catálogo de cópias de segurança no sistema de ficheiros para uma base de dados relevante.
3. Para isso, clique duas vezes na**configuração** > do **sistema,** > **selecione** > filtro de base de dados **(Log)**.
4. Definir **enable_auto_log_backup** para **Nº**.
5. Coloque **log_backup_using_backint** a **Falso**.
6. Pegue uma cópia de segurança completa da base de dados.
7. Aguarde o backup completo e o backup do catálogo termine.
8. Reverta as definições anteriores de volta às do Azure:
    * Definir **enable_auto_log_backup** para **Sim.**
    * Definir **log_backup_using_backint** para **true**.

## <a name="next-steps"></a>Passos seguintes

* Saiba como restaurar as bases de [dados SAP HANA em execução em VMs Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Saiba como gerir as bases de [dados SAP HANA que são apoiadas usando o Azure Backup](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
