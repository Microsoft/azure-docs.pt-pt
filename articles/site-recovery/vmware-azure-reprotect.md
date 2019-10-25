---
title: Proteger novamente as VMs do Azure para um site local durante a recuperação de desastre de VMs VMware e servidores físicos | Microsoft Docs
description: Após o failover para o Azure durante a recuperação de desastre de VMs VMware e servidores físicos, saiba como fazer failback do Azure para o site local.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: mayg
ms.openlocfilehash: cf1ccdf953781ca9b9bd17152f2cf32677997d12
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791811"
---
# <a name="reprotect-and-fail-back-machines-to-an-on-premises-site-after-failover-to-azure"></a>Proteger novamente e executar failback de computadores em um site local após o failover para o Azure

Após o [failover](site-recovery-failover.md) de VMs VMware locais ou de servidores físicos para o Azure, a primeira etapa ao fazer failback para seu site local é proteger novamente as VMs do Azure que foram criadas durante o failover. Este artigo descreve como fazer isso. 

Para obter uma visão geral rápida, Assista ao vídeo a seguir sobre como fazer failover do Azure para um site local.<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="before-you-begin"></a>Antes de começar

Se você usou um modelo para criar suas máquinas virtuais, certifique-se de que cada máquina virtual tenha seu próprio UUID para os discos. Se o UUID da máquina virtual local conflitar com o UUID do destino mestre porque ambos foram criados a partir do mesmo modelo, a nova proteção falhará. Implante outro destino mestre que não foi criado a partir do mesmo modelo. Tenha em atenção as seguintes informações:
- Se você estiver tentando fazer failback para um vCenter alternativo, certifique-se de que seu novo vCenter e o servidor de destino mestre sejam descobertos. Um sintoma típico é que os repositórios de armazenamento não estão acessíveis ou não estão visíveis na caixa de diálogo **proteger** novamente.
- Para replicar de volta para o local, você precisa de uma política de failback. Essa política é criada automaticamente quando você cria uma política de direção de encaminhamento. Tenha em atenção as seguintes informações:
    - Essa política é associada automaticamente ao servidor de configuração durante a criação.
    - Essa política não é editável.
    - Os valores definidos da política são (limite RPO = 15 minutos, retenção do ponto de recuperação = 24 horas, frequência do instantâneo de consistência do aplicativo = 60 min).
- Durante a nova proteção e o failback, o servidor de configuração local deve estar em execução e conectado.
- Se um servidor vCenter gerenciar as máquinas virtuais para as quais você fará failback, verifique se você tem as [permissões necessárias](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) para a descoberta de VMs em servidores vCenter.
- Exclua os instantâneos no servidor de destino mestre antes de proteger novamente. Se os instantâneos estiverem presentes no destino mestre local ou na máquina virtual, a nova proteção falhará. Os instantâneos na máquina virtual são mesclados automaticamente durante um trabalho de nova proteção.
- Todas as máquinas virtuais de um grupo de replicação devem ser do mesmo tipo de sistema operacional (todas as janelas ou todo o Linux). Um grupo de replicação com sistemas operacionais mistos não tem suporte no momento para proteger novamente e fazer failback para local. Isso ocorre porque o destino mestre deve ser do mesmo sistema operacional que a máquina virtual. Todas as máquinas virtuais de um grupo de replicação devem ter o mesmo destino mestre. 
- O destino mestre deve ter uma versão do sistema operacional igual ou superior às versões do sistema operacional dos itens replicados.
- Um servidor de configuração é necessário no local quando você realiza o failback. Durante o failback, a máquina virtual deve existir no banco de dados do servidor de configuração. Caso contrário, o failback não será bem-sucedido. Certifique-se de fazer backups agendados regularmente do seu servidor de configuração. Se houver um desastre, restaure o servidor com o mesmo endereço IP para que o failback funcione. 
- A nova proteção e o failback exigem uma VPN de site a site (S2S) ou um emparelhamento privado do ExpressRoute para replicar dados. Forneça a rede para que as máquinas virtuais com failover no Azure possam acessar (ping) o servidor de configuração local. Você precisa implantar um servidor de processo na rede do Azure das máquinas virtuais com failover. Esse servidor de processo também deve ser capaz de se comunicar com o servidor de configuração local e com o servidor de destino mestre.
- Caso os endereços IP dos itens replicados tenham sido retidos no failover, a conectividade S2S ou do ExpressRoute deve ser estabelecida entre as máquinas virtuais do Azure e a NIC de failback do servidor de configuração. Observe que a retenção de endereço IP requer que o servidor de configuração tenha duas NICs-uma para conectividade de computadores de origem e outra para a conectividade de failback do Azure. Isso é para evitar a sobreposição de intervalos de endereços de sub-rede da origem e as máquinas virtuais com failover.
- Certifique-se de abrir as seguintes portas para failover e failback:

    ![Portas para failover e failback](./media/vmware-azure-reprotect/failover-failback.png)

- Leia todos os [pré-requisitos para portas e lista de permissões de URL](vmware-azure-deploy-configuration-server.md#prerequisites).

## <a name="deploy-a-process-server-in-azure"></a>Implantar um servidor de processo no Azure

Você precisa de um servidor de processo no Azure antes de realizar o failback para o site local:

- O servidor de processo recebe dados das máquinas virtuais protegidas no Azure e, em seguida, envia dados para o site local.
- Uma rede de baixa latência é necessária entre o servidor de processo e a máquina virtual protegida. Portanto, é recomendável que você implante um servidor de processo no Azure. O desempenho da replicação será maior se o servidor de processo estiver mais próximo da máquina virtual de replicação (a máquina com failover no Azure). 
- Para uma prova de conceito, você pode usar o servidor de processo local e o ExpressRoute com emparelhamento privado.

Para implantar um servidor de processo no Azure:

1. Se você precisar implantar um servidor de processo no Azure, consulte [configurar um servidor de processo no Azure para failback](vmware-azure-set-up-process-server-azure.md).
2. As VMs do Azure enviam dados de replicação para o servidor de processo. Configure redes para que as VMs do Azure possam acessar o servidor de processo.
3. Lembre-se de que a replicação do Azure para o local pode ocorrer somente pela VPN S2S ou pelo emparelhamento privado de sua rede de ExpressRoute. Verifique se há largura de banda suficiente disponível nesse canal de rede.

## <a name="deploy-a-separate-master-target-server"></a>Implantar um servidor de destino mestre separado

O servidor de destino mestre recebe dados da reativação pós-falha. Por predefinição, o servidor de destino mestre é executado no servidor de configuração no local. No entanto, dependendo do volume de tráfego de failback, talvez seja necessário criar um servidor de destino mestre separado para o failover. Veja como criar um:

* [Crie um servidor de destino mestre do Linux](vmware-azure-install-linux-master-target.md) para failback de VMs do Linux. Isso é necessário. Observe que, não há suporte para o servidor de destino mestre no LVM.
* Opcionalmente, crie um servidor de destino mestre separado para o failback de VM do Windows. Para fazer isso, execute a instalação unificada novamente e selecione para criar um servidor de destino mestre. [Saiba mais](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers). 

Depois de criar um servidor de destino mestre, execute as seguintes tarefas:

- Se a máquina virtual estiver presente localmente no servidor do vCenter, o servidor de destino mestre precisará acessar o arquivo de disco de máquina virtual (VMDK) da máquina virtual local. O acesso é necessário para gravar os dados replicados nos discos da máquina virtual. Verifique se o repositório de armazenamento da máquina virtual local está montado no host do destino mestre com acesso de leitura/gravação.
- Se a máquina virtual não estiver presente localmente no servidor do vCenter, o serviço de Site Recovery precisará criar uma nova máquina virtual durante a reproteção. Essa máquina virtual é criada no host ESX no qual você cria o destino mestre. Escolha o host ESX com cuidado, para que a máquina virtual de failback seja criada no host desejado.
- Não é possível usar o Storage vMotion para o servidor de destino mestre. Usar o Storage vMotion para o servidor de destino mestre pode causar falha no failback. A máquina virtual não pode ser iniciada porque os discos não estão disponíveis para ela. Para evitar que isso ocorra, exclua os servidores de destino mestre da sua lista do vMotion.
- Se um destino mestre passar por uma tarefa de armazenamento do vMotion após a nova proteção, os discos de máquina virtual protegidos anexados ao destino mestre serão migrados para o destino da tarefa do vMotion. Se você tentar realizar o failback depois disso, o desanexação do disco falhará porque os discos não foram encontrados. Então, torna-se difícil localizar os discos em suas contas de armazenamento. Você precisa localizá-los manualmente e anexá-los à máquina virtual. Depois disso, a máquina virtual local pode ser inicializada.
- Adicione uma unidade de retenção ao servidor de destino mestre do Windows existente. Adicione um novo disco e formate a unidade. A unidade de retenção é usada para interromper os pontos no tempo em que a máquina virtual é replicada de volta para o site local. A seguir estão alguns critérios de uma unidade de retenção. Se esses critérios não forem atendidos, a unidade não será listada para o servidor de destino mestre:
    - O volume não é usado para nenhuma outra finalidade, como um destino de replicação.
    - O volume não está no modo de bloqueio.
    - O volume não é um volume de cache. A instalação do destino mestre não pode existir nesse volume. O volume de instalação personalizada para o servidor de processo e o destino mestre não são elegíveis para um volume de retenção. Quando o servidor de processo e o destino mestre são instalados em um volume, o volume é um volume de cache do destino mestre.
    - O tipo de sistema de arquivos do volume não é FAT ou FAT32.
    - A capacidade do volume é diferente de zero.
    - O volume de retenção padrão para o Windows é o volume R.
    - O volume de retenção padrão para Linux é/mnt/Retention.
- Você deve adicionar uma nova unidade se estiver usando uma máquina do servidor de configuração/servidor de processo existente ou um servidor de escala ou de processo/servidor de destino mestre. A nova unidade deve atender aos requisitos anteriores. Se a unidade de retenção não estiver presente, ela não aparecerá na lista suspensa de seleção no Portal. Depois de adicionar uma unidade ao destino mestre local, levará até 15 minutos para que a unidade apareça na seleção no Portal. Você também pode atualizar o servidor de configuração se a unidade não aparecer após 15 minutos.
- Instale as ferramentas do VMware ou as ferramentas de VM abertas no servidor de destino mestre. Sem as ferramentas, os repositórios de armazenamento no host ESXi do destino mestre não podem ser detectados.
- Defina a configuração `disk.EnableUUID=true` nos parâmetros de configuração da máquina virtual de destino mestre no VMware. Se essa linha não existir, adicione-a. Essa configuração é necessária para fornecer um UUID consistente para o VMDK para que ele seja montado corretamente.
- O host ESX no qual o destino mestre é criado deve ter pelo menos um repositório de armazenamento de sistemas de arquivos de máquina virtual (VMFS) anexado a ele. Se nenhum repositório de dados do VMFS estiver anexado, a entrada do **repositório do armazenamento** na página proteger novamente estará vazia e você não poderá continuar.
- O servidor de destino mestre não pode ter instantâneos nos discos. Se houver instantâneos, a nova proteção e o failback falharão.
- O destino mestre não pode ter um controlador paravirtual SCSI. O controlador só pode ser um controlador lógico LSI. Sem um controlador LSI Logic, a nova proteção falha.
- Para qualquer instância, o destino mestre pode ter no máximo 60 discos anexados a ele. Se o número de máquinas virtuais que estão sendo protegidas novamente para o destino mestre local tiver mais de um número total de 60 discos, o proteja novamente para o destino mestre começará a falhar. Verifique se você tem slots de disco de destino mestre suficientes ou implante outros servidores de destino mestre.
    

## <a name="enable-reprotection"></a>Habilitar a nova proteção

Depois que uma máquina virtual é inicializada no Azure, leva algum tempo para que o agente seja registrado no servidor de configuração (até 15 minutos). Durante esse tempo, você não poderá proteger novamente e uma mensagem de erro indicará que o agente não está instalado. Se isso acontecer, aguarde alguns minutos e tente a nova proteção novamente:


1. Selecione **cofre** > **itens replicados**. Clique com o botão direito do mouse na máquina virtual que passou por failover e, em seguida, selecione **proteger novamente**. Ou, nos botões de comando, selecione o computador e, em seguida, selecione **proteger novamente**.
2. Verifique se a direção do **Azure para local** da proteção está selecionada.
3. Em **servidor de destino mestre** e **servidor de processo**, selecione o servidor de destino mestre local e o servidor de processo.  
4. Para o **repositório de armazenamento**, selecione o repositório de armazenamento no qual você deseja recuperar os discos no local. Essa opção é usada quando a máquina virtual local é excluída e você precisa criar novos discos. Essa opção será ignorada se os discos já existirem. Você ainda precisa especificar um valor.
5. Selecione a unidade de retenção.
6. A política de reativação pós-falha é selecionada automaticamente.
7. Selecione **OK** para iniciar a nova proteção. Um trabalho começa a replicar a máquina virtual do Azure para o site no local. Você pode acompanhar o progresso na guia **trabalhos** . Quando a nova proteção for realizada com sucesso, a máquina virtual entrará em um estado protegido.

Tenha em atenção as seguintes informações:
- Se você quiser recuperar em um local alternativo (quando a máquina virtual local for excluída), selecione a unidade de retenção e o repositório de armazenamento que estão configurados para o servidor de destino mestre. Quando você realiza o failback para o site local, as máquinas virtuais VMware no plano de proteção de failback usam o mesmo repositório de armazenamento que o servidor de destino mestre. Uma nova máquina virtual é criada no vCenter.
- Se você quiser recuperar a máquina virtual no Azure para uma máquina virtual local existente, monte os repositórios de armazenamento da máquina virtual local com acesso de leitura/gravação no host ESXi do servidor de destino mestre.

    ![Caixa de diálogo proteger novamente](./media/vmware-azure-reprotect/reprotectinputs.png)

- Você também pode proteger novamente no nível de um plano de recuperação. Um grupo de replicação só pode ser protegido por meio de um plano de recuperação. Ao proteger novamente usando um plano de recuperação, você deve fornecer os valores para cada computador protegido.
- Utilize o mesmo servidor de destino mestre para voltar a proteger um grupo de replicação. Se você usar um servidor de destino mestre diferente para proteger novamente um grupo de replicação, o servidor não poderá fornecer um ponto comum no tempo.
- A máquina virtual local é desativada durante a nova proteção. Desta forma, garante-se a consistência dos dados durante a replicação. Não ative a máquina virtual após a conclusão da nova proteção.



## <a name="common-issues"></a>Problemas comuns

- Se você executar uma descoberta de vCenter de usuário somente leitura e proteger as máquinas virtuais, a proteção será realizada com sucesso e o failover funcionará. Durante a nova proteção, o failover falha porque os repositórios de armazenamento não podem ser descobertos. Um sintoma é que os repositórios de armazenamento não são listados durante A nova proteção. Para resolver esse problema, você pode atualizar as credenciais do vCenter com uma conta apropriada que tenha permissões e, em seguida, repetir o trabalho. 
- Ao fazer failback de uma máquina virtual Linux e executá-la localmente, você pode ver que o pacote do Gerenciador de rede foi desinstalado do computador. Essa desinstalação ocorre porque o pacote do Gerenciador de rede é removido quando a máquina virtual é recuperada no Azure.
- Quando uma máquina virtual Linux é configurada com um endereço IP estático e faz failover no Azure, o endereço IP é adquirido do DHCP. Quando você faz failover para o local, a máquina virtual continua a usar o DHCP para adquirir o endereço IP. Entre manualmente no computador e, em seguida, defina o endereço IP de volta para um endereço estático, se necessário. Uma máquina virtual do Windows pode adquirir seu endereço IP estático novamente.
- Se você usar a edição gratuita ESXi 5,5 ou o vSphere 6 hypervisor gratuito, o failover terá sucesso, mas o failback não terá sucesso. Para habilitar o failback, atualize para a licença de avaliação do programa.
- Se você não conseguir acessar o servidor de configuração do servidor de processo, use o Telnet para verificar a conectividade com o servidor de configuração na porta 443. Você também pode tentar executar ping no servidor de configuração do servidor de processo. Um servidor de processo também deve ter uma pulsação quando estiver conectado ao servidor de configuração.
- Um servidor Windows Server 2008 R2 SP1 protegido como um servidor local físico não pode passar por failback do Azure para um site local.
- Não é possível fazer failback nas seguintes circunstâncias:
    - Você migrou computadores para o Azure. [Saiba mais](migrate-overview.md#what-do-we-mean-by-migration).
    - Você moveu uma VM para outro grupo de recursos.
    - Você excluiu a VM do Azure.
    - Você desabilitou a proteção da VM.
    - Você criou a VM manualmente no Azure. O computador deve ter sido inicialmente protegido localmente e ter feito failover no Azure antes da nova proteção.
    - Você pode falhar apenas em um host ESXi. Não é possível fazer failback de VMs VMware ou servidores físicos para hosts Hyper-V, máquinas físicas ou estações de trabalho VMware.


## <a name="next-steps"></a>Passos seguintes

Depois que a máquina virtual tiver inserido um estado protegido, você poderá [Iniciar um failback](vmware-azure-failback.md). O failback desliga a máquina virtual no Azure e inicializa a máquina virtual local. Espere algum tempo de inatividade para o aplicativo. Escolha um horário para o failback quando o aplicativo puder tolerar o tempo de inatividade.


