---
title: Cópia de segurança de uma base de dados do SAP HANA no Azure com o Azure Backup | Documentos da Microsoft
description: Este tutorial explica como criar cópias de uma base de dados do SAP HANA no Azure com o serviço de cópia de segurança do Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: raynew
ms.openlocfilehash: 1c5c38e9af58aa9468fb0e22491327d3a35f0dda
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67656648"
---
# <a name="back-up-an-sap-hana-database"></a>Fazer cópias de segurança de uma base de dados do SAP HANA

[O Azure Backup](backup-overview.md) suporta a cópia de segurança de bases de dados do SAP HANA no Azure.

> [!NOTE]
> Esta funcionalidade está atualmente em pré-visualização pública. Atualmente não está pronto para produção e não têm um SLA garantido. 

## <a name="scenario-support"></a>Suporte a cenários

**Suporte** | **Detalhes**
--- | ---
**Áreas geográficas suportadas** | Sudeste da Austrália, leste da Austrália <br> Sul do Brasil <br> Canadá Central, Canadá leste <br> Sudeste asiático, Ásia Oriental <br> E.U.A. leste, E.U.A. Leste 2, EUA Centro-Oeste, E.U.A. oeste, E.U.A. oeste 2, Centro-Norte, E.U.A. Central, Centro-Sul dos E.U.A.<br> Índia Central, Sul da Índia <br> Leste do Japão, Oeste do Japão<br> Centro da Coreia, Sul da Coreia do Sul <br> Europa do Norte, Europa Ocidental <br> Sul do Reino Unido, oeste do Reino Unido
**Sistemas de operativos VM** | SLES 12 com SP2 ou SP3.
**Versões suportadas do HANA** | SDC em HANA 1.x, MDC em HANA 2.x < = SPS03

### <a name="current-limitations"></a>Limitações atuais

- Apenas pode fazer o backup de bancos de dados do SAP HANA em execução em VMs do Azure.
- Só pode configurar a cópia de segurança do SAP HANA no portal do Azure. A funcionalidade não pode ser configurada com o PowerShell, CLI ou a API REST.
- Poderá apenas fazer das bases de dados no modo vertical.
- Pode criar cópias de segurança da base de dados registos a cada 15 minutos. Backups de log apenas começarem a fluir depois de uma cópia de segurança completa da base de dados estar concluída.
- Pode efetuar cópias de segurança completas e diferenciais. Cópia de segurança incremental não é atualmente suportada.
- Não é possível modificar a política de cópia de segurança depois de aplicar para cópias de segurança do SAP HANA. Se pretender criar cópias de segurança com definições diferentes, crie uma nova política ou atribuir uma política diferente.
  - Para criar uma nova política, no cofre clique **políticas** > **políticas de cópia de segurança** >  **+ adicionar** > **SAP HANA no VM do Azure**e especifique as definições de política.
  - Para atribuir uma política diferente, nas propriedades da VM a executar a base de dados, clique no nome da política atual. Em seguida, na **política de cópia de segurança** página, pode selecionar uma política diferente para utilizar para a cópia de segurança.

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que faça o seguinte antes de configurar cópias de segurança:

1. Na VM em execução a base de dados do SAP HANA, instalar o Microsoft oficial [2.1 de tempo de execução do .NET Core](https://dotnet.microsoft.com/download/linux-package-manager/sles/runtime-current) pacote. Tenha em atenção que:
    - Só tem do **tempo de execução-dotnet-2.1** pacote. Não precisa **aspnetcore-tempo de execução-2.1**.
    - Se a VM não tem internet aceder, espelhar ou fornecer um cache offline para o tempo de execução-dotnet-2.1 (e todos os RPMs dependentes) do pacote Microsoft feed especificado na página.
    - Durante a instalação do pacote, poderá ser-lhe pedido para especificar uma opção. Se assim for, especifique **solução 2**.

        ![Opção de instalação do pacote](./media/backup-azure-sap-hana-database/hana-package.png)

2. Na VM, instalar e ativar os pacotes de controladores ODBC do oficial SLES pacote/suporte de dados com o zypper, da seguinte forma:

    ```unix
    sudo zypper update
    sudo zypper install unixODBC
    ```

3. Permitir a ligação da VM à internet, para que ele alcança o Azure, conforme descrito no procedimento [abaixo](#set-up-network-connectivity).

4. Execute o script de pré-registo na máquina virtual em que o HANA é instalado como um utilizador de raiz. O script é fornecido [no portal](#discover-the-databases) no fluxo e é necessária para configurar o [com o botão direito permissões](backup-azure-sap-hana-database-troubleshoot.md#setting-up-permissions).

### <a name="set-up-network-connectivity"></a>Configurar a conectividade de rede

Para todas as operações, a VM do SAP HANA necessita de conectividade para os endereços IP públicos do Azure. Operações de VM (deteção de base de dados, configurar cópias de segurança, agendar cópias de segurança, restaurar pontos de recuperação e assim por diante) não podem funcionar sem conectividade. Estabelecer conectividade ao permitir acesso para os intervalos IP do datacenter do Azure: 

- Pode baixar o [intervalos de endereços IP](https://www.microsoft.com/download/details.aspx?id=41653) para os datacenters do Azure e, em seguida, permitir o acesso a estes endereços IP.
- Se estiver a utilizar grupos de segurança de rede (NSGs), pode usar o AzureCloud [etiqueta de serviço](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) para permitir que o IP público de contas do Azure endereços. Pode utilizar o [cmdlet Set-AzureNetworkSecurityRule](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0) para modificar as regras do NSG.

## <a name="onboard-to-the-public-preview"></a>Carregar para a pré-visualização pública

Carregar para a pré-visualização pública da seguinte forma:

- No portal, registar o seu ID de subscrição para o fornecedor de serviços de serviços de recuperação por [seguindo este artigo](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal). 
- Para o PowerShell, execute este cmdlet. A instalação deve demorar como "Registada".

    ```powershell
    PS C:>  Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```



[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Detetar as bases de dados

1. No cofre, no **introdução**, clique em **cópia de segurança**. Na **em que a sua carga de trabalho é executado?** , selecione **SAP HANA na VM do Azure**.
2. Clique em **Iniciar deteção**. Esta ação inicia a deteção de VMs de Linux não protegida na região do cofre.

   - Após a deteção, VMs não protegidas são apresentados no portal, listadas por nome e grupo de recursos.
   - Se uma VM não estiver listada como esperado, verifique se ele já foi efetuado num cofre.
   - Várias VMs podem ter o mesmo nome mas pertencem a diferentes grupos de recursos.

3. Na **selecionar Virtual Machines**, clique na ligação para transferir o script que fornece permissões para o serviço de cópia de segurança do Azure aceder a VMs do SAP HANA para a deteção de base de dados
4. Execute o script em cada VM que aloja bases de dados do SAP HANA que pretende criar cópias de segurança.
5. Depois de executar o script em VMs, além **selecionar Virtual Machines**, selecione as VMs. Em seguida, clique em **detetar bds**.
6. Cópia de segurança do Azure Deteta todas as bases de dados do SAP HANA na VM. Durante a deteção, o Azure Backup registra a VM com o Cofre e instala uma extensão na VM. Nenhum agente está instalado na base de dados.

    ![Detetar as bases de dados do SAP HANA](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Configurar a cópia de segurança  

Ative agora a cópia de segurança.

1. No passo 2, clique em **configurar a cópia de segurança**.
2. Na **selecionar itens para cópia de segurança**, selecione todas as bases de dados que pretende proteger > **OK**.
3. Na **política de cópia de segurança** > **escolher política de cópia de segurança**, criar uma nova política de cópia de segurança das bases de dados, de acordo com as instruções abaixo.
4. Depois de criar a política e sobre o **cópia de segurança** menu, clique em **ativar cópia de segurança**.
5. Controlar o progresso da configuração de cópia de segurança no **notificações** área do portal.

### <a name="create-a-backup-policy"></a>Criar uma política de cópia de segurança

Uma política de cópia de segurança define quando os backups são feitos e o período de tempo são retidos.

- É criada uma política ao nível do cofre.
- Vários cofres podem utilizar a mesma política de cópia de segurança, mas tem de aplicar a política de cópia de segurança para cada cofre.

Especifique as definições de política da seguinte forma:

1. Na **nome da política**, introduza um nome para a nova política.
2. Na **política de cópia de segurança completa**, selecione um **frequência de cópia de segurança**, escolha **diária** ou **semanal**.
   - **Diária**: Selecione a hora e fuso horário em que a tarefa de cópia de segurança começa.
   
       - Tem de executar uma cópia de segurança completa. Não é possível desativar esta opção.
       - Clique em **cópia de segurança completa** para ver a política.
       - Não é possível criar cópias de segurança diferenciais para cópias de segurança completas diárias.
       
   - **Semanal**: Selecione o dia da semana, hora e fuso horário em que a tarefa de cópia de segurança é executada.
3. Na **período de retenção**, configurar definições de retenção para a cópia de segurança completa.
    - Por predefinição, todas as opções estão selecionadas. Desmarque os limites de intervalo de retenção que não pretende utilizar e configurar esses que fazer.
    - O período de retenção mínimo para qualquer tipo de cópia de segurança (diferencial/completo/log) é de sete dias.
    - Pontos de recuperação são marcados para retenção com base no seu período de retenção. Por exemplo, se selecionar uma cópia de segurança completa diária, apenas uma cópia de segurança completa é acionada por dia.
    - A cópia de segurança para um dia específico é marcado e mantido com base no período de retenção semanal e definição.
    - Mensal e anual períodos de retenção se comportam de maneira semelhante.

4. Na **política de cópia de segurança completa** menu, clique em **OK** para aceitar as configurações.
5. Selecione **cópia de segurança diferencial** para adicionar uma política diferencial.
6. Na **política de cópia de segurança diferencial**, selecione **ativar** para abrir os controles de frequência e retenção.
    - No máximo, pode acionar uma cópia de segurança diferencial por dia.
    - As cópias de segurança diferenciais podem ser mantidas para um máximo de 180 dias. Se precisar de retenção mais longa, tem de utilizar cópias de segurança completas.

    > [!NOTE]
    > Cópias de segurança incrementais não são atualmente suportadas. 

7. Clique em **OK** para guardar a política e retornar o Main **política de cópia de segurança** menu.
8. Selecione **cópia de segurança do registo** para adicionar uma política de cópia de segurança do registo transacional,
    - Na **registo de cópia de segurança**, selecione **ativar**.
    - Defina os controlos de frequência e retenção.

    > [!NOTE]
    > Backups de log apenas a começar a ser aplicada após a conclusão de uma cópia de segurança completa.

9. Clique em **OK** para guardar a política e retornar o Main **política de cópia de segurança** menu.
10. Depois de concluir a definir a política de cópia de segurança, clique em **OK**.


## <a name="run-an-on-demand-backup"></a>Executar cópias de segurança a pedido

Executam cópias de segurança em conformidade com o agendamento de política. Pode executar uma cópia de segurança a pedido da seguinte forma:


1. No menu do cofre, clique em **itens de cópia de segurança**.
2. Na **itens de cópia de segurança**, selecione a VM em execução na base de dados do SAP HANA e, em seguida, clique em **cópia de segurança agora**.
3. Na **cópia de segurança agora**, usar o controle de calendário para selecionar o último dia em que o ponto de recuperação deve ser mantido. Em seguida, clique em **OK**.
4. Monitorize as notificações de portais. Pode monitorizar o progresso da tarefa no dashboard do cofre > **tarefas de cópia de segurança** > em curso. Dependendo do tamanho da base de dados, a criação de cópia de segurança inicial poderá demorar algum tempo.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Executar cópia de segurança do SAP HANA Studio num banco de dados com o Azure Backup ativada

Se quiser fazer um backup local (com o HANA Studio) de uma base de dados que está a cópia de segurança com o Azure Backup, efetue o seguinte:

1. Aguarde que qualquer completo ou backups de log para a base de dados concluir. Verificar o estado no SAP HANA Studio.
2. Desative a backups de log e definir o catálogo de cópia de segurança para o sistema de ficheiros da base de dados relevante.
3. Para tal, faça duplo clique em **systemdb** > **configuração** > **selecionar base de dados** > **filtro (registo)** .
4. Definir **enable_auto_log_backup** ao **não**.
5. Definir **log_backup_using_backint** ao **falso**.
6. Um completo ad hoc efetuar cópia de segurança da base de dados.
7. Aguarde que a cópia de segurança completa e a cópia de segurança de catálogo para concluir.
8. Reverta as definições anteriores para as do Azure:
    - Definir **enable_auto_log_backup** ao **Sim**.
    - Definir **log_backup_using_backint** ao **verdadeiro**.



## <a name="next-steps"></a>Passos Seguintes

[Saiba mais sobre](backup-azure-sap-hana-database-troubleshoot.md) como resolver erros comuns ao utilizar a cópia de segurança do SAP HANA em VMs do Azure.
[Saiba mais sobre](backup-azure-arm-vms-prepare.md) backup das VMs do Azure.
