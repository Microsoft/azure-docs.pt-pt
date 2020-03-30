---
title: Sobre o failover e o failback na Recuperação do Site Azure
description: Aprenda sobre o failover e failable na Recuperação do Site Azure.
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: d9b54f3c452212e12419a5ffd67b116c8660308d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281811"
---
# <a name="about-on-premises-disaster-recovery-failoverfailback"></a>Sobre a recuperação de desastres no local falha/failback

Este artigo fornece uma visão geral do failover e do failback durante a recuperação de desastres de máquinas no local para Azure com a Recuperação do [Local Azure](site-recovery-overview.md).

## <a name="recovery-stages"></a>Fases de recuperação

Failover e failback na Recuperação do Site tem quatro fases:

- **Fase 1: Falhar a partir do local**: Depois de configurar a replicação ao Azure para máquinas no local, quando o seu local no local se apaga, falha-se essas máquinas até ao Azure. Após a failover, os VMs Azure são criados a partir de dados replicados.
- **Fase 2: Reproteger Os VMs Azure**: Em Azure, reprotege os VMs Azure para que comecem a replicar-se de volta ao local. O VM no local (se disponível) é desligado durante a reproteção, para ajudar a garantir a consistência dos dados.
- **Fase 3: Fail over from Azure**: When your on-in-site is running as normal again, you run another failover, this time to fail back Azure VMs to your on-site. Pode falhar de volta ao local original do qual falhou, ou a um local alternativo.
- **Fase 4: Reproteger as máquinas no local**: Depois de falhar, volte a permitir a replicação das máquinas no local para o Azure.

## <a name="failover"></a>Ativação pós-falha

Realiza uma falha como parte da sua estratégia de continuidade de negócios e recuperação de desastres (BCDR).

- Como primeiro passo na sua estratégia BCDR, você replica as suas máquinas no local para Azure de forma contínua. Os utilizadores acedem a cargas de trabalho e aplicações que estão a funcionar nas máquinas de origem no local.
- Se a necessidade surgir, por exemplo, se houver uma paragem no local, falha-se as máquinas de replicação até ao Azure. Os VMs azure são criados usando os dados replicados.
- Para a continuidade do negócio, os utilizadores podem continuar a aceder a aplicações nos VMs Do Azure.

Failover é uma atividade em duas fases:

- **Failover**: A falha que cria e traz um VM Azure utilizando o ponto de recuperação selecionado.
- **Compromisso**: Após a falha verifica-se o VM em Azure:
    - Em seguida, pode comprometer a falha ao ponto de recuperação selecionado, ou selecionar um ponto diferente para o compromisso.
    - Depois de cometer a falha, o ponto de recuperação não pode ser alterado.


## <a name="connect-to-azure-after-failover"></a>Ligue-se a Azure após a falha

Para ligar aos VMs Azure criados após a falha utilizando RDP/SSH, existem uma série de requisitos.

**Ativação pós-falha** | **Localização** | **Ações**
--- | --- | ---
**Azure VM (Windows).** | Na máquina no local antes do fracasso | **Acesso através da internet**: Ativar rdp. Certifique-se de que as regras de TCP e UDP são adicionadas para **publicamente**, e que o RDP é permitido para todos os perfis em**Aplicações permitidas**pelo **Windows Firewall** > .<br/><br/> **Acesso através do SITE-para-site VPN**: Ativar o RDP na máquina. Verifique se o RDP é permitido nas**aplicações e funcionalidades permitidas**pelo **Windows Firewall,** -> para redes **De domínio e privadas.**<br/><br/>  Certifique-se de que a política SAN do sistema operativo está definida para **OnlineAll**. [Saiba mais](https://support.microsoft.com/kb/3031135).<br/><br/> Certifique-se de que não existem atualizações do Windows pendentes no VM quando acionar uma falha. O Windows Update poderá começar quando falhar, e não poderá entrar no VM até que as atualizações estejam prontas.
**Vitrina Azure VM executando janelas** | No VM Azure após falha |  [Adicione um endereço IP público](https://aka.ms/addpublicip) para a VM.<br/><br/> As regras do grupo de segurança da rede sobre o VM falhado (e a subnet Azure a que está ligada) devem permitir a entrada de ligações à porta RDP.<br/><br/> Verifique os **diagnósticos da Boot** para verificar uma imagem do VM. Se não conseguir ligar, verifique se o VM está em execução e reveja as dicas de [resolução de problemas.](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)
**Azure VM executando Linux** | Na máquina no local antes do fracasso | Certifique-se de que o serviço Secure Shell no VM está programado para começar automaticamente no arranque do sistema.<br/><br/> Verifique se as regras de firewall permitem uma ligação SSH ao mesmo.
**Azure VM executando Linux** | No VM Azure após falha | As regras do grupo de segurança da rede sobre o VM falhado (e a subnet Azure a que está ligada) precisam de permitir a entrada de ligações à porta SSH.<br/><br/> [Adicione um endereço IP público](https://aka.ms/addpublicip) para a VM.<br/><br/> Verifique os **diagnósticos** da Boot para obter uma imagem do VM.<br/><br/>

## <a name="types-of-failover"></a>Tipos de failover

A Recuperação do Site fornece diferentes opções de failover.

**Ativação pós-falha** | **Detalhes** | **Recuperação** | **Fluxo de trabalho**
--- | --- | --- | ---
**Falha no teste** | Usado para executar um exercício que valida a sua estratégia BCDR, sem qualquer perda de dados ou tempo de inatividade.| Cria uma cópia do VM em Azure, sem impacto na replicação em curso, ou no seu ambiente de produção. | 1. Executar uma falha de teste num único VM ou em vários VMs num plano de recuperação.<br/><br/> 2. Selecione um ponto de recuperação a utilizar para a falha do teste.<br/><br/> 3. Selecione uma rede Azure na qual o Azure VM será localizado quando for criado após a falha. A rede só é utilizada para a falha do teste.<br/><br/> 4. Verifique se a broca funcionou como esperado. A Recuperação do Local limpa automaticamente os VMs criados em Azure durante a broca.
**Falha planeada-Hiper-V**  | Normalmente usado para o tempo de inatividade planeado.<br/><br/> Os VMs de origem estão desligados. Os dados mais recentes são sincronizados antes de começar a falha. | Perda de dados zero para o fluxo de trabalho planeado. | 1. Planeie uma janela de manutenção de tempo de inatividade e notifique os utilizadores.<br/><br/> 2. Leve as aplicações viradas para o utilizador offline.<br/><br/> 3. Iniciar uma falha planeada com o último ponto de recuperação. A falha não funciona se a máquina não estiver desligada, ou se forem encontrados erros.<br/><br/> 4. Após a falha, verifique se a réplica Azure VM está ativa em Azure.<br/><br/> 5. Comprometa a falha para terminar. A ação de compromisso elimina todos os pontos de recuperação.
**Failover-Hyper-V** | Normalmente, corre se houver uma paragem não planeada, ou se o local principal não estiver disponível.<br/><br/> Desliga opcionalmente o VM e sincroniza as alterações finais antes de começar a falha.  | Perda mínima de dados para apps. | 1. Inicie o seu plano BCDR. <br/><br/> 2. Iniciar uma falha. Especifique se a Recuperação do Site deve desligar o VM e sincronizar/replicar as últimas alterações antes de desencadear a falha.<br/><br/> 3. Pode falhar uma série de opções de pontode recuperação, resumidas na tabela abaixo.<br/><br/> Se não permitir a opção de desligar o VM, ou se a Recuperação do Site não puder desligá-lo, o último ponto de recuperação é usado.<br/>A falha funciona mesmo que a máquina não possa ser desligada.<br/><br/> 4. Após a falha, verifique se a réplica Azure VM está ativa em Azure.<br/> Se necessário, pode selecionar um ponto de recuperação diferente da janela de retenção de 24 horas.<br/><br/> 5. Comprometa a falha para terminar. A ação de compromisso elimina todos os pontos de recuperação disponíveis.
**Failover-VMware** | Normalmente, corre se houver uma paragem não planeada, ou se o local principal não estiver disponível.<br/><br/> Especificar opcionalmente que a Recuperação do Site deve tentar desencadear uma paragem do VM e sincronizar e replicar as alterações finais antes de dar início à falha.  | Perda mínima de dados para apps. | 1. Inicie o seu plano BCDR. <br/><br/> 2. Inicie uma falha da recuperação do local. Especifique se a recuperação do site deve tentar ativar o desactivamento do VM e sincronizar antes de executar a falha.<br/> A falha funciona mesmo que as máquinas não possam ser desligadas.<br/><br/> 3. Após a falha, verifique se a réplica Azure VM está ativa em Azure. <br/>Se necessário, pode selecionar um ponto de recuperação diferente da janela de retenção de 72 horas.<br/><br/> 5. Comprometa a falha para terminar. A ação de compromisso elimina todos os pontos de recuperação.<br/> Para os VMs do Windows, a Recuperação do Site desativa as ferramentas VMware durante a falha.

## <a name="failover-processing"></a>Processamento de failover

Em alguns cenários, o failover requer um processamento adicional que leva cerca de 8 a 10 minutos para ser concluído. Pode notar tempos de falha de teste mais longos para:

* VMware VMs executando uma versão de serviço de Mobilidade com mais de 9.8.
* Servidores físicos.
* VMware Linux VMs.
* VMs hiper-V protegidos como servidores físicos.
* VMware VMs que não têm o serviço DHCP ativado.
* VMware VMs que não têm os seguintes controladores de arranque: storvsc, vmbus, storflt, intelide, atapi.

## <a name="recovery-point-options"></a>Opções de ponto de recuperação

Durante a failover, pode selecionar várias opções de ponto de recuperação.

**Opção** | **Detalhes**
--- | ---
**Mais recente (RPO mais baixo)** | Esta opção fornece o menor objetivo de ponto de recuperação (RPO). Primeiro processa todos os dados que foram enviados para o serviço de Recuperação do Site, para criar um ponto de recuperação para cada VM, antes de falhar. Este ponto de recuperação tem todos os dados replicados para a Recuperação do Local quando a falha foi desencadeada.
**Última saqueada**  | Esta opção falha sobre os VMs até ao mais recente ponto de recuperação processado pela Recuperação do Site. Para ver o mais recente ponto de recuperação de um VM específico, verifique os **últimos pontos** de recuperação nas definições vM. Esta opção proporciona um RTO (Objetivo de Tempo de Recuperação) baixo, porque não é despendido tempo ao processar os dados não processados.
**Mais recente aplicação consistente** |  Esta opção falha sobre os VMs para o mais recente ponto de recuperação consistente de aplicações processado pela Recovery do Site, se os pontos de recuperação consistentes da aplicação estiverem ativados. Verifique o último ponto de recuperação nas definições vm.
**Mais recente multi-VM processado** | Esta opção está disponível para planos de recuperação com um ou mais VMs que têm consistência multi-VM ativada. Os VMs com a definição ativada falham até ao último ponto de recuperação consistente multi-VM comum. Quaisquer outros VMs do plano falham até ao último ponto de recuperação processado.
**Mais recente aplicação multi-VM consistente** |  Esta opção está disponível para planos de recuperação com um ou mais VMs que têm consistência multi-VM ativada. VMs que fazem parte de um grupo de replicação falham até ao mais recente ponto de recuperação consistente com aplicação multi-VM comum. Outros VMs falham no seu mais recente ponto de recuperação consistente com aplicações.
**Personalizado** | Utilize esta opção para falhar sobre um VM específico para um determinado ponto de recuperação no tempo. Esta opção não está disponível para planos de recuperação.

> [!NOTE]
> Os pontos de recuperação não podem ser migrados para outro cofre dos Serviços de Recuperação.

## <a name="reprotectionfailback"></a>Reproteção/recuo

Depois de falharem com O Azure, os VMs Azure replicados estão num estado desprotegido.

- Como primeiro passo para falhar no seu local, precisa de começar os VMs Azure replicando-se no local. O processo de reproteção depende do tipo de máquinas que falhou.
- Depois de as máquinas se replicarem de Azure para as instalações, pode executar uma falha de Azure para o seu local no local.
- Depois de as máquinas voltarem a funcionar no local, pode permitir a replicação para que se reproduzam em Azure para recuperação de desastres.

O failback funciona da seguinte forma:

- Para falhar, um VM precisa de pelo menos um ponto de recuperação para falhar. Num plano de recuperação, todos os VMs do plano precisam de pelo menos um ponto de recuperação.
- Recomendamos que utilize o **último** ponto de recuperação para falhar (este é um ponto consistente com o acidente).
    - Existe uma opção de ponto de recuperação consistente com aplicativos. Neste caso, um único VM recupera para o seu mais recente ponto de recuperação consistente com aplicações disponíveis. Para um plano de recuperação com um grupo de replicação, cada grupo de replicação recupera para o seu ponto de recuperação comum disponível.
    - Os pontos de recuperação consistentes com aplicações podem estar atrasados no tempo, e pode haver perda de dados.
- Durante a falha de Azure para o local no local, a Recuperação do Local encerra os VMs Azure. Quando comete a falha, a Recuperação do Site remove os VMs Azure falhados em Azure.


## <a name="vmwarephysical-reprotectionfailback"></a>VMware/reproteção física/failback

Para reproteger e falhar as máquinas VMware e servidores físicos do Azure para o local, precisa de uma infraestrutura de backback, e há uma série de requisitos.

- Servidor de **processo temporário em Azure**: Para falhar a partir do Azure, configura um VM Azure para funcionar como um servidor de processo para lidar com a replicação do Azure. É possível eliminar esta VM após a conclusão da reativação pós-falha.
- **Ligação VPN**: Para falhar, necessita de uma ligação VPN (ou ExpressRoute) da rede Azure para o local no local.
- **Servidor**de alvo principal separado : Por padrão, o servidor alvo principal que foi instalado com o servidor de configuração no VMware VM no local manipula falha. Se precisar de reativar grandes volumes de tráfego, instale um servidor-alvo principal no local separado para o efeito.
- **Política de reativação pós-falha**: para replicar de novo para o site no local, precisa de uma política de reativação pós-falha. Esta política é criada automaticamente quando se cria uma política de replicação a partir do local para o Azure.
    - Esta política está automaticamente associada ao servidor de configuração.
    - Não pode editar esta apólice.
    - Valores de política: limiar de RPO - 15 minutos; Retenção do ponto de recuperação - 24 Horas; Frequência instantânea consistente com aplicativos - 60 minutos.

Saiba mais sobre VMware/reproteção física e failback:
- [Reveja](vmware-azure-reprotect.md#before-you-begin) os requisitos adicionais para a reproteção e o recuo.
- [Implemente](vmware-azure-prepare-failback.md#deploy-a-process-server-in-azure) um servidor de processo sintetização em Azure.
- [Implemente](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server) um servidor de alvo principal separado.

Quando reproteger os VMs Azure para as instalações, pode especificar que pretende falhar na localização original ou num local alternativo.

- **Recuperação original**da localização : Isto falha de volta de Azure para a mesma máquina de origem no local, se existir. Neste cenário, apenas as mudanças são replicadas de volta ao local.
- **Recuperação alternativa**do local: Se a máquina no local não existir, pode falhar de Azure para um local alternativo. Quando reprotege o Azure VM para o local, a máquina no local é criada. A replicação completa de dados ocorre de Azure para o local. - [Rever](concepts-types-of-failback.md) os requisitos e limitações para o recuo da localização.



## <a name="hyper-v-reprotectionfailback"></a>Reproteção/recuo hiper-V

Para reproteger e falhar os Hiper-V V VMs de Azure para as instalações:

- Só é possível reativar os VMs hiper-V replicando-se usando uma conta de armazenamento. O failback dos Hiper-V V que se replicam usando discos geridos não é suportado.
- Os anfitriões hiper-V no local (ou System Center VMM, se utilizados) devem ser ligados ao Azure.
- Fazes um falhanço planeado de Azure para o local.
- Não é necessário criar componentes específicos para o recuo do VM Hiper-V.
- Durante a failover planeada, pode selecionar opções para sincronizar dados antes de failback:
    - **Sincronizar os dados antes**do failover : Esta opção minimiza o tempo de inatividade das máquinas virtuais à medida que sincroniza as máquinas sem as desligar.
        - Fase 1: Tira uma foto do VM Azure e copia-o para o hospedeiro Hyper-V no local. A máquina continua a funcionar em Azure.
        - Fase 2: Desliga o VM Azure para que não ocorram novas alterações. O conjunto final de alterações delta é transferido para o servidor no local e o VM no local é iniciado.
    - **Sincronizar os dados apenas durante**a failover : Esta opção é mais rápida porque esperamos que a maior parte do disco tenha mudado e, portanto, não efetuar cálculos de verificação. Faz um download do disco. Recomendamos que utilize esta opção se o VM estiver em funcionamento em Azure há algum tempo (um mês ou mais), ou se o VM no local tiver sido eliminado.

[Saiba mais](hyper-v-azure-failback.md) sobre a reproteção hyper-V e o failback.

Quando reproteger os VMs Azure para as instalações, pode especificar que pretende falhar na localização original ou num local alternativo.

- **Recuperação original**da localização : Isto falha de volta de Azure para a mesma máquina de origem no local, se existir. Neste cenário, seleciona uma das opções de sincronização descritas no procedimento anterior.
- **Recuperação alternativa**do local: Se a máquina no local não existir, pode falhar de Azure para um local alternativo. Quando reprotege o Azure VM para o local, a máquina no local é criada. Com esta opção, recomendamos que selecione a opção de sincronizar dados antes de failover
- [Reveja](hyper-v-azure-failback.md) os requisitos e limitações para o recuo da localização.


Depois de ter falhado o local no local, permite que a **Reverse Replicate** comece a replicar o VM para o Azure, completando o ciclo.




## <a name="next-steps"></a>Passos seguintes
- Falhar sobre [VMware VMware específico](vmware-azure-tutorial-failover-failback.md)
- Falhar sobre [os VMs hiper-V específicos](hyper-v-azure-failover-failback-tutorial.md).
- [Criar](site-recovery-create-recovery-plans.md) um plano de recuperação.
- Falhar sobre [os VMs num plano de recuperação.](site-recovery-failover.md)
- [Preparar para](vmware-azure-failback.md) Reproteção vMware e recuo.
- Falhar nas [Hiper-V V.](hyper-v-azure-failback.md)

