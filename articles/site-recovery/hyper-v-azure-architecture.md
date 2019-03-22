---
title: Hyper-V para a arquitetura da recuperação após desastre do Azure no Azure Site Recovery | Documentos da Microsoft
description: Este artigo fornece uma visão geral dos componentes e da arquitetura utilizada ao implementar a recuperação após desastre para VMs de Hyper-V no local (sem VMM) para o Azure com o serviço Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: raynew
ms.openlocfilehash: f77069592fb34caf409b387f5c8452159f55e296
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58311514"
---
# <a name="hyper-v-to-azure-disaster-recovery-architecture"></a>Hyper-V para a arquitetura da recuperação após desastre do Azure


Este artigo descreve a arquitetura e os processos utilizados ao replicar, efetuar a ativação pós-falha e recuperar máquinas de virtuais de Hyper-V (VMs) entre anfitriões de Hyper-V no local e o Azure, utilizando o [do Azure Site Recovery](site-recovery-overview.md) serviço.

Anfitriões Hyper-V, opcionalmente, podem ser geridos em nuvens privadas do System Center Virtual Machine Manager (VMM).



## <a name="architectural-components---hyper-v-without-vmm"></a>Componentes da arquitetura - Hyper-V sem o VMM

A tabela seguinte e o gráfico fornecem uma visão geral dos componentes utilizados para replicação de Hyper-V para o Azure, quando os anfitriões de Hyper-V não são geridos pelo VMM.

**Componente** | **Requisito** | **Detalhes**
--- | --- | ---
**Azure** | Uma subscrição do Azure, uma conta de armazenamento do Azure e uma rede do Azure. | Dados replicados de cargas de trabalho no local são armazenados na conta de armazenamento. VMs do Azure são criadas com os dados de carga de trabalho replicados quando ocorre a ativação pós-falha do seu site no local.<br/><br/> As VMs do Azure ligam-se à rede virtual do Azure quando são criadas.
**Hyper-V** | Durante a implementação da recuperação de sites, agrupa os anfitriões de Hyper-V e clusters em sites Hyper-V. Instalar o agente do Azure Site Recovery Provider e serviços de recuperação em cada anfitrião de Hyper-V autónomo ou em cada nó de cluster do Hyper-V. | O Fornecedor orquestra a replicação com o Site Recovery através da Internet. O agente do Site Recovery trata da replicação de dados.<br/><br/> As comunicações provenientes do Fornecedor e do agente são seguras e encriptadas. Também são encriptados os dados replicados no armazenamento do Azure.
**VMs de Hyper-V** | Uma ou mais VMs em execução no Hyper-V. | Nada tem de estar explicitamente instalado nas VMs.


**Hyper-V para a arquitetura do Azure (sem VMM)**

![Arquitetura](./media/hyper-v-azure-architecture/arch-onprem-azure-hypervsite.png)



## <a name="architectural-components---hyper-v-with-vmm"></a>Componentes da arquitetura - Hyper-V com o VMM

A tabela seguinte e o gráfico fornecem uma visão geral dos componentes utilizada para a replicação de Hyper-V para o Azure, quando os anfitriões de Hyper-V forem geridos em clouds do VMM.

**Componente** | **Requisito** | **Detalhes**
--- | --- | ---
**Azure** | Uma subscrição do Azure, uma conta de armazenamento do Azure e uma rede do Azure. | Dados replicados de cargas de trabalho no local são armazenados na conta de armazenamento. VMs do Azure são criadas com os dados replicados quando ocorre a ativação pós-falha do seu site no local.<br/><br/> As VMs do Azure ligam-se à rede virtual do Azure quando são criadas.
**Servidor VMM** | O servidor VMM tem uma ou mais clouds que contêm anfitriões Hyper-V. | Instalar o Site Recovery Provider no servidor do VMM, para orquestrar a replicação com o Site Recovery e registar o servidor no cofre dos serviços de recuperação.
**Anfitrião Hyper-V** | Um ou mais anfitriões/clusters de Hyper-V geridos pelo VMM. |  Instalar o agente dos serviços de recuperação em cada nó de anfitrião ou cluster Hyper-V.
**VMs de Hyper-V** | Uma ou mais VMs em execução num servidor de anfitrião Hyper-V. | Nada tem de estar explicitamente instalado nas VMs.
**Redes** | Redes lógicas e de VMs configuradas no servidor VMM. A rede VM deve ser ligada a uma rede lógica que está associada à cloud. | Redes VM são mapeadas para redes virtuais do Azure. Quando as VMs do Azure são criadas após a ativação pós-falha, eles são adicionados à rede do Azure que está mapeada para a rede VM.

**Hyper-V para a arquitetura do Azure (com VMM)**

![Componentes](./media/hyper-v-azure-architecture/arch-onprem-onprem-azure-vmm.png)



## <a name="replication-process"></a>Processo de replicação

![Hyper-V para replicação do Azure](./media/hyper-v-azure-architecture/arch-hyperv-azure-workflow.png)

**Processo de replicação e recuperação**


### <a name="enable-protection"></a>Ativar a proteção

1. Depois de ativar a proteção para uma VM Hyper-V, no portal do Azure ou no local, **Ativar a proteção** é iniciado.
2. A tarefa verifica se a máquina está em conformidade com os pré-requisitos, antes de invocar o [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx), para configurar a replicação com as definições que configurou.
3. A tarefa inicia a replicação inicial ao invocar o método [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx), para inicializar uma replicação de VM completa e enviar os discos virtuais da VM para o Azure.
4. Pode monitorizar a tarefa no separador **Tarefas**.      ![Lista de tarefas](media/hyper-v-azure-architecture/image1.png) ![Ativar a desagregação da proteção](media/hyper-v-azure-architecture/image2.png)


### <a name="initial-data-replication"></a>Replicação de dados inicial

1. Quando a replicação inicial é acionada, uma [instantâneo da VM de Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) instantâneo.
2. Discos rígidos virtuais na VM são replicados um de cada, até estarem todos copiados para o Azure. Isto poderá demorar algum tempo, dependendo do tamanho VM e largura de banda de rede. [Saiba como](https://support.microsoft.com/kb/3056159) para aumentar a largura de banda de rede.
3. Se ocorrerem alterações de disco enquanto a replicação inicial está em curso, o controlador de replicação de réplica do Hyper-V controla as alterações como registos de replicação de Hyper-V (. hrl). Estes ficheiros de registo estão localizados na mesma pasta que os discos. Cada disco tem um ficheiro. hrl associado que é enviado para o armazenamento secundário. Os ficheiros de instantâneo e de registo consomem recursos do disco quando a replicação inicial está em curso.
4. Quando a replicação inicial for concluída, o instantâneo da VM é eliminado.
5. As alterações de disco delta no registo são sincronizadas e unidas ao disco principal.


### <a name="finalize-protection-process"></a>Finalizar o processo de proteção

1. Depois de concluída a replicação inicial, o **finalizar proteção na máquina virtual** execuções de tarefas. Ele configura a rede e outras definições de pós-replicação, para que a VM está protegida.
2. Nesta fase, pode verificar as definições de VM para se certificar de que fique preparada para ativação pós-falha. Pode executar um teste de recuperação após desastre (ativação pós-falha de teste) para a VM, para verificar que ele falha mais conforme esperado. 


## <a name="delta-replication"></a>Replicação delta

1. Após a replicação inicial, começa a replicação delta, de acordo com a política de replicação.
2. O controlador de replicação de réplica do Hyper-V controla as alterações para um disco rígido virtual como ficheiros de. hrl. Cada disco que está configurado para replicação tem um ficheiro .hrl associado.
3. O registo é enviado para a conta de armazenamento do cliente. Quando um registo está em trânsito para o Azure, as alterações ao disco principal são controladas noutro ficheiro de registo na mesma pasta.
4. Durante a replicação inicial e delta, pode monitorizar a VM no portal do Azure.

### <a name="resynchronization-process"></a>Processo de ressincronização

1. Se a replicação delta falhar, e uma replicação completa seria dispendiosa em termos de largura de banda ou de tempo, então uma VM fica marcada para ressincronização.
    - Por exemplo, se os ficheiros de .hrl atingirem 50% do tamanho do disco, a VM será marcada para ressincronização.
    -  Por predefinição, a ressincronização está agendada para ser executada automaticamente fora do horário de office.
1.  A ressincronização envia apenas dados de delta.
    - Minimiza a quantidade de dados enviados por computação de somas de verificação das VMs de origem e de destino.
    - Ele usa um algoritmo de segmentação de blocos fixos onde os ficheiros de origem e destino estão divididos em segmentos fixos.
    - As somas de verificação para cada segmento são geradas. Estes são comparadas para determinar quais os blocos da origem precisam ser aplicados ao destino.
2. Após a conclusão da ressincronização, a replicação delta normal deve ser retomada.
3. Se não quiser aguardar ressincronização padrão fora do horário, pode ressincronizar manualmente uma VM. Por exemplo, se ocorrer uma falha. Para tal, no portal do Azure, selecione a VM > **ressincronizar**.

    ![Ressincronização manual](./media/hyper-v-azure-architecture/image4-site.png)


### <a name="retry-process"></a>Repita o processo

Se ocorrer um erro de replicação, haverá uma repetição interna. Repetição é classificada como descrito na tabela.

**Categoria** | **Detalhes**
--- | ---
**Erros não recuperáveis** | Não é efetuada qualquer tentativa. O estado da VM será **Crítico**, e é necessária a intervenção do administrador.<br/><br/> Exemplos destes erros incluem uma cadeia VHD danificada, um Estado inválido da VM, erros de autenticação de rede, erros de autorização, de réplica e a VM não encontrou erros (para servidores de Hyper-V autónomos.
**Erros recuperáveis** | As tentativas ocorrem a cada intervalo de replicação, utilizando um término exponencial que aumenta o intervalo entre tentativas, desde o início da primeira tentativa para 1, 2, 4, 8 e 10 minutos. Se o erro persistir, tente novamente a cada 30 minutos. Estes exemplos de erros de rede, erros no disco de baixa e as condições de falta de memória.



## <a name="failover-and-failback-process"></a>Processo de ativação pós-falha e de reativação pós-falha

1. Pode executar uma ativação pós-falha planeada ou não planeada de VMs de Hyper-V no local para o Azure. Se executar uma ativação pós-falha planeada, as VMs de origem são desligadas para garantir que não há perda de dados. Se o site primário não estiver acessível, execute uma ativação pós-falha não planeada.
2. Pode efetuar a ativação pós-falha de uma máquina individual ou criar planos de recuperação, para orquestrar a ativação pós-falha de várias máquinas.
3. Executar uma ativação pós-falha. Após a conclusão da primeira fase de ativação pós-falha, deve conseguir ver a réplica de criar VMs no Azure. Pode atribuir um endereço IP público à VM, se necessário.
4. Em seguida, consolida a ativação pós-falha, para começar a aceder à carga de trabalho da VM do Azure de réplica.

Após a sua infraestrutura no local novamente em funcionamento, pode efetuar a reativação pós-falha. Reativação pós-falha ocorre em três fases:

1. Inicie uma ativação pós-falha planeada do Azure para o site no local:
    - **Minimizar o período de indisponibilidade**: Se utilizar esta opção de recuperação de Site sincroniza os dados antes da ativação pós-falha. Ele verifica a existência de blocos de dados alterados e transfere-os para o site no local, enquanto mantém a VM do Azure em execução, minimizando o tempo de inatividade. Quando especificar manualmente que a ativação pós-falha deverá ser concluída, a VM do Azure é encerrada, quaisquer alterações de final delta são copiadas e inicia a ativação pós-falha.
    - **Transferência completa**: Com esta opção os dados são sincronizados durante a ativação pós-falha. Esta opção transfere o disco inteiro. É mais rápido porque não existem as somas de verificação são calculadas, mas há mais tempo de inatividade. Utilize esta opção se esteve executando a réplica de VMs do Azure durante algum tempo ou se a VM no local tiver sido eliminada.
    - **Criar VM**: Pode selecionar a falha de volta para a mesma VM ou a uma VM alternativa. Pode especificar que a recuperação de Site deve criar a VM, se ainda não exista.

2. Depois de concluída a sincronização inicial, selecione para concluir a ativação pós-falha. Depois de terminar, pode iniciar sessão na VM no local para verificar que tudo está a funcionar conforme esperado. No portal do Azure, pode ver que as VMs do Azure foram paradas.
3.  Em seguida, consolida a ativação pós-falha para concluir a cópia de segurança e começar a aceder à carga de trabalho da VM no local novamente.
4. Depois de tem realizarão a reativação pós-falha cargas de trabalho, ativar a replicação inversa, para que as VMs no local novamente a replicar para o Azure.



## <a name="next-steps"></a>Passos Seguintes


Siga [deste tutorial](tutorial-prepare-azure.md) para começar a utilizar o Hyper-V para replicação do Azure.


