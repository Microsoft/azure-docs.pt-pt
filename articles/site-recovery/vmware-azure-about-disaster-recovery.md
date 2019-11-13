---
title: Recuperação de desastres do VMware com Azure Site Recovery
description: Este artigo fornece uma visão geral da recuperação de desastres de VMs do VMware para o Azure usando o serviço Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 589dda80d68fba73a729da4b6e59270cc09c18cb
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954398"
---
# <a name="about-disaster-recovery-of-vmware-vms-to-azure"></a>Sobre a recuperação de desastre de VMs VMware para o Azure

Este artigo fornece uma visão geral da recuperação de desastre para VMs VMware locais para o Azure usando o serviço [Azure site Recovery](site-recovery-overview.md) .

## <a name="what-is-bcdr"></a>O que é o BCDR?

Uma estratégia de BCDR (continuidade dos negócios e recuperação de desastre) ajuda a manter seus negócios em funcionamento. Durante o tempo de inatividade planejado e interrupções inesperadas, o BCDR mantém os dados seguros e disponíveis e garante que os aplicativos continuem em execução. Além dos recursos de plataforma BCDR, como emparelhamento regional e armazenamento de alta disponibilidade, o Azure fornece serviços de recuperação como parte integrante da sua solução BCDR. Os serviços de recuperação incluem: 

- O [backup do Azure](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) faz backup dos dados locais e da VM do Azure. Você pode fazer backup de um arquivo e de pastas, de cargas de trabalho específicas ou de uma VM inteira. 
- [Azure site Recovery](site-recovery-overview.md) fornece resiliência e recuperação de desastre para aplicativos e cargas de trabalho em execução em computadores locais ou em VMs IaaS do Azure. Site Recovery orquestra a replicação e manipula o failover para o Azure quando ocorrem interrupções. Ele também trata da recuperação do Azure para seu site primário. 

## <a name="how-does-site-recovery-do-disaster-recovery"></a>Como Site Recovery a recuperação de desastres?

1. Depois de preparar o Azure e seu site local, você configura e habilita a replicação para seus computadores locais.
2. Site Recovery orquestra a replicação inicial da máquina, de acordo com as configurações de política.
3. Após a replicação inicial, Site Recovery Replica as alterações delta para o Azure. 
4. Quando tudo estiver replicando conforme o esperado, você executará uma análise de recuperação de desastre.
    - O Drill ajuda a garantir que o failover funcione conforme o esperado quando surge uma real necessidade.
    - O Drill executa um failover de teste sem afetar seu ambiente de produção.
5. Se ocorrer uma interrupção, você executará um failover completo no Azure. Você pode fazer failover em um único computador ou pode criar um plano de recuperação que faça failover de vários computadores ao mesmo tempo.
6. No failover, as VMs do Azure são criadas a partir dos dados da VM em Managed disks ou contas de armazenamento. Os usuários podem continuar acessando aplicativos e cargas de trabalho da VM do Azure
7. Quando o site local estiver disponível novamente, você realiza o failback do Azure.
8. Depois de realizar o failback e estiver trabalhando em seu site primário mais uma vez, você começará a replicar VMs locais para o Azure novamente.


## <a name="how-do-i-know-if-my-environment-is-suitable-for-disaster-recovery-to-azure"></a>Como fazer saber se meu ambiente é adequado para a recuperação de desastres no Azure?

Site Recovery pode replicar qualquer carga de trabalho em execução em uma VM VMware ou servidor físico com suporte. Aqui estão as coisas que você precisa verificar em seu ambiente:

- Se você estiver replicando VMs VMware, você está executando as versões corretas dos servidores de virtualização VMware? [Marque aqui](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers).
- Os computadores que você deseja replicar estão executando um sistema operacional com suporte? [Marque aqui](vmware-physical-azure-support-matrix.md#replicated-machines).
- Para a recuperação de desastre do Linux, os computadores que executam um armazenamento de sistema de arquivos/convidado com suporte? [Verificar aqui](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)
- Os computadores que você deseja replicar estão em conformidade com os requisitos do Azure? [Marque aqui](vmware-physical-azure-support-matrix.md#azure-vm-requirements).
- Há suporte para sua configuração de rede? [Marque aqui](vmware-physical-azure-support-matrix.md#network).
- Há suporte para a configuração de armazenamento? [Marque aqui](vmware-physical-azure-support-matrix.md#storage).


## <a name="what-do-i-need-to-set-up-in-azure-before-i-start"></a>O que é necessário configurar no Azure antes de começar?

No Azure, você precisa preparar o seguinte:

1. Verifique se sua conta do Azure tem permissões para criar VMs no Azure.
2. Crie uma rede do Azure que as VMs do Azure ingressarão quando forem criadas a partir de contas de armazenamento ou de discos gerenciados após o failover.
3. Configure um cofre dos serviços de recuperação do Azure para Site Recovery. O cofre reside no portal do Azure e é usado para implantar, configurar, orquestrar, monitorar e solucionar problemas de sua implantação do Site Recovery.

*Precisa de mais ajuda?*

Saiba como configurar [o Azure verificando sua conta](tutorial-prepare-azure.md#verify-account-permissions), criando uma [rede](tutorial-prepare-azure.md#set-up-an-azure-network)e [Configurando um cofre](tutorial-prepare-azure.md#create-a-recovery-services-vault).



## <a name="what-do-i-need-to-set-up-on-premises-before-i-start"></a>O que é necessário para configurar localmente antes de começar?

Local aqui está o que você precisa fazer:

1. Você precisa configurar algumas contas:

    - Se você estiver replicando VMs VMware, será necessária uma conta para Site Recovery acessar os hosts vCenter Server ou vSphere ESXi para descobrir automaticamente as VMs.
    - É necessária uma conta para instalar o agente do serviço de mobilidade Site Recovery em cada máquina física ou VM que você deseja replicar.

2. Você precisa verificar a compatibilidade de sua infraestrutura do VMware se você não fez isso anteriormente.
3. Verifique se você pode se conectar a VMs do Azure após um failover. Você configura o RDP em máquinas locais do Windows ou SSH em computadores Linux.

*Precisa de mais ajuda?*
- Prepare contas para [descoberta automática](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) e [instalação do serviço de mobilidade](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation).
- [Verifique](vmware-azure-tutorial-prepare-on-premises.md#check-vmware-requirements) se as configurações do VMware são compatíveis.
- [Prepare](vmware-azure-tutorial-prepare-on-premises.md#prepare-to-connect-to-azure-vms-after-failover) -se para que você se conecte no Azure após o failover.
- Se você quiser uma ajuda mais aprofundada sobre como configurar o endereçamento IP para VMs do Azure após o failover, [Leia este artigo](concepts-on-premises-to-azure-networking.md).

## <a name="how-do-i-set-up-disaster-recovery"></a>Como fazer configurar a recuperação de desastres?

Depois de ter a infraestrutura do Azure e local em vigor, você pode configurar a recuperação de desastre.

1. Para entender os componentes que você precisará implantar, examine a [arquitetura do VMware para o Azure](vmware-azure-architecture.md)e a [arquitetura física para o Azure](physical-azure-architecture.md). Há vários componentes, portanto, é importante entender como todos se encaixam.
2. **Ambiente de origem**: como uma primeira etapa na implantação, você configura o ambiente de origem de replicação. Você especifica o que deseja replicar e para onde deseja replicar.
3. **Servidor de configuração**: você precisa configurar um servidor de configuração no seu ambiente de origem local:
    - O servidor de configuração é um único computador local. Para a recuperação de desastres do VMware, recomendamos que você o implante como uma VM do VMware que pode ser implantada de um modelo de OVF baixável.
    - O servidor de configuração coordena as comunicações entre o local e o Azure
    - Alguns outros componentes são executados no computador do servidor de configuração.
        - O servidor de processo recebe, otimiza e envia dados de replicação para a conta de armazenamento em cache no Azure. Ele também trata da instalação automática do serviço de mobilidade nos computadores que você deseja replicar e executa a descoberta automática de VMs em servidores VMware.
        - O servidor de destino principal processa dados de replicação durante a reativação pós-falha a partir do Azure.
    - Configurar inclui registrar o servidor de configuração no cofre, baixar o servidor MySQL e o VMware PowerCLI e especificar as contas criadas para a instalação automática do serviço de mobilidade e descoberta.
4. **Ambiente de destino**: configure seu ambiente de destino do Azure especificando suas configurações de rede e sua assinatura do Azure.
5. **Política de replicação**: especifique como a replicação deve ocorrer. As configurações incluem com que frequência os pontos de recuperação são criados e armazenados e se os instantâneos consistentes com o aplicativo devem ser criados.
6. **Habilite a replicação**. Você habilita a replicação para computadores locais. Se você criou uma conta para instalar o serviço de mobilidade, ela será instalada quando você habilitar a replicação para um computador. 

*Precisa de mais ajuda?*

- Para obter uma breve explicação dessas etapas, você pode experimentar o nosso [tutorial do VMware](vmware-azure-tutorial.md)e o [Guia do servidor físico](physical-azure-disaster-recovery.md).
- [Saiba mais](vmware-azure-set-up-source.md) sobre como configurar seu ambiente de origem.
- [Saiba mais sobre](vmware-azure-deploy-configuration-server.md) os requisitos do servidor de configuração e Configurando o servidor de configuração com um modelo OVF para replicação do VMware. Se, por alguma razão, você não puder usar um modelo ou estiver replicando servidores físicos, [Use estas instruções](physical-azure-set-up-source.md#set-up-the-source-environment).
- [Saiba mais](vmware-azure-set-up-target.md) sobre as configurações de destino.
- [Obtenha mais informações](vmware-azure-set-up-replication.md) sobre como configurar uma política de replicação.
- [Saiba](vmware-azure-enable-replication.md) como habilitar a replicação e [excluir](vmware-azure-exclude-disk.md) discos da replicação.


## <a name="something-went-wrong-how-do-i-troubleshoot"></a>Algo deu errado, como posso solucionar problemas?

- Como uma primeira etapa, tente [monitorar sua implantação](site-recovery-monitor-and-troubleshoot.md) para verificar o status de itens replicados, trabalhos e problemas de infraestrutura e identificar os erros.
- Se você não conseguir concluir a replicação inicial ou se a replicação em andamento não estiver funcionando conforme o esperado, [examine este artigo](vmware-azure-troubleshoot-replication.md) para obter erros comuns e dicas de solução de problemas.
- Se você estiver tendo problemas com a instalação automática do serviço de mobilidade nos computadores que deseja replicar, examine os erros comuns neste [artigo](vmware-azure-troubleshoot-push-install.md).
- Se o failover não estiver funcionando conforme o esperado, verifique os erros comuns neste [artigo](site-recovery-failover-to-azure-troubleshoot.md).
- Se o failback não estiver funcionando, verifique se o seu problema aparece neste [artigo](vmware-azure-troubleshoot-failback-reprotect.md).



## <a name="next-steps"></a>Passos seguintes

Com a replicação agora em vigor, você deve [executar uma análise de recuperação de desastre](tutorial-dr-drill-azure.md) para garantir que o failover funcione conforme o esperado. 
