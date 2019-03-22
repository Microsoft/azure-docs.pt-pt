---
title: Sobre a recuperação após desastre de VMs de VMware para o Azure com o Azure Site Recovery | Documentos da Microsoft
description: Este artigo fornece uma descrição geral da recuperação após desastre de VMs de VMware para o Azure com o serviço Azure Site Recovery.
author: raynew
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 3/18/2019
ms.author: raynew
ms.openlocfilehash: 9d7b94500320315c1379ba3dfb8b6460bc105b49
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58311203"
---
# <a name="about-disaster-recovery-of-vmware-vms-to-azure"></a>Sobre a recuperação após desastre de VMs de VMware para o Azure

Este artigo fornece uma descrição geral da recuperação após desastre para VMs de VMware no local para o Azure com o [do Azure Site Recovery](site-recovery-overview.md) serviço.

## <a name="what-is-bcdr"></a>O que é BCDR?

Uma estratégia de recuperação (BCDR) de desastre e continuidade empresarial ajuda a manter a sua empresa em funcionamento. Durante o período de indisponibilidade planeado e falhas inesperadas, BCDR mantém os dados seguros e disponíveis e garante que as aplicações continuem em execução. Para além das funcionalidades BCDR de plataforma como emparelhamento regional e o armazenamento de elevada disponibilidade, o Azure fornece serviços de recuperação como parte integrante da sua solução BCDR. Serviços de recuperação incluem: 

- [O Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) cria cópias de segurança os dados de VM do Azure e no local. Pode fazer backup de um ficheiro e pastas, cargas de trabalho específicas ou uma VM inteira. 
- [O Azure Site Recovery](site-recovery-overview.md) fornece resiliência e recuperação após desastre para aplicações e cargas de trabalho em execução em máquinas no local ou em VMs IaaS do Azure. Site Recovery orquestra a replicação e lida com ativação pós-falha do Azure quando ocorrerem falhas. Ele também faz a recuperação do Azure para o site primário. 

## <a name="how-does-site-recovery-do-disaster-recovery"></a>Como é que o Site Recovery fazer recuperação após desastre?

1. Depois de preparar o Azure e o seu site no local, configurar e ativar a replicação para as suas máquinas no local.
2. Recuperação de sites orquestra a replicação inicial da máquina, de acordo com suas configurações de diretiva.
3. Após a replicação inicial, o Site Recovery replica as alterações delta para o Azure. 
4. Quando tudo o que está a replicar conforme esperado, executar um teste de recuperação após desastre.
    - A desagregação ajuda a garantir que a ativação pós-falha irá funcionar conforme esperado quando surge uma necessidade real.
    - A desagregação executa uma ativação pós-falha de teste sem afetar o seu ambiente de produção.
5. Se ocorrer uma falha, executar uma ativação pós-falha completa para o Azure. Pode efetuar a ativação pós-falha de uma máquina individual ou pode criar um plano de recuperação com ativação pós-falha de várias máquinas ao mesmo tempo.
6. Na ativação pós-falha, as VMs do Azure são criadas a partir de dados da VM em discos geridos ou contas de armazenamento. Os utilizadores podem continuar a aceder a aplicações e cargas de trabalho da VM do Azure
7. Quando o seu site no local estiver novamente disponível, efetuar a ativação pós-falha do Azure.
8. Depois de efetuar a reativação pós-falha e trabalhar a partir do seu site primário mais uma vez, começa a replicar VMs no local para o Azure novamente.


## <a name="how-do-i-know-if-my-environment-is-suitable-for-disaster-recovery-to-azure"></a>Como posso saber se meu ambiente é adequada para a recuperação após desastre para o Azure?

Recuperação de sites pode replicar qualquer carga de trabalho em execução num servidor físico ou VM VMware suportada. Seguem-se as coisas que precisa verificar no seu ambiente:

- Se estiver a replicar VMs de VMware, está a executar as versões corretas dos servidores de Virtualização do VMware? [Verifique aqui](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers).
- São as máquinas que pretende replicar com um sistema operativo suportado? [Verifique aqui](vmware-physical-azure-support-matrix.md#replicated-machines).
- Para a recuperação de desastres do Linux, máquinas executem um armazenamento de convidado/sistema de ficheiros suportados? [Verifique aqui](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)
- As máquinas que pretende replicar são compatíveis com os requisitos do Azure? [Verifique aqui](vmware-physical-azure-support-matrix.md#azure-vm-requirements).
- A configuração de rede é suportada? [Verifique aqui](vmware-physical-azure-support-matrix.md#network).
- A configuração de armazenamento é suportada? [Verifique aqui](vmware-physical-azure-support-matrix.md#storage).


## <a name="what-do-i-need-to-set-up-in-azure-before-i-start"></a>O que é necessário configurar no Azure antes de começar?

No Azure, precisa de preparar o seguinte:

1. Certifique-se de que a sua conta do Azure tem permissões para criar VMs no Azure.
2. Crie uma rede do Azure que as VMs do Azure irão aderir quando são criados a partir de contas de armazenamento ou discos geridos após a ativação pós-falha.
3. Configure um cofre de serviços de recuperação do Azure para o Site Recovery. O Cofre reside no portal do Azure e é utilizado para implementar, configurar, orquestrar, monitorizar e resolver problemas relacionados com a implementação da recuperação de sites.

*Precisa de mais ajuda?*

Saiba como configurar o Azure ao [verificação da sua conta](tutorial-prepare-azure.md#verify-account-permissions), criar um [rede](tutorial-prepare-azure.md#set-up-an-azure-network), e [configurar um cofre](tutorial-prepare-azure.md#create-a-recovery-services-vault).



## <a name="what-do-i-need-to-set-up-on-premises-before-i-start"></a>O que é necessário configurar no local antes de começar?

No local aqui está o que precisa fazer:

1. Terá de configurar duas contas:

    - Se estiver a replicar VMs de VMware, é necessária uma conta para o Site Recovery para aceder ao vCenter Server ou anfitriões ESXi do vSphere para detetar automaticamente as VMs.
    - Uma conta é necessária para instalar o agente do serviço de mobilidade de recuperação de Site em cada máquina física ou VM que pretende replicar.

2. Precisa verificar a compatibilidade da sua infraestrutura de VMware, se não fizer isso anteriormente.
3. Certifique-se de que pode ligar a VMs do Azure após uma ativação pós-falha. Configure o RDP em máquinas do Windows no local ou SSH nas máquinas do Linux.

*Precisa de mais ajuda?*
- Preparar contas para [a deteção automática](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) e, para [instalação do serviço de mobilidade](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation).
- [Certifique-se de](vmware-azure-tutorial-prepare-on-premises.md#check-vmware-requirements) que as definições do VMware são compatíveis.
- [Preparar](vmware-azure-tutorial-prepare-on-premises.md#prepare-to-connect-to-azure-vms-after-failover) para que se ligar no Azure após a ativação pós-falha.
- Se quiser obter mais ajuda detalhada sobre como configurar o endereçamento IP para VMs do Azure após a ativação pós-falha, [Leia este artigo](concepts-on-premises-to-azure-networking.md).

## <a name="how-do-i-set-up-disaster-recovery"></a>Como configurar recuperação após desastre?

Depois de ter sua infraestrutura do Azure e no local no local, pode definir a recuperação após desastre.

1. Para compreender os componentes que terá de implementar, reveja os [VMware para a arquitetura do Azure](vmware-azure-architecture.md)e o [físico para a arquitetura do Azure](physical-azure-architecture.md). Existem vários componentes, portanto, é importante entender como elas se encaixam.
2. **Ambiente de origem**: Como primeiro passo na implementação, configure seu ambiente de origem de replicação. Especifique que pretende replicar e, em que pretende replicar para o.
3. **Servidor de configuração**: Terá de configurar um servidor de configuração no seu ambiente de origem no local:
    - O servidor de configuração é uma única máquina no local. Para a recuperação de desastre do VMware, recomendamos que a implementá-lo como uma VM de VMware que podem ser implementadas a partir de um modelo do OVF para download.
    - O servidor de configuração coordena as comunicações entre no local e o Azure
    - Alguns outros componentes em execução na máquina do servidor de configuração.
        - O servidor de processos recebe, otimiza e envia dados de replicação para a conta de armazenamento de cache no Azure. Também processa a instalação automática do serviço de mobilidade nas máquinas que pretende replicar, e efetua a deteção automática de VMs nos servidores de VMware.
        - O servidor de destino principal processa dados de replicação durante a reativação pós-falha a partir do Azure.
    - Conjunto de cópia de segurança inclui a registar o servidor de configuração no cofre, transferir o servidor MySQL e o VMware PowerCLI, e especificar as contas criadas para a deteção automática e instalação do serviço de mobilidade.
4. **Ambiente de destino**: Configurar o destino do ambiente do Azure ao especificar a sua subscrição do Azure e as definições de rede.
5. **Política de replicação**: Especificar como os replicação deve ocorrer. As definições incluem a frequência com que os pontos de recuperação são criados e armazenados, e se devem ser criados os instantâneos consistentes com a aplicação.
6. **Ativar a replicação**. Ativar a replicação para máquinas no local. Se tiver criado uma conta para instalar o serviço de mobilidade, em seguida, esta será instalada quando ativar a replicação para uma máquina. 

*Precisa de mais ajuda?*

- Para instruções rápidas uma dessas etapas, pode experimentar o nosso [VMware tutorial](vmware-azure-tutorial.md), e [instruções de servidor físico](physical-azure-disaster-recovery.md).
- [Saiba mais](vmware-azure-set-up-source.md) sobre como configurar o ambiente de origem.
- [Saiba mais sobre](vmware-azure-deploy-configuration-server.md) requisitos de servidor de configuração e como configurar o servidor de configuração com um modelo do OVF para a replicação de VMware. Se por algum motivo, não é possível utilizar um modelo ou de está a replicar servidores físicos [Utilize estas instruções](physical-azure-set-up-source.md#set-up-the-source-environment).
- [Saiba mais](vmware-azure-set-up-target.md) sobre as definições de destino.
- [Obter mais informações](vmware-azure-set-up-replication.md) sobre como configurar uma política de replicação.
- [Saiba mais](vmware-azure-enable-replication.md) como ativar a replicação, e [excluir](vmware-azure-exclude-disk.md) discos da replicação.


## <a name="something-went-wrong-how-do-i-troubleshoot"></a>Ocorreu um erro, como posso resolver?

- Como primeiro passo, tente [monitorizar a implementação](site-recovery-monitor-and-troubleshoot.md) para verificar o estado dos itens replicados, tarefas e problemas de infraestrutura e identificar eventuais erros.
- Se não for possível concluir a replicação inicial ou a replicação em curso não está a funcionar conforme esperado, [rever este artigo](vmware-azure-troubleshoot-replication.md) para erros comuns e resolução de problemas.
- Se estiver a ter problemas com a instalação automática do serviço de mobilidade nas máquinas que pretende replicar, reveja os erros comuns nos [este artigo](vmware-azure-troubleshoot-push-install.md).
- Se a ativação pós-falha não está a funcionar conforme esperado, verifique os erros comuns na [este artigo](site-recovery-failover-to-azure-troubleshoot.md).
- Se a reativação pós-falha não está a funcionar, verifique se o problema surge [este artigo](vmware-azure-troubleshoot-failback-reprotect.md).



## <a name="next-steps"></a>Passos Seguintes

Com a replicação funcionando, deve [executar um teste de recuperação após desastre](tutorial-dr-drill-azure.md) para se certificar de que a ativação pós-falha funciona conforme esperado. 
