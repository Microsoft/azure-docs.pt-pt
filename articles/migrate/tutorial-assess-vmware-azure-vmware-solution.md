---
title: Avaliar servidores VMware para migração para Azure VMware Solution (AVS) com Azure Migrate
description: Saiba como avaliar servidores em ambiente VMware para migração para AVS com Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: 31bf3909012231996bd340cfa4d388f0fe20a4f5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104782153"
---
# <a name="tutorial-assess-vmware-servers-for-migration-to-avs"></a>Tutorial: Avaliar servidores VMware para migração para AVS

Como parte da sua viagem de migração para Azure, você avalia as suas cargas de trabalho no local para medir a prontidão na nuvem, identificar riscos e estimar custos e complexidade.

Este artigo mostra-lhe como avaliar máquinas/servidores virtuais VMware descobertos para migração para Azure VMware Solution (AVS), utilizando o Azure Migrate. O AVS é um serviço gerido que lhe permite executar a plataforma VMware em Azure.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
- Executar uma avaliação com base em metadados do servidor e informações de configuração.
- Executar uma avaliação com base em dados de desempenho.

> [!NOTE]
> Os tutoriais mostram o caminho mais rápido para experimentar um cenário e usam opções padrão sempre que possível. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.



## <a name="prerequisites"></a>Pré-requisitos

Antes de seguir este tutorial para avaliar os seus servidores para a migração para AVS, certifique-se de que descobriu os servidores que pretende avaliar:

- Para descobrir servidores que utilizem o aparelho Azure Migrate, [siga este tutorial](tutorial-discover-vmware.md). 
- Para descobrir servidores usando um ficheiro CSV importado, [siga este tutorial](tutorial-discover-import.md).


## <a name="decide-which-assessment-to-run"></a>Decidir qual a avaliação a executar


Decida se pretende executar uma avaliação utilizando critérios de dimensionamento baseados em dados de configuração do servidor/metadados recolhidos como-está no local ou em dados de desempenho dinâmicos.

**Avaliação** | **Detalhes** | **Recomendação**
--- | --- | ---
**Como está no local** | Avaliar com base em dados/metadados de configuração do servidor.  | O tamanho recomendado do nó em AVS baseia-se no tamanho VM/servidor no local, juntamente com as definições que especifica na avaliação para o tipo de nó, tipo de armazenamento e definição de falha de toleração.
**Com base no desempenho** | Avaliar com base em dados de desempenho dinâmicos recolhidos. | O tamanho recomendado do nó em AVS baseia-se em dados de CPU e utilização da memória, juntamente com as definições que especifica na avaliação para o tipo de nó, tipo de armazenamento e definição de falha de toleração.

> [!NOTE]
> A avaliação da Solução VMware (AVS) do Azure VMware (AVS) pode ser criada apenas para VMware VMs/servidores.

## <a name="run-an-assessment"></a>Executar uma avaliação

E executar uma avaliação da seguinte forma:

1.  Na página **'Visão Geral'** > **Windows, Linux e SQL Server,** clique em **Avaliar e migrar servidores**.
    :::image type="content" source="./media/tutorial-assess-sql/assess-migrate.png" alt-text="Página geral para Azure Migrate":::

1. Em **Azure Migrate: Descoberta e avaliação,** clique em **Avaliar**.

   ![Localização do botão avaliar](./media/tutorial-assess-vmware-azure-vmware-solution/assess.png)

1. No tipo de avaliação **de servidores,**  >  selecione **Azure VMware Solution (AVS)**.

1. Na **fonte discovery:**

    - Se descobrir servidores que utilizem o aparelho, selecione **Servidores descobertos a partir do aparelho Azure Migrate**.
    - Se descobrir servidores usando um ficheiro CSV importado, selecione **servidores importados**. 
    
1. Clique **em Editar** para rever as propriedades de avaliação.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png" alt-text="Página para selecionar as definições de avaliação":::
 

1. Em **Propriedades de Avaliação**  >  **Propriedades-Alvo**:

    - Na **localização do alvo,** especifique a região de Azure para a qual pretende migrar.
       - As recomendações de tamanho e custo baseiam-se na localização que especifica.
   - O **tipo de armazenamento** é predefinido para **vSAN**. Este é o tipo de armazenamento padrão para uma nuvem privada AVS.
   - **Os casos reservados** não são atualmente suportados para nós AVS.
1. Em **tamanho VM:**
    - O **tipo nó** é predefinido para **AV36**. A azure Migrate recomenda o nó de nós necessários para migrar os servidores para AVS.
    - Na **definição FTT, nível RAID**, selecione a combinação Falha em Tolerar e RAID.  A opção FTT selecionada, combinada com o requisito do disco do servidor no local, determina o armazenamento total de vSAN exigido no AVS.
    - Na **subscrição excessiva do CPU, especifique** a relação de núcleos virtuais associados a um núcleo físico no nó AVS. A subscrição excessiva de mais de 4:1 pode causar degradação do desempenho, mas pode ser usada para cargas de trabalho do tipo web do servidor.
    - No **fator sobrecompromisso de memória,** especifique a relação de memória sobre o compromisso no cluster. Um valor de 1 representa 100% de uso de memória, 0,5 por exemplo é 50%, e 2 usaria 200% da memória disponível. Só pode adicionar valores de 0,5 a 10 até uma casa decimal.
    - No **fator Dedupe e compressão,** especifique o fator de dedupe e compressão previsto para as suas cargas de trabalho. O valor real pode ser obtido a partir de vSAN ou config de armazenamento no local, o que pode variar por carga de trabalho. Um valor de 3 significaria 3x, pelo que para o disco de 300GB apenas seria usado um armazenamento de 100GB. Um valor de 1 significaria não dedupe ou compressão. Só é possível adicionar valores de 1 a 10 até uma casa decimal.
1. No **tamanho do nó:** 
    - No **critério Sizing**, selecione se pretende basear a avaliação em metadados estáticos ou em dados baseados no desempenho. Se utilizar dados de desempenho:
        - No **histórico de desempenho**, indique a duração dos dados em que pretende basear a avaliação
        - Na **utilização do Percentil,** especifique o valor percentil que pretende utilizar para a amostra de desempenho. 
    - No **fator Comfort,** indique o tampão que pretende utilizar durante a avaliação. Isto explica questões como o uso sazonal, o histórico de desempenho curto e, provavelmente, o aumento do uso futuro. Por exemplo, se utilizar um fator de conforto de dois:
    
        **Componente** | **Utilização eficaz** | **Adicionar fator de conforto (2.0)**
        --- | --- | ---
        Núcleos | 2  | 4
        Memória | 8 GB | 16 GB  

1. Na **fixação de preços:**
    - In **Offer**, [Azure offer](https://azure.microsoft.com/support/legal/offer-details/) em que está inscrito é exibido. A Avaliação estima o custo dessa oferta.
    - Em **Moeda,** selecione a moeda de faturação para a sua conta.
    - Em **Desconto (%)**, adicione quaisquer descontos específicos de subscrição que receba em cima da oferta Azure. A predefinição é 0%.

1. Clique **em Guardar** se escoda alterações.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-view-all.png" alt-text="Propriedades da avaliação":::

1. In **Assess Servers**, clique **em Seguinte**.

1. Em **Servidores Selecionados para avaliar** o nome de  >  **avaliação** > especificar um nome para a avaliação. 
 
1. Em **Select ou criar um grupo** > selecione Create **New** e especifique um nome de grupo. 
    
    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-group.png" alt-text="Adicionar servidores a um grupo":::
 
1. Selecione o aparelho e selecione os servidores que pretende adicionar ao grupo. Em seguida, clique em **Seguinte**.

1. Em **Review + criar avaliação,** rever os detalhes da avaliação e clicar em Criar **Avaliação** para criar o grupo e executar a avaliação.

    > [!NOTE]
    > Para avaliações baseadas no desempenho, recomendamos que espere pelo menos um dia após iniciar a descoberta antes de criar uma avaliação. Isto dá tempo para recolher dados de desempenho com maior confiança. Idealmente, depois de começar a ser descoberto, aguarde a duração de desempenho que especifica (dia/semana/mês) para uma classificação de alta confiança.

## <a name="review-an-assessment"></a>Rever uma avaliação

Uma avaliação avs descreve:

- Prontidão AVS: Se os servidores no local são adequados para a migração para Azure VMware Solution (AVS).
- Número de nós AVS: Número estimado de nós AVS necessários para executar os servidores.
- Utilização através de nós AVS: CPU projetado, memória e utilização de armazenamento em todos os nós.
    - A utilização inclui factoring inicial nas seguintes despesas de gestão de clusters, tais como o vCenter Server, NSX Manager (grande), NSX Edge, se o HCX for implantado também o HCX Manager e o aparelho IX consumindo ~ 44vCPU (11 CPU), 75GB de RAM e 722GB de armazenamento antes da compressão e desinsutilação. 
- Estimativa mensal dos custos: Os custos mensais estimados para todos os nós da Azure VMware Solution (AVS) que executam os servidores no local.

## <a name="view-an-assessment"></a>Ver uma avaliação

Para visualizar uma avaliação:

1. No **Windows, Linux e SQL Server**  >  **Azure Migrate: Discovery and assessment**, clique no número ao lado de ** Azure VMware Solution**.

1. Em **Avaliações**, selecione uma avaliação para a abrir. Como exemplo (estimativas e custos, por exemplo: 

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-assessment-summary.png" alt-text="Resumo da avaliação do AVS":::

1. Reveja o resumo da avaliação. Também pode editar as propriedades de avaliação ou recalcular a avaliação.
 

### <a name="review-readiness"></a>Revisão de prontidão

1. Clique **em Azure prontidão**.
2. Em **Azure prontidão**, reveja o estado de prontidão.

    - **Pronto para AVS**: O servidor pode ser migrado como está para Azure AVS, sem alterações. O servidor começará em AVS, com suporte AVS completo.
    - **Pronto com condições**: O servidor pode ter problemas de compatibilidade com a versão atual do vSphere. Pode precisar de ferramentas VMware instaladas, ou outras configurações, antes de ter plena funcionalidade em AVS.
    - **Não está pronto para AVS**: O VM não arranca em AVS. Por exemplo, se um servidor VMware no local tiver um dispositivo externo (como um CD-ROM) ligado ao mesmo e estiver a utilizar vMotion VMware VMotion, a operação VMotion falha.
 - **Prontidão desconhecida**: A Azure Migrate não conseguiu determinar a prontidão do servidor, devido a metadados insuficientes recolhidos do ambiente no local.

3. Reveja a ferramenta sugerida.

    - VMware HCX ou Enterprise: Para servidores VMware, a solução VMware Hybrid Cloud Extension (HCX) é a ferramenta de migração sugerida para migrar a sua carga de trabalho no local para a sua nuvem privada Azure VMware Solution (AVS). Saiba mais.
    - Desconhecido: Para os servidores importados através de um ficheiro CSV, a ferramenta de migração padrão é desconhecida. Embora para servidores VMware, é sugerido que utilize a solução VMware Hybrid Cloud Extension (HCX).
4. Clique num estado de prontidão AVS. Pode ver os detalhes de prontidão do servidor e aprofundar para ver os detalhes do servidor, incluindo configurações de computação, armazenamento e rede.

### <a name="review-cost-estimates"></a>Rever as estimativas de custos

O resumo da avaliação mostra o custo estimado de cálculo e armazenamento de servidores em execução em Azure. 

1. Reveja os custos totais mensais. Os custos são agregados para todos os servidores no grupo avaliado.

    - As estimativas de custos baseiam-se no número de nós AVS necessários, tendo em conta os requisitos de recursos de todos os servidores no total.
    - Como o preço do AVS é por nó, o custo total não tem custo de computação e distribuição de custos de armazenamento.
    - A estimativa de custos é para executar os servidores no local em AVS. A avaliação do AVS não considera os custos do PaaS ou do SaaS.

2. Reveja as estimativas mensais de armazenamento. A vista mostra os custos de armazenamento agregados para o grupo avaliado, divididos sobre diferentes tipos de discos de armazenamento. 
3. Pode perfurar para ver detalhes de custos para servidores específicos.

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

- Encontre dependências do servidor utilizando [o mapeamento de dependência](concepts-dependency-visualization.md).
- Configurar um mapeamento de dependência baseado em [agentes](how-to-create-group-machine-dependencies-agentless.md) ou [por agente.](how-to-create-group-machine-dependencies.md)
