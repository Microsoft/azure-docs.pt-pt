---
title: Crie uma avaliação de VM Azure com a avaliação do servidor Azure Migrate | Microsoft Docs
description: Descreve como criar uma avaliação de VM Azure com a ferramenta de avaliação do servidor Azure Migrate
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/15/2019
ms.openlocfilehash: 178bdca78c6f011c607de8e1f5d5eabcdbaab7d4
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567693"
---
# <a name="create-an-azure-vm-assessment"></a>Criar uma avaliação de VM do Azure

Este artigo descreve como criar uma avaliação VM Azure para VMware VMs ou VMs hiper-V com Azure Migrate: Avaliação do Servidor.

[Azure Migrate](migrate-services-overview.md) ajuda-o a migrar para Azure. A Azure Migrate fornece um centro centralizado para acompanhar a descoberta, avaliação e migração de infraestruturas, aplicações e dados no local para Azure. O hub fornece ferramentas Azure para avaliação e migração, bem como ofertas de fornecedores de software independentes de terceiros (ISV). 

## <a name="before-you-start"></a>Antes de começar

- Certifique-se de ter [criado](./create-manage-projects.md) um projeto Azure Migrate.
- Se já criou um projeto, certifique-se de que [adicionou](how-to-assess.md) a ferramenta Azure Migrate: Server Assessment.
- Para criar uma avaliação, é necessário configurar um aparelho Azure Migrate para [VMware](how-to-set-up-appliance-vmware.md) ou [Hyper-V](how-to-set-up-appliance-hyper-v.md). O aparelho descobre máquinas no local e envia metadados e dados de desempenho para Azure Migrate: Avaliação do servidor. [Saiba mais](migrate-appliance.md).


## <a name="azure-vm-assessment-overview"></a>Visão geral da avaliação do VM de Azure
Existem dois tipos de critérios de dimensionamento que pode utilizar para criar uma avaliação de VM Azure usando Azure Migrate: Avaliação do Servidor.

**Avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Com base no desempenho** | Avaliações com base em dados de desempenho recolhidos | **Tamanho VM recomendado**: Com base em dados de CPU e utilização da memória.<br/><br/> **Tipo de disco recomendado (disco gerido standard ou premium)**: Baseado no IOPS e na produção dos discos no local.
**Como no local** | Avaliações baseadas no dimensionamento no local. | **Tamanho VM recomendado**: Com base no tamanho VM no local<br/><br> **Tipo de disco recomendado**: Com base na definição do tipo de armazenamento que seleciona para a avaliação.

[Saiba mais](concepts-assessment-calculation.md) sobre avaliações.

## <a name="run-an-assessment"></a>Executar uma avaliação

E executar uma avaliação da seguinte forma:

1. Na página de **Servidores** > **servidores Windows e Linux,** clique em **Avaliar e migrar servidores**.

   ![Localização do botão de avaliar e migrar servidores](./media/tutorial-assess-vmware-azure-vm/assess.png)

2. Em **Azure Migrate: Avaliação do servidor,** clique em **Avaliar**.

    ![Localização do botão Avaliar](./media/tutorial-assess-vmware-azure-vm/assess-servers.png)

3. No tipo de avaliação **de servidores,**  >  selecione **Azure VM**.
4. Na **fonte discovery:**

    - Se descobrir máquinas que utilizem o aparelho, selecione **Máquinas descobertas a partir do aparelho Azure Migrate**.
    - Se descobrir máquinas utilizando um ficheiro CSV importado, selecione **máquinas importadas.** 
    
1. Clique **em Editar** para rever as propriedades de avaliação.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vm/assessment-name.png" alt-text="Localização do botão Editar para rever propriedades de avaliação":::

1. Em **Propriedades de Avaliação**  >  **Propriedades-Alvo**:
    - Na **localização do alvo,** especifique a região de Azure para a qual pretende migrar.
        - As recomendações de tamanho e custo baseiam-se na localização que especifica. Assim que alterar a localização do alvo por defeito, será solicitado que especifique **As Instâncias Reservadas** e **as séries VM**.
        - No Governo de Azure, pode visar avaliações [nestas regiões](migrate-support-matrix.md#supported-geographies-azure-government)
    - No **tipo de armazenamento,**
        - Se pretender utilizar dados baseados no desempenho na avaliação, selecione **Automatic** for Azure Migrate para recomendar um tipo de armazenamento, baseado no disco IOPS e na produção.
        - Em alternativa, selecione o tipo de armazenamento que pretende utilizar para VM quando o migrar.
    - Em **Instâncias Reservadas**, especifique se pretende utilizar instâncias de reserva para o VM quando o migrar.
        - Se selecionar para utilizar uma instância reservada, não pode especificar '**Desconto (%)** ou **uptime VM**. 
        - [Saiba mais](https://aka.ms/azurereservedinstances).
 1. Em **tamanho VM:**
     - No **critério Sizing,** selecione se pretende basear a avaliação em dados/metadados de configuração da máquina ou em dados baseados no desempenho. Se utilizar dados de desempenho:
        - No **histórico de desempenho**, indique a duração dos dados em que pretende basear a avaliação
        - Na **utilização do Percentil,** especifique o valor percentil que pretende utilizar para a amostra de desempenho. 
    - Na **Série VM**, especifique a série Azure VM que pretende considerar.
        - Se estiver a utilizar uma avaliação baseada no desempenho, a Azure Migrate sugere um valor para si.
        - Ajuste as definições conforme necessário. Por exemplo, se não tiver um ambiente de produção que precise de VMs da série A em Azure, pode excluir a série A da lista de séries.
    - No **fator Comfort,** indique o tampão que pretende utilizar durante a avaliação. Isto explica questões como o uso sazonal, o histórico de desempenho curto e, provavelmente, o aumento do uso futuro. Por exemplo, se utilizar um fator de conforto de dois:
    
        **Componente** | **Utilização eficaz** | **Adicionar fator de conforto (2.0)**
        --- | --- | ---
        Núcleos | 2  | 4
        Memória | 8 GB | 16 GB
   
1. Na **fixação de preços:**
    - In **Offer**, especifique a [oferta Azure](https://azure.microsoft.com/support/legal/offer-details/) se estiver inscrito. A Avaliação do Servidor estima o custo dessa oferta.
    - Em **Moeda,** selecione a moeda de faturação para a sua conta.
    - Em **Desconto (%)**, adicione quaisquer descontos específicos de subscrição que receba em cima da oferta Azure. A predefinição é 0%.
    - No **VM Uptime,** especifique a duração (dias por mês/hora por dia) que os VMs irão executar.
        - Isto é útil para VMs Azure que não serão executados continuamente.
        - As estimativas de custos baseiam-se na duração especificada.
        - O padrão é de 31 dias por mês/24 horas por dia.
    - Na **Subscrição da EA**, especifique se deve ter em conta um desconto de subscrição do Enterprise Agreement (EA) para a estimativa de custos. 
    - No **Azure Hybrid Benefit**, especifique se já tem uma licença do Windows Server. Se o fizer e estiver coberto com uma garantia de software ativa das subscrições do Servidor do Windows, pode candidatar-se ao [Benefício Híbrido Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/) quando levar licenças ao Azure.

1. Clique **em Guardar** se escoda alterações.

    ![Propriedades da avaliação](./media/tutorial-assess-vmware-azure-vm/assessment-properties.png)

1. Em **Avaliar servidores** > clique em **Seguinte**.

1. Nas **máquinas Select para avaliar o** nome de  >  **avaliação** > especificar um nome para a avaliação. 

1. Em **Select ou criar um grupo** > selecione Create **New** e especifique um nome de grupo. 

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vm/assess-group.png" alt-text="Adicionar VMs a um grupo":::

1. Selecione o aparelho e selecione os VMs que pretende adicionar ao grupo. Em seguida, clique em **Seguinte**.


1. Em **Review + criar avaliação,** rever os detalhes da avaliação e clicar em Criar **Avaliação** para criar o grupo e executar a avaliação.

1. Após a criação da avaliação, veja-a em **Servidor** > **Azure Migrate: Avaliação do Servidor** > **Avaliações**.

1. Clique em **Exportar avaliação**, para transferi-la como um ficheiro do Excel.
    > [!NOTE]
    > Para avaliações baseadas no desempenho, recomendamos que espere pelo menos um dia após iniciar a descoberta antes de criar uma avaliação. Isto dá tempo para recolher dados de desempenho com maior confiança. Idealmente, depois de começar a ser descoberto, aguarde a duração de desempenho que especifica (dia/semana/mês) para uma classificação de alta confiança.


## <a name="review-an-azure-vm-assessment"></a>Rever uma avaliação de VM do Azure

Uma avaliação de VM do Azure descreve:

- **Prontidão azul**: Se os VM são adequados para a migração para Azure.
- **Estimativa mensal dos custos**: Os custos estimados mensalmente de cálculo e armazenamento para a execução dos VMs em Azure.
- **Estimativa do custo de armazenamento mensal**: Custos estimados do armazenamento em disco após a migração.

### <a name="view-an-azure-vm-assessment"></a>Ver uma avaliação de VM Azure

1. Nos **objetivos de migração**  >   **Servidores**, clique em **Avaliações** em **Azure Migrate: Avaliação do servidor**.
2. Em **Avaliações,** clique numa avaliação para abri-la.

    ![Resumo da avaliação](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>Rever a preparação para o Azure

1. Em **Azure prontidão**, verifique se os VMs estão prontos para a migração para Azure.
2. Reveja o estado de VM:
    - **Preparada para o Azure**: O Azure Migrate recomenda um tamanho de VM e estimativas de custo para VMs na avaliação.
    - **Pronto com condições**: Apresenta problemas e sugeriu remediação.
    - **Não está pronto para o Azure**: Apresenta problemas e sugeriu a reparação.
    - **Prontidão desconhecida**: Usado quando a Azure Migrate não consegue avaliar a prontidão, devido a problemas de disponibilidade de dados.

3. Clique num estado **de prontidão Azure.** Pode ver detalhes de prontidão em VM e perfurar para ver detalhes de VM, incluindo configurações de computação, armazenamento e rede.



### <a name="review-cost-details"></a>Rever os detalhes dos custos

Esta vista mostra o custo estimado de computação e armazenamento da execução das VMs no Azure.

1. Rever os custos mensais de computação e armazenamento. Os custos são agregados para todos os VMs do grupo avaliado.

    - As estimativas de custo baseiam-se nas recomendações de tamanho para uma máquina e os respetivos discos e propriedades.
    - Os custos mensais estimados para computação e armazenamento são mostrados.
    - A estimativa de custos é para executar os VMs no local como IaaS VMs. A avaliação do servidor Azure Migrate não considera os custos do PaaS ou do SaaS.

2. Pode rever as estimativas mensais de custos de armazenamento. Esta visão mostra os custos de armazenamento agregados para o grupo avaliado, divididos sobre diferentes tipos de discos de armazenamento.
3. Pode perfurar para ver detalhes para VMs específicos.


### <a name="review-confidence-rating"></a>Rever a classificação de confiança

Quando faz avaliações baseadas no desempenho, é atribuída uma classificação de confiança à avaliação.

![Classificação de confiança](./media/how-to-create-assessment/confidence-rating.png)

- É atribuída uma classificação de 1 estrela (mais baixa) a 5 estrelas (mais alta).
- A classificação de confiança ajuda-o a estimar a fiabilidade das recomendações de tamanho fornecidas pela avaliação.
- A classificação de confiança baseia-se na disponibilidade de pontos de dados necessários para calcular a avaliação.

As classificações de confiança para uma avaliação são as seguintes.

**Disponibilidade de pontos de dados** | **Classificação de confiança**
--- | ---
0%-20% | 1 Estrela
21%-40% | 2 Estrelas
41%-60% | 3 Estrelas
61%-80% | 4 Estrelas
81%-100% | 5 Estrelas




## <a name="next-steps"></a>Passos seguintes

- Aprenda a usar [o mapeamento de dependência](how-to-create-group-machine-dependencies.md) para criar grupos de alta confiança.
- [Saiba mais](concepts-assessment-calculation.md) sobre como são calculadas as avaliações.