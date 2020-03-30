---
title: Capacidade de plano para recuperação de desastres hiper-V com recuperação do site Azure
description: Utilize este artigo para estimar a capacidade ao configurar a recuperação de desastres com o serviço de recuperação do site Azure.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 843d5da26d6791cea880e5dfb654fe27b74f5d9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73936050"
---
# <a name="plan-capacity-for-hyper-v-vm-disaster-recovery"></a>Capacidade de plano para recuperação de desastres de VM Hiper-V 

O [Azure Site Recovery Deployment Planner] (site-recovery-hyper-v-deployment-planner.md) para a implantação Hyper-V a Azure fornece o seguinte:

* Avaliação de elegibilidade vm, com base no número de discos, tamanho do disco, IOPS, churn, e algumas características VM
* Necessidade de largura de banda de rede vs avaliação de RPO
* Requisitos de infraestrutura do Azure
* Requisitos de infraestrutura no local
* Orientação para a criação de batches na replicação inicial
* Custo total estimado de recuperação de desastres para Azure


O Planejador de Capacidade de Recuperação do Site Azure ajuda-o a determinar os seus requisitos de capacidade quando replica VMs Hiper-V com recuperação do site Azure.

Utilize o Planejador de Capacidadede Recuperação do Site para analisar o seu ambiente de origem e cargas de trabalho. Ajuda-o a estimar as necessidades de largura de banda, os recursos do servidor que necessita para a localização de origem e os recursos (como VMs e armazenamento) que necessita na localização alvo.

Pode executar a ferramenta em dois modos:

* **Planeamento rápido**: Fornece projeções de rede e servidor estanques com base num número médio de VMs, discos, armazenamento e taxa de alteração.
* **Planeamento detalhado**: Fornece detalhes de cada carga de trabalho ao nível do VM. Analise a compatibilidade vm e obtenha projeções de rede e servidor.

## <a name="before-you-start"></a>Antes de começar

* Recolha informações sobre o seu ambiente, incluindo VMs, discos por VM, armazenamento por disco.
* Identifique a sua taxa de variação diária (churn) para dados replicados. Descarregue a ferramenta de planeamento de [capacidade seleções Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) para obter a taxa de mudança. [Saiba mais](site-recovery-capacity-planning-for-hyper-v-replication.md) sobre esta ferramenta. Recomendamos que execute esta ferramenta ao longo de uma semana para capturar médias.


## <a name="run-the-quick-planner"></a>Executar o Planejador Rápido
1. Descarregue e abra o Planejador de [Capacidade de Recuperação](https://aka.ms/asr-capacity-planner-excel)do Site. Tens de fazer macros. Quando for solicitado, faça seleções para permitir a edição e o conteúdo.

2. Na caixa de lista **seletiva,** selecione **Quick Planner**.

   ![Introdução](./media/site-recovery-capacity-planner/getting-started.png)

3. Na folha de cálculo do **Planificador** de Capacidade, introduza as informações necessárias. Preencha todos os campos circulados a vermelho na seguinte imagem:

   a. Em **Selecione o seu cenário,** escolha **Hyper-V para Azure** ou **VMware/Físico para Azure**.

   b. Na taxa média diária de alteração de **dados (%)** introduza a informação que recolhe utilizando a ferramenta de planeamento de [capacidade hiper-V](site-recovery-capacity-planning-for-hyper-v-replication.md) ou [o Planejador](./site-recovery-deployment-planner.md)de Implementação de Recuperação do Site .

   c. A definição de **compressão** não é usada quando se replica mVs hiper-V para Azure. Para compressão, utilize um aparelho de terceiros, como o Riverbed.

   d. Na **Retenção em dias,** especifique em dias quanto tempo reter réplicas.

   e. Em número de horas em que a **replicação inicial para o lote de máquinas virtuais deve ser completada** e número de máquinas virtuais por lote de **replicação inicial,** introduza as definições que são usadas para calcular os requisitos iniciais de replicação. Quando a Recuperação do Site é implementada, todo o conjunto de dados inicial é carregado.

   ![Entradas](./media/site-recovery-capacity-planner/inputs.png)

4. Depois de introduzir os valores para o ambiente de origem, a saída apresentada inclui:

   * **Largura de banda necessária para a replicação delta (em Megabits/seg)**: A largura de banda da rede para a replicação delta é calculada na taxa média diária de variação de dados.
   * **Largura de banda necessária para a replicação inicial (em Megabits/seg)**: A largura de banda da rede para a replicação inicial é calculada nos valores de replicação inicial em que entra.
   * **Armazenamento necessário (em GBs)**: O armazenamento total do Azure necessário.
   * **IOPS total em Armazenamento Padrão**: O número é calculado com base no tamanho da unidade IOPS 8K nas contas de armazenamento padrão totais. Para o Quick Planner, o número é calculado com base em todos os discos VM de origem e na taxa diária de alteração de dados. Para o Planejador Detalhado, o número é calculado com base no número total de VMs que são mapeados para VMs-padrão e a taxa de variação de dados nesses VMs.
   * Número de contas de **armazenamento standard necessárias:** O número total de contas de armazenamento padrão necessárias para proteger os VMs. Uma conta de armazenamento padrão pode conter até 20.000 IOPS em todos os VMs em armazenamento padrão. Um máximo de 500 IOPS é suportado por disco.
   * **Número de discos Blob necessários**: O número de discos criados no armazenamento azure.
   * **Número de contas premium exigidas**: O número total de contas de armazenamento premium necessárias para proteger os VMs. Uma fonte VM com IOPS elevado (superior a 20.000) precisa de uma conta de armazenamento premium. Uma conta de armazenamento premium pode conter até 80.000 IOPS.
   * **IOPS total em Armazenamento Premium**: O número é calculado com base no tamanho da unidade IOPS de 256K nas contas totais de armazenamento premium. Para o Quick Planner, o número é calculado com base em todos os discos VM de origem e na taxa diária de alteração de dados. Para o Planejador Detalhado, o número é calculado com base no número total de VMs que são mapeados para VMs De SI (série SD e GS) e a taxa de variação de dados nesses VMs.
   * **Número de Servidores de Configuração necessários**: Mostra quantos servidores de configuração são necessários para a implementação.
   * **Número de servidores de processos adicionais necessários**: Mostra se são necessários servidores de processos adicionais, além do servidor de processo que está a ser funcionado no servidor de configuração por padrão.
   * **Armazenamento 100% adicional na Fonte**: Mostra se é necessário armazenamento adicional na localização de origem.

      ![Saída](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>Executar o Planejador Detalhado

1. Descarregue e abra o Planejador de [Capacidade de Recuperação](https://aka.ms/asr-capacity-planner-excel)do Site. Tens de fazer macros. Quando for solicitado, faça seleções para permitir a edição e o conteúdo.

2. Em Selecionar um tipo de **planejador,** selecione **Planner Detalhado** da caixa da lista.

   ![Guia de introdução](./media/site-recovery-capacity-planner/getting-started-2.png)

3. Na folha de cálculo de qualificação de carga de **trabalho,** introduza as informações necessárias. Deve preencher todos os campos marcados.

   a. Em **Cores**processadores, especifique o número total de núcleos num servidor de origem.

   b. Na **atribuição da memória (em MBs)**, especifique o tamanho RAM de um servidor de origem.

   c. Em **Número de NICs,** especifique o número de adaptadores de rede num servidor de origem.

   d. No **Armazenamento Total (em GB)**, especifique o tamanho total do armazenamento VM. Por exemplo, se o servidor de origem tiver três discos com 500 GB cada, o tamanho total do armazenamento é de 1.500 GB.

   e. No **número de discos anexados,** especifique o número total de discos de um servidor de origem.

   f. Na utilização da **capacidade do disco (%)** especifique a utilização média.

   g. No **Dia-a-Dia de alteração de dados (%)** especificar a taxa diária de alteração de dados de um servidor de origem.

   h. No tamanho de **Mapeamento Azure VM,** insira o tamanho Azure VM que pretende mapear. Se não quiser fazer isto manualmente, selecione **VMs Compute IaaS**. Se introduzir uma definição manual e selecionar **VMs Compute IaaS,** a definição manual pode ser substituída. O processo de computação identifica automaticamente a melhor correspondência no tamanho do VM Azure.

   ![Folha de cálculo de qualificação de carga de trabalho](./media/site-recovery-capacity-planner/workload-qualification.png)

4. Se selecionar **VMs Compute IaaS,** eis o que faz:

   * Valida as inputs obrigatórias.
   * Calcula o IOPS e sugere a melhor combinação de tamanho Azure VM para cada VM que é elegível para replicação para Azure. Se não for possível detetar um tamanho adequado, não é possível detetar um erro. Por exemplo, se o número de discos ligados for de 65, um erro exibe porque o tamanho mais alto para um VM Azure é de 64.
   * Sugere uma conta de armazenamento que pode ser usada para um VM Azure.
   * Calcula o número total de contas de armazenamento padrão e contas de armazenamento premium necessárias para a carga de trabalho. Desloque-se para baixo para ver o tipo de armazenamento Azure e a conta de armazenamento que pode ser usada para um servidor de origem.
   * Completa e classifica o resto da tabela com base no tipo de armazenamento necessário (standard ou premium) atribuído para um VM e no número de discos anexados. Para todos os VMs que cumprem os requisitos para **Yes**o Azure, a coluna **é qualificada vM?** Se um VM não puder ser apoiado até ao Azure, um erro mostra.

As colunas AA a AE são saída e fornecem informações para cada VM.

![Colunas de saída AA a AE](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>Exemplo
Como exemplo, para seis VMs com os valores mostrados na tabela, a ferramenta calcula e atribui a melhor correspondência Azure VM e os requisitos de armazenamento Azure.

![Atribuição de qualificações de carga de trabalho](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* Na saída do exemplo, note o seguinte:

  * A primeira coluna é uma coluna de validação para os VMs, discos e churn.
  * São necessárias duas contas-padrão de armazenamento e uma conta de armazenamento premium para cinco VMs.
  * O VM3 não se qualifica para proteção porque um ou mais discos são mais de 1 TB.
  * VM1 e VM2 podem usar a primeira conta de armazenamento padrão
  * O VM4 pode utilizar a segunda conta de armazenamento padrão.
  * VM5 e VM6 precisam de uma conta de armazenamento premium, e ambos podem usar uma única conta.

    > [!NOTE]
    > Os IOPS sobre armazenamento standard e premium são calculados ao nível do VM e não ao nível do disco. Um VM padrão pode lidar com até 500 IOPS por disco. Se o IOPS para um disco for superior a 500, precisa de armazenamento premium. Se os IOPS para um disco forem superiores a 500, mas os IOPS para o total dos discos VM estão dentro dos limites padrão de suporte do Azure VM, o planificador escolhe um VM padrão e não a série DS ou GS. (Os limites de VM Azure são o tamanho vm, o número de discos, o número de adaptadores, cpu e memória.) É necessário atualizar manualmente a célula de tamanho Azure de mapeamento com o DS ou série GS adequado VM.


Depois de toda a informação ser introduzida, selecione **Enviar dados para a ferramenta de planejador** para abrir o Planner de Capacidade. As cargas de trabalho são destacadas para mostrar se são elegíveis para proteção.

### <a name="submit-data-in-capacity-planner"></a>Enviar dados no Planificador de Capacidade
1. Quando abre a folha de cálculo do **Planificador** de Capacidade, é povoada com base nas definições que especificou. A palavra "Carga de Trabalho" aparece na célula fonte de entrada **infra** para mostrar que a entrada é a folha de cálculo de qualificação de carga de **trabalho.**

2. Se quiser fazer alterações, tem de modificar a folha de cálculo da Qualificação de **Carga de Trabalho.** Em seguida, selecione **Enviar novamente dados para a ferramenta de planejador.**

   ![Planejador de Capacidades](./media/site-recovery-capacity-planner/capacity-planner.png)

## <a name="next-steps"></a>Passos seguintes
[Aprenda a executar](site-recovery-capacity-planning-for-hyper-v-replication.md) a ferramenta de planeamento de capacidades.
