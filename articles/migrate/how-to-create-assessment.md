---
title: Crie uma avaliação com a Avaliação do Servidor Migratório Azure [ Microsoft Docs
description: Descreve como criar uma avaliação com a ferramenta de avaliação do servidor de migração Azure
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: cffde2a677650387dffd19733e082ff7002ccb55
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "68229105"
---
# <a name="create-an-assessment"></a>Criar uma avaliação

Este artigo descreve como criar uma avaliação para VMware VMs no local ou VMs Hiper-V com Azure Migrate: Server Assessment.

[A Azure Migrate](migrate-services-overview.md) ajuda-o a migrar para Azure. A Azure Migrate fornece um centro centralizado para acompanhar a descoberta, avaliação e migração de infraestruturas, aplicações e dados no local para o Azure. O hub fornece ferramentas Azure para avaliação e migração, bem como ofertas de fornecedores de software independentes de terceiros (ISV). 

## <a name="before-you-start"></a>Antes de começar

- Certifique-se de [ter criado](how-to-add-tool-first-time.md) um projeto Azure Migrate.
- Se já criou um projeto, certifique-se de ter [adicionado](how-to-assess.md) a ferramenta de avaliação do servidor Azure Migrate.
- Para criar uma avaliação, é necessário configurar um aparelho Azure Migrate para [VMware](how-to-set-up-appliance-vmware.md) ou [Hyper-V](how-to-set-up-appliance-hyper-v.md). O aparelho descobre máquinas no local e envia dados de metadados e desempenho para o Azure Migrate: Server Assessment. [Saiba mais](migrate-appliance.md).


## <a name="assessment-overview"></a>Descrição geral da avaliação
Existem dois tipos de avaliações que pode criar usando o Azure Migrate: Server Assessment.

**Avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Baseado no desempenho** | Avaliações baseadas em dados de desempenho recolhidos | **Tamanho VM recomendado**: Baseado em CPU e dados de utilização da memória.<br/><br/> **Tipo de disco recomendado (disco gerido padrão ou premium)**: Baseado no IOPS e na entrada dos discos no local.
**Como no local** | Avaliações baseadas no dimensionamento no local. | **Tamanho VM recomendado**: Baseado no tamanho vm no local<br/><br> **Tipo**de disco recomendado : Com base na definição do tipo de armazenamento selecionada para a avaliação.

[Saiba mais](concepts-assessment-calculation.md) sobre avaliações.

## <a name="run-an-assessment"></a>Executar uma avaliação

Eexecutar uma avaliação da seguinte forma:

1. Reveja as [melhores práticas](best-practices-assessment.md) para a criação de avaliações.
2. No separador **Servidores,** em **Azure Migrate: Server Assessment** tile, clique em **Avaliar**.

    ![Avaliar](./media/how-to-create-assessment/assess.png)

2. Em **avaliar os servidores,** especifique um nome para a avaliação.
3. Clique em **Ver tudo** para rever as propriedades de avaliação.

    ![Propriedades de avaliação](./media/how-to-create-assessment//view-all.png)

3. Em **Selecionar ou criar um grupo,** selecione Criar **Novo**e especifique um nome de grupo. Um grupo reúne um ou mais VMs para avaliação.
4. Em **Adicionar máquinas ao grupo,** selecione VMs para adicionar ao grupo.
5. Clique em **Criar Avaliação** para criar o grupo e executar a avaliação.

    ![Criar uma avaliação](./media/how-to-create-assessment//assessment-create.png)

6. Após a criação da avaliação, veja-a em **Servers** > **Azure Migrate:** > **Avaliações**de Avaliação do Servidor .
7. Clique em **Exportar avaliação**, para transferi-la como um ficheiro do Excel.



## <a name="review-an-assessment"></a>Rever uma avaliação

Uma avaliação descreve:

- **Prontidão azure**: Se os VMs são adequados para migração para Azure.
- **Estimativa mensal de custos**: Os custos estimados mensais de cálculo e armazenamento para a execução dos VMs em Azure.
- **Estimativa mensal**do custo do armazenamento : Custos estimados para armazenamento de disco após migração.

### <a name="view-an-assessment"></a>Ver uma avaliação

1. Nos **objetivos** >  de migração**Servidores**, clique em **Avaliações** em **Migração Azure: Avaliação do Servidor**.
2. Em **Avaliações,** clique numa avaliação para abri-la.

    ![Resumo da avaliação](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>Rever a prontidão do Azure

1. Na **prontidão de Azure,** verifique se os VMs estão prontos para a migração para Azure.
2. Reveja o estado vm:
    - **Pronto para Azure**: A Azure Migrate recomenda uma estimativa de tamanho VM e custos para os VMs na avaliação.
    - **Pronto com condições**: Mostra problemas e sugeriu reparação.
    - **Não está pronto para o Azure**: Mostra questões e sugeriu reparação.
    - **Prontidão desconhecida**: Usado quando o Azure Migrate não consegue avaliar a prontidão, devido a problemas de disponibilidade de dados.

2. Clique num estado de **prontidão Azure.** Pode ver detalhes de prontidão vM e perfurar para ver detalhes vm, incluindo definições de computação, armazenamento e rede.



### <a name="review-cost-details"></a>Analisar detalhes de custos

Esta visão mostra o cálculo estimado e o custo de armazenamento de vMs de funcionamento em Azure.

1. Reveja os custos mensais de cálculo e armazenamento. Os custos são agregados para todos os VMs do grupo avaliado.

    - As estimativas de custos baseiam-se nas recomendações de tamanho para uma máquina, e nos seus discos e propriedades.
    - Os custos mensais estimados para a computação e armazenamento são apresentados.
    - A estimativa de custos é para executar os VMs no local como VMs IaaS. A Avaliação do Servidor Migratório Azure não considera os custos do PaaS ou do SaaS.

2. Pode rever as estimativas mensais de custos de armazenamento. Esta vista mostra os custos de armazenamento agregados para o grupo avaliado, divididos sobre diferentes tipos de discos de armazenamento.
3. Você pode perfurar para ver detalhes para VMs específicos.


### <a name="review-confidence-rating"></a>Rever a classificação de confiança

Quando executa avaliações baseadas no desempenho, uma classificação de confiança é atribuída à avaliação.

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

- Aprenda a usar [o mapeamento](how-to-create-group-machine-dependencies.md) da dependência para criar grupos de alta confiança.
- [Saiba mais](concepts-assessment-calculation.md) sobre como são calculadas as avaliações.
