---
title: Fazer backup de um banco de dados SAP HANA no Azure com o backup do Azure
description: Neste artigo, saiba como fazer backup de um banco de dados SAP HANA em máquinas virtuais do Azure com o serviço de backup do Azure.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: ed47f18c9dabc685d6fbe02804562ef86a93190a
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74285848"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>Fazer cópias de segurança de bases de dados SAP HANA nas VMs do Azure

Os bancos de dados do SAP HANA são cargas de trabalho críticas que exigem um RPO (objetivo de ponto de recuperação) baixo e retenção de longo prazo. Você pode fazer backup de bancos de dados SAP HANA em execução em VMs (máquinas virtuais) do Azure usando o [backup do Azure](backup-overview.md).

Este artigo mostra como fazer backup de SAP HANA bancos de dados que estão em execução em VMs do Azure para um cofre dos serviços de recuperação de backup do Azure.

Neste artigo, ficará a saber como:
> [!div class="checklist"]
>
> * Criar e configurar um cofre
> * Descobrir bancos de dados
> * Configurar backups
> * Executar um trabalho de backup sob demanda

## <a name="prerequisites"></a>Pré-requisitos

Consulte as seções [pré-requisitos](tutorial-backup-sap-hana-db.md#prerequisites) e [Configurando permissões](tutorial-backup-sap-hana-db.md#setting-up-permissions) para configurar o banco de dados para backup.

### <a name="set-up-network-connectivity"></a>Configurar a conectividade de rede

Para todas as operações, a VM SAP HANA precisa de conectividade com os endereços IP públicos do Azure. As operações de VM (descoberta de banco de dados, configurar backups, agendar backups, restaurar pontos de recuperação e assim por diante) não funcionam sem conectividade. Estabeleça a conectividade permitindo o acesso aos intervalos de IP do datacenter do Azure:

* Você pode baixar os [intervalos de endereços IP](https://www.microsoft.com/download/details.aspx?id=41653) para data centers do Azure e, em seguida, permitir o acesso a esses endereços IP.
* Se você estiver usando NSGs (grupos de segurança de rede), poderá usar a [marca de serviço](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) AzureCloud para permitir todos os endereços IP públicos do Azure. Você pode usar o [cmdlet Set-AzureNetworkSecurityRule](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0) para modificar as regras de NSG.
* A porta 443 deve ser adicionada à lista de permissões, pois o transporte é via HTTPS.

## <a name="onboard-to-the-public-preview"></a>Integração à visualização pública

Integre à visualização pública da seguinte maneira:

* No portal, registre sua ID de assinatura para o provedor de serviço de serviços de recuperação [seguindo este artigo](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal).
* Para o PowerShell, execute este cmdlet. Ele deve ser concluído como "registrado".

    ```powershell
    Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Descobrir os bancos de dados

1. No cofre, em **introdução**, clique em **backup**. Em **onde sua carga de trabalho está em execução?** , selecione **SAP Hana na VM do Azure**.
2. Clique em **Iniciar descoberta**. Isso inicia a descoberta de VMs do Linux desprotegidas na região do cofre.

   * Após a descoberta, as VMs desprotegidas aparecem no portal, listadas por nome e grupo de recursos.
   * Se uma VM não estiver listada como esperado, verifique se ela já foi submetida a backup em um cofre.
   * Várias VMs podem ter o mesmo nome, mas pertencem a grupos de recursos diferentes.

3. Em **selecionar máquinas virtuais**, clique no link para baixar o script que fornece permissões para o serviço de backup do Azure acessar as VMs de SAP Hana para descoberta de banco de dados.
4. Execute o script em cada VM que hospeda SAP HANA bancos de dados dos quais você deseja fazer backup.
5. Depois de executar o script nas VMs, em **selecionar máquinas virtuais**, selecione as VMs. Em seguida, clique em **descobrir bancos**de os.
6. O backup do Azure descobre todos os bancos de dados SAP HANA na VM. Durante a descoberta, o backup do Azure registra a VM com o cofre e instala uma extensão na VM. Nenhum agente está instalado no banco de dados.

    ![Descobrir bancos de dados SAP HANA](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Configurar a cópia de segurança  

Agora, habilite o backup.

1. Na etapa 2, clique em **Configurar backup**.

    ![Configurar Backup](./media/backup-azure-sap-hana-database/configure-backup.png)
2. Em **selecionar itens para fazer backup**, selecione todos os bancos de dados que você deseja proteger > **OK**.

    ![Selecionar itens para backup](./media/backup-azure-sap-hana-database/select-items.png)
3. Em **política de backup** > **escolha política de backup**, crie uma nova política de backup para os bancos de dados, de acordo com as instruções abaixo.

    ![Escolher política de backup](./media/backup-azure-sap-hana-database/backup-policy.png)
4. Depois de criar a política, no menu **backup** , clique em **habilitar backup**.

    ![Habilitar backup](./media/backup-azure-sap-hana-database/enable-backup.png)
5. Acompanhe o progresso da configuração de backup na área de **notificações** do Portal.

### <a name="create-a-backup-policy"></a>Criar uma política de backup

Uma política de backup define quando os backups são feitos e por quanto tempo eles são retidos.

* Uma política é criada no nível do cofre.
* Vários cofres podem usar a mesma política de backup, mas você deve aplicar a política de backup a cada cofre.

Especifique as configurações de política da seguinte maneira:

1. Em **nome da política**, insira um nome para a nova política.

   ![Inserir nome da política](./media/backup-azure-sap-hana-database/policy-name.png)
2. Em **política de backup completo**, selecione uma **frequência de backup**, escolha **diária** ou **semanal**.
   * **Diário**: selecione a hora e o fuso horário em que o trabalho de backup começa.
       * Você deve executar um backup completo. Você não pode desativar essa opção.
       * Clique em **backup completo** para exibir a política.
       * Não é possível criar backups diferenciais para backups diários completos.
   * **Semanal**: selecione o dia da semana, hora e fuso horário em que o trabalho de backup é executado.

   ![Selecionar frequência de backup](./media/backup-azure-sap-hana-database/backup-frequency.png)

3. Em **período de retenção**, defina as configurações de retenção para o backup completo.
    * Por padrão, todas as opções são selecionadas. Desmarque os limites de período de retenção que você não deseja usar e defina os que você faz.
    * O período de retenção mínimo para qualquer tipo de backup (completo/diferencial/log) é de sete dias.
    * Os pontos de recuperação são marcados para retenção com base em seu período de retenção. Por exemplo, se você selecionar um backup completo diário, apenas um backup completo será disparado todos os dias.
    * O backup de um dia específico é marcado e mantido com base no período de retenção semanal e na configuração.
    * Os intervalos de retenção mensais e anuais se comportam de maneira semelhante.

4. No menu **política de backup completo** , clique em **OK** para aceitar as configurações.
5. Selecione **backup diferencial** para adicionar uma política diferencial.
6. Em **política de backup diferencial**, selecione **habilitar** para abrir os controles de frequência e retenção.
    * No máximo, você pode disparar um backup diferencial por dia.
    * Backups diferenciais podem ser retidos por um máximo de 180 dias. Se precisar de maior retenção, você deverá usar backups completos.

    ![Política de backup diferencial](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > Atualmente, não há suporte para backups incrementais.

7. Clique em **OK** para salvar a política e retornar ao menu principal da **política de backup** .
8. Selecione **backup de log** para adicionar uma política de backup de log transacional,
    * Em **backup de log**, selecione **habilitar**.  Isso não pode ser desabilitado, pois SAP HANA gerencia todos os backups de log.
    * Defina a frequência e os controles de retenção.

    > [!NOTE]
    > Os backups de log só começam a fluir depois que um backup completo bem-sucedido é concluído.

9. Clique em **OK** para salvar a política e retornar ao menu principal da **política de backup** .
10. Depois de terminar de definir a política de backup, clique em **OK**.

## <a name="run-an-on-demand-backup"></a>Executar um backup sob demanda

Os backups são executados de acordo com o agendamento da política. Você pode executar um backup sob demanda da seguinte maneira:

1. No menu do cofre, clique em **itens de backup**.
2. Em **itens de backup**, selecione a VM que executa o banco de dados SAP Hana e clique em **fazer backup agora**.
3. Em **fazer backup agora**, use o controle de calendário para selecionar o último dia em que o ponto de recuperação deve ser retido. Em seguida, clique em **OK**.
4. Monitore as notificações do Portal. Você pode monitorar o andamento do trabalho no painel do cofre > **trabalhos de Backup** > **em andamento**. Dependendo do tamanho do banco de dados, a criação do backup inicial pode demorar um pouco.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Executar o backup do SAP HANA Studio em um banco de dados com o backup do Azure habilitado

Se você quiser fazer um backup local (usando o HANA Studio) de um banco de dados cujo backup está sendo feito com o backup do Azure, faça o seguinte:

1. Aguarde até que os backups completos ou de log do banco de dados sejam concluídos. Verifique o status no SAP HANA Studio/cockpit.
2. Desabilite os backups de log e defina o catálogo de backup para o sistema de arquivos para o banco de dados relevante.
3. Para fazer isso, clique duas vezes em systemdb **configuração** de >  > selecionar **filtro de > de banco de dados (log)** .
4. Defina **enable_auto_log_backup** como **não**.
5. Defina **log_backup_using_backint** como **false**.
6. Faça um backup completo sob demanda do banco de dados.
7. Aguarde até que o backup completo e o backup do catálogo sejam concluídos.
8. Reverter as configurações anteriores para as do Azure:
    * Defina **enable_auto_log_backup** como **Sim**.
    * Defina **log_backup_using_backint** como **true**.

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [restaurar SAP Hana bancos de dados em execução em VMs do Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Saiba como [gerenciar SAP Hana bancos de dados cujo backup é feito usando o backup do Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
