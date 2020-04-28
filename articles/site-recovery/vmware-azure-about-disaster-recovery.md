---
title: Recuperação de desastres vMware com recuperação do site Azure
description: Este artigo fornece uma visão geral da recuperação de desastres de VMware VMs para Azure usando o serviço de recuperação de sites Azure.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 589dda80d68fba73a729da4b6e59270cc09c18cb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73954398"
---
# <a name="about-disaster-recovery-of-vmware-vms-to-azure"></a>Sobre a recuperação de desastres de VMware VMs para Azure

Este artigo fornece uma visão geral da recuperação de desastres para VMware VMware vMs no local para Azure usando o serviço de recuperação de [sites Azure.](site-recovery-overview.md)

## <a name="what-is-bcdr"></a>O que é BCDR?

Uma estratégia de continuidade de negócios e recuperação de desastres (BCDR) ajuda a manter o seu negócio em funcionamento. Durante o tempo de paragem planeado e paragens inesperadas, o BCDR mantém os dados seguros e disponíveis, e garante que as aplicações continuam a funcionar. Além das funcionalidades da plataforma BCDR, como o emparelhamento regional e o armazenamento de alta disponibilidade, o Azure fornece serviços de recuperação como parte integrante da sua solução BCDR. Os serviços de recuperação incluem: 

- [O Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) apoia os seus dados de VM no local e Azure. Pode fazer o backup a um ficheiro e pastas, cargas de trabalho específicas ou um VM inteiro. 
- [A Azure Site Recovery](site-recovery-overview.md) proporciona resiliência e recuperação de desastres para apps e cargas de trabalho em máquinas no local, ou VMs Azure IaaS. A Recuperação do Site orquestra a replicação e trata do failover para Azure quando ocorrem interrupções. Também lida com a recuperação de Azure para o seu local principal. 

## <a name="how-does-site-recovery-do-disaster-recovery"></a>Como é que a Recuperação do Site faz a recuperação de desastres?

1. Depois de preparar o Azure e o seu site no local, configura e permite a replicação para as suas máquinas no local.
2. A Recuperação do Site orquestra a replicação inicial da máquina, de acordo com as definições políticas.
3. Após a replicação inicial, a Recuperação do Site replica as alterações delta para Azure. 
4. Quando tudo está a replicar-se como esperado, fazes um exercício de recuperação de desastres.
    - O berbequim ajuda a garantir que a falha funcionará como esperado quando surgir uma necessidade real.
    - A broca realiza uma falha de teste sem afetar o seu ambiente de produção.
5. Se ocorrer uma paragem, corre-se uma falha total para o Azure. Pode falhar sobre uma única máquina, ou pode criar um plano de recuperação que falha em várias máquinas ao mesmo tempo.
6. No failover, os VMs Azure são criados a partir dos dados VM em discos geridos ou contas de armazenamento. Os utilizadores podem continuar a aceder a apps e cargas de trabalho do Azure VM
7. Quando o seu site no local estiver novamente disponível, você falha de volta de Azure.
8. Depois de falhar e de voltar a trabalhar no seu site principal, volta a replicar vMs no local para Azure novamente.


## <a name="how-do-i-know-if-my-environment-is-suitable-for-disaster-recovery-to-azure"></a>Como sei se o meu ambiente é adequado para a recuperação de desastres para O Azure?

A Recuperação do Site pode replicar qualquer carga de trabalho em execução num VM de VMware suportado ou servidor físico. Aqui estão as coisas que precisa verificar no seu ambiente:

- Se está a replicar VMware VMs, está a executar as versões certas dos servidores de virtualização VMware? [Verifique aqui.](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers)
- As máquinas que pretende replicar executam um sistema operativo suportado? [Verifique aqui.](vmware-physical-azure-support-matrix.md#replicated-machines)
- Para a recuperação de desastres do Linux, as máquinas estão a executar um sistema de ficheiros suportado/armazenamento de hóspedes? [Consulte aqui](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)
- As máquinas que pretende replicar cumprem os requisitos do Azure? [Verifique aqui.](vmware-physical-azure-support-matrix.md#azure-vm-requirements)
- A configuração da sua rede é suportada? [Verifique aqui.](vmware-physical-azure-support-matrix.md#network)
- A sua configuração de armazenamento é suportada? [Verifique aqui.](vmware-physical-azure-support-matrix.md#storage)


## <a name="what-do-i-need-to-set-up-in-azure-before-i-start"></a>O que preciso de instalar em Azure antes de começar?

Em Azure é necessário preparar o seguinte:

1. Verifique se a sua conta Azure tem permissões para criar VMs em Azure.
2. Crie uma rede Azure à qual os VMs Azure se juntarão quando forem criados a partir de contas de armazenamento ou discos geridos após a falha.
3. Instale um cofre de Serviços de Recuperação Azure para recuperação do local. O cofre reside no portal Azure, e é usado para implantar, configurar, orquestrar, monitorizar e resolver problemas a sua implantação de Recuperação do Local.

*Precisa de mais ajuda?*

Aprenda a configurar o Azure [verificando a sua conta,](tutorial-prepare-azure.md#verify-account-permissions)criando uma [rede](tutorial-prepare-azure.md#set-up-an-azure-network)e [instalando um cofre.](tutorial-prepare-azure.md#create-a-recovery-services-vault)



## <a name="what-do-i-need-to-set-up-on-premises-before-i-start"></a>O que preciso de instalar no local antes de começar?

No local, eis o que tens de fazer:

1. Precisa de configurar algumas contas:

    - Se estiver a replicar VMs VMware, é necessária uma conta para a Recuperação do Site aceder aos anfitriões vCenter Server ou vSphere ESXi para descobrir automaticamente VMs.
    - É necessária uma conta para instalar o agente de serviço de Mobilidade de Recuperação do Site em cada máquina física ou VM que pretende replicar.

2. Você precisa verificar a compatibilidade da sua infraestrutura VMware se você não fez isso anteriormente.
3. Certifique-se de que pode ligar-se aos VMs Azure após uma falha. Configurar RDP em máquinas Windows no local ou SSH em máquinas Linux.

*Precisa de mais ajuda?*
- Prepare contas para [a descoberta automática](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) e para a [instalação do serviço mobility.](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation)
- [Verifique se](vmware-azure-tutorial-prepare-on-premises.md#check-vmware-requirements) as definições de VMware são compatíveis.
- [Prepare-se](vmware-azure-tutorial-prepare-on-premises.md#prepare-to-connect-to-azure-vms-after-failover) para que se conecte em Azure após a falha.
- Se quiser mais ajuda aprofundada na criação de endereços IP para VMs Azure após a falha, [leia este artigo](concepts-on-premises-to-azure-networking.md).

## <a name="how-do-i-set-up-disaster-recovery"></a>Como posso preparar a recuperação de desastres?

Depois de ter a sua infraestrutura Azure e no local, pode configurar a recuperação de desastres.

1. Para compreender os componentes que terá de implementar, reveja o [VMware para](vmware-azure-architecture.md)a arquitetura Azure e a [arquitetura física para azure.](physical-azure-architecture.md) Há uma série de componentes, por isso é importante entender como se encaixam todos.
2. **Ambiente de origem**: Como primeiro passo na implantação, configura o ambiente de origem de replicação. Especifica-se o que quer replicar e onde quer replicar.
3. Servidor de **configuração**: Tem de configurar um servidor de configuração no ambiente de origem no local:
    - O servidor de configuração é uma única máquina no local. Para a recuperação de desastres vMware, recomendamos que o implemente como vMware VMque VM que pode ser implantado a partir de um modelo OVF descarregado.
    - O servidor de configuração coordena as comunicações entre as instalações e o Azure
    - Alguns outros componentes funcionam na máquina do servidor de configuração.
        - O servidor de processos recebe, otimiza e envia dados de replicação para a conta de armazenamento cache em Azure. Também trata da instalação automática do serviço mobility em máquinas que pretende replicar e realiza a descoberta automática de VMs em servidores VMware.
        - O servidor de destino principal processa dados de replicação durante a reativação pós-falha a partir do Azure.
    - A configuração inclui registar o servidor de configuração no cofre, descarregar o MySQL Server e vMware PowerCLI, e especificar as contas criadas para a instalação automática de serviços de descoberta e mobilidade.
4. **Ambiente de destino**: Configura o seu ambiente-alvo Azure especificando as definições de subscrição e rede do Azure.
5. **Política de replicação**: Especifica como deve ocorrer a replicação. As definições incluem a frequência com que os pontos de recuperação são criados e armazenados, e se devem ser criados instantâneos consistentes com aplicações.
6. **Ativar a replicação**. Ativa a replicação para máquinas no local. Se criou uma conta para instalar o serviço mobility, então será instalado quando ativar a replicação de uma máquina. 

*Precisa de mais ajuda?*

- Para uma rápida passagem por estes passos, pode experimentar o nosso [tutorial VMware](vmware-azure-tutorial.md)e a [passagem do servidor físico.](physical-azure-disaster-recovery.md)
- [Saiba mais](vmware-azure-set-up-source.md) sobre a criação do seu ambiente de origem.
- [Aprenda sobre](vmware-azure-deploy-configuration-server.md) os requisitos do servidor de configuração e configurar o servidor de configuração com um modelo OVF para a replicação vMware. Se por alguma razão não puder usar um modelo, ou estiver a replicar servidores físicos, [utilize estas instruções](physical-azure-set-up-source.md#set-up-the-source-environment).
- [Saiba mais](vmware-azure-set-up-target.md) sobre as definições do alvo.
- [Obtenha mais informações](vmware-azure-set-up-replication.md) sobre a criação de uma política de replicação.
- [Aprenda](vmware-azure-enable-replication.md) a permitir a replicação e [exclua](vmware-azure-exclude-disk.md) os discos da replicação.


## <a name="something-went-wrong-how-do-i-troubleshoot"></a>Alguma coisa correu mal, como é que eu rebento com problemas?

- Como primeiro passo, tente [monitorizar a sua implantação](site-recovery-monitor-and-troubleshoot.md) para verificar o estado dos itens replicados, empregos e problemas de infraestrutura, e identificar quaisquer erros.
- Se não conseguir completar a replicação inicial, ou a replicação em curso não estiver a funcionar como esperado, [reveja este artigo](vmware-azure-troubleshoot-replication.md) para erros comuns e dicas de resolução de problemas.
- Se estiver a ter problemas com a instalação automática do serviço mobility em máquinas que pretende replicar, reveja erros comuns [neste artigo](vmware-azure-troubleshoot-push-install.md).
- Se a falha não estiver a funcionar como esperado, verifique os erros comuns [neste artigo.](site-recovery-failover-to-azure-troubleshoot.md)
- Se o failback não estiver a funcionar, verifique se o seu problema aparece [neste artigo](vmware-azure-troubleshoot-failback-reprotect.md).



## <a name="next-steps"></a>Passos seguintes

Com a replicação agora em vigor, deve executar um exercício de recuperação de [desastres](tutorial-dr-drill-azure.md) para garantir que a falha funcione como esperado. 
