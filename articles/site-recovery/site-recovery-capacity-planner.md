---
title: Planejar a capacidade de recuperação de desastres do Hyper-V com o Azure Site Recovery | Microsoft Docs
description: Use este artigo para estimar a capacidade ao configurar a recuperação de desastre com o serviço Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: raynew
ms.openlocfilehash: 7501982f90cd145e0fc918bf976a840323a31127
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972577"
---
# <a name="plan-capacity-for-hyper-v-vm-disaster-recovery"></a>Planejar a capacidade de recuperação de desastre de VM Hyper-V 

O [Planejador de Implantações do Azure Site Recovery] (site-recovery-hyper-v-deployment-planner.md) para a implantação do Hyper-V para o Azure fornece o seguinte:

* Avaliação de qualificação de VM, com base no número de discos, tamanho do disco, IOPS, rotatividade e algumas características da VM
* Necessidade de largura de banda de rede versus avaliação de RPO
* Requisitos de infraestrutura do Azure
* Requisitos de infraestrutura local
* Diretrizes de envio em lote de replicação inicial
* Custo estimado total de recuperação de desastre para o Azure


Planejador de Capacidade do Azure Site Recovery ajuda a determinar os requisitos de capacidade ao replicar VMs do Hyper-V com Azure Site Recovery.

Use Site Recovery Planejador de Capacidade para analisar o ambiente de origem e as cargas de trabalho. Ele ajuda a estimar as necessidades de largura de banda, os recursos de servidor necessários para o local de origem e os recursos (como VMs e armazenamento) necessários no local de destino.

Você pode executar a ferramenta em dois modos:

* **Planejamento rápido**: Fornece projeções de rede e de servidor com base em um número médio de VMs, discos, armazenamento e taxa de alteração.
* **Planejamento detalhado**: Fornece detalhes de cada carga de trabalho no nível da VM. Analise a compatibilidade da VM e obtenha projeções de rede e de servidor.

## <a name="before-you-start"></a>Antes de começar

* Reúna informações sobre seu ambiente, incluindo VMs, discos por VM, armazenamento por disco.
* Identifique a taxa de alteração (variação) diária de dados replicados. Baixe a [ferramenta de planejamento de capacidade do Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) para obter a taxa de alteração. [Saiba mais](site-recovery-capacity-planning-for-hyper-v-replication.md) sobre esta ferramenta. Recomendamos que você execute essa ferramenta durante uma semana para capturar as médias.


## <a name="run-the-quick-planner"></a>Executar o planejador rápido
1. Baixe e abra [Site Recovery planejador de capacidade](https://aka.ms/asr-capacity-planner-excel). Você precisa executar macros. Quando for solicitado, faça seleções para habilitar a edição e o conteúdo.

2. Na caixa de listagem **selecionar um tipo de planejador** , selecione **planejador rápido**.

   ![Introdução](./media/site-recovery-capacity-planner/getting-started.png)

3. Na planilha **planejador de capacidade** , insira as informações necessárias. Preencha todos os campos circulados em vermelho na seguinte captura de tela:

   a. Em **selecionar seu cenário**, escolha **Hyper-V para Azure** ou **VMware/físico para o Azure**.

   b. Em **taxa média diária de alteração de dados (%)** , insira as informações coletadas usando a [ferramenta de planejamento de capacidade do Hyper-V](site-recovery-capacity-planning-for-hyper-v-replication.md) ou planejador de implantações de [site Recovery](./site-recovery-deployment-planner.md).

   c. A configuração de compactação não é usada quando você Replica VMs do Hyper-V para o Azure. Para compactação, use um dispositivo de terceiros, como Riverbed.

   d. Em **retenção em dias**, especifique, em dias, o tempo de retenção de réplicas.

   e. Em **número de horas em que a replicação inicial do lote de máquinas virtuais deve ser concluída e o** **número de máquinas virtuais por lote de replicação inicial**, insira as configurações que são usadas para computar os requisitos de replicação inicial. Quando Site Recovery é implantado, todo o conjunto de dados inicial é carregado.

   ![Entradas](./media/site-recovery-capacity-planner/inputs.png)

4. Depois de inserir os valores para o ambiente de origem, a saída exibida inclui:

   * **Largura de banda necessária para a replicação delta (em megabits/s)** : A largura de banda de rede para a replicação delta é calculada na taxa média diária de alteração de dados.
   * **Largura de banda necessária para a replicação inicial (em megabits/s)** : A largura de banda de rede para a replicação inicial é calculada nos valores de replicação inicial que você inserir.
   * **Armazenamento necessário (em GBS)** : O armazenamento total do Azure necessário.
   * **IOPS total no armazenamento Standard**: O número é calculado com base no tamanho da unidade de IOPS de 8K no total de contas de armazenamento padrão. Para o planejador rápido, o número é calculado com base em todos os discos de VM de origem e na taxa de alteração de dados diária. Para o planejador detalhado, o número é calculado com base no número total de VMs que são mapeadas para VMs padrão do Azure e a taxa de alteração de dados nessas VMs.
   * **Número de contas de armazenamento padrão necessárias**: O número total de contas de armazenamento padrão necessárias para proteger as VMs. Uma conta de armazenamento padrão pode conter até 20.000 IOPS em todas as VMs no armazenamento Standard. Há suporte para um máximo de 500 IOPS por disco.
   * **Número de discos de blob necessários**: O número de discos que são criados no armazenamento do Azure.
   * **Número de contas Premium necessárias**: O número total de contas de armazenamento Premium necessárias para proteger as VMs. Uma VM de origem com IOPS alto (maior que 20.000) precisa de uma conta de armazenamento Premium. Uma conta de armazenamento Premium pode conter até 80.000 IOPS.
   * **IOPS total no armazenamento Premium**: O número é calculado com base no tamanho da unidade de IOPS de 256 k no total de contas de armazenamento Premium. Para o planejador rápido, o número é calculado com base em todos os discos de VM de origem e na taxa de alteração de dados diária. Para o planejador detalhado, o número é calculado com base no número total de VMs que são mapeadas para VMs Premium do Azure (série DS e GS) e a taxa de alteração de dados nessas VMs.
   * **Número de servidores de configuração necessários**: Mostra quantos servidores de configuração são necessários para a implantação.
   * **Número de servidores de processo adicionais necessários**: Mostra se os servidores de processo adicionais são necessários, além do servidor de processo em execução no servidor de configuração por padrão.
   * **100% de armazenamento adicional na origem**: Mostra se o armazenamento adicional é necessário no local de origem.

      ![Output](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>Executar o planejador detalhado

1. Baixe e abra [Site Recovery planejador de capacidade](https://aka.ms/asr-capacity-planner-excel). Você precisa executar macros. Quando for solicitado, faça seleções para habilitar a edição e o conteúdo.

2. Em **selecionar um tipo**de planejador, selecione **planejador detalhado** na caixa de listagem.

   ![Guia de introdução](./media/site-recovery-capacity-planner/getting-started-2.png)

3. Na planilha **qualificação de carga de trabalho** , insira as informações necessárias. Você deve preencher todos os campos marcados.

   a. Em **núcleos de processador**, especifique o número total de núcleos em um servidor de origem.

   b. Em **alocação de memória (em MBS)** , especifique o tamanho de RAM de um servidor de origem.

   c. Em **número de NICs**, especifique o número de adaptadores de rede em um servidor de origem.

   d. Em **armazenamento total (em GB)** , especifique o tamanho total do armazenamento da VM. Por exemplo, se o servidor de origem tiver três discos com 500 GB cada, o tamanho de armazenamento total será de 1.500 GB.

   e. Em **número de discos anexados**, especifique o número total de discos de um servidor de origem.

   f. Em **utilização da capacidade do disco (%)** , especifique a utilização média.

   g. Em **taxa diária de alteração de dados (%)** , especifique a taxa diária de alteração de dados de um servidor de origem.

   h. Em **mapeando o tamanho da VM do Azure**, insira o tamanho da VM do Azure que você deseja mapear. Se você não quiser fazer isso manualmente, selecione computar **VMs de IaaS**. Se você inserir uma configuração manual e, em seguida, selecionar **VMs de IaaS de computação**, a configuração manual poderá ser substituída. O processo de computação identifica automaticamente a melhor correspondência no tamanho da VM do Azure.

   ![Planilha de qualificação de carga de trabalho](./media/site-recovery-capacity-planner/workload-qualification.png)

4. Se você selecionar **VMs de IaaS de computação**, aqui está o que ele faz:

   * Valida as entradas obrigatórias.
   * Calcula o IOPS e sugere a melhor correspondência de tamanho de VM do Azure para cada VM qualificada para replicação no Azure. Se um tamanho apropriado da VM do Azure não puder ser detectado, um erro será exibido. Por exemplo, se o número de discos anexados for 65, um erro será exibido porque o tamanho mais alto para uma VM do Azure é 64.
   * Sugere uma conta de armazenamento que pode ser usada para uma VM do Azure.
   * Calcula o número total de contas de armazenamento Standard e as contas de armazenamento Premium necessárias para a carga de trabalho. Role para baixo para exibir o tipo de armazenamento do Azure e a conta de armazenamento que pode ser usada para um servidor de origem.
   * Conclui e classifica o restante da tabela com base no tipo de armazenamento necessário (Standard ou Premium) atribuído para uma VM e o número de discos anexados. Para todas as VMs que atendem aos requisitos do Azure, a coluna **é qualificada pela VM?** mostra **Sim**. Se não for possível fazer backup de uma VM no Azure, um erro será exibido.

As colunas AA a AE são saídas e fornecem informações para cada VM.

![Colunas de saída AA a AE](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>Exemplo
Por exemplo, para seis VMs com os valores mostrados na tabela, a ferramenta calcula e atribui a melhor correspondência de VM do Azure e os requisitos de armazenamento do Azure.

![Atribuições de qualificação de carga de trabalho](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* Na saída de exemplo, observe o seguinte:

  * A primeira coluna é uma coluna de validação para as VMs, os discos e a rotatividade.
  * Duas contas de armazenamento Standard e uma conta de armazenamento Premium são necessárias para cinco VMs.
  * O VM3 não se qualifica para proteção porque um ou mais discos têm mais de 1 TB.
  * VM1 e VM2 podem usar a primeira conta de armazenamento padrão
  * VM4 pode usar a segunda conta de armazenamento padrão.
  * VM5 e VM6 precisam de uma conta de armazenamento Premium, e ambos podem usar uma única conta.

    > [!NOTE]
    > O IOPS no armazenamento Standard e Premium é calculado no nível da VM e não no nível do disco. Uma VM padrão pode lidar com até 500 IOPS por disco. Se o IOPS de um disco for maior que 500, você precisará do armazenamento Premium. Se o IOPS de um disco for maior que 500, mas o IOPS para o total de discos de VM estiver dentro dos limites de VM padrão do Azure de suporte, o planejador escolherá uma VM padrão e não a série DS ou GS. (Os limites de VM do Azure são o tamanho da VM, o número de discos, o número de adaptadores, a CPU e a memória.) Você precisa atualizar manualmente a célula de tamanho do Azure de mapeamento com a VM da série DS ou GS apropriada.


Depois que todas as informações forem inseridas, selecione **enviar dados para a ferramenta do planejador** para abrir o planejador de capacidade. As cargas de trabalho são realçadas para mostrar se elas estão qualificadas para proteção.

### <a name="submit-data-in-capacity-planner"></a>Enviar dados no Planejador de Capacidade
1. Quando você abre a planilha **planejador de capacidade** , ela é preenchida com base nas configurações especificadas. A palavra "carga de trabalho" aparece na célula de **origem de entradas** de infraestrutura para mostrar que a entrada é a planilha de qualificação de **carga de trabalho** .

2. Se você quiser fazer alterações, precisará modificar a planilha de **qualificação de carga** de trabalho. Em seguida, selecione **enviar dados para a ferramenta planejador** novamente.

   ![Capacity Planner](./media/site-recovery-capacity-planner/capacity-planner.png)

## <a name="next-steps"></a>Passos Seguintes
[Saiba como executar](site-recovery-capacity-planning-for-hyper-v-replication.md) a ferramenta de planejamento de capacidade.
