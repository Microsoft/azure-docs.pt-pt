---
title: Capacidade do plano para recuperação de desastres hiper-V com recuperação do local de Azure
description: Utilize este artigo para estimar a capacidade ao configurar a recuperação de desastres com o serviço de Recuperação do Local Azure.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: a5764e44db31755110ac99a3e8e8e0984cdf9604
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87490579"
---
# <a name="plan-capacity-for-hyper-v-vm-disaster-recovery"></a>Planear a capacidade para recuperação após desastre da VM Hyper-V 

O [Azure Site Recovery Deployment Planner] (site-recovery-hyper-v-deployment-planner.md) para a implantação de Hiper-V a Azure fornece o seguinte:

* Avaliação de elegibilidade em VM, com base no número de discos, tamanho do disco, IOPS, churn, e algumas características VM
* Necessidade de largura de banda de rede vs avaliação de RPO
* Requisitos de infraestrutura do Azure
* Requisitos de infraestrutura no local
* Orientação para a criação de batches na replicação inicial
* Custo estimado da recuperação total de desastres para a Azure


O Azure Site Recovery Capacity Planner ajuda-o a determinar os seus requisitos de capacidade quando replica Os VMs hiper-V com a recuperação do local de Azure.

Utilize o Planificador de Capacidade de Recuperação do Local para analisar o seu ambiente de origem e cargas de trabalho. Ajuda-o a estimar as necessidades de largura de banda, os recursos do servidor de que necessita para a localização da fonte, e os recursos (como VMs e armazenamento) que necessita no local do alvo.

Pode executar a ferramenta em dois modos:

* **Planeamento rápido**: Fornece projeções de rede e servidor com base num número médio de VMs, discos, armazenamento e taxa de alteração.
* **Planeamento detalhado**: Fornece detalhes de cada carga de trabalho ao nível de VM. Analise a compatibilidade do VM e obtenha projeções de rede e servidor.

## <a name="before-you-start"></a>Antes de começar

* Recolha informações sobre o seu ambiente, incluindo VMs, discos por VM, armazenamento por disco.
* Identifique a sua taxa de alteração diária (churn) para dados replicados. Descarregue a [ferramenta de planeamento de capacidades Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) para obter a taxa de variação. [Saiba mais](./hyper-v-deployment-planner-overview.md) sobre esta ferramenta. Recomendamos que execute esta ferramenta durante uma semana para capturar médias.


## <a name="run-the-quick-planner"></a>Executar o Planejador Rápido
1. Descarregue e abra [o Planejador da Capacidade de Recuperação do Local.](https://aka.ms/asr-capacity-planner-excel) Tens de fazer macros. Quando for solicitado, faça seleções para ativar a edição e o conteúdo.

2. Na caixa de lista **de tipo de planificador Select,** selecione **Quick Planner**.

   ![Screenshot da opção De tipo planejador, com o Quick Planner selecionado.](./media/site-recovery-capacity-planner/getting-started.png)

3. Na folha de cálculo **do Planeamento de Capacidade,** insira as informações necessárias. Preencha todos os campos circulados a vermelho na seguinte imagem:

   a. Em **Selecione o seu cenário,** escolha **Hyper-V para Azure** ou **VMware/Physical to Azure**.

   b. Na taxa média diária de alteração de **dados (%)**, insira as informações que recolhe utilizando a [ferramenta de planeamento da capacidade de Hiper-V](./hyper-v-deployment-planner-overview.md) ou o [Planejador de Implementação da Recuperação do Local.](./site-recovery-deployment-planner.md)

   c. A **definição de compressão** não é usada quando replicas Os VMs Hiper-V para Azure. Para compressão, utilize um aparelho de terceiros, como o Riverbed.

   d. Em **Retenção em dias**, especifique em dias quanto tempo para reter réplicas.

   e. Em **Número de horas em que a replicação inicial para o lote de máquinas virtuais deve ser completada** e Número de **máquinas virtuais por lote de replicação inicial**, introduza as definições que são usadas para calcular os requisitos iniciais de replicação. Quando a Recuperação do Site é implantada, todo o conjunto de dados inicial é carregado.

   ![Screenshot da folha de cálculo do Capacity Planner, mostrando as informações de entrada necessárias.](./media/site-recovery-capacity-planner/inputs.png)

4. Depois de introduzir os valores para o ambiente de origem, a saída apresentada inclui:

   * **Largura de banda necessária para a replicação delta (em Megabits/seg)**: A largura de banda da rede para replicação delta é calculada na taxa média diária de alteração de dados.
   * **Largura de banda necessária para a replicação inicial (em Megabits/seg)**: A largura de banda da rede para replicação inicial é calculada nos valores iniciais de replicação que introduz.
   * **Armazenamento necessário (em GBs)**: O armazenamento total de Azure necessário.
   * **Total de IOPS no Armazenamento Padrão**: O número é calculado com base no tamanho da unidade 8K IOPS nas contas de armazenamento padrão totais. Para o Quick Planner, o número é calculado com base em todos os discos VM de origem e na taxa de alteração diária de dados. Para o Planificador Detalhado, o número é calculado com base no número total de VMs que são mapeados para VMs standard e a taxa de alteração de dados nesses VMs.
   * **Número de contas de armazenamento standard necessárias**: O número total de contas de armazenamento padrão necessárias para proteger os VMs. Uma conta de armazenamento padrão pode ter até 20.000 IOPS em todos os VMs em armazenamento padrão. Um máximo de 500 IOPS é suportado por disco.
   * **Número de discos Blob necessários**: O número de discos criados no armazenamento do Azure.
   * **Número de contas premium exigidas**: O número total de contas de armazenamento premium necessárias para proteger os VMs. Uma fonte VM com IOPS elevado (superior a 20.000) precisa de uma conta de armazenamento premium. Uma conta de armazenamento premium pode ter até 80.000 IOPS.
   * **Total de IOPS no Armazenamento Premium**: O número é calculado com base no tamanho da unidade IOPS de 256K nas contas totais de armazenamento premium. Para o Quick Planner, o número é calculado com base em todos os discos VM de origem e na taxa de alteração diária de dados. Para o Planificador Detalhado, o número é calculado com base no número total de VMs que são mapeados para VMs premium (sérieS DS e GS) e a taxa de alteração de dados nesses VMs.
   * **Número de Servidores de Configuração necessários**: Mostra quantos servidores de configuração são necessários para a implementação.
   * **Número de servidores de processo adicionais necessários**: Mostra se são necessários servidores de processo adicionais, para além do servidor de processo que está a ser em execução no servidor de configuração por predefinição.
   * **Armazenamento adicional 100% na Fonte**: Mostra se é necessário armazenamento adicional no local de origem.

      ![Screenshot da saída visualizada com base na entrada fornecida.](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>Executar o Planejador Detalhado

1. Descarregue e abra [o Planejador da Capacidade de Recuperação do Local.](https://aka.ms/asr-capacity-planner-excel) Tens de fazer macros. Quando for solicitado, faça seleções para ativar a edição e o conteúdo.

2. Em **Selecione um tipo de planificador,** selecione **Plano detalhado** na caixa de lista.

   ![Screenshot da opção de tipo planejador Select, com o Planner Detalhado selecionado.](./media/site-recovery-capacity-planner/getting-started-2.png)

3. Na folha de qualificação de carga de **trabalho,** insira as informações necessárias. Deve preencher todos os campos marcados.

   a. Nos **Núcleos de Processadores**, especifique o número total de núcleos num servidor de origem.

   b. Na **atribuição de memória (em MBs)**, especifique o tamanho RAM de um servidor de origem.

   c. Em **Número de NICs, especifique**o número de adaptadores de rede num servidor de origem.

   d. No **Armazenamento Total (em GB)**, especifique o tamanho total do armazenamento em VM. Por exemplo, se o servidor de origem tiver três discos com 500 GB cada, o tamanho total do armazenamento é de 1.500 GB.

   e. Em **Número de discos ligados**, especifique o número total de discos de um servidor de origem.

   f. Na **utilização da capacidade do disco (%)**, especifique a utilização média.

   exemplo, Na **taxa diária de alteração de dados (%)**, especifique a taxa diária de alteração de dados de um servidor de origem.

   h. No **mapeamento do tamanho VM do Azure,** insira o tamanho Azure VM que pretende mapear. Se não quiser fazer isto manualmente, **selecione Compute IaaS VMs**. Se inserir uma definição manual e **selecionar Os VMs Compute IaaS,** a definição manual pode ser substituída. O processo de cálculo identifica automaticamente a melhor correspondência no tamanho do Azure VM.

   ![Screenshot da folha de qualificação de carga de trabalho, mostrando as informações de entrada necessárias.](./media/site-recovery-capacity-planner/workload-qualification.png)

4. Se **selecionar Compute IaaS VMs,** eis o que faz:

   * Valida as entradas obrigatórias.
   * Calcula o IOPS e sugere a melhor combinação de tamanho Azure VM para cada VM que é elegível para replicação para Azure. Se não for possível detetar um tamanho adequado Azure VM, um erro aparece. Por exemplo, se o número de discos ligados for de 65, um erro aparece porque o tamanho mais alto para um Azure VM é de 64.
   * Sugere uma conta de armazenamento que pode ser usada para um VM Azure.
   * Calcula o número total de contas de armazenamento padrão e contas de armazenamento premium necessárias para a carga de trabalho. Desloque-se para baixo para ver o tipo de armazenamento Azure e a conta de armazenamento que pode ser usada para um servidor de origem.
   * Completa e classifica o resto da tabela com base no tipo de armazenamento necessário (padrão ou premium) atribuído a um VM e ao número de discos anexados. Para todos os VMs que cumprem os requisitos para Azure, a coluna **É VM qualificada?** **Yes** Se um VM não puder ser apoiado até Azure, um erro aparece.

As colunas AA a AE são saídas e fornecem informações para cada VM.

![Screenshot mostrando colunas de saída AA a AE.](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>Exemplo
Como exemplo, para seis VMs com os valores indicados na tabela, a ferramenta calcula e atribui o melhor fósforo Azure VM e os requisitos de armazenamento Azure.

![Screenshot mostrando atribuições de qualificação de carga de trabalho.](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* Na saída de exemplo, note o seguinte:

  * A primeira coluna é uma coluna de validação para os VMs, discos e churn.
  * São necessárias duas contas de armazenamento padrão e uma conta de armazenamento premium para cinco VMs.
  * O VM3 não se qualifica para proteção porque um ou mais discos são mais de 1 TB.
  * VM1 e VM2 podem usar a primeira conta de armazenamento padrão
  * O VM4 pode utilizar a segunda conta de armazenamento padrão.
  * VM5 e VM6 precisam de uma conta de armazenamento premium, e ambos podem usar uma única conta.

    > [!NOTE]
    > Os IOPS sobre o armazenamento padrão e premium são calculados ao nível do VM e não ao nível do disco. Um VM padrão pode manusear até 500 IOPS por disco. Se o IOPS para um disco for superior a 500, precisa de armazenamento premium. Se o IOPS para um disco for superior a 500, mas o IOPS para o total de discos VM está dentro dos limites de suporte Azure VM, o planejador escolhe um VM padrão e não a série DS ou GS. (Os limites de VM Azure são o tamanho VM, número de discos, número de adaptadores, CPU e memória.) É necessário atualizar manualmente a célula de tamanho Azure de mapeamento com a série DS ou GS VM apropriada.


Depois de todas as informações terem sido inseridas, **selecione Enviar dados para a ferramenta de planeamento** para abrir o Planejador de Capacidade. As cargas de trabalho são destacadas para mostrar se são elegíveis para proteção.

### <a name="submit-data-in-capacity-planner"></a>Enviar dados no Capacity Planner
1. Quando abre a folha **de cálculo do Capacity Planner,** é povoada com base nas definições especificadas. A palavra "Carga de trabalho" aparece na célula **de origem infra-input** para mostrar que a entrada é a folha de qualificação de carga de **trabalho.**

2. Se quiser fazer alterações, tem de modificar a folha de qualificação de carga de **trabalho.** Em seguida, **selecione Enviar novamente dados para a ferramenta de planeamento.**

   ![Screenshot mostrando as entradas modificadas e as saídas resultantes na folha de cálculo do Provedor de Capacidade.](./media/site-recovery-capacity-planner/capacity-planner.png)

## <a name="next-steps"></a>Passos seguintes
[Aprenda a executar a](./hyper-v-deployment-planner-overview.md) ferramenta de planeamento de capacidades.
