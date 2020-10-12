---
title: Sobre failover e failback na Recuperação do Local de Azure
description: Saiba mais sobre falhas e falhas na Recuperação do Local de Azure.
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: d9b54f3c452212e12419a5ffd67b116c8660308d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87089537"
---
# <a name="about-on-premises-disaster-recovery-failoverfailback"></a>Sobre a recuperação de desastres no local/failback

Este artigo fornece uma visão geral do failover e do failback durante a recuperação de desastres de máquinas no local para Azure com [Azure Site Recovery](site-recovery-overview.md).

## <a name="recovery-stages"></a>Fases de recuperação

Failover e failback na Recuperação do Local tem quatro fases:

- **Fase 1: Falha a partir do local**: Depois de configurar a replicação para a Azure para máquinas no local, quando o seu local de aterragem se avariar, falha-as em Azure. Após o failover, os VMS Azure são criados a partir de dados replicados.
- **Fase 2: Reprotect Azure VMs**: Em Azure, reprotete os VMs Azure para que eles comecem a replicar-se de volta para o local. O VM no local (se disponível) é desligado durante a reproteção, para ajudar a garantir a consistência dos dados.
- **Fase 3: Falha a partir de Azure**: Quando o seu site no local estiver a funcionar normalmente novamente, executa outra falha, desta vez para falhar os VMs Azure para o seu local no local. Pode falhar de volta ao local original a partir do qual falhou, ou para um local alternativo.
- **Fase 4: Reprotecte máquinas no local**: Depois de ter recuado, volte a permitir a replicação das máquinas no local para a Azure.

## <a name="failover"></a>Ativação pós-falha

Executa um failover como parte da sua estratégia de continuidade de negócios e recuperação de desastres (BCDR).

- Como primeiro passo na sua estratégia BCDR, replica as suas máquinas no local para o Azure numa base contínua. Os utilizadores acedem a cargas de trabalho e aplicações em execução nas máquinas de origem no local.
- Se a necessidade surgir, por exemplo, se houver uma falha no local, falha-se as máquinas de replicação para o Azure. Os VMs Azure são criados usando os dados replicados.
- Para a continuidade do negócio, os utilizadores podem continuar a aceder a aplicações nos VMs Azure.

Failover é uma atividade em duas fases:

- **Failover**: O failover que cria e traz um VM Azure utilizando o ponto de recuperação selecionado.
- **Comprometer-se**: Após o fracasso, verifique o VM em Azure:
    - Em seguida, pode comprometer a falha no ponto de recuperação selecionado ou selecionar um ponto diferente para o compromisso.
    - Depois de cometer o fracasso, o ponto de recuperação não pode ser alterado.


## <a name="connect-to-azure-after-failover"></a>Conecte-se a Azure após o failover

Para ligar aos VMs Azure criados após a falha usando RDP/SSH, existem uma série de requisitos.

**Ativação pós-falha** | **Localização** | **Ações**
--- | --- | ---
**Azure VM (Windows).** | Na máquina no local antes do failover | **Acesso através da internet**: Ativar RDP. Certifique-se de que as regras TCP e UDP são adicionadas ao **Público**, e que o PDR é permitido para todos os perfis em Apps **Windows Firewall**  >  **Permitidas pelo**Windows Firewall .<br/><br/> **Acesso sobre o local-a-local VPN**: Ative o RDP na máquina. Verifique se o RDP **Windows Firewall**é permitido nas  ->  **aplicações e funcionalidades permitidas pelo**Windows Firewall , para redes **de domínio e privado.**<br/><br/>  Certifique-se de que a política do sistema operativo SAN está definida para **OnlineAll**. [Saiba mais](https://support.microsoft.com/kb/3031135).<br/><br/> Certifique-se de que não existem atualizações do Windows pendentes no VM quando desencadear uma falha. O Windows Update pode começar quando falhar de novo e não poderá iniciar sessão no VM até que as atualizações sejam feitas.
**Azure VM executando janelas** | No Azure VM após falha |  [Adicione um endereço IP público](https://aka.ms/addpublicip) para a VM.<br/><br/> As regras do grupo de segurança da rede sobre o falhado sobre o VM (e a sub-rede Azure à qual está ligada) devem permitir a entrada de ligações à porta RDP.<br/><br/> Verifique **os diagnósticos da Boot** para verificar uma imagem do VM. Se não conseguir ligar, verifique se o VM está em funcionamento e reveja [as dicas de resolução de problemas](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**Azure VM executando Linux** | Na máquina no local antes do failover | Certifique-se de que o serviço Secure Shell no VM está programado para iniciar automaticamente no arranque do sistema.<br/><br/> Verifique se as regras de firewall permitem uma ligação SSH ao mesmo.
**Azure VM executando Linux** | No Azure VM após falha | As regras do grupo de segurança da rede sobre o falhado sobre o VM (e a sub-rede Azure à qual está ligada) precisam de permitir a entrada de ligações à porta SSH.<br/><br/> [Adicione um endereço IP público](https://aka.ms/addpublicip) para a VM.<br/><br/> Verifique **os diagnósticos da Boot** para obter uma imagem do VM.<br/><br/>

## <a name="types-of-failover"></a>Tipos de failover

A Recuperação do Site fornece diferentes opções de failover.

**Ativação pós-falha** | **Detalhes** | **Recuperação** | **Workflow**
--- | --- | --- | ---
**Teste de failover** | Usado para executar um exercício que valida a sua estratégia BCDR, sem qualquer perda de dados ou tempo de inatividade.| Cria uma cópia do VM em Azure, sem impacto na replicação em curso, ou no seu ambiente de produção. | 1. Executar um teste de failover num único VM, ou em vários VMs num plano de recuperação.<br/><br/> 2. Selecione um ponto de recuperação para utilizar para a falha do teste.<br/><br/> 3. Selecione uma rede Azure na qual o Azure VM será localizado quando for criado após o failover. A rede só é utilizada para o teste de falha.<br/><br/> 4. Verifique se a broca funcionou como esperado. A Recuperação do Local limpa automaticamente os VMs criados em Azure durante a broca.
**Failover-Hyper-V planeado**  | Normalmente usado para o tempo de inatividade planeado.<br/><br/> Os VMs de origem estão desligados. Os dados mais recentes são sincronizados antes de iniciar a falha. | Perda zero de dados para o fluxo de trabalho planeado. | 1. Planeie uma janela de manutenção de tempo de inatividade e notifique os utilizadores.<br/><br/> 2. Desativar as aplicações viradas para o utilizador.<br/><br/> 3. Iniciar uma falha planeada com o último ponto de recuperação. O fracasso não funciona se a máquina não for desligada, ou se forem encontrados erros.<br/><br/> 4. Após a falha, verifique se a réplica Azure VM está ativa em Azure.<br/><br/> 5. Comprometa o fracasso para terminar. A ação de cometer elimina todos os pontos de recuperação.
**Failover-Hyper-V** | Normalmente, corre se houver uma paragem não planeada, ou se o site principal não estiver disponível.<br/><br/> Desligue opcionalmente o VM e sincronize as alterações finais antes de iniciar a falha.  | Perda mínima de dados para apps. | 1. Inicie o seu plano BCDR. <br/><br/> 2. Iniciar uma falha. Especificar se a Recuperação do Sítio deve desligar o VM e sincronizar/replicar as alterações mais recentes antes de desencadear a falha.<br/><br/> 3. Pode falhar em várias opções de ponto de recuperação, resumidas na tabela abaixo.<br/><br/> Se não ativar a opção de desligar o VM ou se a Recuperação do Site não puder desligá-la, o último ponto de recuperação é utilizado.<br/>O fracasso funciona mesmo que a máquina não possa ser desligada.<br/><br/> 4. Após o failover, verifique se a réplica Azure VM está ativa em Azure.<br/> Se necessário, pode selecionar um ponto de recuperação diferente da janela de retenção de 24 horas.<br/><br/> 5. Comprometa o fracasso para terminar. A ação de empenhamento elimina todos os pontos de recuperação disponíveis.
**Failover-VMware** | Normalmente, corre se houver uma paragem não planeada, ou se o site principal não estiver disponível.<br/><br/> Especificar opcionalmente que a Recuperação do Sítio deve tentar desencadear uma paragem do VM e sincronizar e replicar as alterações finais antes de iniciar a falha.  | Perda mínima de dados para apps. | 1. Inicie o seu plano BCDR. <br/><br/> 2. Iniciar uma falha da recuperação do local. Especificar se a Recuperação do Site deve tentar ativar o encerramento de VM e sincronizar antes de executar a falha.<br/> O fracasso funciona mesmo que as máquinas não possam ser desligadas.<br/><br/> 3. Após a falha, verifique se a réplica Azure VM está ativa em Azure. <br/>Se necessário, pode selecionar um ponto de recuperação diferente da janela de retenção de 72 horas.<br/><br/> 5. Comprometa o fracasso para terminar. A ação de cometer elimina todos os pontos de recuperação.<br/> Para os VMs do Windows, a Recuperação do Site desativa as ferramentas VMware durante o failover.

## <a name="failover-processing"></a>Processamento de failover

Em alguns cenários, o failover requer um processamento adicional que leva cerca de 8 a 10 minutos para ser concluído. Pode notar tempos de insuição de teste mais longos para:

* VMware VMs executando uma versão de serviço de mobilidade com mais de 9.8.
* Servidores físicos.
* VMware Linux VMs.
* VMs hiper-V protegidos como servidores físicos.
* VMware VMs que não têm o serviço DHCP habilitado.
* VMware VMs que não têm os seguintes controladores de arranque: storvsc, vmbus, storflt, intelide, atapi.

## <a name="recovery-point-options"></a>Opções de ponto de recuperação

Durante o failover, pode selecionar várias opções de ponto de recuperação.

**Opção** | **Detalhes**
--- | ---
**Mais recente (RPO mais baixo)** | Esta opção fornece o objetivo de ponto de recuperação mais baixo (RPO). Primeiro processa todos os dados que foram enviados para o serviço de Recuperação de Sítios, para criar um ponto de recuperação para cada VM, antes de falhar. Este ponto de recuperação tem todos os dados replicados para a Recuperação do Local quando a falha foi desencadeada.
**Mais recente processado**  | Esta opção falha em VMs até ao último ponto de recuperação processado pela Recuperação do Site. Para ver o último ponto de recuperação de um VM específico, consulte os **pontos de recuperação mais recentes** nas definições de VM. Esta opção proporciona um RTO (Objetivo de Tempo de Recuperação) baixo, porque não é despendido tempo ao processar os dados não processados.
**Mais recente aplicações consistentes** |  Esta opção falha em VMs para o mais recente ponto de recuperação consistente com aplicações processado pela Recuperação do Site, se estiverem ativados pontos de recuperação consistentes em aplicações. Verifique o último ponto de recuperação nas definições de VM.
**Mais recente multi-VM processado** | Esta opção está disponível para planos de recuperação com um ou mais VMs que têm consistência multi-VM ativada. Os VMs com a definição ativa falham no último ponto de recuperação consistente multi-VM comum. Quaisquer outros VMs no plano falham no último ponto de recuperação processado.
**Aplicações multi-VM mais recentes consistentes** |  Esta opção está disponível para planos de recuperação com um ou mais VMs que têm consistência multi-VM ativada. Os VMs que fazem parte de um grupo de replicação falham no mais recente ponto de recuperação comum multi-VM consistente com aplicações. Outros VMs falham no seu último ponto de recuperação consistente com aplicações.
**Personalizado** | Utilize esta opção para falhar sobre um VM específico para um determinado ponto de recuperação no tempo. Esta opção não está disponível para planos de recuperação.

> [!NOTE]
> Os pontos de recuperação não podem ser migrados para outro cofre dos Serviços de Recuperação.

## <a name="reprotectionfailback"></a>Reproteção/recuo

Após a falha em Azure, os VMs Azure replicados estão em estado desprotegido.

- Como primeiro passo para não voltar ao seu site no local, precisa iniciar os VMs Azure replicando-se no local. O processo de reproteção depende do tipo de máquinas por onde falhou.
- Depois de as máquinas estarem a replicar-se de Azure para as instalações, pode executar uma falha de Azure para o seu local.
- Depois de as máquinas voltarem a funcionar no local, podes ativar a replicação para que se reproduzam em Azure para recuperação de desastres.

O failback funciona da seguinte forma:

- Para falhar, um VM precisa de pelo menos um ponto de recuperação para falhar. Num plano de recuperação, todos os VMs do plano precisam de pelo menos um ponto de recuperação.
- Recomendamos que utilize **o** último ponto de recuperação para falhar (este é um ponto consistente de colisão).
    - Existe uma opção de ponto de recuperação consistente com aplicações. Neste caso, um único VM recupera para o seu mais recente ponto de recuperação consistente com aplicações disponíveis. Para um plano de recuperação com um grupo de replicação, cada grupo de replicação recupera para o seu ponto de recuperação comum disponível.
    - Os pontos de recuperação consistentes da aplicação podem estar atrasados no tempo, e pode haver perda de dados.
- Durante o failover de Azure para o local, a Recuperação do Local encerra os VMs Azure. Quando comete o failover, a Recuperação do Local remove os VMs Azure falhados em Azure.


## <a name="vmwarephysical-reprotectionfailback"></a>VMware/reproteção física/failback

Para reprotecer e reprotectar máquinas VMware e servidores físicos de Azure para as instalações, precisa de uma infraestrutura de recuo, e há uma série de requisitos.

- **Servidor de processo temporário em Azure**: Para falhar de volta do Azure, configura um Azure VM para agir como um servidor de processo para lidar com a replicação do Azure. É possível eliminar esta VM após a conclusão da reativação pós-falha.
- **Ligação VPN**: Para falhar, precisa de uma ligação VPN (ou ExpressRoute) da rede Azure para o local do local.
- **Servidor-alvo principal separado**: Por padrão, o servidor alvo principal que foi instalado com o servidor de configuração nas pegas VMware VMware no local falha. Se precisar de falhar em grandes volumes de tráfego, crie um servidor-alvo principal separado no local para este fim.
- **Política de reativação pós-falha**: para replicar de novo para o site no local, precisa de uma política de reativação pós-falha. Esta política é criada automaticamente quando se cria uma política de replicação desde as instalações até ao Azure.
    - Esta política está automaticamente associada ao servidor de configuração.
    - Não pode editar esta apólice.
    - Valores políticos: limiar de RPO - 15 minutos; Retenção de ponto de recuperação - 24 Horas; Frequência instantânea consistente com aplicações - 60 minutos.

Saiba mais sobre vMware/reproteção física e failback:
- [Reveja](vmware-azure-reprotect.md#before-you-begin) os requisitos adicionais para a reproteção e o failback.
- [Implementar](vmware-azure-prepare-failback.md#deploy-a-process-server-in-azure) um servidor de processo em Azure.
- [Implemente](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server) um servidor de alvo principal separado.

Quando reprotecta os VMs do Azure para as instalações, pode especificar que pretende voltar a falhar no local original ou num local alternativo.

- **Recuperação original**da localização : Isto falha de Azure para a mesma máquina de origem no local, se existir. Neste cenário, apenas as alterações são replicadas de volta ao local.
- **Recuperação alternativa da localização**: Se a máquina no local não existir, pode falhar de volta de Azure para um local alternativo. Quando reprotete o Azure VM para o local, a máquina no local é criada. A replicação completa de dados ocorre de Azure para o local. - - [Rever](concepts-types-of-failback.md) os requisitos e limitações para a falha de localização.



## <a name="hyper-v-reprotectionfailback"></a>Reproteção/recuo do hiper-V

Para reprotecer e reprotecer e reprotecer os VMs hiper-V de Azure para as instalações:

- Só é possível falhar na replicação de Hiper-VMs através de uma conta de armazenamento. O failback dos VMs Hiper-V que se replicam usando discos geridos não é suportado.
- Os anfitriões Hiper-V no local (ou System Center VMM, se utilizados) devem ser ligados ao Azure.
- Tens uma falha planeada de Azure para o local.
- Não é necessário configurar componentes específicos para o recuo do Hiper-VM.
- Durante o failover planeado, pode selecionar opções para sincronizar dados antes do failback:
    - **Sincronizar os dados antes do failover**: Esta opção minimiza o tempo de inatividade das máquinas virtuais, uma vez que sincroniza as máquinas sem as desligar.
        - Fase 1: Tira uma foto do Azure VM e copia-a para o anfitrião Hiper-V no local. A máquina continua a funcionar em Azure.
        - Fase 2: Desliga o VM Azure para que não ocorram novas alterações. O conjunto final de alterações delta é transferido para o servidor no local e o VM no local é iniciado.
    - **Sincronizar apenas os dados durante o failover**: Esta opção é mais rápida porque esperamos que a maior parte do disco tenha mudado, e assim não efetue cálculos de checkum. Faz um download do disco. Recomendamos que utilize esta opção se o VM estiver em funcionamento em Azure durante algum tempo (um mês ou mais), ou se o VM no local tiver sido eliminado.

[Saiba mais](hyper-v-azure-failback.md) sobre reproteção de Hiper-V e recuo.

Quando reprotecta os VMs do Azure para as instalações, pode especificar que pretende voltar a falhar no local original ou num local alternativo.

- **Recuperação original**da localização : Isto falha de Azure para a mesma máquina de origem no local, se existir. Neste cenário, selecione uma das opções de sincronização descritas no procedimento anterior.
- **Recuperação alternativa da localização**: Se a máquina no local não existir, pode falhar de volta de Azure para um local alternativo. Quando reprotete o Azure VM para o local, a máquina no local é criada. Com esta opção, recomendamos que selecione a opção de sincronizar dados antes de falhar
- [Reveja](hyper-v-azure-failback.md) os requisitos e limitações para a falha de localização.


Depois de ter falhado no local, ativa a **Reverse Replicate** para começar a replicar o VM para Azure, completando o ciclo.




## <a name="next-steps"></a>Passos seguintes
- Falha em [VMware VMs específicos](vmware-azure-tutorial-failover-failback.md)
- Falha em [VMs hiper-V específicos.](hyper-v-azure-failover-failback-tutorial.md)
- [Criar](site-recovery-create-recovery-plans.md) um plano de recuperação.
- Falha em [VMs num plano de recuperação](site-recovery-failover.md).
- [Preparar para](vmware-azure-failback.md) Reproteção vMware e recuo.
- Falha nos [Hiper-V VMs](hyper-v-azure-failback.md).

