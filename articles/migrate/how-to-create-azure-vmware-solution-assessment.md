---
title: Crie uma avaliação AVS com a Azure Migrate | Microsoft Docs
description: Descreve como criar uma avaliação AVS com Azure Migrate
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 72372e6365a2535e449681549a515c3f8594f2f1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786605"
---
# <a name="create-an-azure-vmware-solution-avs-assessment"></a>Criar uma avaliação da Solução VMware Azure (AVS)

Este artigo descreve como criar uma avaliação da Azure VMware Solution (AVS) para servidores no local em ambiente VMware com Azure Migrate: Descoberta e avaliação.

[Azure Migrate](migrate-services-overview.md) ajuda-o a migrar para Azure. A Azure Migrate fornece um centro centralizado para acompanhar a descoberta, avaliação e migração de infraestruturas, aplicações e dados no local para Azure. O hub fornece ferramentas Azure para avaliação e migração, bem como ofertas de fornecedores de software independentes de terceiros (ISV).

## <a name="before-you-start"></a>Antes de começar

- Certifique-se de ter [criado](./create-manage-projects.md) um projeto Azure Migrate.
- Se já criou um projeto, certifique-se de ter [adicionado](how-to-assess.md) a ferramenta Azure Migrate: Discovery and assessment.
- Para criar uma avaliação, é necessário configurar um aparelho Azure Migrate para [VMware,](how-to-set-up-appliance-vmware.md)que descobre os servidores no local, e envia metadados e dados de desempenho para a Azure Migrate: Discovery e avaliação. [Saiba mais](migrate-appliance.md).
- Também pode [importar os metadados do servidor](./tutorial-discover-import.md) em formato de valores separados por vírgula (CSV).


## <a name="azure-vmware-solution-avs-assessment-overview"></a>Visão geral da avaliação da Solução VMware Azure (AVS)

Existem três tipos de avaliações que pode criar usando Azure Migrate: Discovery e assessment.

***Tipo de Avaliação** | **Detalhes**
--- | --- 
**VM do Azure** | Avaliações para migrar os seus servidores no local para máquinas virtuais do Azure. Pode avaliar os seus servidores no local em ambiente [VMware](how-to-set-up-appliance-vmware.md) e [Hiper-V](how-to-set-up-appliance-hyper-v.md) e [servidores físicos](how-to-set-up-appliance-physical.md) para migração para VMs Azure usando este tipo de avaliação.
**SQL do Azure** | Avaliações para migrar os seus servidores SQL no local do seu ambiente VMware para Azure SQL Database ou Azure SQL Managed Instance.
**Solução VMware no Azure (AVS)** | Avaliações para migrar os seus servidores no local para o [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). Pode avaliar os seus servidores no local em [ambiente VMware](how-to-set-up-appliance-vmware.md) para migração para Azure VMware Solution (AVS) utilizando este tipo de avaliação. [Saiba mais](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> A avaliação da Solução VMware (AVS) do Azure VMware Solution (AVS) só pode ser criada para servidores em ambiente VMware.


Existem dois tipos de critérios de dimensionamento que pode utilizar para criar avaliações da Solução VMware Azure (AVS):

**Avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Com base no desempenho** | Avaliações baseadas em dados de desempenho recolhidos de servidores no local. | **Tamanho recomendado do nó**: Com base em dados de utilização da CPU e da memória, juntamente com o tipo de nó, o tipo de armazenamento e a definição de FTT que seleciona para a avaliação.
**Como no local** | Avaliações baseadas no dimensionamento no local. | **Tamanho do nó recomendado**: Com base no tamanho do servidor no local, juntamente com o tipo de nó, tipo de armazenamento e definição FTT que seleciona para a avaliação.


## <a name="run-an-azure-vmware-solution-avs-assessment"></a>Executar uma avaliação da Solução VMware Azure (AVS)

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


## <a name="review-an-azure-vmware-solution-avs-assessment"></a>Reveja uma avaliação da Solução VMware Azure (AVS)

Uma avaliação da Solução Azure VMware (AVS) descreve:

- **Prontidão Azure VMware Solution (AVS):** Se os servidores no local são adequados para a migração para Azure VMware Solution (AVS).
- **Número de nós AVS**: Número estimado de nós AVS necessários para executar os servidores.
- **Utilização através de nós AVS**: CPU projetado, memória e utilização de armazenamento em todos os nós.
    - A utilização inclui factoring frontal nas seguintes despesas de gestão de clusters, tais como o vCenter Server, NSX Manager (grande), NSX Edge, se o HCX for implantado também o HCX Manager e o aparelho IX consumindo ~ 44vCPU (11 CPU), 75GB de RAM e 722GB de armazenamento antes da compressão e desinsuplicou.
- **Estimativa mensal dos custos**: Os custos mensais estimados para todos os nós da Azure VMware Solution (AVS) que executam os VMs no local.


### <a name="view-an-assessment"></a>Ver uma avaliação

1. No **Windows, Linux e SQL Server**  >  **Azure Migrate: Discovery and assessment**, clique no número ao lado de ** Azure VMware Solution**.

1. Em **Avaliações**, selecione uma avaliação para a abrir. Como exemplo (estimativas e custos, por exemplo: 

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-assessment-summary.png" alt-text="Resumo da avaliação do AVS":::

1. Reveja o resumo da avaliação. Também pode editar as propriedades de avaliação ou recalcular a avaliação.

### <a name="review-azure-vmware-solution-avs-readiness"></a>Rever prontidão Azure VMware Solution (AVS)

1. Em **Azure,** verifique se os servidores estão prontos para a migração para AVS.

2. Reveja o estado do servidor:
    - **Pronto para AVS**: O servidor pode ser migrado como-é para Azure (AVS) sem alterações. Começará em AVS com suporte AVS completo.
    - **Pronto com condições**: O servidor pode ter problemas de compatibilidade com a versão atual do vSphere, bem como necessitar de ferramentas e outras configurações de VMware e ou outras configurações antes de a funcionalidade completa do servidor ser alcançada em AVS.
    - **Não está pronto para AVS**: O servidor não arrancará em AVS. Por exemplo, se o servidor no local tiver um dispositivo externo ligado, como um cd-rom, a operação VMotion falhará (se utilizar vMware VMotion).
    - **Prontidão desconhecida**: A Azure Migrate não conseguiu determinar a prontidão do servidor devido a metadados insuficientes recolhidos do ambiente no local.

3. Reveja a ferramenta sugerida:
    - **VMware HCX ou Enterprise**: Para servidores VMware, a solução VMware Hybrid Cloud Extension (HCX) é a ferramenta de migração sugerida para migrar a sua carga de trabalho no local para a sua nuvem privada Azure VMware Solution (AVS). [Saiba Mais](../azure-vmware/tutorial-deploy-vmware-hcx.md).
    - **Desconhecido**: Para os servidores importados através de um ficheiro CSV, a ferramenta de migração padrão é desconhecida. Embora para servidores VMware, é sugerido que utilize a solução VMware Hybrid Cloud Extension (HCX). 

4. Clique num estado **de prontidão AVS.** Pode ver detalhes de prontidão em VM e perfurar para ver detalhes de VM, incluindo configurações de computação, armazenamento e rede.

### <a name="review-cost-details"></a>Rever os detalhes dos custos

Esta vista mostra o custo estimado de funcionamento dos servidores em Azure VMware Solution (AVS).

1. Reveja os custos totais mensais. Os custos são agregados para todos os servidores no grupo avaliado. 

    - As estimativas de custos baseiam-se no número de nós AVS necessários, tendo em conta os requisitos de recursos de todos os servidores no total.
    - Como o preço da Azure VMware Solution (AVS) é por nó, o custo total não tem custo de computação e distribuição de custos de armazenamento.
    - A estimativa de custos é para executar os servidores no local em AVS. A avaliação do AVS não considera os custos do PaaS ou do SaaS.
    
2. Pode rever as estimativas mensais de custos de armazenamento. Esta visão mostra os custos de armazenamento agregados para o grupo avaliado, divididos sobre diferentes tipos de discos de armazenamento.

3. Pode perfurar para ver detalhes para servidores específicos.


### <a name="review-confidence-rating"></a>Rever a classificação de confiança

Quando faz avaliações baseadas no desempenho, é atribuída uma classificação de confiança à avaliação.

![Classificação de confiança](./media/how-to-create-assessment/confidence-rating.png)

- É atribuída uma classificação de 1 estrela (mais baixa) a 5 estrelas (mais alta).
- A classificação de confiança ajuda-o a estimar a fiabilidade das recomendações de tamanho fornecidas pela avaliação.
- A classificação de confiança baseia-se na disponibilidade de pontos de dados necessários para calcular a avaliação.
- Para dimensionamento baseado no desempenho, as avaliações avs precisam dos dados de utilização para cpu e memória do servidor. Os seguintes dados de desempenho são recolhidos, mas não utilizados em recomendações de dimensionamento para avaliações avs:
  - O disco IOPS e os dados de produção de cada disco ligado ao servidor.
  - A rede I/O para lidar com o tamanho baseado no desempenho de cada adaptador de rede ligado a um servidor.

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