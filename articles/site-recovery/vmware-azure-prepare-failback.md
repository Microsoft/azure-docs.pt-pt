---
title: Prepare VMware VMs para reproteção e failback com recuperação do local de Azure
description: Prepare-se para falhar de VMware VMs após falha com recuperação do local de Azure
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: f3e352577d65bda021f886d6612c8c0101922003
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727511"
---
# <a name="prepare-for-reprotection-and-failback-of-vmware-vms"></a>Preparar para reproteção e reativação pós-falha de VMs do VMware

Após [a falha](site-recovery-failover.md) de VMware VMware no local ou servidores físicos para Azure, reprotete os VMs Azure criados após o failover, de modo que eles se reproduzem de volta ao local. Com a replicação de Azure para as instalações no local, pode então falhar, executando uma falha de Azure para o local quando estiver pronto.

Antes de continuar, obtenha uma visão geral rápida com este vídeo sobre como falhar de volta de Azure para um site no local.<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

## <a name="reprotectionfailback-components"></a>Componentes de reproteção/recuo

Precisa de vários componentes e configurações no lugar antes de poder reprotegir e falhar de volta do Azure.

| **Componente**| **Detalhes** |
| --- | --- |
| **Servidor de configuração no local** | O servidor de configuração no local deve estar a funcionar e ligado ao Azure.<br/><br/> O VM a que está a falhar deve existir na base de dados do servidor de configuração. Se o desastre afetar o servidor de configuração, restaure-o com o mesmo endereço IP para garantir que o failback funciona.<br/><br/>  Se os endereços IP das máquinas replicadas forem mantidos em falha, a conectividade site-a-local (ou conectividade ExpressRoute) deve ser estabelecida entre as máquinas Azure VMs e o NIC de recuo do servidor de configuração. Para endereços IP retidos, o servidor de configuração necessita de dois NICs - um para conectividade da máquina de origem e outro para conectividade failback do Azure. Isto evita a sobreposição de intervalos de endereços de sub-rede para a fonte e falhou em VMs. |
| **Servidor de processo em Azure** | Precisa de um servidor de processo em Azure antes de poder voltar ao seu site no local.<br/><br/> O servidor de processo recebe dados do Azure VM protegido e envia-os para o site no local.<br/><br/> Precisa de uma rede de baixa latência entre o servidor de processo e o VM protegido, por isso recomendamos que implemente o servidor de processo em Azure para um desempenho de replicação mais elevado.<br/><br/> Para uma prova de conceito, pode utilizar o servidor de processo no local e o ExpressRoute com um esprecuramento privado.<br/><br/> O servidor de processo deve estar na rede Azure na qual está localizado o falhado sobre vM. O servidor de processo também deve ser capaz de comunicar com o servidor de configuração no local e o servidor alvo principal. |
| **Servidor de alvo principal separado** | O servidor alvo principal recebe dados de falha e, por padrão, um servidor-alvo principal do Windows funciona no servidor de configuração no local.<br/><br/> Um servidor-alvo principal pode ter até 60 discos ligados a ele. Os VMs que estão a ser falhados têm mais de um total coletivo de 60 discos, ou se estiveres a falhar em grandes volumes de tráfego, cria um servidor-alvo principal separado para falhas.<br/><br/> Se as máquinas forem recolhidas num grupo de replicação para a consistência multi-VM, os VMs devem ser todos Windows, ou devem ser todos Linux. Porquê? Porque todos os VMs de um grupo de replicação devem usar o mesmo servidor alvo principal, e o servidor alvo principal deve ter o mesmo sistema operativo (com a mesma versão ou versão superior) do que os das máquinas replicadas.<br/><br/> O servidor principal do alvo não deve ter instantâneos nos discos, caso contrário a reproteção e o failback não funcionarão.<br/><br/> O alvo principal não pode ter um controlador SCSI paravirtual. O controlador só pode ser um controlador LSI Logic. Sem um controlador LSI Logic, a reproteção falha. |
| **Política de replicação de recuos** | Para replicar de volta ao local, precisa de uma política de recuo. Esta política é criada automaticamente quando cria uma política de replicação para o Azure.<br/><br/> A política é associada automaticamente ao servidor de configuração. Está definido para um limiar de RPO de 15 minutos, retenção de ponto de recuperação de 24 horas, e a frequência de instantâneo consistente com aplicações é de 60 minutos. A apólice não pode ser editada. |
| **VPN local/ExpressRoute** | A reproteção e o failback precisam de uma ligação VPN site-to-site, ou o ExpressRoute private peering para replicar dados. |


## <a name="ports-for-reprotectionfailback"></a>Portas para reproteção/recuo

Algumas portas devem estar abertas para reprotecção/falha. O gráfico que se segue ilustra as portas e o fluxo reprotetor/failback.

![Portas para failover e failback](./media/vmware-azure-reprotect/failover-failback.png)


## <a name="deploy-a-process-server-in-azure"></a>Implementar um servidor de processo em Azure

1. [Crie um servidor de processo](vmware-azure-set-up-process-server-azure.md) em Azure para o failback.
2. Certifique-se de que os VMs Azure podem chegar ao servidor de processos. 
3. Certifique-se de que a ligação VPN site-to-site ou a rede de pares privado ExpressRoute tem largura de banda suficiente para enviar dados do servidor de processo para o site no local.

## <a name="deploy-a-separate-master-target-server"></a>Implementar um servidor-alvo principal separado

1. Note os [requisitos e limitações](#reprotectionfailback-components)do servidor alvo principal .
2. Crie um servidor-alvo principal [Windows](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers) ou [Linux,](vmware-azure-install-linux-master-target.md) para corresponder ao sistema operativo dos VMs que pretende reprotegir e falhar.
3. Certifique-se de que não utiliza o Storage vMotion para o servidor alvo principal, ou o failback pode falhar. A máquina VM não pode ser iniciada porque os discos não estão disponíveis.
    - Para evitar isto, exclua o servidor alvo principal da sua lista de vMotion.
    - Se um alvo principal passar por uma tarefa de Armazenamento vMotion após a reprotecção, os discos VM protegidos ligados ao servidor alvo principal migram para o alvo da tarefa vMotion. Se tentares falhar depois disto, o desprendimento do disco falha porque os discos não são encontrados. É então difícil encontrar os discos nas suas contas de armazenamento. Se isto ocorrer, encontre-os manualmente e prenda-os ao VM. Depois disso, os VM no local podem ser iniciados.

4. Adicione uma unidade de retenção ao servidor alvo principal do Windows existente. Adicione um novo disco e formate a unidade. A unidade de retenção é utilizada para parar os pontos no tempo em que o VM se reproduz de volta ao local. Note estes critérios. Se não forem cumpridos, a unidade não está listada para o servidor principal alvo:
    - O volume não é usado para qualquer outro propósito, como um alvo de replicação, e não está em modo de bloqueio.
    - O volume não é um volume de cache. O volume de instalação personalizado para o servidor de processo e o alvo principal não é elegível para um volume de retenção. Quando o servidor de processo e o alvo principal são instalados num volume, o volume é um volume de cache do alvo principal.
    - O tipo de sistema de ficheiros do volume não é FAT ou FAT32.
    - A capacidade de volume não é zero.
    - O volume de retenção predefinido para o Windows é o volume R.
    - O volume de retenção predefinido para Linux é /mnt/retenção.

5. Adicione uma unidade se estiver a utilizar um servidor de processo existente. A nova unidade deve satisfazer os requisitos no último passo. Se a unidade de retenção não estiver presente, não aparece na lista de desistências de seleção no portal. Depois de adicionar uma unidade ao alvo principal no local, leva até 15 minutos para que a unidade apareça na seleção no portal. Pode refrescar o servidor de configuração se a unidade não aparecer após 15 minutos.
6. Instale ferramentas VMware ou ferramentas open-vm no servidor principal alvo. Sem as ferramentas, as datas-lojas do hospedeiro ESXi do alvo principal não podem ser detetadas.
7. Desemote o disco. EnableUUID=definição verdadeira nos parâmetros de configuração do VM alvo principal em VMware. Se esta linha não existir, adicione- a. Esta definição é necessária para fornecer um UUID consistente ao VMDK de modo a que este se monte corretamente.
8. Consulte os requisitos de acesso do vCenter Server:
    - Se o VM ao qual está a falhar está num anfitrião ESXi gerido pelo VMware vCenter Server, o servidor alvo principal necessita de acesso ao ficheiro VM Virtual Machine Disk (VMDK) no local, a fim de escrever os dados replicados nos discos da máquina virtual. Certifique-se de que a loja de dados VM no local está montada no anfitrião principal com acesso de leitura/escrita.
    - Se o VM não estiver num anfitrião ESXi gerido por um VMware vCenter Server, a Recuperação do Site cria um novo VM durante a reproteção. Este VM é criado no anfitrião ESXi no qual cria o servidor alvo principal VM. Escolha cuidadosamente o anfitrião ESXi para criar o VM no anfitrião que pretende. O disco rígido da VM tem de estar num arquivo de dados que seja acessível pelo anfitrião no qual o servidor de destino principal está em execução.
    - Outra opção, se o VM no local já existe para o failback, é eliminá-lo antes de fazer uma falha. O Failback cria então um novo VM no mesmo anfitrião que o anfitrião principal ESXi. Quando falha em uma localização alternativa, os dados são recuperados para a mesma datastore e o mesmo anfitrião ESXi que o utilizado pelo servidor-alvo principal no local.
9. No caso de máquinas físicas que falhem de volta aos VMware VMs, deve completar a descoberta do hospedeiro em que o servidor alvo principal está a funcionar, antes de poder reprotegir a máquina.
10. Verifique se o anfitrião ESXi no qual o VM alvo principal tem pelo menos uma loja de ficheiros de máquina virtual (VMFS) anexada a ele. Se não forem anexadas lojas de dados VMFS, a entrada da loja de dados nas definições de reproteção está vazia e não pode prosseguir.


## <a name="next-steps"></a>Passos seguintes

[Reprotegir](vmware-azure-reprotect.md) um VM.
