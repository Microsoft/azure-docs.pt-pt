---
title: Fazer backup de VMs do Azure em um cofre dos serviços de recuperação
description: Descreve como fazer backup de VMs do Azure em um cofre de serviços de recuperação usando o backup do Azure
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: e5ff3a00d8cb3bf0c5fa3cb4929b7c22d92c7834
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513818"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Fazer backup de VMs do Azure em um cofre dos serviços de recuperação

Este artigo descreve como fazer backup de VMs do Azure em um cofre de serviços de recuperação usando o serviço de [backup do Azure](backup-overview.md) .

Neste artigo, vai aprender a:

> [!div class="checklist"]
>
> * Preparar VMs do Azure.
> * Crie um cofre.
> * Descubra VMs e configure uma política de backup.
> * Habilite o backup para VMs do Azure.
> * Execute a cópia de segurança inicial.

> [!NOTE]
> Este artigo descreve como configurar um cofre e selecionar VMs para fazer backup. É útil se você quiser fazer backup de várias VMs. Como alternativa, você pode [fazer backup de uma única VM do Azure](backup-azure-vms-first-look-arm.md) diretamente das configurações da VM.

## <a name="before-you-start"></a>Antes de começar

* [Examine](backup-architecture.md#architecture-built-in-azure-vm-backup) a arquitetura de backup da VM do Azure.
* [Saiba mais](backup-azure-vms-introduction.md) Backup de VM do Azure e a extensão de backup.
* [Examine a matriz de suporte antes de configurar o](backup-support-matrix-iaas.md) backup.

Além disso, há algumas coisas que você pode precisar fazer em algumas circunstâncias:

* **Instalar o agente de VM na VM: o**backup do Azure faz backup de VMs do Azure instalando uma extensão para o agente de VM do Azure em execução no computador. Se sua VM foi criada a partir de uma imagem do Azure Marketplace, o agente está instalado e em execução. Se você criar uma VM personalizada ou migrar um computador local, talvez seja necessário [instalar o agente manualmente](#install-the-vm-agent).

## <a name="create-a-vault"></a>Criar um cofre

 Um cofre armazena backups e pontos de recuperação criados ao longo do tempo e armazena políticas de backup associadas a computadores de backup. Crie um cofre da seguinte maneira:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Em pesquisa, digite **serviços de recuperação**. Em **Serviços**, clique em **cofres dos serviços de recuperação**.

     ![Pesquisar cofres dos serviços de recuperação](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png)

3. No menu **cofres dos serviços de recuperação** , clique em **+ Adicionar**.

     ![Passo 2 da Criação do Cofre dos Serviços de Recuperação](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

4. Em **cofre dos serviços de recuperação**, digite um nome amigável para identificar o cofre.
    * O nome tem de ser exclusivo para a subscrição do Azure.
    * Ele pode conter de 2 a 50 caracteres.
    * Ele deve começar com uma letra e pode conter apenas letras, números e hifens.
5. Selecione a assinatura do Azure, o grupo de recursos e a região geográfica na qual o cofre deve ser criado. Em seguida, clique em **Criar**.
    * Pode levar algum tempo para que o cofre seja criado.
    * Monitore as notificações de status na área superior direita do Portal.

Depois que o cofre é criado, ele aparece na lista cofres dos serviços de recuperação. Se você não vir seu cofre, selecione **Atualizar**.

![Lista de cofres de cópia de segurança](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

>[!NOTE]
> O backup do Azure agora permite a personalização do nome do grupo de recursos criado pelo serviço de backup do Azure. Para obter mais informações, consulte [grupo de recursos de backup do Azure para máquinas virtuais](backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines).

### <a name="modify-storage-replication"></a>Modificar a replicação de armazenamento

Por padrão, os cofres usam o [grs (armazenamento com redundância geográfica)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Se o cofre for seu mecanismo de backup primário, recomendamos que você use GRS.
* Você pode usar o [armazenamento com redundância local (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para uma opção mais barata.

Modifique o tipo de replicação de armazenamento da seguinte maneira:

1. No novo cofre, clique em **Propriedades** na seção **configurações** .
2. Em **Propriedades**, em **configuração de backup**, clique em **Atualizar**.
3. Selecione o tipo de replicação de armazenamento e clique em **salvar**.

      ![Definir a configuração de armazenamento do novo cofre](./media/backup-try-azure-backup-in-10-mins/full-blade.png)

> [!NOTE]
   > Não é possível modificar o tipo de replicação de armazenamento depois que o cofre é configurado e contém itens de backup. Se desejar fazer isso, você precisará recriar o cofre.

## <a name="apply-a-backup-policy"></a>Aplicar uma política de backup

Configure uma política de backup para o cofre.

1. No cofre, clique em **+ backup** na seção **visão geral** .

   ![Botão de backup](./media/backup-azure-arm-vms-prepare/backup-button.png)

2. Em **meta de Backup** > **em que sua carga de trabalho está em execução?** selecione **Azure**. Em **o que você deseja fazer backup?** selecione a **máquina virtual** >  **OK**. Isso registra a extensão de VM no cofre.

   ![Painéis de meta de backup e backup](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. Em **política de backup**, selecione a política que você deseja associar ao cofre.
    * A política padrão faz o backup da VM uma vez por dia. Os backups diários são mantidos por 30 dias. Instantâneos de recuperação instantânea são mantidos por dois dias.
    * Se você não quiser usar a política padrão, selecione **criar nova**e crie uma política personalizada, conforme descrito no próximo procedimento.

      ![Política de backup padrão](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. Em **selecionar máquinas virtuais**, selecione as VMs que você deseja fazer backup usando a política. Em seguida, clique em **OK**.

   * As VMs selecionadas são validadas.
   * Você só pode selecionar VMs na mesma região que o cofre.
   * Somente é possível fazer backup das VMs em um único cofre.

     ![Painel "selecionar máquinas virtuais"](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. Em **backup**, clique em **habilitar backup**. Isso implanta a política no cofre e nas VMs e instala a extensão de backup no agente de VM em execução na VM do Azure.

     ![Botão "Habilitar backup"](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Depois de habilitar o backup:

* O serviço de backup instala a extensão de backup, independentemente de a VM estar em execução ou não.
* Um backup inicial será executado de acordo com o agendamento de backup.
* Quando os backups são executados, observe que:
  * Uma VM em execução tem a maior chance de capturar um ponto de recuperação consistente com o aplicativo.
  * No entanto, mesmo que a VM seja desativada, o backup é feito. Essa VM é conhecida como VM offline. Nesse caso, o ponto de recuperação será consistente com falhas.
* A conectividade de saída explícita não é necessária para permitir o backup de VMs do Azure.

### <a name="create-a-custom-policy"></a>Criar uma política personalizada

Se você optou por criar uma nova política de backup, preencha as configurações de política.

1. Em **nome da política**, especifique um nome significativo.
2. Em **agendamento de backup**, especifique quando os backups devem ser feitos. Você pode fazer backups diários ou semanais para VMs do Azure.
3. Em **restauração instantânea**, especifique por quanto tempo deseja manter os instantâneos localmente para restauração instantânea.
    * Quando você restaura, os discos de VM de backup são copiados do armazenamento, pela rede para o local de armazenamento de recuperação. Com a restauração instantânea, você pode aproveitar os instantâneos armazenados localmente feitos durante um trabalho de backup, sem esperar que os dados de backup sejam transferidos para o cofre.
    * Você pode reter instantâneos para restauração instantânea entre um ou cinco dias. Dois dias é a configuração padrão.
4. Em **período de retenção**, especifique por quanto tempo deseja manter seus pontos de backup diários ou semanais.
5. Em **retenção do ponto de backup mensal**, especifique se deseja manter um backup mensal de seus backups diários ou semanais.
6. Clique em **OK** para salvar a política.

    ![Nova política de backup](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > O backup do Azure não dá suporte ao ajuste de relógio automático para alterações de salvamento de verão para backups de VM do Azure. À medida que ocorrem alterações de tempo, modifique as políticas de backup manualmente, conforme necessário.

## <a name="trigger-the-initial-backup"></a>Disparar o backup inicial

O backup inicial será executado de acordo com o agendamento, mas você poderá executá-lo imediatamente da seguinte maneira:

1. No menu do cofre, clique em **itens de backup**.
2. Em **itens de backup**, clique em **máquina virtual do Azure**.
3. Na lista **itens de backup** , clique nas reticências (...).
4. Clique em **fazer backup agora**.
5. Em **fazer backup agora**, use o controle de calendário para selecionar o último dia em que o ponto de recuperação deve ser retido. Em seguida, clique em **OK**.
6. Monitore as notificações do Portal. Você pode monitorar o andamento do trabalho no painel do cofre > **trabalhos de Backup** > **em andamento**. Dependendo do tamanho da sua VM, a criação da cópia de segurança inicial poderá demorar algum tempo.

## <a name="verify-backup-job-status"></a>Verificar o status do trabalho de backup

Os detalhes do trabalho de backup para cada backup de VM consistem em duas fases, a fase de **instantâneo** seguida da fase **transferir dados para o cofre** .<br/>
A fase de instantâneo garante a disponibilidade de um ponto de recuperação armazenado junto com os discos para **restaurações instantâneas** e está disponível por um máximo de cinco dias, dependendo da retenção de instantâneo configurada pelo usuário. Transferir dados para o cofre cria um ponto de recuperação no cofre para retenção de longo prazo. A transferência de dados para o cofre só é iniciada depois que a fase de instantâneo é concluída.

  ![Status do trabalho de backup](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

Há duas **subtarefas** em execução no back-end, uma para o trabalho de backup de front-ends que pode ser verificada na folha detalhes do **trabalho de backup** , conforme indicado abaixo:

  ![Status do trabalho de backup](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

A fase **transferir dados para o cofre** pode levar vários dias para ser concluída, dependendo do tamanho dos discos, da rotatividade por disco e de vários outros fatores.

O status do trabalho pode variar dependendo dos seguintes cenários:

**Instantânea** | **Transferir dados para o cofre** | **Status do trabalho**
--- | --- | ---
Concluído | Em curso | Em curso
Concluído | Ignorado | Concluído
Concluído | Concluído | Concluído
Concluído | Com Falhas | Concluído com aviso
Com Falhas | Com Falhas | Com Falhas

Agora, com esse recurso, para a mesma VM, dois backups podem ser executados em paralelo, mas, em qualquer fase (instantâneo, transferir dados para o cofre), apenas uma subtarefa pode ser executada. Assim, em cenários, um trabalho de backup em andamento resultou na falha do backup do dia seguinte, com essa funcionalidade de desacoplamento. Os backups do dia seguinte podem ter o instantâneo concluído ao **transferir dados para o cofre** ignorados se o trabalho de backup de um dia anterior estiver em estado de andamento.
O ponto de recuperação incremental criado no cofre irá capturar toda a rotatividade do último ponto de recuperação criado no cofre. Não há impacto no custo do usuário.

## <a name="optional-steps"></a>Passos opcionais

### <a name="install-the-vm-agent"></a>Instalar o agente de VM

O backup do Azure faz backup de VMs do Azure instalando uma extensão para o agente de VM do Azure em execução no computador. Se sua VM foi criada a partir de uma imagem do Azure Marketplace, o agente está instalado e em execução. Se você criar uma VM personalizada ou migrar um computador local, talvez seja necessário instalar o agente manualmente, conforme resumido na tabela.

**VM** | **Detalhes**
--- | ---
**Windows** | 1. [Baixe e instale](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) o arquivo MSI do agente.<br/><br/> 2. Instale com permissões de administrador no computador.<br/><br/> 3. Verifique a instalação. Em *C:\WindowsAzure\Packages* na VM, clique com o botão direito do mouse em **WaAppAgent. exe** > **Propriedades**. Na guia **detalhes** , a **versão do produto** deve ser 2.6.1198.718 ou superior.<br/><br/> Se você estiver atualizando o agente, certifique-se de que nenhuma operação de backup esteja em execução e [reinstale o agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Instale o usando um RPM ou um pacote de DEB do repositório de pacotes de sua distribuição. Esse é o método preferencial para instalar e atualizar o agente Linux do Azure. Todos os [provedores de distribuição endossados](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integram o pacote do agente Linux do Azure em suas imagens e repositórios. O agente está disponível no [GitHub](https://github.com/Azure/WALinuxAgent), mas não recomendamos a instalação a partir daí.<br/><br/> Se você estiver atualizando o agente, verifique se nenhuma operação de backup está em execução e atualize os binários.

>[!NOTE]
> O backup do Azure agora oferece suporte a backup e restauração de disco seletivo usando a solução de backup de máquina virtual do Azure.
>
>Hoje, o backup do Azure dá suporte ao backup de todos os discos (sistema operacional e dados) em uma VM em conjunto usando a solução de backup de máquina virtual. Com a funcionalidade excluir disco, você obtém uma opção para fazer backup de um ou alguns dos vários discos de dados em uma VM. Isso fornece uma solução eficiente e econômica para suas necessidades de backup e restauração. Cada ponto de recuperação contém dados dos discos incluídos na operação de backup, o que permite que você tenha um subconjunto de discos restaurados do ponto de recuperação fornecido durante a operação de restauração. Isso se aplica à restauração tanto do instantâneo quanto do cofre.
>
> Essa solução é particularmente útil nos seguintes cenários:
>  
>1. Você tem dados críticos para fazer backup em apenas um disco e não deseja fazer backup do restante dos discos anexados a uma VM. Isso minimiza os custos de armazenamento de backup.  
>2. Você tem outras soluções de backup para parte dos dados da VM. Por exemplo, você faz o backup de seus bancos de dados ou de sua capacidade com uma solução de backup de carga de trabalho diferente e deseja usar o backup de nível de VM do Azure para o restante dos seus discos e dados para criar um sistema eficiente e robusto utilizando os melhores recursos disponíveis.
>
>Para se inscrever na versão prévia, escreva para nós em AskAzureBackupTeam@microsoft.com

## <a name="next-steps"></a>Passos seguintes

* Solucione quaisquer problemas com [agentes de VM do Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) ou [backup de VM do Azure](backup-azure-vms-troubleshoot.md).
* [Restaurar](backup-azure-arm-restore-vms.md) VMs do Azure.
