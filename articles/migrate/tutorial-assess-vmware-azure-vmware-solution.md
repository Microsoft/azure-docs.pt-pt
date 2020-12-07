---
title: Avaliar VMware VMs para migração para Azure VMware Solution (AVS) com Azure Migrate
description: Saiba como avaliar VMware VMs para migração para AVS com avaliação do servidor Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: f6d3c6f77b062939a88e7277cb7f0ab6ecff9fcb
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753081"
---
# <a name="tutorial-assess-vmware-vms-for-migration-to-avs"></a>Tutorial: Avaliar VMware VMs para migração para AVS

Como parte da sua viagem de migração para Azure, você avalia as suas cargas de trabalho no local para medir a prontidão na nuvem, identificar riscos e estimar custos e complexidade.

Este artigo mostra-lhe como avaliar máquinas virtuais VMware (VMs) descobertas para migração para Azure VMware Solution (AVS), utilizando a ferramenta Azure Migrate: Server Assessment. O AVS é um serviço gerido que lhe permite executar a plataforma VMware em Azure.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
- Executar uma avaliação com base em metadados de máquina e informações de configuração.
- Executar uma avaliação com base em dados de desempenho.

> [!NOTE]
> Os tutoriais mostram o caminho mais rápido para experimentar um cenário e usam opções padrão sempre que possível. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.



## <a name="prerequisites"></a>Pré-requisitos

Antes de seguir este tutorial para avaliar as suas máquinas de migração para o AVS, certifique-se de ter descoberto as máquinas que pretende avaliar:

- Para descobrir máquinas que utilizem o aparelho Azure Migrate, [siga este tutorial](tutorial-discover-vmware.md). 
- Para descobrir máquinas que utilizem um ficheiro CSV importado, [siga este tutorial.](tutorial-discover-import.md)


## <a name="decide-which-assessment-to-run"></a>Decidir qual a avaliação a executar


Decida se pretende executar uma avaliação utilizando critérios de dimensionamento baseados em dados de configuração de máquinas/metadados recolhidos como-está no local, ou em dados de desempenho dinâmicos.

**Avaliação** | **Detalhes** | **Recomendação**
--- | --- | ---
**Como está no local** | Avaliar com base em dados de configuração da máquina/metadados.  | O tamanho recomendado do nó em AVS baseia-se no tamanho VM no local, juntamente com as definições que especifica na avaliação para o tipo de nó, tipo de armazenamento e definição de falha de toleração.
**Com base no desempenho** | Avaliar com base em dados de desempenho dinâmicos recolhidos. | O tamanho recomendado do nó em AVS baseia-se em dados de CPU e utilização da memória, juntamente com as definições que especifica na avaliação para o tipo de nó, tipo de armazenamento e definição de falha de toleração.

## <a name="run-an-assessment"></a>Executar uma avaliação

E executar uma avaliação da seguinte forma:

1. Na página de **Servidores** > **servidores Windows e Linux,** clique em **Avaliar e migrar servidores**.

   ![Localização do botão de avaliar e migrar servidores](./media/tutorial-assess-vmware-azure-vmware-solution/assess.png)

2. Em **Azure Migrate: Avaliação do servidor,** clique em **Avaliar**.

3. No tipo de avaliação **de servidores,**  >  **Assessment type** selecione **Azure VMware Solution (AVS) (Preview)**.
4. Na **fonte discovery:**

    - Se descobrir máquinas que utilizem o aparelho, selecione **Máquinas descobertas a partir do aparelho Azure Migrate**.
    - Se descobrir máquinas utilizando um ficheiro CSV importado, selecione **máquinas importadas.** 
    
5. Especifique um nome para a avaliação. 
6. Clique em **Ver tudo** para rever as propriedades de avaliação.

    ![Página para selecionar as definições de avaliação](./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png)


7. 1n **Propriedades de avaliação**  >  **Propriedades-alvo:**

    - Na **localização do alvo,** especifique a região de Azure para a qual pretende migrar.
       - As recomendações de tamanho e custo baseiam-se na localização que especifica.
       - Atualmente, pode avaliar três regiões (Leste dos EUA, Eua Ocidental, Europa Ocidental)
   - No **tipo de armazenamento,** deixe **vSAN**. Este é o tipo de armazenamento padrão para uma nuvem privada AVS.
   - **Os casos reservados** não são atualmente suportados para nós AVS.
8. Em **tamanho VM:**
    - No **tipo nó,** selecione um tipo de nó com base nas cargas de trabalho em execução nos VMs no local.
        - Azure Migrate recomenda o nó de nós necessários para migrar os VMs para AVS.
        - O tipo de nó predefinido é AV36.
    - **Definição FTT, nível RAID,** selecione a combinação Falha em Tolerar e RAID.  A opção FTT selecionada, combinada com o requisito do disco VM no local, determina o armazenamento total de vSAN exigido em AVS.
    - Na **subscrição excessiva do CPU, especifique** a relação de núcleos virtuais associados a um núcleo físico no nó AVS. A subscrição excessiva de mais de 4:1 pode causar degradação do desempenho, mas pode ser usada para cargas de trabalho do tipo web do servidor.

9. No **tamanho do nó:** 
    - No **critério Sizing**, selecione se pretende basear a avaliação em metadados estáticos ou em dados baseados no desempenho. Se utilizar dados de desempenho:
        - No **histórico de desempenho**, indique a duração dos dados em que pretende basear a avaliação
        - Na **utilização do Percentil,** especifique o valor percentil que pretende utilizar para a amostra de desempenho. 
    - No **fator Comfort,** indique o tampão que pretende utilizar durante a avaliação. Isto explica questões como o uso sazonal, o histórico de desempenho curto e, provavelmente, o aumento do uso futuro. Por exemplo, se utilizar um fator de conforto de dois:
    
        **Componente** | **Utilização eficaz** | **Adicionar fator de conforto (2.0)**
        --- | --- | ---  
        Núcleos | 2 | 4
        Memória | 8 GB | 16 GB     

10. Na **fixação de preços:**
    - In **Offer**, [A oferta Azure](https://azure.microsoft.com/support/legal/offer-details/) em que está inscrito é apresentado A Avaliação do Servidor estima o custo dessa oferta.
    - Em **Moeda,** selecione a moeda de faturação para a sua conta.
    - Em **Desconto (%)**, adicione quaisquer descontos específicos de subscrição que receba em cima da oferta Azure. A predefinição é 0%.

11. Clique **em Guardar** se escoda alterações.

    ![Propriedades da avaliação](./media/tutorial-assess-vmware-azure-vmware-solution/view-all.png)

12. In **Assess Servers**, clique **em Seguinte**.
13. Em **Avaliar servidores**  >  **Selecione máquinas para avaliar,** para criar um novo grupo de servidores para avaliação, selecione **Create New** e especifique um nome de grupo. 
14. Selecione o aparelho e selecione os VMs que pretende adicionar ao grupo. Em seguida, clique em **Seguinte**.
15. Em **Review + criar avaliação,** rever os detalhes da avaliação e clicar em Criar **Avaliação** para criar o grupo e executar a avaliação.

    > [!NOTE]
    > Para avaliações baseadas no desempenho, recomendamos que espere pelo menos um dia após iniciar a descoberta antes de criar uma avaliação. Isto dá tempo para recolher dados de desempenho com maior confiança. Idealmente, depois de começar a ser descoberto, aguarde a duração de desempenho que especifica (dia/semana/mês) para uma classificação de alta confiança.

## <a name="review-an-assessment"></a>Rever uma avaliação

Uma avaliação avs descreve:

- Prontidão AVS: Se os VMs no local são adequados para a migração para Azure VMware Solution (AVS).
- Número de nós AVS: Número estimado de nós AVS necessários para executar os VMs.
- Utilização através de nós AVS: CPU projetado, memória e utilização de armazenamento em todos os nós.
- Estimativa mensal dos custos: Os custos mensais estimados para todos os nós da Azure VMware Solution (AVS) que executam os VMs no local.

## <a name="view-an-assessment"></a>Ver uma avaliação

Para visualizar uma avaliação:

1. Nos **servidores**  >  **Azure Migrate: Avaliação do servidor,** clique no número ao lado **das Avaliações**.
2. Em **Avaliações**, selecione uma avaliação para a abrir. 
3. Reveja o resumo da avaliação. Também pode editar as propriedades de avaliação ou recalcular a avaliação.
 

### <a name="review-readiness"></a>Revisão de prontidão

1. Clique **em Azure prontidão**.
2. Em **prontidão Azure,** reveja o estado de VM.

    - **Pronto para AVS**: A máquina pode ser migrada como-é para Azure AVS, sem alterações. A máquina começará em AVS, com suporte AVS completo.
    - **Pronto com condições**: A máquina pode ter problemas de compatibilidade com a versão atual da vSphere. Pode precisar de ferramentas VMware instaladas, ou outras configurações, antes de ter plena funcionalidade em AVS.
    - **Não está pronto para AVS**: O VM não arranca em AVS. Por exemplo, se um VMware VMware VMware no local tiver um dispositivo externo (como um CD-ROM) ligado a ele e estiver a utilizar vMotion VMware VMotion, a operação VMotion falha.
 - **Prontidão desconhecida**: A Azure Migrate não conseguiu determinar a prontidão da máquina, devido a metadados insuficientes recolhidos do ambiente no local.

3. Reveja a ferramenta sugerida.

    - VMware HCX ou Enterprise: Para máquinas VMware, a solução VMWare Hybrid Cloud Extension (HCX) é a ferramenta de migração sugerida para migrar a sua carga de trabalho no local para a sua nuvem privada Azure VMware Solution (AVS). Saiba mais.
    - Desconhecida: para as máquinas virtuais importadas através de um ficheiro CSV, a ferramenta de migração predefinida é desconhecida. Embora para máquinas VMware, é sugerido que utilize a solução VMware Hybrid Cloud Extension (HCX).
4. Clique num estado de prontidão AVS. Pode ver detalhes de prontidão em VM e perfurar para ver detalhes de VM, incluindo configurações de computação, armazenamento e rede.

### <a name="review-cost-estimates"></a>Rever as estimativas de custos

O resumo da avaliação mostra o custo estimado do cálculo e armazenamento dos VM em funcionamento em Azure. 

1. Reveja os custos totais mensais. Os custos são agregados para todos os VMs do grupo avaliado.

    - As estimativas de custos baseiam-se no número de nós AVS necessários, tendo em conta os requisitos de recursos de todos os VMs no total.
    - Como o preço do AVS é por nó, o custo total não tem custo de computação e distribuição de custos de armazenamento.
    - A estimativa de custos é para a execução dos VMs no local em AVS. A avaliação do servidor Azure Migrate não considera os custos do PaaS ou do SaaS.

2. Reveja as estimativas mensais de armazenamento. A vista mostra os custos de armazenamento agregados para o grupo avaliado, divididos sobre diferentes tipos de discos de armazenamento. 
3. Você pode perfurar para ver detalhes de custos para VMs específicos.

### <a name="review-confidence-rating"></a>Rever a classificação de confiança

A Avaliação do Servidor atribui uma classificação de confiança a avaliações baseadas no desempenho. A classificação é de uma estrela (mais baixa) para cinco estrelas (mais alta).

![Classificação de confiança](./media/tutorial-assess-vmware-azure-vmware-solution/confidence-rating.png)

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
- Configurar um mapeamento de dependência baseado em [agentes](how-to-create-group-machine-dependencies-agentless.md) ou [por agente.](how-to-create-group-machine-dependencies.md)
