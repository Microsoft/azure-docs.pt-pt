---
title: Executar um failover durante a recuperação de desastres com a recuperação do local de Azure
description: Como falhar sobre VMs/servidores físicos para Azure com Azure Site Recovery.
ms.service: site-recovery
ms.topic: article
ms.date: 12/10/2019
ms.openlocfilehash: 481e7c692be24bbebd14584f8158740a5b7043ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317893"
---
# <a name="run-a-failover-from-on-premises-to-azure"></a>Executar uma ativação pós-falha de local para o Azure

Este artigo descreve como falhar sobre máquinas no local para Azure na [Recuperação do Local de Azure](site-recovery-overview.md)

## <a name="before-you-start"></a>Antes de começar

- [Conheça](failover-failback-overview.md) o processo de falha na recuperação de desastres.
- Se quiser falhar com várias máquinas, [aprenda](recovery-plan-overview.md) a juntar máquinas num plano de recuperação.
- Antes de fazer um fracasso total, faça um [exercício de recuperação de desastres](site-recovery-test-failover-to-azure.md) para garantir que tudo está funcionando como esperado.

## <a name="prepare-to-connect-after-failover"></a>Prepare-se para ligar após falha

Para se certificar de que pode ligar-se aos VMs Azure que são criados após o failover, aqui estão algumas coisas que precisa de fazer no local antes de falhar.


### <a name="prepare-on-premises-to-connect-after-failover"></a>Preparar no local para ligar após falha

Se pretender ligar-se aos VMs Azure utilizando RDP/SSH após o failover, há uma série de coisas que precisa de fazer no local antes do failover.

**Depois do failover** | **Localização** | **Ações**
--- | --- | ---
**Azure VM executando janelas** | Máquina no local antes do failover | Para aceder ao Azure VM através da internet, ative o RDP e certifique-se de que as regras TCP e UDP são adicionadas ao **Público**, e que o PDR é permitido para todos os perfis em Apps Permitidas do Windows **Firewall**  >  **Allowed Apps**.<br/><br/> Para aceder ao Azure VM sobre uma ligação site-to-site, ative o RDP **Windows Firewall**na máquina e certifique-se de que o RDP é permitido nas  ->  **aplicações e funcionalidades permitidas**pelo Windows Firewall , para redes **de domínio e privado.**<br/><br/> <br/><br/> Remova quaisquer rotas estáticas persistentes e procuração winHTTP. Certifique-se de que a política do sistema operativo SAN está definida para **OnlineAll**. [Saiba mais](https://support.microsoft.com/kb/3031135).<br/><br/> Certifique-se de que não existem atualizações do Windows pendentes no VM quando desencadear uma falha. A atualização do Windows pode começar quando falhar e não poderá iniciar sessão no VM até que a atualização esteja concluída.
**Azure VM executando Linux** | Máquina no local antes do failover | Certifique-se de que o serviço Secure Shell no VM está programado para iniciar automaticamente no arranque do sistema.<br/><br/> Verifique se as regras de firewall permitem uma ligação SSH ao mesmo.


## <a name="run-a-failover"></a>Executar uma ativação pós-falha

Este procedimento descreve como executar um failover para um [plano de recuperação](site-recovery-create-recovery-plans.md). Se pretender executar uma falha para um único VM, siga as instruções para um [VMware VM,](vmware-azure-tutorial-failover-failback.md)um [servidor físico](physical-to-azure-failover-failback.md)ou um [VM Hiper-V](hyper-v-azure-failover-failback-tutorial.md).


Executar o plano de recuperação falha da seguinte forma:

1. No cofre de recuperação do local, selecione **Planos de Recuperação**  >  *recoveryplan_name*.
2. Clique **em Failover**.

    ![Screenshot da Recuperação do Local Azure mostrando o painel ADRP com Failover selecionado no menu Mais.](./media/site-recovery-failover/Failover.png)

3. Na **Failover**  >  **direção Failover Failover,** deixe o padrão se estiver a replicar-se em Azure.
4. Em **Failover**, selecione um **Ponto de Recuperação** para o qual falhar.

    - **Mais recente**: Use o último ponto. Isto processa todos os dados enviados para o serviço de Recuperação de Sítios, e cria um ponto de recuperação para cada máquina. Esta opção fornece o RPO mais baixo (Objetivo de Ponto de Recuperação) porque o VM criado após o failover tem todos os dados que foram replicados para a Recuperação do Local quando a falha foi desencadeada.
    Por favor, note que quando a região de origem cai, não há mais processamento de registo possível. Por isso, terás de falhar até ao último ponto de recuperação processado. Veja o próximo ponto para entender mais.
   - **Mais recente processo**: Utilize esta opção para falhar sobre VMs até ao último ponto de recuperação já processado pela Recuperação do Site. Pode ver o mais recente ponto de recuperação processado nos **últimos Pontos de Recuperação do**VM. Esta opção fornece um RTO baixo, uma vez que nenhum tempo é gasto para o processamento dos dados não processados
   - **Mais recente aplicações consistentes**: Utilize esta opção para falhar os VMs até ao mais recente ponto de recuperação consistente da aplicação que foi processado pela Recuperação do Site.
   - **Processo multi-VM mais recente**: Com esta opção, os VMs que fazem parte de um grupo de replicação falham no último ponto de recuperação consistente multi-VM comum. Outras máquinas virtuais falham no seu último ponto de recuperação processado. Esta opção destina-se apenas a planos de recuperação que tenham pelo menos um VM com consistência multi-VM ativada.
   - **Aplicações multi-VM mais recentes consistentes**: Com esta opção, os VMs que fazem parte de um grupo de replicação falham no mais recente ponto comum de recuperação multi-VM consistente com aplicações. Outras máquinas virtuais falham no seu mais recente ponto de recuperação consistente com aplicações. Apenas para planos de recuperação que tenham pelo menos um VM com consistência multi-VM habilitado.
   - **Costume**: Não disponível para planos de recuperação. Esta opção destina-se apenas ao failover de VMs individuais.

5. Selecione **a máquina de desligar antes de começar a falhar** se quiser que a Recuperação do Local desligue os VMs de origem antes de iniciar a falha. A ativação pós-falha continua, mesmo que o encerramento falhe.  

    > [!NOTE]
    > Se falhar com os Hiper-VMs, o encerramento tenta sincronizar e replicar os dados no local que ainda não foram enviados para o serviço, antes de desencadear a falha. 

6. Acompanhe o progresso na página **Jobs.** Mesmo que ocorram erros, o plano de recuperação decorre até estar completo.
7. Após o failover, inscreva-se no VM para validá-lo. 
8. Se pretender mudar para diferente ponto de recuperação para utilizar para a falha, utilize o **ponto de recuperação change**.
9. Quando estiver pronto, pode cometer o fracasso. A ação **Commit** elimina todos os pontos de recuperação disponíveis com o serviço. A opção **Ponto de Recuperação de Alteração** deixará de estar disponível.

## <a name="run-a-planned-failover-hyper-v"></a>Executar um failover planeado (Hiper-V)

Pode executar uma falha planeada para VMs hiper-V.

- Uma falha planeada é uma opção de falha de perda de dados zero.
- Quando uma falha planeada é desencadeada, primeiro as máquinas virtuais de origem são desligadas, os dados mais recentes são sincronizados e, em seguida, um failover é desencadeado.
- Executou um falhanço planeado usando a opção **de failover planeada.** Funciona de forma semelhante a uma falha regular.
 
## <a name="track-failovers"></a>Falhas na pista

Há uma série de postos de trabalho associados à falta de emprego.

![Screenshot da página Jobs mostrando uma lista de Empregos com Grupo 1: Início(1) expandido na coluna Nome. A linha para o trabalho sqlServer está em destaque.](./media/site-recovery-failover/FailoverJob.png)

- **Pré-requisitos de verificação**: Certifique-se de que todas as condições necessárias para o failover são satisfeitas.
- **Failover**: Processa os dados de modo a que possa ser criado um VM Azure a partir do mesmo. Se escolheu **o último** ponto de recuperação, é criado um ponto de recuperação a partir dos dados enviados para o serviço.
- **Início**: Cria um VM Azure utilizando os dados tratados no passo anterior.

> [!WARNING]
> **Não cancele uma falha em curso**: Antes de começar o failover, a replicação é interrompida para o VM. Se cancelar um trabalho em curso, o failover para, mas o VM não começará a replicar-se. A replicação não pode ser re recomeço.


### <a name="extra-failover-time"></a>Tempo extra de insusição

Em alguns casos, o failover VM requer um passo intermédio que normalmente leva cerca de 8 a 10 minutos para ser concluído. Estas são as máquinas que são afetadas por este passo/tempo adicional:

* Máquinas virtuais VMware executando uma versão de serviço de mobilidade com mais de 9.8.
* Servidores físicos e Hiper-VMs protegidos como servidores físicos.
* VMware Linux VMs.
* VMware VMs em que estes condutores não estão presentes como motoristas de arranque:
    * storvsc
    * vmbus
    * storflt
    * inteleto
    * atapi
* VMware VMs que não têm DHCP habilitado, independentemente de estarem a usar endereços IP DHCP ou estáticos.


## <a name="automate-actions-during-failover"></a>Automatizar ações durante o failover

É melhor automatizar ações durante o fracasso. Para isso, pode utilizar scripts ou runbooks de automação Azure em planos de recuperação.

- [Saiba como](site-recovery-create-recovery-plans.md) criar e personalizar planos de recuperação, incluindo adicionar scripts.
- [Saiba](site-recovery-runbook-automation.md) mais adicionar os runbooks da Azure Automation aos planos de recuperação.


## <a name="configure-settings-after-failover"></a>Configurações de configuração após falha

### <a name="retain-drive-letters-after-failover"></a>Reter letras de unidade após falha

A Recuperação do Site lida com a retenção de letras de unidade. Se estiver a excluir discos durante a replicação de VM, [reveja um exemplo](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) de como isto funciona.

### <a name="prepare-in-azure-to-connect-after-failover"></a>Prepare-se em Azure para ligar após falha

Se pretender ligar-se aos VMs Azure que são criados após o failover utilizando RDP ou SSH, siga os requisitos resumidos na tabela.

**Ativação pós-falha** | **Localização** | **Ações**
--- | --- | ---
**Azure VM executando janelas** | Azure VM após falha |  [Adicione um endereço IP público](https://aka.ms/addpublicip) para a VM.<br/><br/> As regras do grupo de segurança da rede sobre o falhado sobre o VM (e a sub-rede Azure à qual está ligada) precisam de permitir a entrada de ligações à porta RDP.<br/><br/> Verifique **os diagnósticos da Boot** para verificar uma imagem do VM.<br/><br/> Se não conseguir ligar, verifique se o VM está em funcionamento e reveja estas [dicas de resolução de problemas](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**Azure VM executando Linux** | Azure VM após falha | As regras do grupo de segurança da rede sobre o falhado sobre o VM (e a sub-rede Azure à qual está ligada) precisam de permitir a entrada de ligações à porta SSH.<br/><br/> [Adicione um endereço IP público](https://aka.ms/addpublicip) para a VM.<br/><br/> Verifique **os diagnósticos da Boot** para obter uma imagem do VM.<br/><br/>

Siga os passos descritos [aqui](site-recovery-failover-to-azure-troubleshoot.md) para resolver quaisquer problemas de conectividade após a ativação pós-falha.

## <a name="set-up-ip-addressing"></a>Configurar endereço IP

- **Endereços IP internos**: Para definir o endereço IP interno de um VM Azure após o failover, tem algumas opções:
    - Reter o mesmo endereço IP: Pode utilizar o mesmo endereço IP no VM Azure que o atribuído à máquina no local.
    - Utilize diferentes endereços IP: Pode utilizar um endereço IP diferente para o VM Azure.
    - [Saiba mais](concepts-on-premises-to-azure-networking.md#assign-an-internal-address) sobre a configuração de endereços IP internos.
- **Endereços IP externos**: Pode reter endereços IP públicos no failover. Os VMs Azure criados como parte do processo de failover devem ser atribuídos um endereço IP público Azure disponível na região de Azure. Pode atribuir um endereço IP público manualmente ou automatizando o processo com um plano de recuperação. [Saiba mais](concepts-public-ip-address-with-site-recovery.md).


## <a name="next-steps"></a>Passos seguintes

Depois de ter falhado, precisa de ser reprotegido para começar a replicar os VMs do Azure de volta ao local. Depois de a replicação estar a funcionar, podes voltar ao local quando estiveres pronto.

- [Saiba mais](failover-failback-overview.md#reprotectionfailback) sobre reproteção e recuo.
- [Prepare-se](vmware-azure-reprotect.md) para a reproteção de VMware e para o failback.
- [Falhar de volta](hyper-v-azure-failback.md) VMs hiper-V.
- [Saiba mais sobre](physical-to-azure-failover-failback.md) o processo de failover e failback para servidores físicos.

