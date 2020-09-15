---
title: Avaliar os casos da AWS para a migração para Azure com a avaliação do servidor Azure Migrate
description: Descreve como avaliar os casos de AWS para migração para Azure usando a Avaliação do Servidor Azure Migrate.
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: 14928c8a3249cca172ad088f290b54a22a125ae7
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90108901"
---
# <a name="tutorial-assess-aws-instances-for-migration-to-azure"></a>Tutorial: Avaliar os casos da AWS para a migração para Azure

Como parte da sua viagem de migração para Azure, você avalia as suas cargas de trabalho no local para medir a prontidão na nuvem, identificar riscos e estimar custos e complexidade.

Este artigo mostra-lhe como avaliar os casos da Amazon Web Services (AWS) para migração para Azure, utilizando a ferramenta Azure Migrate: Server Assessment.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
- Executar uma avaliação com base em metadados de máquina e informações de configuração.
- Executar uma avaliação com base em dados de desempenho.

> [!NOTE]
> Os tutoriais mostram o caminho mais rápido para experimentar um cenário e usam opções padrão sempre que possível. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

- Antes de seguir os passos neste tutorial, complete o primeiro tutorial desta série para [descobrir o seu inventário no local.](tutorial-discover-aws.md) 
- Certifique-se de que as instâncias AWS não estão a executar o Windows Server 2003 ou o SUSE Linux. A avaliação não é apoiada para estas máquinas.


## <a name="decide-which-assessment-to-run"></a>Decidir qual a avaliação a executar


Decida se pretende executar uma avaliação utilizando critérios de dimensionamento baseados em dados de configuração de máquinas/metadados recolhidos como-está no local, ou em dados de desempenho dinâmicos.

**Avaliação** | **Detalhes** | **Recomendação**
--- | --- | ---
**As-is in-ins** | Avaliar com base em dados de configuração da máquina/metadados.  | O tamanho Azure VM recomendado baseia-se no tamanho VM no local.<br/><br> O tipo de disco Azure recomendado baseia-se no que seleciona na definição do tipo de armazenamento na avaliação.
**Baseado no desempenho** | Avaliar com base em dados de desempenho dinâmicos recolhidos. | O tamanho Azure VM recomendado baseia-se em dados de CPU e utilização da memória.<br/><br/> O tipo de disco recomendado baseia-se no IOPS e na produção dos discos no local.

## <a name="run-an-assessment"></a>Executar uma avaliação

E executar uma avaliação da seguinte forma:

1. Na página de **Servidores** > **servidores Windows e Linux,** clique em **Avaliar e migrar servidores**.

   ![Localização do botão de avaliar e migrar servidores](./media/tutorial-assess-aws/assess.png)

2. In **Azure Migrate: Avaliação do servidor, clique em **Avaliar**.

    ![Localização do botão Avaliar](./media/tutorial-assess-aws/assess-servers.png)

3. No tipo de avaliação **de servidores,**  >  **Assessment type**selecione **Azure VM**.
4. Na **fonte discovery:**

    - Se descobrir máquinas que utilizem o aparelho, selecione **Máquinas descobertas a partir do aparelho Azure Migrate**.
    - Se descobrir máquinas utilizando um ficheiro CSV importado, selecione **máquinas importadas.** 
5. Especifique um nome para a avaliação. 
6. Clique em **Ver tudo** para rever as propriedades de avaliação.

    ![Localização do botão Ver todo o botão para rever propriedades de avaliação](./media/tutorial-assess-aws/assessment-name.png)

7. Em **Propriedades de Avaliação**  >  **Propriedades-Alvo**:
    - Na **localização do alvo,** especifique a região de Azure para a qual pretende migrar.
        - As recomendações de tamanho e custo baseiam-se na localização que especifica.
        - No Governo de Azure, pode visar avaliações [nestas regiões](migrate-support-matrix.md#supported-geographies-azure-government)
    - No **tipo de armazenamento,**
        - Se pretender utilizar dados baseados no desempenho na avaliação, selecione **Automatic** for Azure Migrate para recomendar um tipo de armazenamento, baseado no disco IOPS e na produção.
        - Em alternativa, selecione o tipo de armazenamento que pretende utilizar para VM quando o migrar.
    - Em **Instâncias Reservadas**, especifique se pretende utilizar instâncias de reserva para o VM quando o migrar.
        - Se selecionar para utilizar uma instância reservada, não pode especificar '**Desconto (%)** ou **uptime VM**. 
        - [Saiba mais](https://aka.ms/azurereservedinstances).
8. Em **tamanho VM:**
 
    - No **critério Sizing,** selecione se pretende basear a avaliação em dados/metadados de configuração da máquina ou em dados baseados no desempenho. Se utilizar dados de desempenho:
        - No **histórico de desempenho**, indique a duração dos dados em que pretende basear a avaliação
        - Na **utilização do Percentil,** especifique o valor percentil que pretende utilizar para a amostra de desempenho. 
    - Na **Série VM**, especifique a série Azure VM que pretende considerar.
        - Se estiver a utilizar uma avaliação baseada no desempenho, a Azure Migrate sugere um valor para si.
        - Ajuste as definições conforme necessário. Por exemplo, se não tiver um ambiente de produção que precise de VMs da série A em Azure, pode excluir a série A da lista de séries.
    - No **fator Comfort,** indique o tampão que pretende utilizar durante a avaliação. Isto explica questões como o uso sazonal, o histórico de desempenho curto e, provavelmente, o aumento do uso futuro. Por exemplo, se utilizar um fator de conforto de dois: **Detalhes**  |  **Utilização**Adicionar fator de  |  **conforto (2.0)** Ler IOPS / 100 / 200 Write IOPS / 100 / 200 Read throughput / 100 Mbps [ 200 Mbps Write / 100 Mbps / 200 Mbps / 200 Mbps / 200 Mbps / 200 Mbps / 200 Mbps / 200 Mbps / 200 Mbps / 200 Mbps / 200 Mbps / 200 Mbps / 200 Mbps / 200 Mbps / 200 Mbps / 200 Mbps / 200 Mbps / 200 Mbps / 200 Mbps / 200 Mbps / 200 Mbps / 200 Mbps
   
9. Na **fixação de preços:**
    - In **Offer**, especifique a [oferta Azure](https://azure.microsoft.com/support/legal/offer-details/) se estiver inscrito. A Avaliação do Servidor estima o custo dessa oferta.
    - Em **Moeda,** selecione a moeda de faturação para a sua conta.
    - Em **Desconto (%)**, adicione quaisquer descontos específicos de subscrição que receba em cima da oferta Azure. A predefinição é 0%.
    - No **VM Uptime,** especifique a duração (dias por mês/hora por dia) que os VMs irão executar.
        - Isto é útil para VMs Azure que não serão executados continuamente.
        - As estimativas de custos baseiam-se na duração especificada.
        - O padrão é de 31 dias por mês/24 horas por dia.

    - Na **Subscrição da EA**, especifique se deve ter em conta um desconto de subscrição do Enterprise Agreement (EA) para a estimativa de custos. 
    - No **Azure Hybrid Benefit**, especifique se já tem uma licença do Windows Server. Se o fizer e estiver coberto com uma garantia de software ativa das subscrições do Servidor do Windows, pode candidatar-se ao [Benefício Híbrido Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/) quando levar licenças ao Azure.

10. Clique **em Guardar** se escoda alterações.

    ![Propriedades de avaliação](./media/tutorial-assess-aws/assessment-properties.png)

11. In **Assess Servers**, clique **em Seguinte**.
12. Nas **máquinas Select para avaliar,** selecione **Create New**e especifique um nome de grupo. 
13. Selecione o aparelho e selecione os VMs que pretende adicionar ao grupo. Em seguida, clique em **Seguinte**.
14. Em **Review + criar avaliação, rever os detalhes da avaliação e clicar em **Criar Avaliação** para criar o grupo e executar a avaliação.


    > [!NOTE]
    > Para avaliações baseadas no desempenho, recomendamos que espere pelo menos um dia após iniciar a descoberta antes de criar uma avaliação. Isto dá tempo para recolher dados de desempenho com maior confiança. Idealmente, depois de começar a ser descoberto, aguarde a duração de desempenho que especifica (dia/semana/mês) para uma classificação de alta confiança.

## <a name="review-an-assessment"></a>Rever uma avaliação

Uma avaliação descreve:

- **Prontidão azul**: Se os VM são adequados para a migração para Azure.
- **Estimativa mensal dos custos**: Os custos estimados mensalmente de cálculo e armazenamento para a execução dos VMs em Azure.
- **Estimativa mensal dos custos de armazenamento**: Custos estimados para o armazenamento do disco após a migração.

Para visualizar uma avaliação:

1. Nos **servidores**  >  **Azure Migrate: Avaliação do servidor,** clique no número ao lado **das Avaliações**.
2. Em **Avaliações,** selecione uma avaliação para abri-la. Como exemplo (estimativas e custos, por exemplo: 

    ![Resumo da avaliação](./media/tutorial-assess-aws/assessment-summary.png)

3. Reveja o resumo da avaliação. Também pode editar as propriedades de avaliação ou recalcular a avaliação.
 
 
### <a name="review-readiness"></a>Revisão de prontidão

1. Clique **em Azure prontidão**.
2. Em **prontidão Azure**, reveja o estado de VM:
    - **Pronto para Azure**: Usado quando a Azure Migrate recomenda uma estimativa de tamanho e custo de VM, para VMs na avaliação.
    - **Pronto com condições**: Apresenta problemas e sugeriu remediação.
    - **Não está pronto para o Azure**: Apresenta problemas e sugeriu a reparação.
    - **Prontidão desconhecida**: Usado quando a Azure Migrate não consegue avaliar a prontidão, devido a problemas de disponibilidade de dados.

3. Selecione um estado **de prontidão Azure.** Pode ver detalhes de prontidão em VM. Também pode perfurar para ver detalhes do VM, incluindo configurações de computação, armazenamento e rede.

### <a name="review-cost-estimates"></a>Rever as estimativas de custos

O resumo da avaliação mostra o custo estimado do cálculo e armazenamento dos VM em funcionamento em Azure. 

1. Reveja os custos totais mensais. Os custos são agregados para todos os VMs do grupo avaliado.

    - As estimativas de custos baseiam-se nas recomendações de tamanho para uma máquina, nos seus discos e nas suas propriedades.
    - São apresentados custos mensais estimados para o cálculo e armazenamento.
    - A estimativa de custos é para a execução dos VMs no local em VMs Azure. A estimativa não considera os custos do PaaS ou do SaaS.

2. Reveja os custos de armazenamento mensais. A vista mostra os custos de armazenamento agregados para o grupo avaliado, divididos sobre diferentes tipos de discos de armazenamento. 
3. Você pode perfurar para ver detalhes de custos para VMs específicos.

### <a name="review-confidence-rating"></a>Rever a classificação de confiança

A Avaliação do Servidor atribui uma classificação de confiança a avaliações baseadas no desempenho. A classificação é de uma estrela (mais baixa) para cinco estrelas (mais alta).

![Classificação de confiança](./media/tutorial-assess-aws/confidence-rating.png)

A classificação de confiança ajuda-o a estimar a fiabilidade das recomendações de tamanho na avaliação. A classificação baseia-se na disponibilidade de pontos de dados necessários para calcular a avaliação.

> [!NOTE]
> As classificações de confiança não são atribuídas se criar uma avaliação com base num ficheiro CSV.


As classificações de confiança são as seguintes.

**Disponibilidade de pontos de dados** | **Classificação de confiança**
--- | ---
0%-20% | 1 estrela
21%-40% | 2 estrelas
41%-60% | 3 estrelas
61%-80% | 4 estrelas
81%-100% | 5 estrelas

[Saiba mais](concepts-assessment-calculation.md#confidence-ratings-performance-based) sobre as classificações de confiança.

## <a name="next-steps"></a>Passos seguintes

- Encontre dependências de máquinas utilizando [o mapeamento de dependência.](concepts-dependency-visualization.md)
- Configurar o mapeamento de dependência [baseado em agente.](how-to-create-group-machine-dependencies.md)
