---
title: Arquitetura de recuperação de desastres do Hyper-V no Azure Site Recovery
description: Este artigo fornece uma visão geral dos componentes e da arquitetura usada ao implantar a recuperação de desastre para VMs do Hyper-V locais (sem VMM) no Azure com o serviço Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 022d6edad1e907173dfde3481e60d2523be087a1
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082670"
---
# <a name="hyper-v-to-azure-disaster-recovery-architecture"></a>Arquitetura de recuperação de desastre do Hyper-V para o Azure


Este artigo descreve a arquitetura e os processos usados quando você faz a replicação, o failover e a recuperação de máquinas virtuais (VMs) do Hyper-V entre hosts Hyper-V locais e o Azure, usando o serviço [Azure site Recovery](site-recovery-overview.md) .

Os hosts do Hyper-V podem, opcionalmente, ser gerenciados em nuvens privadas do System Center Virtual Machine Manager (VMM).



## <a name="architectural-components---hyper-v-without-vmm"></a>Componentes arquitetônicos – Hyper-V sem VMM

A tabela e o gráfico a seguir fornecem uma exibição de alto nível dos componentes usados para replicação do Hyper-V para o Azure, quando os hosts do Hyper-V não são gerenciados pelo VMM.

**Componente** | **Requisito** | **Detalhes**
--- | --- | ---
**Azure** | Uma assinatura do Azure, uma conta de armazenamento do Azure e uma rede do Azure. | Os dados replicados de cargas de trabalho de VM local são armazenados na conta de armazenamento. As VMs do Azure são criadas com os dados de carga de trabalho replicados quando ocorre failover do site local.<br/><br/> As VMs do Azure ligam-se à rede virtual do Azure quando são criadas.
**Hyper-V** | Durante a implantação de Site Recovery, você coleta hosts e clusters do Hyper-V em sites do Hyper-V. Instale o provedor de Azure Site Recovery e o agente de serviços de recuperação em cada host autônomo do Hyper-V ou em cada nó de cluster do Hyper-V. | O Fornecedor orquestra a replicação com o Site Recovery através da Internet. O agente do Site Recovery trata da replicação de dados.<br/><br/> As comunicações provenientes do Fornecedor e do agente são seguras e encriptadas. Também são encriptados os dados replicados no armazenamento do Azure.
**VMs de Hyper-V** | Uma ou mais VMs em execução no Hyper-V. | Nada precisa ser instalado explicitamente em VMs.


**Arquitetura do Hyper-V para o Azure (sem VMM)**

![Arquitetura](./media/hyper-v-azure-architecture/arch-onprem-azure-hypervsite.png)


## <a name="architectural-components---hyper-v-with-vmm"></a>Componentes arquitetônicos – Hyper-V com o VMM

A tabela e o gráfico a seguir fornecem uma exibição de alto nível dos componentes usados para replicação do Hyper-V para o Azure, quando os hosts do Hyper-V são gerenciados em nuvens do VMM.

**Componente** | **Requisito** | **Detalhes**
--- | --- | ---
**Azure** | Uma assinatura do Azure, uma conta de armazenamento do Azure e uma rede do Azure. | Os dados replicados de cargas de trabalho de VM local são armazenados na conta de armazenamento. As VMs do Azure são criadas com os dados replicados quando ocorre o failover do site local.<br/><br/> As VMs do Azure ligam-se à rede virtual do Azure quando são criadas.
**Servidor VMM** | O servidor VMM tem uma ou mais clouds que contêm anfitriões Hyper-V. | Você instala o provedor de Site Recovery no servidor do VMM, para orquestrar a replicação com Site Recovery e registrar o servidor no cofre dos serviços de recuperação.
**Anfitrião Hyper-V** | Um ou mais anfitriões/clusters de Hyper-V geridos pelo VMM. |  Instale o agente dos serviços de recuperação em cada host ou nó de cluster do Hyper-V.
**VMs de Hyper-V** | Uma ou mais VMs em execução num servidor de anfitrião Hyper-V. | Nada tem de estar explicitamente instalado nas VMs.
**Redes** | Redes lógicas e de VMs configuradas no servidor VMM. A rede VM deve ser vinculada a uma rede lógica associada à nuvem. | As redes VM são mapeadas para redes virtuais do Azure. Quando as VMs do Azure são criadas após o failover, elas são adicionadas à rede do Azure que é mapeada para a rede VM.

**Arquitetura do Hyper-V para o Azure (com o VMM)**

![Componentes](./media/hyper-v-azure-architecture/arch-onprem-onprem-azure-vmm.png)



## <a name="replication-process"></a>Processo de replicação

![Replicação do Hyper-V para o Azure](./media/hyper-v-azure-architecture/arch-hyperv-azure-workflow.png)

**Processo de replicação e recuperação**


### <a name="enable-protection"></a>Ativar a proteção

1. Depois de ativar a proteção para uma VM Hyper-V, no portal do Azure ou no local, **Ativar a proteção** é iniciado.
2. A tarefa verifica se a máquina está em conformidade com os pré-requisitos, antes de invocar o [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx), para configurar a replicação com as definições que configurou.
3. A tarefa inicia a replicação inicial ao invocar o método [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx), para inicializar uma replicação de VM completa e enviar os discos virtuais da VM para o Azure.
4. Você pode monitorar o trabalho na guia **trabalhos** .      ![lista de trabalhos](media/hyper-v-azure-architecture/image1.png) ![habilitar a busca detalhada de proteção](media/hyper-v-azure-architecture/image2.png)


### <a name="initial-data-replication"></a>Replicação de dados inicial

1. Quando a replicação inicial é disparada, um instantâneo de [instantâneo da VM do Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) é obtido.
2. Os discos rígidos virtuais na VM são replicados um a um, até que eles sejam copiados para o Azure. Isso pode demorar um pouco, dependendo do tamanho da VM e da largura de banda da rede. [Saiba como](https://support.microsoft.com/kb/3056159) aumentar a largura de banda da rede.
3. Se ocorrerem alterações no disco enquanto a replicação inicial estiver em andamento, o controlador de replicação de réplica do Hyper-V acompanhará as alterações como logs de replicação do Hyper-V (. HRL). Esses arquivos de log estão localizados na mesma pasta que os discos. Cada disco tem um arquivo. HRL associado que é enviado para o armazenamento secundário. Os ficheiros de instantâneo e de registo consomem recursos do disco quando a replicação inicial está em curso.
4. Quando a replicação inicial for concluída, o instantâneo da VM é eliminado.
5. As alterações de disco delta no registo são sincronizadas e unidas ao disco principal.


### <a name="finalize-protection-process"></a>Finalizar processo de proteção

1. Após a conclusão da replicação inicial, o trabalho **finalizar a proteção na máquina virtual** é executado. Ele configura a rede e outras configurações de pós-replicação, para que a VM seja protegida.
2. Neste estágio, você pode verificar as configurações da VM para certificar-se de que ela está pronta para failover. Você pode executar uma análise de recuperação de desastre (failover de teste) para a VM, para verificar se ela faz failover conforme o esperado. 


## <a name="delta-replication"></a>Replicação delta

1. Após a replicação inicial, a replicação delta começa, de acordo com a política de replicação.
2. O controlador de replicação de réplica do Hyper-V rastreia as alterações em um disco rígido virtual como arquivos. HRL. Cada disco que está configurado para replicação tem um ficheiro .hrl associado.
3. O log é enviado para a conta de armazenamento do cliente. Quando um log está em trânsito para o Azure, as alterações no disco primário são rastreadas em outro arquivo de log, na mesma pasta.
4. Durante a replicação inicial e Delta, você pode monitorar a VM no portal do Azure.

### <a name="resynchronization-process"></a>Processo de ressincronização

1. Se a replicação delta falhar, e uma replicação completa seria dispendiosa em termos de largura de banda ou de tempo, então uma VM fica marcada para ressincronização.
    - Por exemplo, se os ficheiros de .hrl atingirem 50% do tamanho do disco, a VM será marcada para ressincronização.
    -  Por padrão, a ressincronização é agendada para ser executada automaticamente fora do horário comercial.
1.  A ressincronização envia apenas dados Delta.
    - Ele minimiza a quantidade de dados enviados ao computar as somas de verificação das VMs de origem e de destino.
    - Ele usa um algoritmo de agrupamento de blocos fixos onde os arquivos de origem e de destino são divididos em partes fixas.
    - As somas de verificação para cada parte são geradas. Eles são comparados para determinar quais blocos da origem precisam ser aplicados ao destino.
2. Após a conclusão da ressincronização, a replicação delta normal deve ser retomada.
3. Se você não quiser aguardar a ressincronização padrão fora do horário, poderá ressincronizar uma VM manualmente. Por exemplo, se ocorrer uma interrupção. Para fazer isso, na portal do Azure, selecione a VM > **ressincronizar**.

    ![Ressincronização manual](./media/hyper-v-azure-architecture/image4-site.png)


### <a name="retry-process"></a>Processo de repetição

Se ocorrer um erro de replicação, haverá uma repetição interna. Retry é classificado conforme descrito na tabela.

**Categoria** | **Detalhes**
--- | ---
**Erros não recuperáveis** | Não é efetuada qualquer tentativa. O estado da VM será **Crítico**, e é necessária a intervenção do administrador.<br/><br/> Exemplos desses erros incluem uma cadeia VHD quebrada, um estado inválido para a VM de réplica, erros de autenticação de rede, erros de autorização e erros de VM não encontrados (para servidores Hyper-V autônomos.
**Erros recuperáveis** | As tentativas ocorrem a cada intervalo de replicação, utilizando um término exponencial que aumenta o intervalo entre tentativas, desde o início da primeira tentativa para 1, 2, 4, 8 e 10 minutos. Se o erro persistir, tente novamente a cada 30 minutos. Exemplos desses incluem erros de rede, erros de disco insuficiente e condições de memória insuficiente.



## <a name="failover-and-failback-process"></a>Processo de ativação pós-falha e de reativação pós-falha

1. Você pode executar um failover planejado ou não planejado de VMs do Hyper-V locais para o Azure. Se executar uma ativação pós-falha planeada, as VMs de origem são desligadas para garantir que não há perda de dados. Execute um failover não planejado se o seu site primário não estiver acessível.
2. Você pode fazer failover de um único computador ou criar planos de recuperação para orquestrar o failover de vários computadores.
3. Você executa um failover. Após a conclusão do primeiro estágio de failover, você poderá ver as VMs de réplica criadas no Azure. Pode atribuir um endereço IP público à VM, se necessário.
4. Em seguida, você confirma o failover para começar a acessar a carga de trabalho da VM do Azure de réplica.

Depois que sua infraestrutura local estiver funcionando novamente, você poderá fazer failback. O failback ocorre em três estágios:

1. Disparar um failover planejado do Azure para o site local:
    - **Minimizar o tempo de inatividade**: se você usar essa opção Site Recovery sincronizará os dados antes do failover. Ele verifica os blocos de dados alterados e os baixa no site local, enquanto a VM do Azure continua em execução, minimizando o tempo de inatividade. Quando você especifica manualmente que o failover deve ser concluído, a VM do Azure é desligada, todas as alterações delta finais são copiadas e o failover é iniciado.
    - **Download completo**: com esta opção, os dados são sincronizados durante o failover. Essa opção baixa todo o disco. É mais rápido porque nenhuma soma de verificação é calculada, mas há mais tempo de inatividade. Use esta opção se você estiver executando as VMs do Azure de réplica por algum tempo ou se a VM local foi excluída.
    - **Criar VM**: você pode optar por fazer failback para a mesma VM ou para uma VM alternativa. Você pode especificar que Site Recovery deve criar a VM, caso ela ainda não exista.

2. Após a conclusão da sincronização inicial, selecione para concluir o failover. Após a conclusão, você pode fazer logon na VM local para verificar se tudo está funcionando conforme o esperado. No portal do Azure, você pode ver que as VMs do Azure foram interrompidas.
3.  Em seguida, você confirma o failover para concluir e começa a acessar a carga de trabalho da VM local novamente.
4. Após o failback das cargas de trabalho, você habilita a replicação inversa, para que as VMs locais sejam replicadas para o Azure novamente.



## <a name="next-steps"></a>Passos seguintes


Siga [este tutorial](tutorial-prepare-azure.md) para começar a usar a replicação do Hyper-V para o Azure.


