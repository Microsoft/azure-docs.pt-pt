---
title: Planear a capacidade para recuperação após desastre de Hyper-V com o Azure Site Recovery | Documentos da Microsoft
description: Utilize este artigo para estimar a capacidade, ao configurar a recuperação após desastre com o serviço Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: raynew
ms.openlocfilehash: eeadfd6a57ff8a26f3f124e2a807fcd66e77b85f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61036748"
---
# <a name="plan-capacity-for-hyper-v-vm-disaster-recovery"></a>Planear a capacidade para a recuperação de desastres da VM de Hyper-V 

Uma nova versão melhorada do [do Azure Site Recovery Deployment Planner do Hyper-V para implementação do Azure](site-recovery-hyper-v-deployment-planner.md) está agora disponível. Ele substitui a ferramenta antiga. Utilize a nova ferramenta para o planeamento da implementação.
A ferramenta fornece as seguintes diretrizes:

* Avaliação de elegibilidade de VMS com base no número de discos, tamanho do disco, IOPS, alterações a dados e algumas características da VM
* Largura de banda de rede tem de avaliação de RPO
* Requisitos de infraestrutura do Azure
* Requisitos de infraestrutura no local
* Documentação de orientação de criação de batches de replicação inicial
* Custo de recuperação após desastre total para o Azure estimado


O Azure Site Recovery Capacity Planner ajuda-o a determinar os requisitos de capacidade, ao replicar VMs de Hyper-V com o Azure Site Recovery.

Utilize o Site Recovery Capacity Planner para analisar o seu ambiente de origem e cargas de trabalho. Ele ajuda a calcular as necessidades de largura de banda, os recursos de servidor que necessários para a localização de origem e os recursos (por exemplo, VMs e armazenamento) tem na localização de destino.

Pode executar a ferramenta em dois modos:

* **Guia de planejamento**: Fornece as projeções de rede e de servidores com base num número médio de VMs, discos, armazenamento e a taxa de alteração.
* **Detalhadas de planeamento**: Fornece detalhes de cada carga de trabalho no nível da VM. Analisar a compatibilidade VM e obter as projeções de rede e servidor.

## <a name="before-you-start"></a>Antes de começar

* Recolha informações sobre o seu ambiente, incluindo VMs, discos por VM, armazenamento por disco.
* Identifique a sua taxa de alteração (renovação) diária para os dados replicados. Transfira o [ferramenta de planejamento de capacidade de Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) para obter a taxa de alteração. [Saiba mais](site-recovery-capacity-planning-for-hyper-v-replication.md) sobre esta ferramenta. Recomendamos que execute essa ferramenta durante uma semana para capturar as médias.


## <a name="run-the-quick-planner"></a>Executar o planeador de rápida
1. Transfira e abra [Planeador de capacidade de recuperação de Site](https://aka.ms/asr-capacity-planner-excel). Precisa executar macros. Quando lhe for pedido, faça as seleções para edição de enable e conteúdo.

2. Na **selecionar um tipo de planner** caixa de listagem, selecione **Planner rápida**.

   ![Introdução](./media/site-recovery-capacity-planner/getting-started.png)

3. Sobre o **Planeador de capacidade** folha de cálculo, introduza as informações necessárias. Preencha todos os campos circulados em vermelho na captura de ecrã seguinte:

   a. Na **selecione o seu cenário**, escolha **Hyper-V para o Azure** ou **VMware/físico para Azure**.

   b. Na **(%) de taxa de alteração de dados diários médios**, introduza as informações recolhidas através do [ferramenta de planejamento de capacidade de Hyper-V](site-recovery-capacity-planning-for-hyper-v-replication.md) ou [Site Recovery Deployment Planner](./site-recovery-deployment-planner.md).

   c. O **compressão** definição não é utilizada ao replicar VMs Hyper-V para o Azure. Para compressão, utilize uma aplicação de terceiros, como o Riverbed.

   d. Na **retenção em dias**, especifique em dias quanto para manter as réplicas.

   e. Na **número de horas em que a replicação inicial para o batch de máquinas virtuais deve ser concluídas** e **número de máquinas virtuais por lote de replicação inicial**, introduza as definições que são utilizadas para computação requisitos de replicação inicial. Quando o Site Recovery é implementado, o conjunto completo de dados inicial é carregado.

   ![Entradas](./media/site-recovery-capacity-planner/inputs.png)

4. Depois de introduzir os valores para o ambiente de origem, a saída exibida inclui:

   * **Largura de banda necessária para a replicação delta (em Megabits por segundo)**: A taxa média de alteração de dados diário é calculada largura de banda de rede para a replicação delta.
   * **Largura de banda necessária para a replicação inicial (em Megabits por segundo)**: Largura de banda de rede para a replicação inicial é calculada sobre os valores de replicação inicial que introduzir.
   * **Armazenamento necessário (em GBs)**: O total de armazenamento do Azure necessário.
   * **Total de IOPS no armazenamento Standard**: O número é calculado com base no tamanho de unidade IOPS 8K nas contas de armazenamento standard total. Para o planeador de rápida, o número é calculado com base em todos os discos VM de origem e os dados diários de taxa de alteração. Para o planeamento detalhado, o número é calculado com base no número total de VMs que são mapeados para VMs padrão do Azure e os dados alterados taxa nessas VMS.
   * **Número de contas de armazenamento Standard necessárias**: O número total de contas de armazenamento standard é necessário para proteger as VMs. Uma conta de armazenamento standard pode conter até 20 000 IOPS por todas as VMs no armazenamento standard. É suportado um máximo de 500 IOPS por disco.
   * **Número de discos de Blob necessários**: O número de discos que são criadas no armazenamento do Azure.
   * **Número de contas do premium necessárias**: O número total de contas de armazenamento premium é necessário para proteger as VMs. Uma VM com IOPS elevado (mais de 20.000) de origem tem uma conta de armazenamento premium. Uma conta de armazenamento premium pode conter até 80 000 IOPS.
   * **Total de IOPS no armazenamento Premium**: O número é calculado com base no tamanho de unidade IOPS de 256K nas contas de armazenamento total premium. Para o planeador de rápida, o número é calculado com base em todos os discos VM de origem e os dados diários de taxa de alteração. Para o planeamento detalhado, o número é calculado com base no número total de VMs que são mapeados para VMs do Azure premium (séries DS e GS) e os dados alterados taxa nessas VMS.
   * **Número de servidores de configuração necessários**: Mostra o número de servidores de configuração é necessário para a implementação.
   * **Número de servidores de processos adicionais necessários**: Mostra se os servidores de processos adicionais são necessários, além do servidor de processos que está em execução no servidor de configuração por predefinição.
   * **100% armazenamento adicional na origem**: Mostra se o armazenamento adicional é necessária no local de origem.

      ![Saída](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>Executar o planeador de detalhadas

1. Transfira e abra [Planeador de capacidade de recuperação de Site](https://aka.ms/asr-capacity-planner-excel). Precisa executar macros. Quando lhe for pedido, faça as seleções para edição de enable e conteúdo.

2. Na **selecionar um tipo de planner**, selecione **Planner detalhadas** da caixa de listagem.

   ![Guia de introdução](./media/site-recovery-capacity-planner/getting-started-2.png)

3. Sobre o **qualificação de carga de trabalho** folha de cálculo, introduza as informações necessárias. Deve preencher todos os campos marcados.

   a. Na **núcleos de processador**, especifique o número total de núcleos num servidor de origem.

   b. Na **alocação de memória (em MB)**, especifique o tamanho de RAM de um servidor de origem.

   c. Na **número de NICs**, especifique o número de adaptadores de rede num servidor de origem.

   d. Na **armazenamento Total (em GB)**, especifique o tamanho total do armazenamento VM. Por exemplo, se o servidor de origem tem três discos com 500 GB, o tamanho de armazenamento total é 1500 GB.

   e. Na **número de discos ligados**, especifique o número total de discos de um servidor de origem.

   f. Na **utilização de capacidade (%) de disco**, especifique a média de utilização.

   g. Na **(%) de taxa de alteração de dados diária**, especificar os dados diários de alterar a taxa de um servidor de origem.

   h. Na **tamanho da VM do Azure de mapeamento**, introduza o tamanho de VM do Azure que pretende mapear. Se não quiser fazê-lo manualmente, selecione **VMs de IaaS de computação**. Se uma configuração manual de entrada e, em seguida, selecione **VMs de IaaS de computação**, a definição manual pode ser substituída. O processo de computação identifica automaticamente a melhor correspondência no tamanho de VM do Azure.

   ![Folha de cálculo de qualificação de carga de trabalho](./media/site-recovery-capacity-planner/workload-qualification.png)

4. Se selecionou **VMs de IaaS de computação**, eis o que ele faz:

   * Valida as entradas obrigatórias.
   * Calcula a IOPS e sugere a melhor correspondência de tamanho de VM do Azure para cada VM que é elegível para a replicação para o Azure. Se um tamanho adequado, que não é possível detetar a VM do Azure, apresenta um erro. Por exemplo, se o número de discos anexados é 65, apresenta um erro porque o tamanho maior para uma VM do Azure é 64.
   * Sugere uma conta de armazenamento que pode ser utilizada para uma VM do Azure.
   * Calcula o número total de contas de armazenamento standard e contas de armazenamento premium necessárias para a carga de trabalho. Role para baixo para ver o tipo de armazenamento do Azure e a conta de armazenamento que pode ser utilizada para um servidor de origem.
   * Conclusão e ordena o resto da tabela com base no tipo de armazenamento (standard ou premium) atribuído para uma VM e o número de discos ligados. Para todas as VMs que cumprem os requisitos para o Azure, a coluna **VM é qualificado?** mostra **Sim**. Se uma VM não pode ser feita para o Azure, apresenta um erro.

Colunas AA para AE são produzidas e fornecem informações para cada VM.

![Colunas de saída AA para AE](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>Exemplo
Por exemplo, para seis VMs com os valores mostrados na tabela, a ferramenta calcula e atribui a melhor correspondência de VM do Azure e os requisitos de armazenamento do Azure.

![Atribuições de qualificação de carga de trabalho](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* Na saída de exemplo, tenha em atenção o seguinte:

  * A primeira coluna é uma coluna de validação para as VMs, discos e alterações a dados.
  * De VMs de cinco, são necessárias duas contas de armazenamento standard e conta de armazenamento um premium.
  * VM3 não se qualificar para a proteção porque um ou mais discos são mais de 1 TB.
  * A VM1 e VM2 podem utilizar a primeira conta de armazenamento standard
  * VM4 pode usar a segunda conta de armazenamento standard.
  * VM5 e VM6 precisam de uma conta de armazenamento premium, e ambos podem utilizar uma única conta.

    > [!NOTE]
    > IOPS no armazenamento standard e premium são calculados ao nível da VM e não ao nível do disco. Um padrão de VM pode processar até 500 IOPS por disco. Se o IOPS para um disco for superiores a 500, terá do armazenamento premium. Se o IOPS para um disco estiver mais de 500, mas IOPS para os discos VM total estão dentro dos limites de VM do Azure standard de suporte, o planeador de implementações escolhe uma VM padrão e não a série DS ou GS. (Os limites de VM do Azure são o tamanho da VM, número de discos, número de adaptadores de CPU e memória.) Terá de atualizar manualmente a célula de tamanho do Azure de mapeamento com a apropriado série DS ou GS VM.


Após introduzir todas as informações, selecione **enviar dados para a ferramenta Planeador de implementações** para abrir o planeador de capacidade. Cargas de trabalho são realçadas para mostrar se são elegíveis para proteção.

### <a name="submit-data-in-capacity-planner"></a>Enviar dados em Capacity Planner
1. Quando abre o **Planeador de capacidade** folha de cálculo, ele é preenchido com base nas definições que especificou. A palavra "Carga de trabalho" aparece no **origem de entradas do Uniform** célula para mostrar que a entrada é a **qualificação de carga de trabalho** folha de cálculo.

2. Se desejar fazer alterações, tem de modificar os **qualificação de carga de trabalho** folha de cálculo. Em seguida, selecione **enviar dados para a ferramenta Planeador de implementações** novamente.

   ![Capacity Planner](./media/site-recovery-capacity-planner/capacity-planner.png)

## <a name="next-steps"></a>Passos Seguintes
[Saiba como executar](site-recovery-capacity-planning-for-hyper-v-replication.md) a ferramenta de planejamento de capacidade.
