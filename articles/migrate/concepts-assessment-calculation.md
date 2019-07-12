---
title: Cálculos de avaliação no Azure Migrate | Documentos da Microsoft
description: Fornece uma visão geral dos cálculos de avaliação no serviço Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: raynew
ms.openlocfilehash: a328549307772cbdf470160cc1ad713fe1ee5e05
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805990"
---
# <a name="assessment-calculations"></a>Cálculos de avaliação

Avaliação Server do Azure Migrate avalia cargas de trabalho no local para migração para o Azure. Este artigo fornece informações sobre como são calculadas as avaliações.


[O Azure Migrate](migrate-services-overview.md) fornece um concentrador central para controlar a deteção, avaliação e migração de aplicações no local e cargas de trabalho e instâncias de nuvem pública/privada, para o Azure. O hub fornece ferramentas do Azure Migrate para avaliação e migração, bem como ofertas de (ISV fornecedor) de terceiros independentes de software.

## <a name="overview"></a>Descrição geral

Uma avaliação de avaliação do servidor de migrar do Azure tem três fases. Avaliação começa com uma análise de adequação, seguida de dimensionamento, e por último, estimativa de custos mensais. Uma máquina apenas move ao longo para numa fase posterior se for aprovado relativamente anterior. Por exemplo, se um computador falhar a verificação de adequação do Azure, está marcada como não adequado para dimensionamento e custos e do Azure, a não ser feito.


## <a name="whats-in-an-assessment"></a>Novidades nas avaliações

**Propriedade** | **Detalhes**
--- | ---
**Localização de destino** | A localização do Azure para a qual pretende migrar.<br/><br/> Atualmente, o Azure Migrate suporta estas regiões de destino: Leste da Austrália, Sudeste da Austrália, sul do Brasil, Canadá Central, leste do Canadá, Índia Central, E.U.A. Central, leste da China, Norte da China, Ásia Oriental, E.U.A. leste, e.u.a. Leste 2, Alemanha Central, Alemanha Nordeste, Japão leste, oeste do Japão, Coreia Central, Coreia, Sul, Norte Centro dos E.U.A., Europa do Norte, dos E.U.A. centro-Sul, Sudeste asiático, Sul da Índia, sul do Reino Unido, oeste do Reino Unido, E.U.A. Gov Arizona, US Gov Texas, US Gov Virgínia, EUA Centro-Oeste, Europa Ocidental, Índia Ocidental, E.U.A. oeste e E.u.a. oeste2.<br/> Por predefinição, a região de destino está definida como E.U.A. Oeste 2.
**Tipo de armazenamento** | Discos/Standard do padrão HHD discos SSD/Premium.<br/><br/> Quando especificar o tipo de armazenamento como automática nas avaliações, a recomendação de disco baseia-se nos dados de desempenho de discos (IOPS e débito).<br/><br/> Se especificar o tipo de armazenamento como Premium ou Standard, a recomenda de avaliação um disco SKU dentro do tipo de armazenamento selecionada.<br/><br/> Se quer atingir uma única instância de VM SLA de 99,9%, pode definir o tipo de armazenamento como discos geridos Premium. Em seguida, todos os discos na avaliação irão ser recomendados como discos geridos Premium. <br/> O Azure Migrate só suporta discos geridos para avaliação de migrações.<br/> 
**Instâncias reservadas (RIS)** | Especifica esta propriedade se têm reservados instâncias no Azure. Estimativas de custos na avaliação levará descontos de RI em conta. Instâncias reservadas são atualmente apenas suportado para pay as you go oferece no Azure Migrate.
**Critérios de dimensionamento** | Utilizado para VMs de tamanho adequado. Dimensionamento pode ser baseado no desempenho, ou como-está no local, sem considerar o histórico de desempenho.
**Histórico de desempenho** | A duração a considerar para avaliar o desempenho da VM. Esta propriedade só é aplicável quando o dimensionamento é baseado no desempenho.
**Utilização de percentil** | O valor de percentil da amostra de desempenho que é utilizado para VMs de redimensionamento. Esta propriedade só é aplicável quando o dimensionamento é baseado no desempenho.
**Série das VMs** | A série das VMs utilizada para os cálculos de tamanho. Por exemplo, se tiver um ambiente de produção que não pretende migrar para VMs da série A no Azure, poderá excluir a série A da lista ou da série. O dimensionamento tem por base apenas as séries selecionadas.
**Fator de conforto** | Avaliação Server do Azure Migrate considera uma memória intermédia (fator de conforto) durante a avaliação. Esta memória intermédia é aplicada em cima dos dados de utilização das VMs (CPU, memória, disco e rede). O fator de conforto dá conta de problemas como utilização sazonal, histórico de desempenho breve e prováveis aumentos na utilização futura.<br/><br/> Por exemplo, uma VM com 10 núcleos e 20% de utilização resulta, normalmente, numa VM de 2 núcleos. No entanto, com um fator de conforto de 2,0 x, o resultado é uma VM de 4 núcleos.
**Oferta** | A [oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/) em que está escrito. O Azure Migrate calcula o custo em conformidade.
**Moeda** | A moeda de faturação. 
**Desconto (%)** | Eventuais descontos para uma subscrição específica que receba sobre a oferta do Azure.<br/> A predefinição é 0%.
**Tempo de atividade de VM** | Se não se pretender que as VMs em execução 24x7 no Azure, pode especificar a duração (número de dias por mês) e o número de horas por dia para que eles estariam em execução e as estimativas de custos teriam de ser feitas em conformidade.<br/> O valor predefinido é 31 dias por mês e 24 horas por dia.
**Benefício Híbrido do Azure** | Especifica se tem o software assurance e são elegíveis para [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Se definido como Sim, são considerados para as VMs do Windows preços não Microsoft Azure. 



## <a name="azure-suitability-analysis"></a>Análise de adequação do Azure

Nem todas as máquinas são adequadas para executar no Azure. Avaliação Server do Azure Migrate avalia cada máquina no local para migração e categoriza máquinas em uma das seguintes categorias de adequação:
- **Preparado para o Azure** -a máquina pode ser migrada como-é para o Azure sem quaisquer alterações. Ele será inicializado no Azure com suporte completo do Azure.
- **Condicionalmente preparado para o Azure** -a máquina pode arrancar no Azure, mas pode não ter suporte total do Azure. Por exemplo, uma máquina com uma versão antiga do SO do Windows Server não é suportada no Azure. Precisa ter cuidado antes de a migração desses computadores para o Azure e siga as orientações de remediação sugeridas na avaliação para corrigir os problemas de preparação antes de migrar.
- **Não preparado para o Azure** -a máquina não irão arrancar no Azure. Por exemplo, se uma máquina no local tem um disco de tamanho de mais de 4 TB ligados ao mesmo, não pode ser alojada no Azure. Tem de seguir as diretrizes de remediação sugeridas na avaliação para corrigir o problema de preparação antes de migrar para o Azure. Estimativa de redimensionamento e de custo não é feita para as máquinas que estão marcadas como não preparado para o Azure.
- **Preparação desconhecida** -Azure Migrate não foi possível localizar a preparação da máquina devido a dados suficientes disponíveis no vCenter Server.



### <a name="machine-properties"></a>Propriedades da máquina

O Azure Migrate analisa as seguintes propriedades da VM no local para identificar se ele pode ser executado no Azure.

**Propriedade** | **Detalhes** | **Estado de preparação para o Azure**
--- | --- | ---
**Tipo de arranque** | O Azure suporta VMs com o tipo de arranque como o BIOS e UEFI não. | Condicionalmente preparado se é o tipo de arranque UEFI.
**Núcleos** | O número de núcleos nas máquinas tem de ser igual ou menor que o número máximo de núcleos (128 núcleos) suportada para uma VM do Azure.<br/><br/> Se o histórico de desempenho está disponível, o Azure Migrate considera os núcleos utilizados para comparação. Se for um fator de conforto é especificado nas definições de avaliação, o número de núcleos utilizados é multiplicado pelo fator de conforto.<br/><br/> Se não houver nenhum histórico de desempenho, o Azure Migrate utiliza núcleos alocados, sem aplicar o fator de conforto. | Pronto, se tiver menos do que ou igual a limites.
**Memória** | O tamanho de memória da máquina tem de ser igual ou menor que o máximo de memória (GB 3892 na série de M de Azure Standard_M128m&nbsp;<sup>2</sup>) permitido para uma VM do Azure. [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Se o histórico de desempenho está disponível, o Azure Migrate considera a memória utilizada para comparação. Se não for especificado um fator de conforto, a memória utilizada é multiplicada pelo fator de conforto.<br/><br/> Se não houver nenhum histórico é utilizada a memória alocada, sem aplicar o fator de conforto.<br/><br/> | Pronto se dentro dos limites.
**Disco de armazenamento** | Tamanho atribuído de um disco deve ser de 4 TB (4096 GB) ou menos.<br/><br/> O número de discos ligados à máquina tem de ser 65 ou menos, incluindo o disco do SO. | Pronto se dentro dos limites.
**Redes** | Uma máquina tem de 32 ou menos NICs ligadas à mesma. | Pronto se dentro dos limites.

### <a name="guest-operating-system"></a>Sistema operativo convidado
Juntamente com as propriedades da VM, a avaliação do servidor de migrar do Azure analisa o sistema de operativo das máquinas, para identificar se ele pode ser executado no Azure.

> [!NOTE]
> Avaliação de servidor migrar do Azure utiliza o sistema operativo especificado para a VM no vCenter Server para análise. Avaliação de servidor migrar do Azure se baseia na aplicação para deteção de VMS, e não consegue verificar se o sistema operativo em execução na VM é a mesmo que foi especificado no vCenter Server.

A seguinte lógica é utilizada pela avaliação migrar do servidor do Azure, para identificar a preparação para o Azure com base no sistema operativo.

**Sistema operativo** | **Detalhes** | **Estado de preparação para o Azure**
--- | --- | ---
Windows Server 2016 e SPs todos os | O Azure fornece suporte completo. | Preparado para o Azure
Windows Server 2012 R2 e SPs todos os | O Azure fornece suporte completo. | Preparado para o Azure
Windows Server 2012 e SPs todos os | O Azure fornece suporte completo. | Preparado para o Azure
Windows Server 2008 R2 com todos os SPs | O Azure fornece suporte completo.| Preparado para o Azure
Windows Server 2008 (32 bits e 64 bits) | O Azure fornece suporte completo. | Preparado para o Azure
Windows Server 2003, 2003 R2 | Esses sistemas operacionais tenham passado o fim do suporte data e a necessidade de um [contrato de suporte personalizado (CSA)](https://aka.ms/WSosstatement) para suporte no Azure. | Condicionalmente preparado para o Azure, considere atualizar o sistema operacional antes de migrar para o Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Estes sistemas operativos passaram a sua data de fim de suporte, a máquina pode arrancar no Azure, mas não existe suporte de SO é fornecida pelo Azure. | Condicionalmente preparado para o Azure, recomenda-se para atualizar o sistema operacional antes de migrar para o Azure.
Cliente do Windows 7, 8 e 10 | O Azure fornece suporte com [apenas a subscrição do Visual Studio.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Condicionalmente preparado para o Azure
Ambiente de trabalho do Windows 10 Pro | O Azure fornece suporte com [direitos de alojamento multi-inquilino.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Condicionalmente preparado para o Azure
Windows Vista, XP Professional | Estes sistemas operativos passaram a sua data de fim de suporte, a máquina pode arrancar no Azure, mas não existe suporte de SO é fornecida pelo Azure. | Condicionalmente preparado para o Azure, recomenda-se para atualizar o sistema operacional antes de migrar para o Azure.
Linux | Azure endossa estes [sistemas operativos Linux](../virtual-machines/linux/endorsed-distros.md). Outros sistemas de operativos Linux pode arrancar no Azure, mas é recomendado que Atualize o sistema operativo para uma versão apoiada antes de migrar para o Azure. | Preparado para o Azure se a versão é apoiado pelo.<br/><br/>Condicionalmente preparado se a versão não é apoiado pelo.
Outros sistemas operativos<br/><br/> Por exemplo, Oracle Solaris, etc. de SO do Apple Mac, FreeBSD, etc. | Azure apoia estes sistemas operativos. A máquina pode arrancar no Azure, mas não existe suporte de SO é fornecida pelo Azure. | Condicionalmente preparado para o Azure, é recomendado para instalar um SO suportado antes de migrar para o Azure.  
Sistema operacional especificado como **outros** no vCenter Server | O Azure Migrate não consegue identificar, neste caso, o sistema operacional. | Preparação desconhecida. Certifique-se de que o sistema operacional em execução dentro da VM é suportado no Azure.
sistemas operativos de 32 bits | A máquina pode arrancar no Azure, mas o Azure não pode fornecer suporte total. | Condicionalmente preparado para o Azure, considere atualizar o sistema operacional da máquina do sistema operacional de 32 bits para o sistema operacional de 64 bits antes de migrar para o Azure.

## <a name="sizing"></a>Dimensionamento

Depois de uma máquina é marcada como preparado para o Azure, Azure Migrate tamanhos de VM e os respetivos discos para o Azure.

- Se a avaliação de utilizar o dimensionamento com base no desempenho, o Azure Migrate considera o histórico de desempenho da máquina para identificar o tipo de disco e de tamanho VM no Azure. Este método é especialmente útil se alocou excesso da VM no local, mas a utilização for baixa e pretende dimensionar a VM no Azure para reduzir os custos.
- Se estiver usar um, assim como no local avaliação, avaliação de servidor de migrar do Azure seja dimensionado as VMs com base nas definições no local, sem considerar os dados de utilização. Neste caso, disco de tamanho, é baseada no tipo de armazenamento que especificar nas propriedades de avaliação (disco Standard ou disco Premium).

### <a name="performance-based-sizing"></a>Dimensionamento com base no desempenho

Para o dimensionamento baseado no desempenho, do Azure Migrate começa com os discos ligados à VM, seguida de adaptadores de rede, e, em seguida, mapas de uma VM do Azure com base nos requisitos de computação da VM no local.

- **Armazenamento**: O Azure Migrate tenta mapear cada disco ligado à máquina para um disco no Azure.
    - Para obter a e/s de disco eficiente por segundo (IOPS) e o débito (MBps), do Azure Migrate multiplica o IOPS de disco e a taxa de transferência com o fator de conforto. Com base nos valores de débito e IOPS em vigor, Azure Migrate identificar se o disco deve ser mapeado para um disco standard ou premium no Azure.
    - Se o Azure Migrate não é possível localizar um disco com o IOPS e do débito necessário, marca o computador que não são adequados para o Azure. [Saiba mais](../azure-subscription-service-limits.md#storage-limits) sobre o Azure limita por disco e a VM.
    - Se ele encontrar um conjunto de discos adequados, o Azure Migrate seleciona os que suportam o método de redundância de armazenamento e a localização especificada nas definições de avaliação.
    - Se existirem vários discos elegíveis, seleciona aquele com o custo mais baixo.
    - Se os dados de desempenho para discos não estiverem disponíveis, todos os discos são mapeados para os discos standard no Azure.

- **Rede**: O Azure Migrate tenta localizar uma VM do Azure que pode suportar o número de adaptadores de rede ligados à máquina no local e o desempenho necessário por estas placas de rede.
    - Para obter o desempenho de rede em vigor a partir da VM no local, Azure Migrate agrega os dados transmitidos por segundo (MBps) fora da máquina (rede de saída), em todos os adaptadores de rede e aplica-se o fator de conforto. Este número é utilizado para localizar uma VM do Azure que pode suportar o desempenho de rede necessária.
    - Juntamente com o desempenho da rede, também considera se a VM do Azure pode suportar necessários o número de adaptadores de rede.
    - Se não existem dados de desempenho de rede estiverem disponíveis, é considerada apenas a contagem de adaptadores de rede para o dimensionamento de VM.

- **Computação**: Depois dos requisitos de armazenamento e rede são calculados, o Azure Migrate considera os requisitos de CPU e memória para encontrar um tamanho VM adequado no Azure.
    - O Azure Migrate analisa as utilizados núcleos e memória e aplica o fator de conforto, para obter o eficaz núcleos e memória. Com base no número, ele tenta encontrar um tamanho VM adequado no Azure.
    - Se não for encontrado nenhum tamanho adequado, a máquina está marcada como não são adequados para o Azure.
    - Se não for encontrado um tamanho adequado, do Azure Migrate aplica-se os cálculos de armazenamento e rede. Aplica-se, em seguida, localização e as definições do escalão, para a recomendação de tamanho VM final de preços.
    - Se existirem vários tamanhos de VMs do Azure elegíveis, é recomendado aquele que tiver o custo mais baixo.

### <a name="as-on-premises-sizing"></a>Como no local de dimensionamento

Se usar, como o dimensionamento no local, a avaliação de servidor aloca um SKU de VM no Azure com base no tamanho no local. Da mesma forma para o dimensionamento do disco, ele examina o tipo de armazenamento especificado nas propriedades de avaliação (Standard/Premium) e recomenda o tipo de disco em conformidade. O tipo de armazenamento predefinida é discos Premium.

## <a name="confidence-ratings"></a>Classificações de confiança
Cada avaliação com base em desempenho no Azure Migrate é associada uma classificação de confiança a partir de um (mais baixa) a que é iniciada a cinco (mais alta).
- A classificação de confiança é alocada a uma avaliação com base na disponibilidade dos pontos de dados necessários para calcular a avaliação.
- A classificação de confiança de uma avaliação ajuda a calcular a fiabilidade das recomendações de tamanho fornecidas pelo Azure Migrate.
- Classificação de confiança não é aplicável para como no local avaliações.
- Para o dimensionamento baseado no desempenho, tem de avaliação do servidor de migrar do Azure:
    - Os dados de utilização para CPU e memória da VM.
    - Além disso, para cada disco ligado à VM, é necessário o IOPS do disco e os dados de débito.
    - Da mesma forma para cada adaptador de rede ligada a uma VM, a classificação de confiança tem da e/s de rede para fazer o dimensionamento com base no desempenho.
    - Se qualquer um dos números de utilização acima não estão disponíveis no vCenter Server, a recomendação de tamanho poderá não ser fiável. 

Consoante a percentagem de pontos de dados disponíveis, a classificação de confiança da avaliação é fornecida abaixo:

   **Disponibilidade de pontos de dados** | **Classificação de confiança**
   --- | ---
   0%-20% | 1 Estrela
   21%-40% | 2 Estrelas
   41%-60% | 3 Estrelas
   61%-80% | 4 Estrelas
   81%-100% | 5 Estrelas

### <a name="low-confidence-ratings"></a>Classificações de confiança baixa

Algumas das razões por que motivo uma avaliação foi possível obter uma classificação de confiança baixa:

- Não crie perfis para seu ambiente durante o período para o qual está a criar a avaliação. Por exemplo, se criar a avaliação com duração de desempenho definida para 1 dia, terá de aguardar pelo menos um dia após iniciar a deteção para todos os pontos de dados obter recolhidos.
- Algumas VMs foram encerradas durante o período para o qual a avaliação é calculada. Se quaisquer VMs desligadas durante algum, a avaliação do servidor de migrar do Azure não é possível recolher os dados de desempenho durante esse período.
- Algumas VMs foram criadas no período para o qual a avaliação é calculada. Por exemplo, se criar uma avaliação para o histórico de desempenho do último mês, mas algumas VMs foram criadas no ambiente há apenas uma semana, o histórico de desempenho das novas VMs não será lá para o período completo.

  > [!NOTE]
  > Se a classificação de confiança de qualquer avaliação for inferior a cinco estrelas, recomendamos que aguarde, pelo menos, um dia para a aplicação para o ambiente de perfil e, em seguida, recalcular a avaliação. Se não o fizer, dimensionamento com base no desempenho poderá não ser fiável e podemos e recomendado que altere a avaliação para utilizar como o dimensionamento no local.
  
## <a name="monthly-cost-estimation"></a>Estimativa de custo mensal

Depois de concluir as recomendações de dimensionamento, o Azure Migrate calcula os custos de computação e armazenamento para após a migração.

- **Custo de computação**: Utilizar o tamanho recomendado da VM do Azure, Azure Migrate utiliza a API de faturação para calcular o custo mensal para a VM.
    - O cálculo leva o sistema operativo, do software assurance, instâncias reservadas, a VM de tempo de atividade, localização e as configurações de moeda em conta.
    - Agrega o custo em todas as máquinas, para calcular o custo de computação mensal total.
- **Custo de armazenamento**: O custo de armazenamento mensal de uma máquina é calculado ao agregar o custo mensal de todos os discos ligados à máquina
    - Avaliação de servidor migrar do Azure calcula os custos de armazenamento mensal total ao agregar os custos de armazenamento de todas as máquinas.
    - Atualmente, o cálculo não tire oferece especificado nas definições de avaliação em conta.

Os custos são exibidos na moeda especificada nas definições de avaliação.


## <a name="next-steps"></a>Passos Seguintes

Criar uma avaliação para [VMs de VMware](tutorial-assess-vmware.md) ou [VMs de Hyper-V](tutorial-assess-hyper-v.md).
