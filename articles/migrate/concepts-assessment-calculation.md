---
title: Cálculos de avaliação no Azure Migrate | Documentos da Microsoft
description: Fornece uma visão geral dos cálculos de avaliação no serviço Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: raynew
ms.openlocfilehash: 012a352b00de2e2d1bf64fd18125ddd10faba5cd
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58122812"
---
# <a name="assessment-calculations"></a>Cálculos de avaliação

[O Azure Migrate](migrate-overview.md) avalia as cargas de trabalho no local para migração para o Azure. Este artigo fornece informações sobre como são calculadas as avaliações.


## <a name="overview"></a>Descrição geral

Uma avaliação do Azure Migrate tem três fases. Avaliação começa com uma análise de adequação, seguida de dimensionamento, e por último, estimativa de custos mensais. Uma máquina apenas move ao longo para numa fase posterior se for aprovado relativamente anterior. Por exemplo, se um computador falhar a verificação de adequação do Azure, está marcada como não adequado para dimensionamento e custos e do Azure, a não ser feito.

## <a name="azure-suitability-analysis"></a>Análise de adequação do Azure

Nem todas as máquinas são adequadas para execução na cloud, como nuvem tem seus próprios requisitos e limitações. O Azure Migrate avalia cada máquina no local para a aptidão de migração para o Azure e categoriza as máquinas em uma das seguintes categorias:
- **Preparado para o Azure** -a máquina pode ser migrada como-é para o Azure sem quaisquer alterações. Ele será inicializado no Azure com suporte completo do Azure.
- **Condicionalmente preparado para o Azure** -a máquina pode arrancar no Azure, mas pode não ter suporte total do Azure. Por exemplo, uma máquina com uma versão antiga do SO do Windows Server não é suportada no Azure. Precisa ter cuidado antes de a migração desses computadores para o Azure e siga as orientações de remediação sugeridas na avaliação para corrigir os problemas de preparação antes de migrar.
- **Não preparado para o Azure** -a máquina não irão arrancar no Azure. Por exemplo, se uma máquina no local tem um disco de tamanho de mais de 4 TB ligados ao mesmo, não pode ser alojada no Azure. Tem de seguir as diretrizes de remediação sugeridas na avaliação para corrigir o problema de preparação antes de migrar para o Azure. Estimativa de redimensionamento e de custo não é feita para as máquinas que estão marcadas como não preparado para o Azure.
- **Preparação desconhecida** -Azure Migrate não foi possível localizar a preparação da máquina devido a dados suficientes disponíveis no vCenter Server.

O Azure Migrate analisa as propriedades de máquina e o sistema operativo convidado para identificar a preparação para o Azure da máquina no local.

### <a name="machine-properties"></a>Propriedades da máquina
O Azure Migrate analisa as seguintes propriedades da VM no local para identificar se uma VM pode ser executado no Azure.

**Propriedade** | **Detalhes** | **Estado de preparação para o Azure**
--- | --- | ---
**Tipo de arranque** | O Azure suporta VMs com o tipo de arranque como o BIOS e UEFI não. | Condicionalmente preparado se é o tipo de arranque UEFI.
**Núcleos** | O número de núcleos nas máquinas tem de ser igual ou menor que o número máximo de núcleos (128 núcleos) suportada para uma VM do Azure.<br/><br/> Se o histórico de desempenho está disponível, o Azure Migrate considera os núcleos utilizados para comparação. Se for um fator de conforto é especificado nas definições de avaliação, o número de núcleos utilizados é multiplicado pelo fator de conforto.<br/><br/> Se não houver nenhum histórico de desempenho, o Azure Migrate utiliza núcleos alocados, sem aplicar o fator de conforto. | Pronto, se tiver menos do que ou igual a limites.
**Memória** | O tamanho de memória da máquina tem de ser igual ou menor que o máximo de memória (GB 3892 na série de M de Azure Standard_M128m&nbsp;<sup>2</sup>) permitido para uma VM do Azure. [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Se o histórico de desempenho está disponível, o Azure Migrate considera a memória utilizada para comparação. Se não for especificado um fator de conforto, a memória utilizada é multiplicada pelo fator de conforto.<br/><br/> Se não houver nenhum histórico é utilizada a memória alocada, sem aplicar o fator de conforto.<br/><br/> | Pronto se dentro dos limites.
**Disco de armazenamento** | Tamanho atribuído de um disco deve ser de 4 TB (4096 GB) ou menos.<br/><br/> O número de discos ligados à máquina tem de ser 65 ou menos, incluindo o disco do SO. | Pronto se dentro dos limites.
**Redes** | Uma máquina tem de 32 ou menos NICs ligadas à mesma. | Pronto se dentro dos limites.

### <a name="guest-operating-system"></a>Sistema operativo convidado
Juntamente com as propriedades da VM, do Azure Migrate também analisa o sistema operacional convidado da VM no local para identificar se a VM pode ser executado no Azure.

> [!NOTE]
> O Azure Migrate considera o sistema operacional especificado no vCenter Server para fazer a análise seguinte. Uma vez que a descoberta feita pelo Azure Migrate é baseado no dispositivo, não tem uma forma de verificar se o sistema operacional em execução dentro da VM é a mesmo que especificado num vCenter Server.

A seguinte lógica é utilizada pelo Azure Migrate para identificar a preparação para o Azure da VM com base no sistema operativo.

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

Depois de uma máquina é marcada como preparado para o Azure, Azure Migrate tamanhos de VM e os respetivos discos para o Azure. Se o critério de dimensão especificado nas propriedades de avaliação é fazer o dimensionamento com base no desempenho, o Azure Migrate considera o histórico de desempenho da máquina para identificar o tipo de disco e de tamanho VM no Azure. Este método é útil em cenários em que alocou demasiado a VM no local, mas a utilização for baixa e gostaria de dimensionar as VMs no Azure para poupar no custo.

Se não pretender considerar o histórico de desempenho para o dimensionamento de VM e deseja levar a VM como-é para o Azure, pode especificar o critério de dimensionamento como *como no local* e o Azure Migrate, em seguida, irá dimensionar as VMs com base no local configuração sem considerar os dados de utilização. Disco de tamanho, neste caso, será feita com base no tipo de armazenamento que especificar nas propriedades de avaliação (disco Standard ou disco Premium)

### <a name="performance-based-sizing"></a>Dimensionamento com base no desempenho

Para o dimensionamento baseado no desempenho, do Azure Migrate começa com os discos ligados à VM, seguido de adaptadores de rede e, em seguida, mapas de uma VM do Azure com base nos requisitos de computação da VM no local.

- **Armazenamento**: O Azure Migrate tenta mapear cada disco ligado à máquina para um disco no Azure.

    > [!NOTE]
    > O Azure Migrate suporta apenas os discos para a avaliação de geridos.

    - Para obter a e/s de disco eficiente por segundo (IOPS) e o débito (MBps), do Azure Migrate multiplica o IOPS de disco e a taxa de transferência com o fator de conforto. Com base nos valores de débito e IOPS em vigor, Azure Migrate identificar se o disco deve ser mapeado para um disco standard ou premium no Azure.
    - Se o Azure Migrate não é possível localizar um disco com o IOPS e do débito necessário, marca o computador que não são adequados para o Azure. [Saiba mais](../azure-subscription-service-limits.md#storage-limits) sobre o Azure limita por disco e a VM.
    - Se ele encontrar um conjunto de discos adequados, o Azure Migrate seleciona os que suportam o método de redundância de armazenamento e a localização especificada nas definições de avaliação.
    - Se existirem vários discos elegíveis, seleciona aquele com o custo mais baixo.
    - Se os dados de desempenho para discos no indisponível, todos os discos estão mapeados para os discos standard no Azure.

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
Se o critério de dimensionamento for *no local dimensionamento*, do Azure Migrate não considera o histórico de desempenho das VMs e discos e aloca um SKU de VM no Azure com base no tamanho atribuído no local. Da mesma forma para o dimensionamento do disco, ele examina o tipo de armazenamento especificado nas propriedades de avaliação (Standard/Premium) e recomenda o tipo de disco em conformidade. Tipo de armazenamento predefinido é de discos Premium.

### <a name="confidence-rating"></a>Classificação de confiança
Cada avaliação baseada no desempenho no Azure Migrate é associada a uma classificação de confiança de 1 a 5 estrelas (sendo que 1 estrela corresponde à mais baixa e 5 à mais alta). A classificação de confiança é alocada a uma avaliação com base na disponibilidade dos pontos de dados necessários para calcular a avaliação. A classificação de confiança de uma avaliação ajuda a calcular a fiabilidade das recomendações de tamanho fornecidas pelo Azure Migrate. A classificação de confiança não é aplicável como avaliações no local.

Para o dimensionamento com base em desempenho, o Azure Migrate requer os dados de utilização da CPU, da memória e da VM. Além disso, para cada disco ligado à VM, é necessário o IOPS do disco e os dados de débito. De forma semelhante, para cada adaptador de rede anexado a uma VM, o Azure Migrate requer a entrada/saída de rede para efetuar o dimensionamento com base no desempenho. Se algum dos números de utilização acima não estiver disponível no vCenter Server, a recomendação de tamanho feita pelo Azure Migrate poderá não ser fiável. Consoante a percentagem de pontos de dados disponíveis, a classificação de confiança da avaliação é fornecida abaixo:

   **Disponibilidade de pontos de dados** | **Classificação de confiança**
   --- | ---
   0%-20% | 1 Estrela
   21%-40% | 2 Estrelas
   41%-60% | 3 Estrelas
   61%-80% | 4 Estrelas
   81%-100% | 5 Estrelas

   Abaixo estão os motivos pelos quais relativamente à razão pela qual uma avaliação foi possível obter uma classificação de confiança baixa:

- Não analisou o ambiente durante o tempo para a qual está a criar a avaliação. Por exemplo, se estiver a criar a avaliação com duração de desempenho definida como um dia, terá de aguardar pelo menos um dia após iniciar a deteção para todos os pontos de dados serem recolhidos.

- Poucas VMs foram encerradas durante o período para o qual a avaliação é calculada. Se tiver desligado alguma VM durante algum tempo, não poderemos recolher os dados de desempenho correspondentes a esse período.

- Poucas VMs foram criadas no período para o qual a avaliação é calculada. Por exemplo, se estiver a criar uma avaliação para o histórico de desempenho do último mês, mas poucas VMs tiverem sido criadas no ambiente há apenas uma semana. Em casos como este, o histórico de desempenho das novas VMs não estará lá para o período completo.

  > [!NOTE]
  > Se a classificação de confiança de qualquer avaliação for inferior a 5 estrelas, recomendamos que aguarde, pelo menos, um dia para a aplicação para o ambiente de perfil e, em seguida *recalcular* a avaliação. Se não conseguir fazê-lo, o dimensionamento baseado em desempenho poderá não ser fiável e recomenda-se que mude para o *dimensionamento no local* ao alterar as propriedades de avaliação.

## <a name="monthly-cost-estimation"></a>Estimativa de custo mensal

Depois de concluir as recomendações de dimensionamento, o Azure Migrate calcula os custos de computação e armazenamento de pós-migração.

- **Custo de computação**: Utilizar o tamanho recomendado da VM do Azure, Azure Migrate utiliza a API de faturação para calcular o custo mensal para a VM. O cálculo leva o sistema operativo, do software assurance, instâncias reservadas, a VM de tempo de atividade, localização e as configurações de moeda em conta. Agrega o custo em todas as máquinas, para calcular o custo de computação mensal total.
- **Custo de armazenamento**: O custo de armazenamento mensal de uma máquina é calculado ao agregar o custo mensal de todos os discos ligados à máquina. O Azure Migrate calcula os custos de armazenamento mensal total ao agregar os custos de armazenamento de todas as máquinas. Atualmente, o cálculo não tire oferece especificado nas definições de avaliação em conta.

Os custos são exibidos na moeda especificada nas definições de avaliação.


## <a name="next-steps"></a>Passos Seguintes

[Criar uma avaliação para VMs de VMware no local](tutorial-assessment-vmware.md)
