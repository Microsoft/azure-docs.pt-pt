---
title: Executar uma falha durante recuperação de desastres com recuperação do site Azure
description: Como falhar sobre VMs/servidores físicos para Azure com recuperação do site Azure.
ms.service: site-recovery
ms.topic: article
ms.date: 12/10/2019
ms.openlocfilehash: 99a197e8f5ebac8a3b0be1b567ee41b43a2c4476
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79471273"
---
# <a name="run-a-failover-from-on-premises-to-azure"></a>Executar uma falha de entrada no local para Azure

Este artigo descreve como falhar as máquinas no local para Azure em [Azure Site Recovery](site-recovery-overview.md)

## <a name="before-you-start"></a>Antes de começar

- [Conheça](failover-failback-overview.md) o processo de failover na recuperação de desastres.
- Se quiser falhar em várias máquinas, [aprenda](recovery-plan-overview.md) a juntar máquinas num plano de recuperação.
- Antes de fazer uma falha completa, faça um exercício de recuperação de [desastres](site-recovery-test-failover-to-azure.md) para garantir que tudo esteja funcionando como esperado.

## <a name="prepare-to-connect-after-failover"></a>Prepare-se para ligar após a falha

Para se certificar de que pode ligar-se aos VMs Azure que são criados após a falha, aqui estão uma série de coisas que você precisa fazer no local antes de falhar.


### <a name="prepare-on-premises-to-connect-after-failover"></a>Prepare-se no local para se ligar após a falha

Se quiser ligar-se aos VMs Azure utilizando RDP/SSH após a falha, há uma série de coisas que precisa de fazer no local antes de falhar.

**Depois do fracasso** | **Localização** | **Ações**
--- | --- | ---
**Vitrina Azure VM executando janelas** | Máquina no local antes da falha | Para aceder ao Azure VM através da internet, ativar o RDP e certificar-se de que as regras de TCP e UDP são adicionadas para **publicamente**, e que o RDP é permitido para todos os perfis em**Aplicações permitidas**pelo **Windows Firewall** > .<br/><br/> Para aceder ao Azure VM sobre uma ligação site-to-site, ative rdP na máquina e certifique-se de que o RDP é permitido nas**aplicações e funcionalidades permitidas**pelo **Windows Firewall,** -> para redes **De domínio e privadas.**<br/><br/> <br/><br/> Remova quaisquer rotas estáticas persistentes e procuração WinHTTP. Certifique-se de que a política SAN do sistema operativo está definida para **OnlineAll**. [Saiba mais](https://support.microsoft.com/kb/3031135).<br/><br/> Certifique-se de que não existem atualizações do Windows pendentes no VM quando acionar uma falha. A atualização do Windows poderá começar quando falhar novamente e não poderá entrar no VM até que a atualização esteja concluída.
**Azure VM executando Linux** | Máquina no local antes da falha | Certifique-se de que o serviço Secure Shell no VM está programado para começar automaticamente no arranque do sistema.<br/><br/> Verifique se as regras de firewall permitem uma ligação SSH ao mesmo.


## <a name="run-a-failover"></a>Executar uma ativação pós-falha

Este procedimento descreve como executar uma falha para um plano de [recuperação](site-recovery-create-recovery-plans.md). Se pretender executar uma falha para um único VM, siga as instruções para um [VMware VM](vmware-azure-tutorial-failover-failback.md), um [servidor físico](physical-to-azure-failover-failback.md)ou um [VM Hiper-V](hyper-v-azure-failover-failback-tutorial.md).


Executar o plano de recuperação failover da seguinte forma:

1. No cofre de recuperação do local, selecione **Planos** > de Recuperação*recoveryplan_name*.
2. Clique em **Failover**.

    ![Ativação pós-falha](./media/site-recovery-failover/Failover.png)

3. Na direção **Failover** > **Failover,** deixe o padrão se estiver a replicar-se em Azure.
4. Em **Failover,** selecione um **Ponto de Recuperação** para falhar.

    - **Mais recente**: Use o último ponto. Isto processa todos os dados que foram enviados para o serviço de Recuperação do Site, e cria um ponto de recuperação para cada máquina. Esta opção fornece o RPO mais baixo (Objetivo do Ponto de Recuperação) porque o VM criado após a falha tem todos os dados que foram replicados para a Recuperação do Local quando a falha foi desencadeada.
   - **Mais recente processado**: Utilize esta opção para falhar sobre os VMs até ao mais recente ponto de recuperação já processado pela Recovery do Site. Pode ver o mais recente ponto de recuperação processado nos pontos de **recuperação mais recentes**da VM . Esta opção fornece um RTO baixo, uma vez que não é gasto tempo para processar os dados não processados
   - **Mais recente:** Use esta opção para falhar VMs até ao mais recente ponto de recuperação consistente da aplicação que foi processado pela Recovery do Site.
   - **Mais recente multi-VM processado**: Com esta opção VMs que fazem parte de um grupo de replicação falha para o mais recente ponto de recuperação consistente multi-VM comum. Outras máquinas virtuais falham até ao seu mais recente ponto de recuperação processado. Esta opção é apenas para planos de recuperação que tenham pelo menos um VM com consistência multi-VM ativada.
   - **Mais recente aplicação multi-VM consistente**: Com esta opção VMs que fazem parte de um grupo de replicação falha mato para o mais recente ponto de recuperação consistente com vários VM comum. Outras máquinas virtuais falham no seu mais recente ponto de recuperação consistente com aplicações. Apenas para planos de recuperação que tenham pelo menos um VM com consistência multi-VM habilitado.
   - **Personalizado**: Não disponível para planos de recuperação. Esta opção é apenas para falhas de VMs individuais.

5. Selecione a **máquina de desligar antes** de iniciar a falha se pretender que a Recuperação do Site desligue os VMs da fonte antes de iniciar a falha. A ativação pós-falha continua, mesmo que o encerramento falhe.  

    > [!NOTE]
    > Se falhar em Hiper-V VMs, o encerramento tenta sincronizar e replicar os dados no local que ainda não foram enviados para o serviço, antes de desencadear a falha. 

6. Acompanhe os progressos na página **Jobs.** Mesmo que ocorram erros, o plano de recuperação decorre até estar concluído.
7. Depois da falha, assine no VM para validá-lo. 
8. Se quiser mudar para diferentes pontos de recuperação para utilizar para a falha, utilize o ponto de **recuperação da alteração**.
9. Quando estiver pronto, pode cometer o fracasso. A ação **'Cometer'** elimina todos os pontos de recuperação disponíveis com o serviço. A opção ponto de **recuperação change** deixará de estar disponível.

## <a name="run-a-planned-failover-hyper-v"></a>Executar uma falha planeada (Hyper-V)

Pode executar uma falha planeada para os VMs hiper-V.

- Uma falha planeada é uma opção de falha de dados zero.
- Quando uma falha planeada é desencadeada, primeiro as máquinas virtuais de origem são desligadas, os dados mais recentes são sincronizados e, em seguida, uma falha é desencadeada.
- Executa uma falha planeada usando a opção **de failover planeada.** Funciona de forma semelhante a uma falha regular.
 
## <a name="track-failovers"></a>Falhas na pista

Há uma série de empregos associados à falha.

![Ativação pós-falha](./media/site-recovery-failover/FailoverJob.png)

- **Verificação prévia**: Assegura-se de que todas as condições necessárias para a falha são satisfeitas.
- **Failover**: Processa os dados para que um VM Azure possa ser criado a partir dele. Se escolheu o **último** ponto de recuperação, é criado um ponto de recuperação a partir dos dados que foram enviados para o serviço.
- **Início**: Cria um VM Azur e utilizando os dados tratados no passo anterior.

> [!WARNING]
> **Não cancele uma falha em curso**: Antes do início da falha, a replicação parou para o VM. Se cancelar estoque de um trabalho em curso, o fracasso para, mas o VM não começará a replicar-se. A replicação não pode ser reiniciada.


### <a name="extra-failover-time"></a>Tempo extra de failover

Em alguns casos, a falha da VM requer um passo intermédio que normalmente demora cerca de 8 a 10 minutos a ser concluído. Estas são as máquinas que são afetadas por este passo/tempo adicional:

* Máquinas virtuais VMware executando uma versão de serviço de Mobilidade com mais de 9.8.
* Servidores físicos e VMs hiper-V protegidos como servidores físicos.
* VMware Linux VMs.
* VMware VMs em que estes condutores não estão presentes como condutores de botas:
    * storvsc
    * vmbus
    * storflt
    * intelide
    * atapi
* VMware VMs que não têm DHCP ativado, independentemente de estarem a usar endereços IP DHCP ou estáticos.


## <a name="automate-actions-during-failover"></a>Automatizar ações durante a failover

Talvez queira automatizar ações durante a falha. Para isso, pode utilizar scripts ou livros de automação Azure em planos de recuperação.

- [Aprenda](site-recovery-create-recovery-plans.md) sobre a criação e personalização de planos de recuperação, incluindo a adição de scripts.
- [Saiba](site-recovery-runbook-automation.md) adicionar livros de execução da Azure Automation aos planos de recuperação.


## <a name="configure-settings-after-failover"></a>Configurar as definições após a falha

### <a name="retain-drive-letters-after-failover"></a>Reter cartas de unidade após falha

A Recuperação do Site trata da retenção de cartas de unidade. Se estiver a excluir discos durante a replicação vm, [reveja um exemplo](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) de como isto funciona.

### <a name="prepare-in-azure-to-connect-after-failover"></a>Prepare-se em Azure para ligar após falha

Se pretender ligar-se aos VMs Azure que são criados após a falha utilizando RDP ou SSH, siga os requisitos resumidos na tabela.

**Ativação pós-falha** | **Localização** | **Ações**
--- | --- | ---
**Vitrina Azure VM executando janelas** | VM Azure após falha |  [Adicione um endereço IP público](https://aka.ms/addpublicip) para a VM.<br/><br/> As regras do grupo de segurança da rede sobre o VM falhado (e a subnet Azure a que está ligada) precisam de permitir a entrada de ligações à porta RDP.<br/><br/> Verifique os **diagnósticos da Boot** para verificar uma imagem do VM.<br/><br/> Se não conseguir ligar, verifique se o VM está em execução e reveja estas dicas de resolução de [problemas.](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)
**Azure VM executando Linux** | VM Azure após falha | As regras do grupo de segurança da rede sobre o VM falhado (e a subnet Azure a que está ligada) precisam de permitir a entrada de ligações à porta SSH.<br/><br/> [Adicione um endereço IP público](https://aka.ms/addpublicip) para a VM.<br/><br/> Verifique os **diagnósticos** da Boot para obter uma imagem do VM.<br/><br/>

Siga os passos descritos [aqui](site-recovery-failover-to-azure-troubleshoot.md) para resolver quaisquer problemas de conectividade após a ativação pós-falha.

## <a name="set-up-ip-addressing"></a>Configurar endereçoip

- **Endereços IP internos**: Para definir o endereço IP interno de um VM Azure após a falha, tem algumas opções:
    - Reter o mesmo endereço IP: Pode utilizar o mesmo endereço IP no VM Azure que o atribuído à máquina no local.
    - Utilize um endereço IP diferente: Pode utilizar um endereço IP diferente para o VM Azure.
    - [Saiba mais](concepts-on-premises-to-azure-networking.md#assign-an-internal-address) sobre a criação de endereços IP internos.
- **Endereços IP externos:** Pode reter endereços IP públicos no failover. Os VMs Azure criados no âmbito do processo de failover devem ser atribuídos a um endereço IP público Azure disponível na região de Azure. Pode atribuir um endereço IP público manualmente ou automatizando o processo com um plano de recuperação. [Saiba mais](concepts-public-ip-address-with-site-recovery.md).


## <a name="next-steps"></a>Passos seguintes

Depois de ter falhado, precisa de se reproteger para começar a replicar os VMs Azure de volta ao local. Depois da replicação estar a funcionar, podes voltar a falhar no local quando estiveres pronto.

- [Saiba mais](failover-failback-overview.md#reprotectionfailback) sobre reproteção e recuo.
- [Prepare-se](vmware-azure-reprotect.md) para a reproteção da VMware e para o recuo.
- [Falhar de volta](hyper-v-azure-failback.md) VMs hiper-V.
- [Conheça](physical-to-azure-failover-failback.md) o processo de failover e failback para servidores físicos.

