---
title: Arquitetura de recuperação de desastres hiper-V na recuperação do local de Azure
description: Este artigo fornece uma visão geral dos componentes e arquitetura utilizados na implementação da recuperação de desastres para os VMs Hiper-V (sem VMM) para Azure com o serviço de Recuperação do Local Azure.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: c5025b83619b505728bfdf5c4e1ccc81d3bb225e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97654766"
---
# <a name="hyper-v-to-azure-disaster-recovery-architecture"></a>Hiper-V para Azure arquitetura de recuperação de desastres


Este artigo descreve a arquitetura e os processos utilizados quando replica, falha e recupera máquinas virtuais Hiper-V (VMs) entre anfitriões Hiper-V no local e Azure, utilizando o serviço de Recuperação do [Local Azure.](site-recovery-overview.md)

Os anfitriões hiper-V podem ser geridos opcionalmente em nuvens privadas do System Center Virtual Machine Manager (VMM).



## <a name="architectural-components---hyper-v-without-vmm"></a>Componentes arquitetónicos - Hiper-V sem VMM

A tabela e gráfico seguintes fornecem uma visão de alto nível dos componentes utilizados para a replicação do Hiper-V para Azure, quando os anfitriões Hiper-V não são geridos por VMM.

**Componente** | **Requisito** | **Detalhes**
--- | --- | ---
**Azure** | Uma subscrição Azure, conta de armazenamento Azure e rede Azure. | Os dados replicados das cargas de trabalho em VM no local são armazenados na conta de armazenamento. Os VMs Azure são criados com os dados de carga de trabalho replicados quando ocorre uma falha no seu site no local.<br/><br/> As VMs do Azure ligam-se à rede virtual do Azure quando são criadas.
**Hyper-V** | Durante a implementação da Recuperação do Local, você recolhe anfitriões e agrupamentos hiper-V em sites de Hiper-V. Instale o agente Azure Site Recovery Provider and Recovery Services em cada anfitrião autónomo do Hyper-V ou em cada nó de cluster Hyper-V. | O Fornecedor orquestra a replicação com o Site Recovery através da Internet. O agente do Site Recovery trata da replicação de dados.<br/><br/> As comunicações provenientes do Fornecedor e do agente são seguras e encriptadas. Também são encriptados os dados replicados no armazenamento do Azure.
**VMs Hyper-V** | Um ou mais VMs a funcionar no Hyper-V. | Nada precisa de ser explicitamente instalado em VMs.


**Arquitetura Hyper-V a Azure (sem VMM)**

![Diagrama mostrando no local hiper-V para arquitetura Azure sem VMM.](./media/hyper-v-azure-architecture/arch-onprem-azure-hypervsite.png)


## <a name="architectural-components---hyper-v-with-vmm"></a>Componentes arquitetónicos - Hiper-V com VMM

A tabela e gráfico seguintes fornecem uma visão de alto nível dos componentes utilizados para a replicação do Hiper-V para Azure, quando os anfitriões Hiper-V são geridos em nuvens VMM.

**Componente** | **Requisito** | **Detalhes**
--- | --- | ---
**Azure** | Uma subscrição Azure, conta de armazenamento Azure e rede Azure. | Os dados replicados das cargas de trabalho em VM no local são armazenados na conta de armazenamento. Os VMs Azure são criados com os dados replicados quando ocorre uma falha no seu site no local.<br/><br/> As VMs do Azure ligam-se à rede virtual do Azure quando são criadas.
**Servidor VMM** | O servidor VMM tem uma ou mais clouds que contêm anfitriões Hyper-V. | Instala o Fornecedor de Recuperação de Sítio no servidor VMM, para orquestrar a replicação com a Recuperação do Local e registar o servidor no cofre dos Serviços de Recuperação.
**Anfitrião Hyper-V** | Um ou mais anfitriões/clusters de Hyper-V geridos pelo VMM. |  Instale o agente Recovery Services em cada hospedeiro Hiper-V ou nó de cluster.
**VMs Hyper-V** | Uma ou mais VMs em execução num servidor de anfitrião Hyper-V. | Nada tem de estar explicitamente instalado nas VMs.
**Rede** | Redes lógicas e de VMs configuradas no servidor VMM. A rede VM deve estar ligada a uma rede lógica associada à nuvem. | As redes VM estão mapeadas para redes virtuais Azure. Quando os VMs Azure são criados após o failover, são adicionados à rede Azure que está mapeada para a rede VM.

**Arquitetura Hyper-V a Azure (com VMM)**

![Diagrama mostrando no local hiper-V para arquitetura Azure com VMM.](./media/hyper-v-azure-architecture/arch-onprem-onprem-azure-vmm.png)

## <a name="set-up-outbound-network-connectivity"></a>Configurar a conectividade da rede de saída

Para que a Recuperação do Site funcione como esperado, é necessário modificar a conectividade da rede de saída para permitir que o seu ambiente se reproduza.

> [!NOTE]
> A Recuperação do Site não suporta a utilização de um representante de autenticação para controlar a conectividade da rede.

### <a name="outbound-connectivity-for-urls"></a>Conectividade de saída para URLs

Se estiver a usar um proxy de firewall baseado em URL para controlar a conectividade de saída, permita o acesso a estes URLs:

| **Nome**                  | **Comercial**                               | **Administração Pública**                                 | **Descrição** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Armazenamento                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Permite que os dados sejam escritos da VM para a conta de armazenamento em cache na região de origem. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Fornece autorização e autenticação para os URLs do serviço Site Recovery. |
| Replicação               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Permite que a VM comunique com o serviço Site Recovery. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Permite que a VM escreva dados de monitorização e diagnóstico do Site Recovery. |


## <a name="replication-process"></a>Processo de replicação

![Diagrama mostrando o processo de replicação do Hiper-V para Azure](./media/hyper-v-azure-architecture/arch-hyperv-azure-workflow.png)

**Processo de replicação e recuperação**


### <a name="enable-protection"></a>Ativar a proteção

1. Depois de ativar a proteção para uma VM Hyper-V, no portal do Azure ou no local, **Ativar a proteção** é iniciado.
2. A tarefa verifica se a máquina está em conformidade com os pré-requisitos, antes de invocar o [CreateReplicationRelationship](/windows/win32/hyperv_v2/createreplicationrelationship-msvm-replicationservice), para configurar a replicação com as definições que configurou.
3. A tarefa inicia a replicação inicial ao invocar o método [StartReplication](/windows/win32/hyperv_v2/startreplication-msvm-replicationservice), para inicializar uma replicação de VM completa e enviar os discos virtuais da VM para o Azure.
4. Pode monitorizar o trabalho na conta **Jobs.**      ![Screenshot da lista de empregos na conta Jobs. ](media/hyper-v-azure-architecture/image1.png) ![Screenshot do ecrã de proteção Enable com mais detalhes.](media/hyper-v-azure-architecture/image2.png)


### <a name="initial-data-replication"></a>Replicação inicial de dados

1. Quando a replicação inicial é desencadeada, é tirada uma imagem [instantânea Hyper-VM.](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560637(v=ws.10))
2. Os discos rígidos virtuais no VM são replicados um a um, até que todos sejam copiados para o Azure. Isto pode demorar algum tempo, dependendo do tamanho de VM e largura de banda de rede. [Aprenda](https://support.microsoft.com/kb/3056159) a aumentar a largura de banda da rede.
3. Se as alterações do disco ocorrerem enquanto a replicação inicial está em andamento, o Localizador de Replicação de Réplica Hiper-V rastreia as alterações como registos de replicação de Hiper-V (.hrl). Estes ficheiros de registo estão localizados na mesma pasta que os discos. Cada disco tem um ficheiro .hrl associado que é enviado para armazenamento secundário. Os ficheiros de instantâneo e de registo consomem recursos do disco quando a replicação inicial está em curso.
4. Quando a replicação inicial for concluída, o instantâneo da VM é eliminado.
5. As alterações de disco delta no registo são sincronizadas e unidas ao disco principal.


### <a name="finalize-protection-process"></a>Finalizar o processo de proteção

1. Após o final da replicação inicial, a **proteção finalize no** trabalho da máquina virtual. Configura a rede e outras definições de pós-replicação, de modo a que o VM seja protegido.
2. Nesta fase, pode verificar as definições de VM para se certificar de que está pronto para o failover. Pode executar um exercício de recuperação de desastres (teste failover) para o VM, para verificar se falha como esperado. 


## <a name="delta-replication"></a>Replicação delta

1. Após a replicação inicial, a replicação delta começa, de acordo com a política de replicação.
2. O Localizador de Replicação de Réplicas Hiper-V rastreia as alterações num disco rígido virtual como ficheiros .hrl. Cada disco que está configurado para replicação tem um ficheiro .hrl associado.
3. O registo é enviado para a conta de armazenamento do cliente. Quando um registo está em trânsito para Azure, as alterações no disco primário são rastreadas noutro ficheiro de registo, na mesma pasta.
4. Durante a replicação inicial e delta, pode monitorizar o VM no portal Azure.

### <a name="resynchronization-process"></a>Processo de ressincronização

1. Se a replicação delta falhar, e uma replicação completa seria dispendiosa em termos de largura de banda ou de tempo, então uma VM fica marcada para ressincronização.
    - Por exemplo, se os ficheiros de .hrl atingirem 50% do tamanho do disco, a VM será marcada para ressincronização.
    -  Por padrão, a ressincronização está programada para funcionar automaticamente fora do horário de expediente.
1.  A ressincronização envia apenas dados delta.
    - Minimiza a quantidade de dados enviados através da computação de dados da origem e dos VMs-alvo.
    - Usa um algoritmo de corte de bloco fixo onde os ficheiros de origem e alvo são divididos em pedaços fixos.
    - São geradas as caixas de verificação de cada pedaço. Estes são comparados para determinar quais os blocos da fonte que devem ser aplicados ao alvo.
2. Após a conclusão da ressincronização, a replicação delta normal deve ser retomada.
3. Se não quiser esperar pela ressincronização padrão fora de horas, pode ressincronizar manualmente um VM. Por exemplo, se ocorrer uma paragem. Para isso, no portal Azure, selecione o VM > **Resynchronize**.

    ![Screenshot mostrando a opção Resynchronize.](./media/hyper-v-azure-architecture/image4-site.png)


### <a name="retry-process"></a>Processo de retíria

Se ocorrer um erro de replicação, haverá uma repetição interna. A relemissão é classificada como descrita na tabela.

**Categoria** | **Detalhes**
--- | ---
**Erros não recuperáveis** | Não é efetuada qualquer tentativa. O estado da VM será **Crítico**, e é necessária a intervenção do administrador.<br/><br/> Exemplos destes erros incluem uma cadeia VHD quebrada, um estado inválido para a réplica VM, erros de autenticação de rede, erros de autorização e VM não encontrados erros (para servidores Hiper-V autónomos.
**Erros recuperáveis** | As tentativas ocorrem a cada intervalo de replicação, utilizando um término exponencial que aumenta o intervalo entre tentativas, desde o início da primeira tentativa para 1, 2, 4, 8 e 10 minutos. Se o erro persistir, tente novamente a cada 30 minutos. Exemplos destes incluem erros de rede, erros de disco baixos e condições de memória baixas.



## <a name="failover-and-failback-process"></a>Processo de ativação pós-falha e de reativação pós-falha

1. Executa uma ativação pós-falha planeada ou não planeada a partir das VMs de Hyper-V no local para o Azure. Se executar uma ativação pós-falha planeada, as VMs de origem são desligadas para garantir que não há perda de dados. Executar uma falha não planeada se o seu site principal não estiver acessível.
2. Pode fazer a ativação pós-falha de uma máquina individual ou criar planos de recuperação para orquestrar a ativação pós-falha de várias máquinas.
3. Tem um fracasso. Após a conclusão da primeira fase de failover, deverá ser capaz de ver os VMs de réplica criados em Azure. Pode atribuir um endereço IP público à VM, se necessário.
4. Em seguida, você comete o failover, para começar a aceder à carga de trabalho a partir da réplica Azure VM.

Depois de a sua infraestrutura no local estar a funcionar novamente, pode falhar. O recuo ocorre em três fases:

1. Inicie uma falha planeada de Azure para o local:
    - **Minimizar o tempo de inatividade**: Se utilizar esta opção, a Recuperação do Sítio sincroniza os dados antes do failover. Verifica os blocos de dados alterados e transfere-os para o local, enquanto o VM Azure continua a funcionar, minimizando o tempo de inatividade. Quando especificar manualmente que a falha deve ser concluída, o VM Azure é desligado, quaisquer alterações delta finais são copiadas e a falha começa.
    - **Download completo**: Com esta opção os dados são sincronizados durante o failover. Esta opção descarrega todo o disco. É mais rápido porque não são calculadas as contas, mas há mais tempo de inatividade. Utilize esta opção se estiver a executar a réplica Azure VMs há algum tempo, ou se o VM no local foi eliminado.
    - **Criar VM**: Pode selecionar para voltar a falhar no mesmo VM ou num VM alternativo. Pode especificar que a Recuperação do Site deve criar o VM se já não existir.

2. Após o final da sincronização inicial, selecione para completar o failover. Depois de concluído, pode iniciar sessão no local VM para verificar se está tudo a funcionar como esperado. No portal Azure, pode ver que os VMs do Azure foram parados.
3.  Em seguida, você comete o failover para terminar, e começar a aceder à carga de trabalho a partir do VM no local novamente.
4. Depois de as cargas de trabalho terem falhado, permite a replicação inversa, de modo a que os VMs no local se reproduzam novamente para Azure.



## <a name="next-steps"></a>Passos seguintes


Siga [este tutorial](tutorial-prepare-azure.md) para começar com a replicação do Hyper-V para Azure.


