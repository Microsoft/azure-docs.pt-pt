---
title: Arquitetura de recuperação de desastres hiper-V em Recuperação do Site Azure
description: Este artigo fornece uma visão geral dos componentes e arquitetura utilizados na implementação da recuperação de desastres para os VMs Hiper-V no local (sem VMM) para Azure com o serviço de recuperação do site Azure.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 022d6edad1e907173dfde3481e60d2523be087a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74082670"
---
# <a name="hyper-v-to-azure-disaster-recovery-architecture"></a>Arquitetura da recuperação após desastre do Hyper-V para o Azure


Este artigo descreve a arquitetura e os processos utilizados quando se replica, falha e recupera máquinas virtuais Hyper-V (VMs) entre os anfitriões hyper-V no local e o Azure, utilizando o serviço de Recuperação do [Sítio Azure.](site-recovery-overview.md)

Os anfitriões hiper-V podem ser geridos opcionalmente em nuvens privadas do System Center Virtual Machine Manager (VMM).



## <a name="architectural-components---hyper-v-without-vmm"></a>Componentes arquitetónicos - Hiper-V sem VMM

A tabela e o gráfico que se seguem proporcionam uma visão de alto nível dos componentes utilizados para a replicação hyper-V para o Azure, quando os anfitriões hyper-V não são geridos por VMM.

**Componente** | **Requisito** | **Detalhes**
--- | --- | ---
**Azure** | Uma subscrição Azure, conta de armazenamento Azure e rede Azure. | Os dados replicados a partir de cargas de trabalho VM no local são armazenados na conta de armazenamento. Os VMs azure são criados com os dados de carga de trabalho replicados quando ocorre a falha do seu site no local.<br/><br/> As VMs do Azure ligam-se à rede virtual do Azure quando são criadas.
**Hyper-V** | Durante a implantação da Recuperação do Local, você reúne anfitriões e clusters hyper-V em sites hiper-V. Instala o agente azure de recuperação e serviços de recuperação em cada hospedeiro Hiper-V autónomo ou em cada nó de cluster Hyper-V. | O Fornecedor orquestra a replicação com o Site Recovery através da Internet. O agente do Site Recovery trata da replicação de dados.<br/><br/> As comunicações provenientes do Fornecedor e do agente são seguras e encriptadas. Também são encriptados os dados replicados no armazenamento do Azure.
**VMs Hyper-V** | Um ou mais VMs a correr no Hyper-V. | Nada precisa de ser explicitamente instalado em VMs.


**Arquitetura hyper-V para Azure (sem VMM)**

![Arquitetura](./media/hyper-v-azure-architecture/arch-onprem-azure-hypervsite.png)


## <a name="architectural-components---hyper-v-with-vmm"></a>Componentes arquitetónicos - Hyper-V com VMM

A tabela e o gráfico que se seguem proporcionam uma visão de alto nível dos componentes utilizados para a replicação hyper-V para o Azure, quando os anfitriões hyper-V são geridos em nuvens vmm.

**Componente** | **Requisito** | **Detalhes**
--- | --- | ---
**Azure** | Uma subscrição Azure, conta de armazenamento Azure e rede Azure. | Os dados replicados a partir de cargas de trabalho VM no local são armazenados na conta de armazenamento. Os VMs azure são criados com os dados replicados quando ocorre a falha do seu site no local.<br/><br/> As VMs do Azure ligam-se à rede virtual do Azure quando são criadas.
**Servidor VMM** | O servidor VMM tem uma ou mais clouds que contêm anfitriões Hyper-V. | Instala o Fornecedor de Recuperação do Site no servidor VMM, para orquestrar a replicação com a Recuperação do Site e registar o servidor no cofre dos Serviços de Recuperação.
**Anfitrião Hyper-V** | Um ou mais anfitriões/clusters de Hyper-V geridos pelo VMM. |  Instala o agente de Serviços de Recuperação em cada anfitrião hiper-V ou nó de cluster.
**VMs Hyper-V** | Uma ou mais VMs em execução num servidor de anfitrião Hyper-V. | Nada tem de estar explicitamente instalado nas VMs.
**Redes** | Redes lógicas e de VMs configuradas no servidor VMM. A rede VM deve estar ligada a uma rede lógica que está associada à nuvem. | As redes VM estão mapeadas para redes virtuais Azure. Quando os VMs Azure são criados após a falha, são adicionados à rede Azure que está mapeada para a rede VM.

**Arquitetura hyper-V para Azure (com VMM)**

![Componentes](./media/hyper-v-azure-architecture/arch-onprem-onprem-azure-vmm.png)



## <a name="replication-process"></a>Processo de replicação

![Replicação hyper-V para Azure](./media/hyper-v-azure-architecture/arch-hyperv-azure-workflow.png)

**Processo de replicação e recuperação**


### <a name="enable-protection"></a>Ativar a proteção

1. Depois de ativar a proteção para uma VM Hyper-V, no portal do Azure ou no local, **Ativar a proteção** é iniciado.
2. A tarefa verifica se a máquina está em conformidade com os pré-requisitos, antes de invocar o [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx), para configurar a replicação com as definições que configurou.
3. A tarefa inicia a replicação inicial ao invocar o método [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx), para inicializar uma replicação de VM completa e enviar os discos virtuais da VM para o Azure.
4. Pode monitorizar o trabalho na **conta** Jobs.      ![](media/hyper-v-azure-architecture/image1.png) Lista ![de empregos Permitir a perfuração de proteção para baixo](media/hyper-v-azure-architecture/image2.png)


### <a name="initial-data-replication"></a>Replicação inicial de dados

1. Quando a replicação inicial é desencadeada, é tirada uma fotografia de [instantâneo de VM Hiper-V.](https://technet.microsoft.com/library/dd560637.aspx)
2. Discos rígidos virtuais no VM são replicados um a um, até serem todos copiados para o Azure. Isto pode demorar algum tempo, dependendo do tamanho vm e largura de banda da rede. [Aprenda](https://support.microsoft.com/kb/3056159) a aumentar a largura de banda da rede.
3. Se ocorrerem alterações no disco durante a replicação inicial, o Rastreador de Replicação de Replicação de Réplica seleções Hyper-V rastreia as alterações à medida que os registos de replicação Hyper-V (.hrl). Estes ficheiros de registo estão localizados na mesma pasta que os discos. Cada disco tem um ficheiro .hrl associado que é enviado para armazenamento secundário. Os ficheiros de instantâneo e de registo consomem recursos do disco quando a replicação inicial está em curso.
4. Quando a replicação inicial for concluída, o instantâneo da VM é eliminado.
5. As alterações de disco delta no registo são sincronizadas e unidas ao disco principal.


### <a name="finalize-protection-process"></a>Finalizar processo de proteção

1. Após o final da replicação inicial, a **proteção finalização no** trabalho da máquina virtual funciona. Confunde a rede e outras definições de pós-replicação, de modo a que o VM esteja protegido.
2. Nesta fase, pode verificar as definições de VM para se certificar de que está pronto para a falha. Pode executar um exercício de recuperação de desastres (falha de teste) para o VM, para verificar se falha como esperado. 


## <a name="delta-replication"></a>Replicação delta

1. Após a replicação inicial, começa a replicação delta, de acordo com a política de replicação.
2. O Rastreador de Replicação de Replicação Hyper-V rastreia alterações num disco rígido virtual como ficheiros .hrl. Cada disco que está configurado para replicação tem um ficheiro .hrl associado.
3. O registo é enviado para a conta de armazenamento do cliente. Quando um registo está em trânsito para o Azure, as alterações no disco primário são rastreadas noutro ficheiro de registo, na mesma pasta.
4. Durante a replicação inicial e delta, pode monitorizar o VM no portal Azure.

### <a name="resynchronization-process"></a>Processo de ressincronização

1. Se a replicação delta falhar, e uma replicação completa seria dispendiosa em termos de largura de banda ou de tempo, então uma VM fica marcada para ressincronização.
    - Por exemplo, se os ficheiros de .hrl atingirem 50% do tamanho do disco, a VM será marcada para ressincronização.
    -  Por padrão, a ressincronização está programada para ser executada automaticamente fora do horário de expediente.
1.  A ressincronização envia apenas dados delta.
    - Minimiza a quantidade de dados enviados por verificação de controlos da fonte e dos VMs-alvo.
    - Usa um algoritmo de chunking de bloco fixo onde os ficheiros de origem e alvo são divididos em pedaços fixos.
    - São gerados cheques para cada pedaço. Estes são comparados para determinar quais os blocos da fonte que precisam de ser aplicados ao alvo.
2. Após a conclusão da ressincronização, a replicação delta normal deve ser retomada.
3. Se não quiser esperar pela ressincronização predefinida fora de horas, pode ressincronizar manualmente um VM. Por exemplo, se ocorrer uma paragem. Para isso, no portal Azure, selecione o VM > **Ressincronizar**.

    ![Ressincronização manual](./media/hyper-v-azure-architecture/image4-site.png)


### <a name="retry-process"></a>Processo de retry

Se ocorrer um erro de replicação, haverá uma repetição interna. A retry é classificada como descrito na tabela.

**Categoria** | **Detalhes**
--- | ---
**Erros não recuperáveis** | Não é efetuada qualquer tentativa. O estado da VM será **Crítico**, e é necessária a intervenção do administrador.<br/><br/> Exemplos destes erros incluem uma cadeia VHD quebrada, um estado inválido para a réplica VM, erros de autenticação de rede, erros de autorização e VM não encontradoerros (para servidores Hiper-V autónomos.
**Erros recuperáveis** | As tentativas ocorrem a cada intervalo de replicação, utilizando um término exponencial que aumenta o intervalo entre tentativas, desde o início da primeira tentativa para 1, 2, 4, 8 e 10 minutos. Se o erro persistir, tente novamente a cada 30 minutos. Exemplos destes incluem erros de rede, erros de disco baixos e baixas condições de memória.



## <a name="failover-and-failback-process"></a>Processo de ativação pós-falha e de reativação pós-falha

1. Executa uma ativação pós-falha planeada ou não planeada a partir das VMs de Hyper-V no local para o Azure. Se executar uma ativação pós-falha planeada, as VMs de origem são desligadas para garantir que não há perda de dados. Faça uma falha não planeada se o seu site principal não estiver acessível.
2. Pode fazer a ativação pós-falha de uma máquina individual ou criar planos de recuperação para orquestrar a ativação pós-falha de várias máquinas.
3. Faz uma falha. Após a primeira fase de failover complete, você deve ser capaz de ver as VMs réplicas criadas em Azure. Pode atribuir um endereço IP público à VM, se necessário.
4. Em seguida, compromete-se a falha, para começar a aceder à carga de trabalho da réplica Azure VM.

Depois da sua infraestrutura no local estar a funcionar novamente, pode falhar. O failback ocorre em três fases:

1. Inicie uma falha planeada de Azure para o local no local:
    - Minimizar o tempo de **inatividade**: Se utilizar esta opção A recuperação do site sincroniza os dados antes do failover. Verifica os blocos de dados alterados e transfere-os para o local, enquanto o Azure VM continua a funcionar, minimizando o tempo de inatividade. Quando especifica manualmente que a falha deve ser completada, o VM Azure é desligado, quaisquer alterações delta finais são copiadas e a falha começa.
    - **Download completo**: Com esta opção os dados são sincronizados durante a failover. Esta opção descarrega todo o disco. É mais rápido porque não são calculados cheques, mas há mais tempo de inatividade. Use esta opção se estiver a executar as réplicas de VMs Azure há algum tempo, ou se o VM no local foi eliminado.
    - **Criar VM**: Pode selecionar para voltar ao mesmo VM ou a um VM alternativo. Pode especificar que a Recuperação do Site deve criar o VM se já não existir.

2. Após os acabamentos iniciais da sincronização, selecione-se para completar a falha. Depois de concluído, pode entrar no VM no local para verificar se está tudo a funcionar como esperado. No portal Azure, pode ver-se que os VMs Azure foram parados.
3.  Depois, compromete-se a falhar para terminar e começa a aceder à carga de trabalho do VM no local novamente.
4. Depois de as cargas de trabalho terem falhado, permite a replicação inversa, de modo que os VMs no local se reproduzam novamente para Azure.



## <a name="next-steps"></a>Passos seguintes


Siga [este tutorial](tutorial-prepare-azure.md) para começar com a replicação Hyper-V a Azure.


