---
title: Recuperação de desastres VMware com recuperação do local de Azure
description: Este artigo fornece uma visão geral da recuperação de desastres de VMware VMs para Azure usando o serviço de Recuperação do Local Azure.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: dfbdff01064b483085233ece47d1d3b635b68743
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87021465"
---
# <a name="about-disaster-recovery-of-vmware-vms-to-azure"></a>Sobre a recuperação de VMware VMs para Azure

Este artigo fornece uma visão geral da recuperação de desastres para VMware VMs para Azure no local utilizando o serviço [de recuperação do local Azure.](site-recovery-overview.md)

## <a name="what-is-bcdr"></a>O que é BCDR?

Uma estratégia de continuidade de negócios e recuperação de desastres (BCDR) ajuda a manter o seu negócio a funcionar. Durante o tempo de inatividade planeado e paragens inesperadas, o BCDR mantém os dados seguros e disponíveis, e garante que as aplicações continuam a funcionar. Além das funcionalidades da plataforma BCDR, como o emparelhamento regional e o armazenamento de alta disponibilidade, o Azure fornece serviços de recuperação como parte integrante da sua solução BCDR. Os serviços de recuperação incluem: 

- [O Azure Backup](../backup/backup-overview.md) confirma os seus dados no local e Azure VM. Pode fazer uma erção de um ficheiro e pastas, cargas de trabalho específicas ou um VM inteiro. 
- [A Azure Site Recovery](site-recovery-overview.md) proporciona resiliência e recuperação de desastres para apps e cargas de trabalho em funcionamento em máquinas no local, ou VMs Azure IaaS. A recuperação do local orquestra a replicação e as pegas falham em Azure quando ocorrem interrupções. Também trata da recuperação de Azure para o seu local principal. 

> [!NOTE]
> A Recuperação do Site não move nem armazena os dados dos clientes para fora da região alvo, na qual foi configurada a recuperação de desastres para as máquinas de origem. Os clientes podem selecionar um Cofre de Serviços de Recuperação de uma região diferente, se assim o desejarem. O Cofre de Serviços de Recuperação contém metadados, mas nenhum dado real do cliente.

## <a name="how-does-site-recovery-do-disaster-recovery"></a>Como é que a Recuperação do Local faz a recuperação de desastres?

1. Depois de preparar o Azure e o seu site no local, instalou e permitiu a replicação para as suas máquinas no local.
2. A Recuperação do Site orquestra a replicação inicial da máquina, de acordo com as definições da sua política.
3. Após a replicação inicial, a Recuperação do Local replica alterações delta para Azure. 
4. Quando tudo se está a replicar como esperado, fazes um exercício de recuperação de desastres.
    - A broca ajuda a garantir que o failover funcionará como esperado quando surge uma necessidade real.
    - A broca executa um teste de falha sem afetar o seu ambiente de produção.
5. Se ocorrer uma paragem, corre-se um fracasso total para O Azure. Pode falhar sobre uma única máquina, ou pode criar um plano de recuperação que falha em várias máquinas ao mesmo tempo.
6. No failover, os VMs Azure são criados a partir dos dados VM em discos geridos ou contas de armazenamento. Os utilizadores podem continuar a aceder a apps e cargas de trabalho a partir do Azure VM
7. Quando o seu site no local estiver novamente disponível, você recua de Azure.
8. Depois de falhar e estiver a trabalhar a partir do seu site principal mais uma vez, você começa a replicar VMs no local novamente.


## <a name="how-do-i-know-if-my-environment-is-suitable-for-disaster-recovery-to-azure"></a>Como sei se o meu ambiente é adequado para a recuperação de desastres para Azure?

A Recuperação do Site pode replicar qualquer carga de trabalho em execução num VMware VM ou servidor físico suportado. Aqui estão as coisas que precisa de verificar no seu ambiente:

- Se está a replicar VMware VMs, está a executar as versões certas dos servidores de virtualização VMware? [Veja aqui.](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers)
- As máquinas que pretende replicar estão a executar um sistema operativo suportado? [Veja aqui.](vmware-physical-azure-support-matrix.md#replicated-machines)
- Para a recuperação de desastres linux, as máquinas estão a executar um sistema de ficheiros suportado/armazenamento de hóspedes? [Veja aqui](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)
- As máquinas que pretende replicar cumprem os requisitos do Azure? [Veja aqui.](vmware-physical-azure-support-matrix.md#azure-vm-requirements)
- A sua configuração de rede está suportada? [Veja aqui.](vmware-physical-azure-support-matrix.md#network)
- A sua configuração de armazenamento está suportada? [Veja aqui.](vmware-physical-azure-support-matrix.md#storage)


## <a name="what-do-i-need-to-set-up-in-azure-before-i-start"></a>O que preciso de montar em Azure antes de começar?

Em Azure tem de preparar o seguinte:

1. Verifique se a sua conta Azure tem permissões para criar VMs em Azure.
2. Crie uma rede Azure que os VMs do Azure se juntarão quando forem criados a partir de contas de armazenamento ou discos geridos após o failover.
3. Crie um cofre dos Serviços de Recuperação Azure para recuperação do local. O cofre reside no portal Azure, e é usado para implantar, configurar, orquestrar, monitorizar e resolver problemas na sua implantação de Recuperação do Local.

*Precisa de mais ajuda?*

Aprenda a configurar o Azure [verificando a sua conta,](tutorial-prepare-azure.md#verify-account-permissions)criando uma [rede](tutorial-prepare-azure.md#set-up-an-azure-network)e [criando um cofre.](tutorial-prepare-azure.md#create-a-recovery-services-vault)



## <a name="what-do-i-need-to-set-up-on-premises-before-i-start"></a>O que preciso para montar no local antes de começar?

No local, eis o que precisa de fazer:

1. Precisa de configurar algumas contas:

    - Se estiver a replicar VMware VMs, é necessária uma conta para a Recuperação do Site aceder aos anfitriões vCenter Server ou vSphere ESXi para descobrir automaticamente VMs.
    - É necessária uma conta para instalar o agente de serviço de mobilidade de recuperação do local em cada máquina física ou VM que pretende replicar.

2. Tem de verificar a compatibilidade da sua infraestrutura VMware se não o fez anteriormente.
3. Certifique-se de que pode ligar-se aos VMs Azure após uma falha. Instale RDP em máquinas Windows ou SSH em máquinas Linux.

*Precisa de mais ajuda?*
- Prepare contas para [a descoberta automática](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) e para a [instalação do serviço mobility.](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation)
- [Verifique se](vmware-azure-tutorial-prepare-on-premises.md#check-vmware-requirements) as definições de VMware são compatíveis.
- [Prepare-se](vmware-azure-tutorial-prepare-on-premises.md#prepare-to-connect-to-azure-vms-after-failover) para que se conecte em Azure após o fracasso.
- Se quiser uma ajuda mais aprofundada na configuração do endereço IP para VMs Azure após o failover, [leia este artigo](concepts-on-premises-to-azure-networking.md).

## <a name="how-do-i-set-up-disaster-recovery"></a>Como posso preparar a recuperação de desastres?

Depois de ter a sua infraestrutura Azure e no local, pode preparar a recuperação de desastres.

1. Para compreender os componentes que terá de implementar, reveja o [VMware para a arquitetura Azure,](vmware-azure-architecture.md)e o [físico para a arquitetura Azure.](physical-azure-architecture.md) Há uma série de componentes, por isso é importante entender como se encaixam.
2. **Ambiente de origem**: Como primeiro passo na implementação, configura o ambiente de fonte de replicação. Especifica o que quer replicar e onde quer replicar.
3. **Servidor de configuração**: É necessário configurar um servidor de configuração no ambiente de origem no local:
    - O servidor de configuração é uma única máquina no local. Para a recuperação de desastres VMware, recomendamos que o implemente como VMware VMware que pode ser implantado a partir de um modelo OVF transferível.
    - O servidor de configuração coordena as comunicações entre as instalações e o Azure
    - Alguns outros componentes funcionam na máquina do servidor de configuração.
        - O servidor de processo recebe, otimiza e envia dados de replicação para cache conta de armazenamento em Azure. Também trata da instalação automática do serviço mobility em máquinas que pretende replicar e realiza a descoberta automática de VMs em servidores VMware.
        - O servidor de destino principal processa dados de replicação durante a reativação pós-falha a partir do Azure.
    - A configuração inclui registar o servidor de configuração no cofre, descarregar o MySQL Server e vMware PowerCLI e especificar as contas criadas para a instalação automática de serviços de descoberta e mobilidade.
4. **Ambiente-alvo**: Configura o ambiente Azure alvo especificando as definições de subscrição e rede do Azure.
5. **Política de replicação**: Especifica como deve ocorrer a replicação. As definições incluem a frequência com que os pontos de recuperação são criados e armazenados, e se devem ser criados instantâneos consistentes com aplicações.
6. **Ativar a replicação**. Permite a replicação de máquinas no local. Se criou uma conta para instalar o serviço Mobility, então será instalada quando ativar a replicação de uma máquina. 

*Precisa de mais ajuda?*

- Para uma rápida passagem por estes passos, pode experimentar o nosso [tutorial VMware](vmware-azure-tutorial.md)e [o servidor físico walkthrough](physical-azure-disaster-recovery.md).
- [Saiba mais](vmware-azure-set-up-source.md) sobre a configuração do seu ambiente de origem.
- [Saiba mais sobre](vmware-azure-deploy-configuration-server.md) os requisitos do servidor de configuração e configurar o servidor de configuração com um modelo OVF para replicação de VMware. Se por alguma razão não puder utilizar um modelo, ou se estiver a replicar servidores físicos, [utilize estas instruções](physical-azure-set-up-source.md#set-up-the-source-environment).
- [Saiba mais](vmware-azure-set-up-target.md) sobre as definições do alvo.
- [Obtenha mais informações](vmware-azure-set-up-replication.md) sobre a criação de uma política de replicação.
- [Saiba](vmware-azure-enable-replication.md) como permitir a replicação e [exclua](vmware-azure-exclude-disk.md) os discos da replicação.


## <a name="something-went-wrong-how-do-i-troubleshoot"></a>Se alguma coisa correu mal, como é que resolvo problemas?

- Como primeiro passo, tente [monitorizar a sua implantação](site-recovery-monitor-and-troubleshoot.md) para verificar o estado dos itens replicados, empregos e problemas de infraestrutura, e identifique quaisquer erros.
- Se não conseguir completar a replicação inicial, ou a replicação em curso não estiver a funcionar como esperado, [reveja este artigo](vmware-azure-troubleshoot-replication.md) para obter erros comuns e dicas de resolução de problemas.
- Se tiver problemas com a instalação automática do serviço mobility em máquinas que pretende replicar, reveja erros comuns [neste artigo.](vmware-azure-troubleshoot-push-install.md)
- Se o fracasso não estiver a funcionar como esperado, verifique os erros comuns [neste artigo](site-recovery-failover-to-azure-troubleshoot.md).
- Se o failback não estiver a funcionar, verifique se o seu problema aparece [neste artigo](vmware-azure-troubleshoot-failback-reprotect.md).



## <a name="next-steps"></a>Passos seguintes

Com a replicação agora no lugar, você deve [executar um exercício de recuperação de desastres](tutorial-dr-drill-azure.md) para garantir que o failover funciona como esperado. 
