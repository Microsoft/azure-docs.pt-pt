---
title: Back up VMs Azure em um cofre de serviços de recuperação
description: Descreve como fazer backup de VMs Azure num cofre de Serviços de Recuperação usando o Backup Azure
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: aeadd7bc798f690c67eef38c6dc645204ff39115
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273517"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Back up VMs Azure em um cofre de serviços de recuperação

Este artigo descreve como apoiar os VMs Azure num cofre dos Serviços de Recuperação, utilizando o serviço [de backup Azure.](backup-overview.md)

Neste artigo, vai aprender a:

> [!div class="checklist"]
>
> * Prepare mVs Azure.
> * Criar um cofre.
> * Descubra VMs e configure uma política de backup.
> * Ative cópias de segurança para VMs Azure.
> * Execute a cópia de segurança inicial.

> [!NOTE]
> Este artigo descreve como configurar um cofre e selecionar VMs para fazer apoio. É útil se quiser esquelhá-lo em vários VMs. Em alternativa, pode [fazer o back up de um único VM Azure](backup-azure-vms-first-look-arm.md) diretamente a partir das definições VM.

## <a name="before-you-start"></a>Antes de começar

* [Reveja](backup-architecture.md#architecture-built-in-azure-vm-backup) a arquitetura de backup Azure VM.
* [Saiba mais sobre](backup-azure-vms-introduction.md) Reforço Azure VM e a extensão de reserva.
* [Reveja a matriz de suporte](backup-support-matrix-iaas.md) antes de configurar a cópia de segurança.

Além disso, há algumas coisas que talvez precises de fazer em algumas circunstâncias:

* **Instale o agente VM no VM**: Azure Backup apoia os VMs Azure instalando uma extensão ao agente Azure VM em funcionamento na máquina. Se o seu VM foi criado a partir de uma imagem de mercado Azure, o agente está instalado e em execução. Se criar um VM personalizado, ou migrar uma máquina no local, poderá ter de [instalar o agente manualmente](#install-the-vm-agent).

## <a name="create-a-vault"></a>Criar um cofre

 Um cofre armazena backups e pontos de recuperação criados ao longo do tempo, e armazena políticas de backup associadas a máquinas de reserva. Crie um cofre da seguinte forma:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Em busca, digite Serviços de **Recuperação.** Em **Serviços,** clique em **cofres de Serviços**de Recuperação .

     ![Pesquisa de cofres de Serviços de Recuperação](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png)

3. No menu dos **cofres dos Serviços de Recuperação,** clique em **+Adicionar**.

     ![Passo 2 da Criação do Cofre dos Serviços de Recuperação](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

4. No cofre dos Serviços de **Recuperação,** escreva um nome amigável para identificar o cofre.
    * O nome tem de ser exclusivo para a subscrição do Azure.
    * Pode conter 2 a 50 caracteres.
    * Deve começar com uma letra, e só pode conter letras, números e hífenes.
5. Selecione a subscrição Azure, o grupo de recursos e a região geográfica em que o cofre deve ser criado. Em seguida, clique em **Criar**.
    * Pode levar um tempo para o cofre ser criado.
    * Monitorize as notificações de estado na zona superior direita do portal.

Depois do cofre ser criado, aparece na lista de cofres dos Serviços de Recuperação. Se não vir o seu cofre, selecione **Refresh**.

![Lista de cofres de cópia de segurança](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

>[!NOTE]
> O Azure Backup permite agora a personalização do nome do grupo de recursos criado pelo serviço de backup Azure. Para mais informações, consulte o grupo de [recursos Azure Backup para Máquinas Virtuais](backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines).

### <a name="modify-storage-replication"></a>Modificar a replicação do armazenamento

Por padrão, os cofres utilizam [armazenamento geo-redundante (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Se o cofre for o seu principal mecanismo de reserva, recomendamos que use GRS.
* Pode utilizar [armazenamento redundante localmente (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para uma opção mais barata.

Modificar o tipo de replicação do armazenamento da seguinte forma:

1. No novo cofre, clique em **Propriedades** na secção **Definições.**
2. Em **Propriedades**, sob **configuração de backup,** clique em **Atualizar**.
3. Selecione o tipo de replicação de armazenamento e clique em **Guardar**.

      ![Definir a configuração de armazenamento do novo cofre](./media/backup-try-azure-backup-in-10-mins/full-blade.png)

> [!NOTE]
   > Não pode modificar o tipo de replicação de armazenamento depois de o cofre ser configurado e conter itens de reserva. Se quiser fazer isso, precisa recriar o cofre.

## <a name="apply-a-backup-policy"></a>Aplicar uma política de backup

Configure uma política de reserva para o cofre.

1. No cofre, clique em **+Backup** na secção **Visão Geral.**

   ![Botão de backup](./media/backup-azure-arm-vms-prepare/backup-button.png)

2. Em **Backup Goal** > Onde está **Azure**a sua carga**de trabalho?** Em O que quer fazer **Virtual machine** >  **OK** **de volta?** Isto regista a extensão VM no cofre.

   ![Painel de backup e backup Goal](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. Na **política de backup,** selecione a política que pretende associar ao cofre.
    * A política de incumprimento apoia o VM uma vez por dia. Os backups diários são retidos por 30 dias. Os instantâneos instantâneos de recuperação são retidos por dois dias.
    * Se não quiser usar a política de predefinição, selecione **Create New**e crie uma política personalizada como descrito no próximo procedimento.

      ![Política de backup padrão](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. Em **Selecionar máquinas virtuais,** selecione os VMs que pretende fazer de volta utilizando a apólice. Em seguida, clique em **OK**.

   * Os VMs selecionados são validados.
   * Só se pode selecionar VMs na mesma região que o cofre.
   * VMs só podem ser apoiados num único cofre.

     ![Painel "Selecione máquinas virtuais"](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. Na **cópia de segurança,** clique em **ativar a cópia de segurança**. Isto implementa a política para o cofre e para os VMs, e instala a extensão de reserva no agente VM em execução no VM Azure.

     ![Botão "Ativar a cópia de segurança"](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Depois de permitir a cópia de segurança:

* O serviço de backup instala a extensão de reserva, quer o VM esteja ou não a funcionar.
* Um backup inicial será executado de acordo com o seu horário de reserva.
* Quando as cópias de segurança forem executadas, note que:
  * Um VM que está em execução tem a maior hipótese de capturar um ponto de recuperação consistente.
  * No entanto, mesmo que o VM esteja desligado, está apoiado. Tal VM é conhecido como um VM offline. Neste caso, o ponto de recuperação será consistente com a queda.
* A conectividade de saída explícita não é necessária para permitir a cópia de segurança dos VMs Azure.

### <a name="create-a-custom-policy"></a>Criar uma política personalizada

Se selecionar para criar uma nova política de backup, preencha as definições de política.

1. No **nome da Política,** especifique um nome significativo.
2. No **horário de backup,** especifique quando devem ser tomadas cópias de segurança. Pode receber cópias de segurança diárias ou semanais para VMs Azure.
3. Em **Restore Instantâneo,** especifique quanto tempo pretende reter instantâneos localmente para restauro instantâneo.
    * Quando restaurar, os discos VM apoiados são copiados do armazenamento, através da rede para o local de armazenamento de recuperação. Com restauro instantâneo, você pode aproveitar fotos armazenadas localmente tiradas durante um trabalho de backup, sem esperar que os dados de backup sejam transferidos para o cofre.
    * Pode reter instantâneos para restauro instantâneo entre um a cinco dias. Dois dias é a definição padrão.
4. Na **gama Retenção,** especifique quanto tempo pretende manter os seus pontos de backup diários ou semanais.
5. Na **Retenção do ponto de backup mensal,** especifique se pretende manter uma cópia de segurança mensal das suas cópias de segurança diárias ou semanais.
6. Clique **em OK** para salvar a apólice.

    ![Nova política de backup](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > O Azure Backup não suporta o ajuste automático do relógio para alterações de poupança de luz do dia para backups De VM Azure. À medida que ocorrem alterações de tempo, modifique manualmente as políticas de backup conforme necessário.

## <a name="trigger-the-initial-backup"></a>Desencadear a cópia inicial

O backup inicial será executado de acordo com o horário, mas você pode executá-lo imediatamente da seguinte forma:

1. No menu do cofre, clique em **itens de backup**.
2. Em **itens de backup,** clique em **Máquina Virtual Azure**.
3. Na lista **de Itens de Backup,** clique nas elipses (...).
4. Clique em **Backup agora**.
5. Em **Backup Now,** utilize o controlo do calendário para selecionar o último dia em que o ponto de recuperação deve ser mantido. Em seguida, clique em **OK**.
6. Monitorize as notificações do portal. Você pode monitorizar o progresso do trabalho no painel de instrumentos do cofre > **Backup Jobs** > **Em andamento.** Dependendo do tamanho da sua VM, a criação da cópia de segurança inicial poderá demorar algum tempo.

## <a name="verify-backup-job-status"></a>Verificar o estado do trabalho de backup

Os detalhes do trabalho de backup para cada backup VM consistem em duas fases, a fase **Snapshot** seguida dos dados de transferência para a fase **do cofre.**<br/>
A fase instantânea garante a disponibilidade de um ponto de recuperação armazenado juntamente com os discos para **Restauros Instantâneos** e estão disponíveis por um período máximo de cinco dias, dependendo da retenção instantânea configurada pelo utilizador. Os dados de transferência para o cofre criam um ponto de recuperação no cofre para retenção a longo prazo. Os dados de transferência para o cofre só começam depois de concluída a fase instantânea.

  ![Estatuto de Trabalho de Backup](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

Existem duas **Sub Tasks** a funcionar no backend, uma para trabalho de backup frontal que pode ser verificada a partir da lâmina de detalhes de **Backup Job,** como indicado abaixo:

  ![Estatuto de Trabalho de Backup](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

Os **dados de transferência para a** fase do cofre podem demorar vários dias a ser concluídos dependendo do tamanho dos discos, churn por disco e vários outros fatores.

O estatuto de trabalho pode variar dependendo dos seguintes cenários:

**Instantâneo** | **Transferir dados para o cofre** | **Estatuto do Emprego**
--- | --- | ---
Concluído | Em curso | Em curso
Concluído | Ignorado | Concluído
Concluído | Concluído | Concluído
Concluído | Falhou | Concluído com aviso
Falhou | Falhou | Falhou

Agora, com esta capacidade, para o mesmo VM, duas cópias de segurança podem ser executadas paralelamente, mas em ambas as fases (instantâneo, dados de transferência para cofre) apenas uma sub tarefa pode estar em execução. Assim, em cenários foram um trabalho de backup em andamento resultou no backup do dia seguinte para falhar será evitado com esta funcionalidade de dissociação. As cópias de segurança do dia seguinte podem ter instantâneos concluídos enquanto os dados de **transferência para o cofre** saltaram se o trabalho de reserva de um dia anterior estiver em andamento.
O ponto de recuperação incremental criado no cofre capturará todo o barulho do último ponto de recuperação criado no cofre. Não existe qualquer impacto no custo no utilizador.

## <a name="optional-steps"></a>Passos opcionais

### <a name="install-the-vm-agent"></a>Instalar o agente da VM

O Azure Backup apoia os VMs Azure instalando uma extensão ao agente Azure VM em funcionamento na máquina. Se o seu VM foi criado a partir de uma imagem do Azure Marketplace, o agente está instalado e em execução. Se criar um VM personalizado, ou migrar uma máquina no local, poderá ter de instalar o agente manualmente, como resumiu a tabela.

**VM** | **Detalhes**
--- | ---
**Windows** | 1. [Descarregue e instale](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) o ficheiro MSI do agente.<br/><br/> 2. Instale com permissões de administração na máquina.<br/><br/> 3. Verifique a instalação. Em *C:\WindowsAzure\Packages* no VM, clique à direita **WaAppAgent.exe** > **Properties**. No separador **Details,** a versão do **produto** deve ser 2.6.1198.718 ou superior.<br/><br/> Se estiver a atualizar o agente, certifique-se de que não estão a decorrer operações de backup e [reinstale o agente.](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)
**Linux** | Instale utilizando um pacote RPM ou UM DEB do repositório de pacotes da sua distribuição. Este é o método preferido para instalar e atualizar o agente Azure Linux. Todos os fornecedores de [distribuição endossados](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integram o pacote de agentes Azure Linux nas suas imagens e repositórios. O agente está disponível no [GitHub,](https://github.com/Azure/WALinuxAgent)mas não recomendamos a instalação a partir daí.<br/><br/> Se estiver a atualizar o agente, certifique-se de que não estão a decorrer operações de backup e atualize os binários.

>[!NOTE]
> **O Azure Backup suporta agora a cópia de segurança seletiva do disco e restaura utilizando a solução de backup da Máquina Virtual Azure.**
>
>Hoje em dia, o Azure Backup suporta o backup de todos os discos (Sistema Operativo e dados) num VM juntos utilizando a solução de backup da Máquina Virtual. Com a funcionalidade de exclusão de disco, obtém-se a opção de fazer backup de um ou alguns dos muitos discos de dados de um VM. Isto fornece uma solução eficiente e rentável para as suas necessidades de backup e restauro. Cada ponto de recuperação contém dados dos discos incluídos na operação de backup, o que permite ainda ter um subconjunto de discos restaurado sabotado a partir do ponto de recuperação dado durante a operação de restauro. Isto aplica-se para restaurar tanto a partir do instantâneo como do cofre.
>
>**Para se inscrever para a pré-visualização, escreva-nos emAskAzureBackupTeam@microsoft.com**

## <a name="next-steps"></a>Passos seguintes

* Problemas de resolução de problemas com [agentes Da VM Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) ou [backup Azure VM](backup-azure-vms-troubleshoot.md).
* [Restaurar](backup-azure-arm-restore-vms.md) VMs azure.
