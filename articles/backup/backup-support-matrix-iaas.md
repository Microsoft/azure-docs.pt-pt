---
title: Matriz de suporte da cópia de segurança de uma VM do Azure
description: Fornece um resumo das definições de suporte e limitações ao apoiar os VMs Azure com o serviço de backup Azure.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 1dd060840e589d601d87d8be235eda5c34283a4f
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2020
ms.locfileid: "77369898"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Matriz de suporte da cópia de segurança de uma VM do Azure

Pode utilizar o [serviço de backup Azure](backup-overview.md) para fazer backup nas instalações de máquinas e cargas de trabalho, e máquinas virtuais Azure (VMs). Este artigo resume as definições e limitações de suporte quando faz backup de VMs Azure com Backup Azure.

Outras matrizes de apoio:

- [Matriz geral de suporte](backup-support-matrix.md) para Backup Azure
- [Matriz de suporte](backup-support-matrix-mabs-dpm.md) para backup do servidor de backup do Azure/System Center Data Protection Manager (DPM)
- [Matriz de suporte](backup-support-matrix-mars-agent.md) para backup com o agente microsoft Azure Recovery Services (MARS)

## <a name="supported-scenarios"></a>Cenários suportados

Eis como pode fazer backup e restaurar os VMs Azure com o serviço de backup Azure.

**Cenário** | **Cópia de segurança** | **Agente** |**Restaurar**
--- | --- | --- | ---
Backup direto dos VMs Azure  | Apoia todo o VM.  | Não é necessário um agente adicional no VM Azure. A Azure Backup instala e utiliza uma extensão do [agente Azure VM](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) que está a funcionar no VM. | Restaurar da seguinte forma:<br/><br/> - **Criar um VM básico.** Isto é útil se o VM não tiver uma configuração especial, como vários endereços IP.<br/><br/> - **Restaurar o disco VM**. Restaurar o disco. Em seguida, anexe-o a um VM existente, ou crie um novo VM a partir do disco utilizando o PowerShell.<br/><br/> - **Substitua o disco VM**. Se existir um VM e utilizar discos geridos (não encriptados), pode restaurar um disco e utilizá-lo para substituir um disco existente no VM.<br/><br/> - **Restaurar ficheiros/pastas específicos**. Pode restaurar ficheiros/pastas de um VM em vez de todo o VM.
Backup direto de VMs Azure (apenas Windows)  | Volte a fazer um backfiles/pastas/volume específicos. | Instale o agente dos Serviços de [Recuperação Azure.](backup-azure-file-folder-backup-faq.md)<br/><br/> Pode executar o agente MARS juntamente com a extensão de reserva para o agente Azure VM fazer o backup do VM ao nível de ficheiro/pasta. | Restaurar pastas/ficheiros específicos.
Backup Azure VM para servidor de backup  | Back up ficheiros/pastas/volumes; sistema estado/ficheiros metálicos nus; dados de aplicações para O DPM do System Center ou para o Microsoft Azure Backup Server (MABS).<br/><br/> DPM/MABS, em seguida, recua para o cofre de reserva. | Instale o agente de proteção DPM/MABS no VM. O agente MARS está instalado em DPM/MABS.| Restaurar ficheiros/pastas/volumes; sistema estado/ficheiros metálicos nus; dados da aplicação.

Saiba mais sobre a cópia de segurança [utilizando um servidor](backup-architecture.md#architecture-back-up-to-dpmmabs) de backup e sobre os [requisitos de suporte](backup-support-matrix-mabs-dpm.md).

>[!NOTE]
> **O Azure Backup suporta agora a cópia de segurança seletiva do disco e restaura utilizando a solução de backup da Máquina Virtual Azure.**
>
>Hoje, o backup do Azure dá suporte ao backup de todos os discos (sistema operacional e dados) em uma VM em conjunto usando a solução de backup de máquina virtual. Com a funcionalidade excluir disco, você obtém uma opção para fazer backup de um ou alguns dos vários discos de dados em uma VM. Isso fornece uma solução eficiente e econômica para suas necessidades de backup e restauração. Cada ponto de recuperação contém dados dos discos incluídos na operação de backup, o que permite que você tenha um subconjunto de discos restaurados do ponto de recuperação fornecido durante a operação de restauração. Isso se aplica à restauração tanto do instantâneo quanto do cofre.
>
>**Para se inscrever na pré-estreia, escreva-nos na AskAzureBackupTeam@microsoft.com**

## <a name="supported-backup-actions"></a>Ações de backup apoiadas

**Ação** | **Suporte**
--- | ---
Ative a cópia de segurança quando criar um Windows Azure VM | Apoiado para: <br/><br/> - Windows Server 2019 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2016 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2012 R2 (Datacenter/Standard) <br/><br/> - Windows Server 2008 R2 (RTM e SP1 Standard)
Ative o backup quando criar um Linux VM | Apoiado para:<br/><br/> - Ubuntu Server: 18.04, 17.10, 17.04, 16.04 (LTS), 14.04 (LTS)<br/><br/> - Chapéu Vermelho: RHEL 6.7, 6.8, 6.9, 7.2, 7.3, 7.4<br/><br/> - SUSE Linux Enterprise Server: 11 SP4, 12 SP2, 12 SP3, 15 <br/><br/> - Debiano: 8, 9<br/><br/> - CentOS: 6.9, 7.3<br/><br/> - Oracle Linux: 6.7, 6.8, 6.9, 7.2, 7.3
Back up a VM que é vM desligado/offline | Suportado.<br/><br/> O snapshot é consistente apenas com acidentes, não consistente com aplicações.
Volte a subir os discos após migrar para discos geridos | Suportado.<br/><br/> Os reforços continuarão a funcionar. nenhuma ação necessária.
Back up gerido discos após ativar bloqueio de grupo de recursos | Não suportado.<br/><br/> O Azure Backup não pode eliminar os pontos de restauro mais antigos, e as cópias de segurança começarão a falhar quando o limite máximo dos pontos de restauro for atingido.
Modificar a política de backup para um VM | Suportado.<br/><br/> O VM será apoiado utilizando as definições de horário e retenção em nova política. Se as definições de retenção forem estendidas, os pontos de recuperação existentes são marcados e mantidos. Se forem reduzidos, os pontos de recuperação existentes serão podados no próximo trabalho de limpeza e eventualmente eliminados.
Cancele um trabalho de reserva| Suportado durante o processo de instantâneo.<br/><br/> Não suportado quando a foto está sendo transferida para o cofre.
Apoiar o VM para uma região ou subscrição diferente |Não suportado.
Backups por dia (através da extensão Azure VM) | Um reforço programado por dia.<br/><br/>O serviço de backup do Azure dá suporte a até nove backups sob demanda por dia, mas a Microsoft recomenda não mais do que quatro backups diários sob demanda para garantir o melhor desempenho.
Backups por dia (através do agente MARS) | Três reforços programados por dia.
Backups por dia (via DPM/MABS) | Dois reforços programados por dia.
Backup mensal/anual| Não suportado ao apoiar-se com a extensão Azure VM. Só diariamente e semanalmente é apoiado.<br/><br/> Pode configurar a política de retenção de backups diários/semanais para o período de retenção mensal/anual.
Regulação automática do relógio | Não suportado.<br/><br/> A Cópia de Segurança Azure não se ajusta automaticamente para alterações de horário de verão ao fazer o backup de um VM.<br/><br/>  Modifique a política manualmente conforme necessário.
[Funcionalidades de segurança para backup híbrido](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |Desativar as funcionalidades de segurança não é suportado.
Apoiar o VM cujo tempo de máquina é alterado | Não suportado.<br/><br/> Se o tempo da máquina for alterado para uma data futura após permitir a cópia de segurança para esse VM; No entanto, mesmo que a mudança de tempo seja revertida, o reforço bem sucedido não está garantido.  

## <a name="operating-system-support-windows"></a>Suporte do sistema operativo (Windows)

A tabela seguinte resume os sistemas operativos suportados ao apoiar os VMs do Windows Azure.

**Cenário** | **Apoio ao OS**
--- | ---
Back up com extensão de agente Azure VM | - Cliente windows 10 (apenas 64 bits) <br/><br/>- Windows Server 2019 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2016 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2012 R2 (Datacenter/Standard) <br/><br/> - Windows Server 2008 R2 (RTM e SP1 Standard)  <br/><br/> - Windows 2008 (apenas 64 bits)
Back up with MARS agent | Sistemas operativos [suportados.](backup-support-matrix-mars-agent.md#support-for-direct-backups)
Back up com DPM/MABS | Sistemas operativos suportados para backup com [MABS](backup-mabs-protection-matrix.md) e [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807).

O Azure Backup não suporta sistemas operativos de 32 bits.

## <a name="support-for-linux-backup"></a>Suporte para backup Linux

Aqui está o que é suportado se quiser esquelhá-lo máquinas Linux.

**Ação** | **Suporte**
--- | ---
Back up Linux Azure VMs com o agente Linux Azure VM | Cópia de segurança consistente.<br/><br/> Backup consistente com aplicativos usando [scripts personalizados](backup-azure-linux-app-consistent.md).<br/><br/> Durante a restauração, pode criar um novo VM, restaurar um disco e usá-lo para criar um VM, ou restaurar um disco e usá-lo para substituir um disco num VM existente. Também pode restaurar ficheiros e pastas individuais.
Back up Linux Azure VMs com agente MARS | Não suportado.<br/><br/> O agente MARS só pode ser instalado em máquinas Windows.
Back up Linux Azure VMs com DPM/MABS | Não suportado.

## <a name="operating-system-support-linux"></a>Suporte do sistema operativo (Linux)

Para backups Azure VM Linux, o Azure Backup suporta a lista de distribuições linux [endossadas pelo Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros). Tenha em atenção o seguinte:

- O Azure Backup não suporta o Núcleo OS Linux.
- O Azure Backup não suporta sistemas operativos de 32 bits.
- Outras distribuições linux trazem-nos até ao momento em que o [agente Azure VM para o Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) esteja disponível no VM, e enquanto python for suportado.
- O Azure Backup não suporta um Linux VM configurado por procuração se não tiver a versão 2.7 da Python instalada.

## <a name="backup-frequency-and-retention"></a>Frequência e retenção de cópias de segurança

**Definição** | **Limites**
--- | ---
Pontos máximos de recuperação por instância protegida (máquina/carga de trabalho) | 9999.
Prazo máximo de validade para um ponto de recuperação | Sem limite.
Frequência máxima de reserva para o cofre (extensão Azure VM) | Uma vez por dia.
Frequência máxima de reserva para o cofre (agente MARS) | Três reforços por dia.
Frequência máxima de backup para DPM/MABS | A cada 15 minutos para o Servidor SQL.<br/><br/> Uma vez por hora para outras cargas de trabalho.
Retenção de pontos de recuperação | Diariamente, semanalmente, mensalmente e anualmente.
Período de retenção máximo | Depende da frequência de reserva.
Pontos de recuperação no disco DPM/MABS | 64 para servidores de ficheiros e 448 para servidores de aplicações.<br/><br/> Os pontos de recuperação de fitas são ilimitados para o DPM no local.

## <a name="supported-restore-methods"></a>Métodos de restauro suportados

**Restaurar o método** | **Detalhes**
--- | ---
Crie uma nova VM | Pode criar um VM durante o processo de restauro. <br/><br/> Esta opção obtém um VM básico em funcionamento. Pode especificar o nome VM, grupo de recursos, rede virtual, sub-rede e armazenamento.  
Restaurar discos | Pode restaurar um disco e usá-lo para criar um VM.<br/><br/> Ao selecionar esta opção, o Azure Backup copia os dados do cofre para uma conta de armazenamento que seleciona. O trabalho de restauro gera um modelo. Pode descarregar este modelo, usá-lo para especificar as definições de VM personalizadas e criar um VM.<br/><br/> Esta opção permite especificar mais definições que a opção anterior para criar um VM.<br/><br/>
Substitua um disco existente | Pode restaurar um disco e, em seguida, utilizar o disco restaurado para substituir um disco que está atualmente num VM.
Restaurar ficheiros | Pode recuperar ficheiros de um ponto de recuperação selecionado. Você descarrega um script para montar o disco VM a partir do ponto de recuperação. Em seguida, navegue pelos volumes do disco para encontrar os ficheiros/pastas que pretende recuperar e desmontar o disco quando terminar.

## <a name="support-for-file-level-restore"></a>Suporte para restauro ao nível de ficheiros

**Restaurar** | **Suportado**
--- | ---
Restaurar ficheiros através de sistemas operativos | Pode restaurar ficheiros em qualquer máquina que tenha o mesmo (ou compatível) SISTEMA que o VM suportado. Consulte a [tabela Os Compatível](backup-azure-restore-files-from-vm.md#system-requirements).
Restaurar ficheiros em VMs clássicos | Não suportado.
Restaurar ficheiros de VMs encriptados | Não suportado.
Restaurar ficheiros de contas de armazenamento restritas de rede | Não suportado.
Restaurar ficheiros em VMs utilizando espaços de armazenamento do Windows | Restaurar não suportado no mesmo VM.<br/><br/> Em vez disso, restaure os ficheiros num VM compatível.
Restaurar ficheiros em Linux VM utilizando matrizes LVM/raid | Restaurar não suportado no mesmo VM.<br/><br/> Restaurar num VM compatível.
Restaurar ficheiros com definições especiais de rede | Restaurar não suportado no mesmo VM. <br/><br/> Restaurar num VM compatível.

## <a name="support-for-vm-management"></a>Apoio à gestão vm

A tabela seguinte resume o suporte para backup durante as tarefas de gestão vm, tais como adicionar ou substituir discos VM.

**Restaurar** | **Suportado**
--- | ---
Restaurar através da subscrição/região/zona. | Não suportado.
Restaurar a um VM existente | Utilize a opção de substituição do disco.
Restaurar o disco com conta de armazenamento ativada para encriptação do serviço de armazenamento Azure (SSE) | Não suportado.<br/><br/> Restaurar para uma conta que não tenha SSE ativada.
Restaurar as contas de armazenamento mistas |Não suportado.<br/><br/> Com base no tipo de conta de armazenamento, todos os discos restaurados serão premium ou standard, e não misturados.
Restaurar o VM diretamente para um conjunto de disponibilidade | Para discos geridos, pode restaurar o disco e utilizar a opção de disponibilidade definida no modelo.<br/><br/> Não suportado para discos não geridos. Para discos não geridos, restaure o disco e, em seguida, crie um VM no conjunto de disponibilidade.
Restaurar a cópia de segurança de VMs não geridos após a modernização para VM gerido| Suportado.<br/><br/> Pode restaurar discos e, em seguida, criar um VM gerido.
Restaurar o VM para restaurar o ponto antes do VM ser migrado para discos geridos | Suportado.<br/><br/> Restaurá-lo em discos não geridos (predefinidos), converter os discos restaurados para o disco gerido e criar um VM com os discos geridos.
Restaurar um VM que foi apagado. | Suportado.<br/><br/> Pode restaurar o VM a partir de um ponto de recuperação.
Restaurar um VM controlador de domínio (DC) que faz parte de uma configuração multi-DC através do portal | Suportado se restaurar o disco e criar um VM utilizando o PowerShell.
Restaurar vM em diferentes redes virtuais |Suportado.<br/><br/> A rede virtual deve estar na mesma subscrição e região.

## <a name="vm-compute-support"></a>Suporte computacional VM

**Computação** | **Suporte**
--- | ---
Tamanho da VM |Qualquer tamanho De VM Azure com pelo menos 2 núcleos CPU e RAM de 1-GB.<br/><br/> [Saiba mais.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
Back up VMs em [conjuntos de disponibilidade](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) | Suportado.<br/><br/> Não é possível restaurar um VM num conjunto disponível utilizando a opção para criar rapidamente um VM. Em vez disso, quando restaurar o VM, restaure o disco e use-o para implantar um VM, ou restaurar um disco e usá-lo para substituir um disco existente.
VMs de back up que são implantados com [Hybrid Use Benefit (HUB)](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | Suportado.
Back up VMs que são implantados em um conjunto de [escala](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) |Não suportado.
VMs de reserva que são implantados do [Mercado Azure](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images)<br/><br/> (Publicado pela Microsoft, terceiros) |Suportado.<br/><br/> O VM deve estar a executar um sistema operativo suportado.<br/><br/> Ao recuperar ficheiros no VM, só pode restaurar um Sistema operativo compatível (não um Sistema operativo anterior ou posterior). Não restauramos os VMs do Azure Marketplace apoiados como VMs, uma vez que estes precisam de informações de compra, mas apenas como Discos.
VMs de back up que são implantados a partir de uma imagem personalizada (terceiros) |Suportado.<br/><br/> O VM deve estar a executar um sistema operativo suportado.<br/><br/> Ao recuperar ficheiros no VM, só pode restaurar um Sistema operativo compatível (não um Sistema operativo anterior ou posterior).
Back up VMs que são migrados para Azure| Suportado.<br/><br/> Para fazer o apoio ao VM, o agente VM deve ser instalado na máquina migrada.
Apoiar a consistência multi-VM | A Azure Backup não fornece dados e consistência de aplicação em vários VMs.
Backup com [Definições de Diagnóstico](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)  | Não suportado. <br/><br/> Se a restauração do VM Azure com configurações de diagnóstico for acionada utilizando a [Create New](backup-azure-arm-restore-vms.md#create-a-vm) option, então o restauro falha.
Restauro de VMs com fixação de zonas | Suportado (para VM que é apoiado após janeiro de 2019 e onde a [zona de disponibilidade](https://azure.microsoft.com/global-infrastructure/availability-zones/) está disponível).<br/><br/>Atualmente, apoiamos o restauro para a mesma zona que está presa em VMs. No entanto, se a zona não estiver disponível, a restauração falha.
Gen2 VMs | Suportado <br> O Azure Backup suporta backup e restauro de [VMs Gen2](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/). Quando estes VMs são restaurados do ponto de recuperação, são restaurados como [VMs Gen2](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/).

## <a name="vm-storage-support"></a>Suporte de armazenamento VM

**Componente** | **Suporte**
--- | ---
Discos de dados Azure VM | Volte a fazer um VM com 16 ou menos discos de dados.<BR> Para se registar para obter a pré-visualização privada de VMs com mais de 16 discos (até 32 discos), contacte-nos através de AskAzureBackupTeam@microsoft.com
Tamanho do disco de dados | O tamanho do disco individual pode ser até 32 TB e um máximo de 256 TB combinado para todos os discos de um VM.
Tipo de armazenamento | HDD padrão, SSD padrão, SSD premium.
Managed disks | Suportado.
Discos encriptados | Suportado.<br/><br/> Os VMs Azure ativados com encriptação de disco Azure podem ser apoiados (com ou sem a aplicação Azure AD).<br/><br/> VMs encriptados não podem ser recuperados ao nível de ficheiro/pasta. Tens de recuperar todo o VM.<br/><br/> Pode ativar a encriptação em VMs que já estão protegidos pelo Azure Backup.
Discos com acelerador de escrita ativado | Não suportado.<br/><br/> A cópia de segurança Azure exclui automaticamente os Discos com acelerador de escrita ativado durante a cópia de segurança. Uma vez que não estão apoiados, não poderá restaurar estes discos a partir de Pontos de Recuperação do VM.
Back up & Restaurar VMs/discos duplicados | O Azure Backup não suporta a duplicação. Para mais informações, consulte este [artigo](https://docs.microsoft.com/azure/backup/backup-support-matrix#disk-deduplication-support) <br/> <br/>  - O Azure Backup não desfaz a duplicação de VMs no cofre dos Serviços de Recuperação <br/> <br/>  - Se houver VMs em estado de desduplicação durante a restauração, os ficheiros não podem ser restaurados, uma vez que o cofre não compreende o formato. No entanto, poderá realizar com sucesso a restauração completa do VM.
Adicione o disco ao VM protegido | Suportado.
Redimensionar o disco em VM protegido | Suportado.
Armazenamento compartilhado| Não é suportado o backup de VMs utilizando volume partilhado de cluster (CSV) ou servidor de ficheiroscale-out. É provável que os escritores de CSV falhem durante o backup. No restauro, os discos que contenham volumes CSV podem não surgir.

## <a name="vm-network-support"></a>Suporte à rede VM

**Componente** | **Suporte**
--- | ---
Número de interfaces de rede (NICs) | Até ao número máximo de NICs suportados para um tamanho vm azure específico.<br/><br/> Os NICs são criados quando o VM é criado durante o processo de restauro.<br/><br/> O número de NICs no VM restaurado espelha o número de NICs no VM quando ativava a proteção. Remover NICs depois de ativar a proteção não afeta a contagem.
Equilibrador de carga externo/interno |Suportado. <br/><br/> [Saiba mais](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sobre restaurar VMs com configurações especiais de rede.
Múltiplos endereços IP reservados |Suportado. <br/><br/> [Saiba mais](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sobre restaurar VMs com configurações especiais de rede.
VMs com vários adaptadores de rede| Suportado. <br/><br/> [Saiba mais](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sobre restaurar VMs com configurações especiais de rede.
VMs com endereços IP públicos| Suportado.<br/><br/> Associe um endereço IP público existente com o NIC, ou crie um endereço e associe-o ao NIC após a restauração.
Grupo de segurança de rede (NSG) em NIC/subnet. |Suportado.
Endereço IP estático | Não suportado.<br/><br/> Um novo VM que é criado a partir de um ponto de restauro é atribuído um endereço IP dinâmico.<br/><br/> Para VMs clássicos, não pode fazer o back-up de um VM com um endereço IP reservado e sem ponto final definido.
Endereço IP dinâmico |Suportado.<br/><br/> Se o NIC na fonte VM utilizar endereços IP dinâmicos, por padrão, o NIC no VM restaurado também o utilizará.
Traffic Manager do Azure| Suportado.<br/><br/>Se o VM apoiado estiver no Traffic Manager, adicione manualmente o VM restaurado à mesma instância do Gestor de Tráfego.
DNS do Azure |Suportado.
DNS Personalizado |Suportado.
Conectividade de saída via http proxy | Suportado.<br/><br/> Um representante autenticado não é apoiado.
Pontos finais de serviço de rede virtual| Suportado.<br/><br/> As definições de firewall e de armazenamento de rede virtual devem permitir o acesso de todas as redes.

## <a name="vm-security-and-encryption-support"></a>Suporte de segurança e encriptação VM

A Azure Backup suporta encriptação para dados em trânsito e em repouso:

Tráfego de rede para Azure:

- O tráfego de backup dos servidores para o cofre dos Serviços de Recuperação é encriptado utilizando o Advanced Encryption Standard 256.
- Os dados de cópia de segurança são enviados sobre um link HTTPS seguro.
- Os dados de backup são armazenados no cofre dos Serviços de Recuperação de forma encriptada.
- Só tem a frase-passe para desbloquear estes dados. A Microsoft não pode desencriptar os dados de cópia de segurança em nenhum momento.

  > [!WARNING]
  > Depois de montar o cofre, só tem acesso à chave de encriptação. A Microsoft nunca mantém uma cópia e não tem acesso à chave. Se a chave estiver deslocada, a Microsoft não pode recuperar os dados de backup.

Segurança de dados:

- Ao fazer backup de VMs Azure, é necessário configurar a encriptação *dentro* da máquina virtual.
- O Azure Backup suporta a Encriptação azure disk, que utiliza o BitLocker em máquinas virtuais do Windows e nos **dm-criptografias** em máquinas virtuais Linux.
- No back-end, o Azure Backup utiliza a [encriptação do Serviço de Armazenamento do Azure](../storage/common/storage-service-encryption.md), que protege os dados inativos.

**Máquina** | **Em trânsito** | **Em repouso**
--- | --- | ---
No local máquinas Windows sem DPM/MABS | ![Sim][green] | ![Sim][green]
VMs do Azure | ![Sim][green] | ![Sim][green]
VMs no local/Azure com DPM | ![Sim][green] | ![Sim][green]
VMs no local/Azure com MABS | ![Sim][green] | ![Sim][green]

## <a name="vm-compression-support"></a>Suporte de compressão VM

O backup suporta a compressão do tráfego de reserva, tal como resumido na tabela seguinte. Tenha em atenção o seguinte:

- Para os VMs Azure, a extensão VM lê os dados diretamente da conta de armazenamento Azure sobre a rede de armazenamento. Não é necessário comprimir este tráfego.
- Se estiver a utilizar DPM ou MABS, pode poupar largura de banda comprimindo os dados antes de ser apoiado em DPM/MABS.

**Máquina** | **Compressa para MABS/DPM (TCP)** | **Compressa ao cofre (HTTPS)**
--- | --- | ---
No local máquinas Windows sem DPM/MABS | ND | ![Sim][green]
VMs do Azure | ND | ND
VMs no local/Azure com DPM | ![Sim][green] | ![Sim][green]
VMs no local/Azure com MABS | ![Sim][green] | ![Sim][green]

## <a name="next-steps"></a>Passos seguintes

- [Back up VMs Azure](backup-azure-arm-vms-prepare.md).
- [Faça o backup das máquinas do Windows diretamente,](tutorial-backup-windows-server-to-azure.md)sem um servidor de reserva.
- [Configurar o MABS](backup-azure-microsoft-azure-backup.md) para obter cópias de segurança para o Azure e, em seguida, fazer backup das cargas de trabalho para o MABS.
- [Instale dPM](backup-azure-dpm-introduction.md) para reforços para Azure, e, em seguida, faça backup de cargas para DPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
