---
title: Criar uma avaliação de VM Azure com avaliação do servidor Azure Migrate Microsoft Docs
description: Descreve como criar uma avaliação de VM Azure com a ferramenta de avaliação do servidor Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: c4132ca675af136d7fd50b8ddd02277919a5ed28
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361097"
---
# <a name="create-an-azure-vm-assessment"></a>Criar uma avaliação de VM do Azure

Este artigo descreve como criar uma avaliação VM Azure para VMware VMs ou VMs hiper-V com Azure Migrate: Avaliação do Servidor.

[Azure Migrate](migrate-services-overview.md) ajuda-o a migrar para Azure. A Azure Migrate fornece um centro centralizado para acompanhar a descoberta, avaliação e migração de infraestruturas, aplicações e dados no local para Azure. O hub fornece ferramentas Azure para avaliação e migração, bem como ofertas de fornecedores de software independentes de terceiros (ISV). 

## <a name="before-you-start"></a>Antes de começar

- Certifique-se de ter [criado](how-to-add-tool-first-time.md) um projeto Azure Migrate.
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

1. Reveja as [melhores práticas](best-practices-assessment.md) para criar avaliações.
2. No separador **Servidores,** em **Azure Migrate: Telha de Avaliação** do Servidor, clique em **Avaliar**.

    ![A screenshot mostra servidores Azure Migrate com Avaliação selecionados em ferramentas de avaliação.](./media/how-to-create-assessment/assess.png)

3. Nos **servidores avaliação**, selecione o tipo de avaliação como "Azure VM", selecione a fonte de descoberta e especifique o nome de avaliação.

    ![Noções Básicas de Avaliação](./media/how-to-create-assessment/assess-servers-azurevm.png)

4. Clique em **Ver tudo** para rever as propriedades de avaliação.

    ![Propriedades da avaliação](./media/how-to-create-assessment//view-all.png)

5. Clique em **seguinte** para **Selecionar as máquinas a avaliar**. Em **Selecionar ou criar um grupo**, selecione **Criar Novo** e especifique um nome do grupo. Um grupo reúne uma ou mais VMs para avaliação.
6. No **Adicionar máquinas ao grupo,** selecione VMs para adicionar ao grupo.
7. Clique em **seguinte** para **Rever + criar avaliação** e reveja os detalhes da avaliação.
8. Clique **em Criar Avaliação** para criar o grupo e executar a avaliação.

    ![Criar uma avaliação](./media/how-to-create-assessment//assessment-create.png)

9. Após a criação da avaliação, veja-a em **Servidor** > **Azure Migrate: Avaliação do Servidor** > **Avaliações**.
10. Clique em **Exportar avaliação**, para transferi-la como um ficheiro do Excel.



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
