---
title: Prepare VMware VMs para reproteção e failback com recuperação do site Azure
description: Prepare-se para a reprovação de VMware VMs após falha com recuperação do site Azure
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: 5a330f8cba31640d0116ca3d5ccab352ce5b3509
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362852"
---
# <a name="prepare-for-reprotection-and-failback-of-vmware-vms"></a>Prepare-se para reproteção e reprovação de VMware VMs

Após a [falha](site-recovery-failover.md) de VMs no local ou servidores físicos para o Azure, reprotege os VMs Azure criados após a failover, de modo que se replicam de volta ao local. Com a replicação de Azure para as instalações no local, pode então falhar com uma falha de Azure para as instalações quando estiver pronto.

Antes de continuar, obtenha uma visão geral rápida com este vídeo sobre como falhar de Azure para um local no local.<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

## <a name="reprotectionfailback-components"></a>Componentes de reproteção/falha

Precisa de uma série de componentes e configurações no lugar antes de poder reproteger e falhar no Azure.

**Componente**| **Detalhes**
--- | ---
**Servidor de configuração no local** | O servidor de configuração no local deve estar a funcionar e ligado ao Azure.<br/><br/> O VM a que está a falhar deve existir na base de dados do servidor de configuração. Se o desastre afetar o servidor de configuração, restaure-o com o mesmo endereço IP para garantir que o failback funciona.<br/><br/>  Se os endereços IP de máquinas replicadas foram retidos no failover, a conectividade site-a-site (ou conectividade ExpressRoute) deve ser estabelecida entre as máquinas De VMs Azure e o NIC de backback do servidor de configuração. Para endereços IP retidos, o servidor de configuração precisa de dois NICs - um para conectividade da máquina de origem e outro para a conectividade de failback do Azure. Isto evita a sobreposição de gamas de endereços de sub-rede para a fonte e falhou sobre os VMs.
**Servidor de processos em Azure** | Precisa de um servidor de processo sintetização em Azure antes de poder voltar ao seu site no local.<br/><br/> O servidor de processos recebe dados do VM Azure protegido e envia-os para o local.<br/><br/> Precisa de uma rede de baixa latência entre o servidor de processo e o VM protegido, por isso recomendamos que implemente o servidor de processo sintetizador em Azure para um desempenho de replicação mais elevado.<br/><br/> Para a prova de conceito, pode utilizar o servidor de processos no local e o ExpressRoute com o peering privado.<br/><br/> O servidor de processo deve estar na rede Azure na qual o VM falhado está localizado. O servidor de processo também deve ser capaz de comunicar com o servidor de configuração no local e o servidor de alvo principal.
**Servidor de alvo principal separado** | O servidor de alvo principal recebe dados de falha e, por padrão, um servidor de alvo principal do Windows funciona no servidor de configuração no local.<br/><br/> Um servidor de alvo principal pode ter até 60 discos ligados a ele. Os VMs que estão a ser falhados têm mais de um total coletivo de 60 discos, ou se estiver a falhar grandes volumes de tráfego, crie um servidor-alvo principal separado para o failback.<br/><br/> Se as máquinas forem reunidas num grupo de replicação para a consistência multi-VM, os VMs devem ser todos Windows, ou devem ser todos Linux. Porquê? Porque todos os VMs de um grupo de replicação devem usar o mesmo servidor alvo principal, e o servidor alvo principal deve ter o mesmo sistema operativo (com a mesma ou uma versão mais alta) do que os das máquinas replicadas.<br/><br/> O servidor-alvo principal não deve ter fotografias nos seus discos, caso contrário a reprotecção e o recuo não funcionarão.<br/><br/> O alvo principal não pode ter um controlador SCSI paravirtual. O controlador só pode ser um controlador LSI Logic. Sem um controlador LSI Logic, a reprotecção falha.
**Política de replicação de failback** | Para replicar de volta ao local, precisa de uma política de retrocesso. Esta política é criada automaticamente quando se cria uma política de replicação para o Azure.<br/><br/> A política é associada automaticamente ao servidor de configuração. Está definido para um limiar de RPO de 15 minutos, retenção de ponto de recuperação de 24 horas, e frequência instantânea consistente com aplicativos é de 60 minutos. A apólice não pode ser editada. 
**Peering privado site-to-site VPN/ExpressRoute** | A reproteção e o failback precisam de uma ligação VPN site-to-site, ou de um peering privado ExpressRoute para replicar dados. 


## <a name="ports-for-reprotectionfailback"></a>Portas para reproteção/recuo

Algumas portas devem estar abertas para reprotecção/reprovação. O gráfico seguinte ilustra as portas e reprotege/falha o fluxo.

![Portos para falha e recuo](./media/vmware-azure-reprotect/failover-failback.png)


## <a name="deploy-a-process-server-in-azure"></a>Implementar um servidor de processo sintetizar em Azure

1. [Instale um servidor](vmware-azure-set-up-process-server-azure.md) de processo em Azure para o failback.
2. Certifique-se de que os VMs Azure podem chegar ao servidor de processos. 
3. Certifique-se de que a ligação VPN site-to-site ou a rede de peering privado ExpressRoute tem largura de banda suficiente para enviar dados do servidor de processo para o site no local.

## <a name="deploy-a-separate-master-target-server"></a>Implementar um servidor de alvo principal separado

1. Note os [requisitos e limitações](#reprotectionfailback-components)do servidor alvo principal .
2. Crie um servidor-alvo principal [do Windows](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers) ou [do Linux,](vmware-azure-install-linux-master-target.md) para combinar com o sistema operativo dos VMs que pretende reproteger e falhar.
3. Certifique-se de que não utiliza o Armazenamento vMotion para o servidor de alvo principal, ou o failback pode falhar. A máquina VM não pode arrancar porque os discos não estão disponíveis.
    - Para evitar isto, exclua o servidor alvo principal da sua lista de vMotion.
    - Se um alvo principal for submetido a uma tarefa de armazenamento vMotion após a reprotecção, os discos VM protegidos ligados ao servidor alvo principal migram para o alvo da tarefa vMotion. Se tentar esfalhar depois disto, o destacamento do disco falha porque os discos não são encontrados. Então é difícil encontrar os discos nas suas contas de armazenamento. Se isto ocorrer, encontre-os manualmente e prenda-os ao VM. Depois disso, o VM no local pode ser iniciado.

4. Adicione uma unidade de retenção ao servidor-alvo principal do Windows existente. Adicione um novo disco e forte a unidade. A unidade de retenção é usada para parar os pontos no tempo quando o VM se replica de volta ao local no local. Note estes critérios. Se não forem cumpridos, a unidade não está listada para o servidor principal do alvo:
    - O volume não é usado para qualquer outro propósito, como um alvo de replicação, e não está em modo de bloqueio.
    - O volume não é um volume de cache. O volume de instalação personalizado para o servidor de processo e alvo principal não é elegível para um volume de retenção. Quando o servidor de processo e o alvo principal são instalados num volume, o volume é um volume de cache do alvo principal.
    - O tipo de sistema de ficheiros do volume não é FAT ou FAT32.
    - A capacidade de volume não é zero.
    - O volume de retenção predefinido para Windows é o volume R.
    - O volume de retenção padrão para linux é /mnt/retenção.

5. Adicione uma unidade se estiver a utilizar um servidor de processo existente. A nova unidade deve satisfazer os requisitos no último passo. Se a unidade de retenção não estiver presente, não aparece na lista de seleção no portal. Depois de adicionar uma unidade ao alvo principal no local, leva até 15 minutos para a unidade aparecer na seleção no portal. Pode atualizar o servidor de configuração se a unidade não aparecer após 15 minutos.
6. Instale ferramentas VMware ou ferramentas abertas de vm no servidor alvo principal. Sem as ferramentas, as lojas de dados do anfitrião ESXi do alvo principal não podem ser detetadas.
7. Desloque o disco. EnableUUID=definição verdadeira nos parâmetros de configuração do VM alvo principal em VMware. Se esta linha não existir, adicione. Esta definição é necessária para fornecer um UUID consistente ao VMDK de modo a que monte corretamente.
8. Verifique os requisitos de acesso do VCenter Server:
    - Se o VM ao qual está a falhar estiver num hospedeiro ESXi gerido pelo VMware vCenter Server, o servidor-alvo principal precisa de acesso ao ficheiro VM Virtual Machine Disk (VMDK) no local, para escrever os dados replicados nos discos da máquina virtual. Certifique-se de que a loja de dados VM no local está montada no anfitrião principal alvo com acesso de leitura/escrita.
    - Se o VM não estiver num hospedeiro ESXi gerido por um VMware vCenter Server, a Recovery do Site cria um novo VM durante a reproteção. Este VM é criado no hospedeiro ESXi no qual cria o VM do servidor alvo principal. Escolha cuidadosamente o hospedeiro ESXi para criar o VM no hospedeiro que desejar. O disco rígido da VM tem de estar num arquivo de dados que seja acessível pelo anfitrião no qual o servidor de destino principal está em execução.
    - Outra opção, se o VM no local já existir para o failback, é apagá-lo antes de fazer um failback. O failback cria então um novo VM no mesmo hospedeiro que o anfitrião esXi alvo principal. Quando não consegue voltar a um local alternativo, os dados são recuperados para a mesma loja de dados e para o mesmo hospedeiro ESXi que o utilizado pelo servidor-alvo principal no local.
9. Para máquinas físicas que não reemter os VMs VMware, deve concluir a descoberta do hospedeiro em que o servidor alvo principal está a funcionar, antes de poder reproteger a máquina.
10. Verifique se o hospedeiro ESXi em que o alvo principal VM tem pelo menos uma loja de dados virtual de ficheiros de máquinas (VMFS) anexada ao mesmo. Se não forem anexadas reservas de dados VMFS, a entrada da loja de dados nas definições de reproteção está vazia e não pode proceder.


## <a name="next-steps"></a>Passos seguintes

[Reproteja](vmware-azure-reprotect.md) um VM.
