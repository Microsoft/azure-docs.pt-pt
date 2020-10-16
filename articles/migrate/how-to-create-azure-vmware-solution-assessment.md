---
title: Criar uma avaliação AVS com avaliação do servidor Azure Migrate / Microsoft Docs
description: Descreve como criar uma avaliação AVS com a ferramenta de avaliação do servidor Azure Migrate
author: rashi-ms
ms.service: azure-migrate
ms.topic: article
ms.date: 06/26/2020
ms.author: rajosh
ms.openlocfilehash: 89dc962c73a6446afc79eec2a0fcfd5c0e32e84e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91576715"
---
# <a name="create-an-azure-vmware-solution-avs-assessment"></a>Criar uma avaliação da Solução VMware Azure (AVS)

Este artigo descreve como criar uma avaliação de Azure VMware Solution (AVS) para VMware VMware VMs no local com Azure Migrate: Avaliação do servidor.

[Azure Migrate](migrate-services-overview.md) ajuda-o a migrar para Azure. A Azure Migrate fornece um centro centralizado para acompanhar a descoberta, avaliação e migração de infraestruturas, aplicações e dados no local para Azure. O hub fornece ferramentas Azure para avaliação e migração, bem como ofertas de fornecedores de software independentes de terceiros (ISV).

## <a name="before-you-start"></a>Antes de começar

- Certifique-se de ter [criado](how-to-add-tool-first-time.md) um projeto Azure Migrate.
- Se já criou um projeto, certifique-se de que [adicionou](how-to-assess.md) a ferramenta Azure Migrate: Server Assessment.
- Para criar uma avaliação, é necessário configurar um aparelho Azure Migrate para [VMware,](how-to-set-up-appliance-vmware.md)que descobre as máquinas no local, e envia metadados e dados de desempenho para a Azure Migrate: Avaliação do Servidor. [Saiba mais](migrate-appliance.md).
- Também pode [importar os metadados do servidor](tutorial-assess-import.md) em formato de valores separados por vírgula (CSV).


## <a name="azure-vmware-solution-avs-assessment-overview"></a>Visão geral da avaliação da Solução VMware Azure (AVS)

Existem dois tipos de avaliações que pode criar usando Azure Migrate: Avaliação do servidor.

**Tipo de Avaliação** | **Detalhes**
--- | --- 
**VM do Azure** | Avaliações para migrar os seus servidores no local para máquinas virtuais do Azure. <br/><br/> Pode avaliar os seus [VMS VMware](how-to-set-up-appliance-vmware.md)no local, [VMs hiper-V](how-to-set-up-appliance-hyper-v.md)e [servidores físicos](how-to-set-up-appliance-physical.md) para migração para Azure utilizando este tipo de avaliação. [Saiba mais](concepts-assessment-calculation.md)
**Solução VMware no Azure (AVS)** | Avaliações para migrar os seus servidores no local para o [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Pode avaliar as [VMs VMware](how-to-set-up-appliance-vmware.md) no local para a migração para o Azure VMware Solution (AVS) com este tipo de avaliação.[Saiba mais](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> A avaliação da Solução VMware (AVS) da Azure está atualmente em pré-visualização e pode ser criada apenas para VMware VMs.


Existem dois tipos de critérios de dimensionamento que pode utilizar para criar avaliações da Solução VMware Azure (AVS):

**Avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Com base no desempenho** | Avaliações baseadas em dados de desempenho recolhidos de VMs no local. | **Tamanho recomendado do nó**: Com base em dados de utilização da CPU e da memória, juntamente com o tipo de nó, o tipo de armazenamento e a definição de FTT que seleciona para a avaliação.
**Como no local** | Avaliações baseadas no dimensionamento no local. | **Tamanho do nó recomendado**: Com base no tamanho VM no local, juntamente com o tipo de nó, tipo de armazenamento e definição FTT que seleciona para a avaliação.


## <a name="run-an-azure-vmware-solution-avs-assessment"></a>Executar uma avaliação da Solução VMware Azure (AVS)

Executar uma avaliação da Solução VMware Azure (AVS) da seguinte forma:

1. Reveja as [melhores práticas](best-practices-assessment.md) para criar avaliações.

2. No separador **Servidores,** em **Azure Migrate: Telha de Avaliação** do Servidor, clique em **Avaliar**.

    ![A screenshot mostra servidores Azure Migrate com Avaliação selecionados em ferramentas de avaliação.](./media/how-to-create-assessment/assess.png)

3. Nos **servidores avaliação**, selecione o tipo de avaliação como "Azure VMware Solution (AVS)", selecione a fonte de descoberta e especifique o nome de avaliação.

    ![Noções Básicas de Avaliação](./media/how-to-create-avs-assessment/assess-servers-avs.png)

4. Clique em **Ver tudo** para rever as propriedades de avaliação.

    ![Propriedades de avaliação AVS](./media/how-to-create-avs-assessment/avs-view-all.png)

5. Clique em **seguinte** para **Selecionar as máquinas a avaliar**. Em **Selecionar ou criar um grupo**, selecione **Criar Novo** e especifique um nome do grupo. Um grupo reúne uma ou mais VMs para avaliação.

6. No **Adicionar máquinas ao grupo,** selecione VMs para adicionar ao grupo.

7. Clique em **seguinte** para **Rever + criar avaliação** e reveja os detalhes da avaliação.

8. Clique **em Criar Avaliação** para criar o grupo e executar a avaliação.

    ![Criar uma avaliação do AVS](./media/how-to-create-avs-assessment/avs-assessment-create.png)

9. Após a criação da avaliação, veja-a em **Servidor** > **Azure Migrate: Avaliação do Servidor** > **Avaliações**.

10. Clique em **Exportar avaliação**, para transferi-la como um ficheiro do Excel.


## <a name="review-an-azure-vmware-solution-avs-assessment"></a>Reveja uma avaliação da Solução VMware Azure (AVS)

Uma avaliação da Solução Azure VMware (AVS) descreve:

- **Prontidão Azure VMware Solution (AVS):** Se os VMs no local são adequados para a migração para Azure VMware Solution (AVS).
- **Número de nós AVS**: Número estimado de nós AVS necessários para executar os VMs.
- **Utilização através de nós AVS**: CPU projetado, memória e utilização de armazenamento em todos os nós.
- **Estimativa mensal dos custos**: Os custos mensais estimados para todos os nós da Azure VMware Solution (AVS) que executam os VMs no local.


### <a name="view-an-assessment"></a>Ver uma avaliação

1. Nos **objetivos de migração**  >   **Servidores**, clique em **Avaliações** em **Azure Migrate: Avaliação do servidor**.

2. Em **Avaliações,** clique numa avaliação para abri-la.

    ![Resumo da avaliação do AVS](./media/how-to-create-avs-assessment/avs-assessment-summary.png)

### <a name="review-azure-vmware-solution-avs-readiness"></a>Rever prontidão Azure VMware Solution (AVS)

1. Em **prontidão Azure,** verifique se os VMs estão prontos para a migração para AVS.

2. Reveja o estado de VM:
    - **Pronto para AVS**: A máquina pode ser migrada como-é para Azure (AVS) sem alterações. Começará em AVS com suporte AVS completo.
    - **Pronto com condições**: O VM pode ter problemas de compatibilidade com a versão atual do vSphere, bem como exigir ferramentas e outras configurações de VMware e ou outras configurações antes de a funcionalidade completa do VM poder ser alcançada em AVS.
    - **Não está pronto para AVS**: O VM não começará em AVS. Por exemplo, se o VMware VMware VMware no local tiver um dispositivo externo ligado, como um cd-rom, a operação VMotion falhará (se utilizar vMware VMotion).
    - **Prontidão desconhecida**: A Azure Migrate não conseguiu determinar a prontidão da máquina devido a metadados insuficientes recolhidos do ambiente no local.

3. Reveja a ferramenta sugerida:
    - **VMware HCX ou Enterprise**: Para máquinas VMware, a solução VMWare Hybrid Cloud Extension (HCX) é a ferramenta de migração sugerida para migrar a sua carga de trabalho no local para a sua nuvem privada Azure VMware Solution (AVS). [Saiba Mais](../azure-vmware/tutorial-deploy-vmware-hcx.md).
    - **Desconhecida**: para as máquinas virtuais importadas através de um ficheiro CSV, a ferramenta de migração predefinida é desconhecida. Embora para máquinas VMware, é sugerido que utilize a solução VMware Hybrid Cloud Extension (HCX). 

4. Clique num estado **de prontidão AVS.** Pode ver detalhes de prontidão em VM e perfurar para ver detalhes de VM, incluindo configurações de computação, armazenamento e rede.



### <a name="review-cost-details"></a>Rever os detalhes dos custos

Esta visão mostra o custo estimado de funcionamento de VMs em Azure VMware Solution (AVS).

1. Reveja os custos totais mensais. Os custos são agregados para todos os VMs do grupo avaliado. 

    - As estimativas de custos baseiam-se no número de nós AVS necessários, tendo em conta os requisitos de recursos de todos os VMs no total.
    - Como o preço da Azure VMware Solution (AVS) é por nó, o custo total não tem custo de computação e distribuição de custos de armazenamento.
    - A estimativa de custos é para a execução dos VMs no local em AVS. A avaliação do servidor Azure Migrate não considera os custos do PaaS ou do SaaS.
    
2. Pode rever as estimativas mensais de custos de armazenamento. Esta visão mostra os custos de armazenamento agregados para o grupo avaliado, divididos sobre diferentes tipos de discos de armazenamento.

3. Pode perfurar para ver detalhes para VMs específicos.


### <a name="review-confidence-rating"></a>Rever a classificação de confiança

Quando faz avaliações baseadas no desempenho, é atribuída uma classificação de confiança à avaliação.

![Classificação de confiança](./media/how-to-create-assessment/confidence-rating.png)

- É atribuída uma classificação de 1 estrela (mais baixa) a 5 estrelas (mais alta).
- A classificação de confiança ajuda-o a estimar a fiabilidade das recomendações de tamanho fornecidas pela avaliação.
- A classificação de confiança baseia-se na disponibilidade de pontos de dados necessários para calcular a avaliação.
- Para dimensionamento baseado no desempenho, as avaliações de AVS na Avaliação do Servidor precisam dos dados de utilização da memória CPU e VM. Os seguintes dados de desempenho são recolhidos, mas não utilizados em recomendações de dimensionamento para avaliações avs:
  - O disco IOPS e os dados de produção de cada disco ligado ao VM.
  - A rede I/O para lidar com o tamanho baseado no desempenho de cada adaptador de rede ligado a um VM.

As classificações de confiança para uma avaliação são as seguintes.

**Disponibilidade de pontos de dados** | **Classificação de confiança**
--- | ---
0%-20% | 1 Estrela
21%-40% | 2 Estrelas
41%-60% | 3 Estrelas
61%-80% | 4 Estrelas
81%-100% | 5 Estrelas

[Saiba mais](concepts-azure-vmware-solution-assessment-calculation.md) sobre dados de desempenho 


## <a name="next-steps"></a>Passos seguintes

- Aprenda a usar [o mapeamento de dependência](how-to-create-group-machine-dependencies.md) para criar grupos de alta confiança.
- [Saiba mais](concepts-azure-vmware-solution-assessment-calculation.md) sobre como as avaliações do AVS são calculadas.
