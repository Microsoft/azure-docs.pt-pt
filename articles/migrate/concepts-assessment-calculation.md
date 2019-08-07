---
title: Cálculos de avaliação em migrações para Azure | Microsoft Docs
description: Fornece uma visão geral dos cálculos de avaliação no serviço migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: raynew
ms.openlocfilehash: 4511c42514a5399d41029b61297bd4c1b0b63d9a
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827541"
---
# <a name="assessment-calculations-in-azure-migrate"></a>Cálculos de avaliação em migrações para Azure

As migrações para [Azure](migrate-services-overview.md) fornecem um hub central para acompanhar a descoberta, avaliação e migração de seus aplicativos e cargas de trabalho locais. Ele também controla suas instâncias de nuvem pública e privada para o Azure. O Hub oferece ferramentas de migração do Azure para avaliação e migração, bem como ofertas de ISVs (fornecedores independentes de software) de terceiros.

A avaliação do servidor é uma ferramenta em migrações para Azure que avalia servidores locais para migração para o Azure. Este artigo fornece informações sobre como as avaliações são calculadas.

## <a name="whats-in-an-assessment"></a>Novidades nas avaliações

**Propriedade** | **Detalhes**
--- | ---
**Localização de destino** | Especifica o local do Azure para o qual você deseja migrar.<br/><br/>Atualmente, a avaliação do servidor dá suporte a estas regiões de destino: Leste da Austrália, sudeste da Austrália, sul do Brasil, centro do Canadá, leste do Canadá, Índia central, EUA Central, Leste da China, Norte da China, Ásia Oriental, leste dos EUA, leste dos dos EUA 2, Alemanha central, Alemanha nordeste, leste do Japão, oeste do Japão, Coreia central, sul da Coreia, norte EUA Central, Europa Setentrional, Sul EUA Central, Sudeste Asiático, sul da Índia, Sul do Reino Unido, Oeste do Reino Unido, US Gov Arizona, US Gov Texas, US Gov-Virgínia, West EUA Central, Europa Ocidental, oeste da Índia, oeste dos EUA e West dos EUA 2.
**Tipo de armazenamento** | Especifica o tipo de discos que você deseja usar para armazenamento no Azure. <br/><br/> Para o dimensionamento local, você pode especificar o tipo de disco de armazenamento de destino como gerenciado Premium, gerenciado por SSD Standard ou gerenciado por HDD Standard. Para o dimensionamento baseado em desempenho, você pode especificar o tipo de disco de armazenamento de destino como automático, gerenciado Premium, gerenciado por HDD Standard ou gerenciado por SSD Standard. Quando você especifica o tipo de armazenamento como automático, a recomendação de disco é baseada nos dados de desempenho dos discos: as operações de entrada/saída por segundo (IOPS) e taxa de transferência. <br/><br/>Se você especificar o tipo de armazenamento como Premium ou Standard, a avaliação recomendará um SKU de disco dentro do tipo de armazenamento selecionado. Se você quiser obter um SLA de VM de instância única de 99,9%, talvez queira especificar o tipo de armazenamento como discos gerenciados Premium. Isso garante que todos os discos na avaliação sejam recomendados como discos gerenciados Premium. Note que o Azure Migrate só suporta discos geridos para avaliação de migrações.
**Instâncias reservadas (RIs)** | Essa propriedade ajuda a especificar [instâncias reservadas](https://azure.microsoft.com/pricing/reserved-vm-instances/) no Azure. As estimativas de custo na avaliação levam em conta os descontos de RI. Atualmente, o RIs tem suporte apenas para ofertas pagas conforme o uso nas migrações para Azure.
**Critérios de dimensionamento** | Define os critérios a serem usados para as VMs de *tamanho certo* para o Azure. Você pode optar pelo dimensionamento *baseado em desempenho* ou dimensionar as VMs *como locais* sem considerar o histórico de desempenho.
**Histórico de desempenho** | Define a duração a ser considerada na avaliação dos dados de desempenho dos computadores. Essa propriedade é aplicável somente quando os critérios de dimensionamento são *baseados em desempenho*.
**Utilização de percentil** | Especifica o valor percentual do conjunto de amostras de desempenho a ser considerado para o dimensionamento correto. Essa propriedade é aplicável somente quando o dimensionamento é baseado em desempenho.
**Série das VMs** | Permite que você especifique a série de VMs que deseja considerar para o dimensionamento correto. Por exemplo, se você tiver um ambiente de produção que não planeja migrar para VMs da série A no Azure, poderá excluir uma série da lista ou da série e o dimensionamento à direita será feito somente na série selecionada.
**Fator de conforto** | A avaliação de servidor de migrações para Azure considera um buffer (fator de conforto) durante a avaliação. Esta memória intermédia é aplicada em cima dos dados de utilização das VMs (CPU, memória, disco e rede). O fator de conforto dá conta de problemas como utilização sazonal, histórico de desempenho breve e prováveis aumentos na utilização futura.<br/><br/> Por exemplo, uma VM com 10 núcleos e 20% de utilização resulta, normalmente, numa VM de 2 núcleos. No entanto, com um fator de conforto de 2,0 x, o resultado é uma VM de 4 núcleos.
**Oferta** | Exibe a [oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/) que você está inscrito no. O Azure Migrate calcula o custo em conformidade.
**Moeda** | Mostra a moeda de cobrança da sua conta.
**Desconto (%)** | Lista qualquer desconto específico de assinatura que você recebe na parte superior da oferta do Azure. A predefinição é 0%.
**Tempo de atividade de VM** | Se suas VMs não estiverem sendo executadas 24 horas por dia, 7 dias por semana no Azure, você poderá especificar a duração (número de dias por mês e o número de horas por dia) para as quais elas serão executadas e as estimativas de custo serão manipuladas de acordo. O valor padrão é 31 dias por mês e 24 horas por dia.
**Benefício Híbrido do Azure** | Especifica se você tem o Software Assurance e se está qualificado para [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Se definido como Sim, são considerados para as VMs do Windows preços não Microsoft Azure. A configuração padrão é sim.

## <a name="how-are-assessments-calculated"></a>Como as avaliações são calculadas?

Uma avaliação na avaliação do servidor de migrações para Azure é calculada usando os metadados coletados sobre os servidores locais. O cálculo da avaliação é tratado em três estágios. Para cada servidor, o cálculo da avaliação começa com uma análise de adequação do Azure, seguida pelo dimensionamento e pela última vez, uma estimativa de custo mensal. Um servidor se moverá para um estágio posterior somente se ele passar o anterior. Por exemplo, se um servidor falhar na verificação de adequação do Azure, ele será marcado como não adequado para o Azure, e o dimensionamento e o custo não serão feitos para esse servidor.

## <a name="azure-suitability-analysis"></a>Análise de adequação do Azure

Nem todos os computadores são adequados para execução no Azure. A avaliação do servidor avalia cada computador local para sua adequação à migração do Azure. Ele também atribui cada computador avaliado a uma das seguintes categorias de adequação:
- **Pronto para o Azure**: O computador pode ser migrado no estado em que se encontra no Azure sem nenhuma alteração. Ele será iniciado no Azure com o suporte completo do Azure.
- **Condicionalmente pronto para o Azure**: O computador pode iniciar no Azure, mas pode não ter o suporte completo do Azure. Por exemplo, um computador que está executando uma versão mais antiga do Windows Server não tem suporte no Azure. Você deve ter cuidado antes de migrar esses computadores para o Azure e seguir as diretrizes de correção sugeridas na avaliação para corrigir os problemas de preparação.
- **Não está pronto para o Azure**: O computador não será iniciado no Azure. Por exemplo, se um computador local tiver um disco de mais de 64 terabytes (TB) anexados a ele, ele não poderá ser hospedado no Azure. Você deve seguir as diretrizes de correção sugeridas na avaliação para corrigir o problema de preparação antes de migrar o computador para o Azure. O dimensionamento à direita e a estimativa de custo não são feitos para computadores marcados como não prontos para o Azure.
- **Preparação desconhecida**: As migrações para Azure não puderam determinar a prontidão do computador devido a metadados insuficientes coletados do ambiente local.

A avaliação do servidor revisa as propriedades da máquina e o sistema operacional convidado para determinar a prontidão do Azure do computador local.

### <a name="machine-properties"></a>Propriedades da máquina

A avaliação do servidor revisa as seguintes propriedades da VM local para determinar se ela pode ser executada no Azure.

**Propriedade** | **Detalhes** | **Status de preparação do Azure**
--- | --- | ---
**Tipo de inicialização** | O Azure dá suporte a VMs com um tipo de inicialização de BIOS, não UEFI. | Condicionalmente pronto se o tipo de inicialização for UEFI.
**Núcleos** | O número de núcleos nos computadores deve ser igual ou menor que o número máximo de núcleos (128) com suporte para uma VM do Azure.<br/><br/> Se o histórico de desempenho estiver disponível, as migrações para Azure considerarão os núcleos utilizados para comparação. Se um fator de conforto for especificado nas configurações de avaliação, o número de núcleos utilizados será multiplicado pelo fator de conforto.<br/><br/> Se não houver histórico de desempenho, as migrações para Azure usarão os núcleos alocados sem aplicar o fator de conforto. | Pronto se for menor ou igual a limites.
**Memória** | O tamanho da memória do computador deve ser igual ou menor que a memória máxima (3892 gigabytes [GB] no Azure série M&nbsp;Standard_M128m<sup>2</sup>) permitida para uma VM do Azure. [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Se o histórico de desempenho estiver disponível, as migrações para Azure considerarão a memória utilizada para comparação. Se um fator de conforto for especificado, a memória utilizada será multiplicada pelo fator de conforto.<br/><br/> Se não houver nenhum histórico, a memória alocada será usada sem aplicar o fator de conforto.<br/><br/> | Pronto se estiver dentro dos limites.
**Disco de armazenamento** | O tamanho alocado de um disco deve ser de 32 TB ou menos. Embora o Azure dê suporte a discos de 64 TB com discos SSD Ultra, migre o Azure: Atualmente, a avaliação de servidor verifica 32 TB como os limites de tamanho de disco, pois ele ainda não oferece suporte a SSD Ultra. <br/><br/> O número de discos anexados ao computador deve ser de 65 ou menos, incluindo o disco do sistema operacional. | Pronto se estiver dentro dos limites.
**Redes** | Um computador deve ter 32 ou menos NICs (interfaces de rede) conectados a ele. | Pronto se estiver dentro dos limites.

### <a name="guest-operating-system"></a>Sistema operacional convidado
Juntamente com as propriedades da VM, a avaliação do servidor examina o sistema operacional convidado das máquinas para determinar se ele pode ser executado no Azure.

> [!NOTE]
> Para VMs VMware, a avaliação do servidor usa o sistema operacional especificado para a VM no vCenter Server para lidar com a análise do SO convidado. Para VMs do Linux em execução no VMware, atualmente ele não identifica a versão exata do kernel do sistema operacional convidado.

A lógica a seguir é usada pela avaliação do servidor para identificar a prontidão do Azure com base no sistema operacional.

**Sistema Operacional** | **Detalhes** | **Status de preparação do Azure**
--- | --- | ---
Windows Server 2016 & todos os SPs | O Azure fornece suporte completo. | Preparado para o Azure
Windows Server 2012 R2 & todos os SPs | O Azure fornece suporte completo. | Preparado para o Azure
Windows Server 2012 & todos os SPs | O Azure fornece suporte completo. | Preparado para o Azure
Windows Server 2008 R2 com todos os SPs | O Azure fornece suporte completo.| Preparado para o Azure
Windows Server 2008 (32 bits e 64 bits) | O Azure fornece suporte completo. | Preparado para o Azure
Windows Server 2003, 2003 R2 | Esses sistemas operacionais passaram sua data de fim de suporte e precisam de um [contrato de suporte personalizado (CSA)](https://aka.ms/WSosstatement) para dar suporte no Azure. | Condicionalmente pronto para o Azure. Considere atualizar o sistema operacional antes de migrar para o Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Esses sistemas operacionais passaram pela data de fim do suporte. O computador pode iniciar no Azure, mas o Azure não fornece suporte ao sistema operacional. | Condicionalmente pronto para o Azure. Recomendamos que você atualize o sistema operacional antes de migrar para o Azure.
Windows Client 7, 8 e 10 | O Azure fornece suporte [apenas com a assinatura do Visual Studio.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Condicionalmente preparado para o Azure
Área de trabalho do Windows 10 pro | O Azure fornece suporte com [direitos de hospedagem multilocatário.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Condicionalmente preparado para o Azure
Windows Vista, XP Professional | Esses sistemas operacionais passaram pela data de fim do suporte. O computador pode iniciar no Azure, mas o Azure não fornece suporte ao sistema operacional. | Condicionalmente pronto para o Azure. Recomendamos que você atualize o sistema operacional antes de migrar para o Azure.
Linux | O Azure endossa esses [sistemas operacionais Linux](../virtual-machines/linux/endorsed-distros.md). Outros sistemas operacionais Linux podem ser iniciados no Azure, mas é recomendável que você atualize o sistema operacional para uma versão endossada antes de migrar para o Azure. | Pronto para o Azure se a versão for endossada.<br/><br/>Condicionalmente pronto se a versão não for endossada.
Outros sistemas operacionais<br/><br/> Por exemplo, Oracle Solaris, Apple Mac OS, etc., FreeBSD, etc. | O Azure não endossa esses sistemas operacionais. O computador pode iniciar no Azure, mas o Azure não fornece suporte ao sistema operacional. | Condicionalmente pronto para o Azure. Recomendamos que você instale um sistema operacional com suporte antes de migrar para o Azure.  
Sistema operacional especificado como **outro** no vCenter Server | As migrações para Azure não podem identificar o sistema operacional nesse caso. | Preparação desconhecida. Verifique se o sistema operacional em execução dentro da VM tem suporte no Azure.
sistemas operacionais de 32 bits | O computador pode iniciar no Azure, mas o Azure pode não fornecer suporte completo. | Condicionalmente pronto para o Azure. Considere atualizar o sistema operacional do computador de um sistema operacional de 32 bits para o sistema operacional de 64 bits antes de migrar para o Azure.

## <a name="sizing"></a>Dimensionamento

Depois que um computador é marcado como pronto para o Azure, a avaliação do servidor faz recomendações de dimensionamento, o que envolve identificar a VM do Azure e o SKU de disco apropriados para a VM local. Essas recomendações variam, dependendo das propriedades de avaliação especificadas.

- Se a avaliação usar o *dimensionamento baseado em desempenho*, as migrações para Azure considerarão o histórico de desempenho do computador para identificar o tamanho da VM e o tipo de disco no Azure. Esse método é especialmente útil se você tiver alocado a VM local com excesso, mas a utilização estiver baixa e você quiser dimensionar a VM no Azure para economizar custos. Esse método ajudará você a otimizar os tamanhos durante a migração.
- Se você não quiser considerar os dados de desempenho para o dimensionamento da VM e desejar colocar os computadores locais no estado em que se encontram no Azure, poderá definir os critérios de dimensionamento *como locais*. Em seguida, a avaliação do servidor irá dimensionar as VMs com base na configuração local sem considerar os dados de utilização. Nesse caso, as atividades de dimensionamento de disco são baseadas no tipo de armazenamento que você especifica nas propriedades de avaliação (HDD Standard, SSD Standard ou discos Premium).

### <a name="performance-based-sizing"></a>Dimensionamento baseado em desempenho

Para o dimensionamento baseado em desempenho, a avaliação do servidor começa com os discos anexados à VM, seguidos por adaptadores de rede. Em seguida, ele mapeia uma SKU de VM do Azure com base nos requisitos de computação da VM local. O dispositivo de migrações para Azure faz o perfil do ambiente local para coletar dados de desempenho para CPU, memória, discos e adaptador de rede.

**Etapas da coleta de dados de desempenho:**

1. Para VMs VMware, o dispositivo migrações para Azure coleta um ponto de exemplo em tempo real em cada intervalo de 20 segundos. Para VMs do Hyper-V, o ponto de exemplo em tempo real é coletado a cada intervalo de 30 segundos.
1. O dispositivo acumula os pontos de exemplo coletados a cada 10 minutos e envia o valor máximo para a avaliação dos últimos 10 minutos para o servidor.
1. A avaliação do servidor armazena todos os pontos de exemplo de 10 minutos para o último mês. Em seguida, dependendo das propriedades de avaliação especificadas para o *histórico de desempenho* e a utilização de *percentil*, ele identifica o ponto de dados apropriado a ser usado para o dimensionamento correto. Por exemplo, se o histórico de desempenho for definido como 1 dia e a utilização do percentil for o 95 º percentil, a avaliação do servidor usará os pontos de exemplo de 10 minutos para o último dia, os classificará em ordem crescente e escolherá o valor do 95 º percentil para o dimensionamento correto.
1. Esse valor é multiplicado pelo fator de conforto para obter os dados de utilização de desempenho efetivos de cada métrica (utilização de CPU, utilização de memória, IOPS de disco (leitura e gravação), taxa de transferência de disco (leitura e gravação) e taxa de transferência de rede (dentro e fora) que o o dispositivo coleta.

Depois que o valor de utilização efetivo é determinado, o armazenamento, a rede e o dimensionamento de computação são tratados da seguinte maneira.

**Dimensionamento de armazenamento**: As migrações para Azure tentam Mapear todos os discos anexados à máquina para um disco no Azure.

> [!NOTE]
> A avaliação do servidor de migrações para Azure dá suporte apenas a discos gerenciados para avaliação.

  - A avaliação do servidor adiciona o IOPS de leitura e gravação de um disco para obter o IOPS total necessário. Da mesma forma, ele adiciona os valores de taxa de transferência de leitura e gravação para obter a taxa de transferência total de cada disco.
  - Se você tiver especificado o tipo de armazenamento como automático, com base nos valores de taxa de transferência e IOPS efetivos, a avaliação do servidor determinará se o disco deve ser mapeado para um HDD padrão, SSD Standard ou um disco Premium no Azure. Se o tipo de armazenamento estiver definido como HDD Standard/SSD/Premium, a avaliação do servidor tentará encontrar um SKU de disco dentro do tipo de armazenamento selecionado (discos HDD Standard/SSD/Premium).
  - Se a avaliação do servidor não conseguir localizar um disco com a IOPS e a taxa de transferência necessárias, ela marcará a máquina como inadequada para o Azure.
  - Se a avaliação do servidor encontrar um conjunto de discos adequados, ela selecionará os discos que dão suporte ao local especificado nas configurações de avaliação.
  - Se houver vários discos qualificados, a avaliação do servidor selecionará o disco com o menor custo.
  - Se os dados de desempenho de qualquer disco não estiverem disponíveis, os dados de configuração do disco (tamanho do disco) serão usados para localizar um disco SSD padrão no Azure.

**Dimensionamento de rede**: A avaliação do servidor tenta encontrar uma VM do Azure que pode dar suporte ao número de adaptadores de rede anexados à máquina local e o desempenho exigido por esses adaptadores de rede.
- Para obter o desempenho de rede efetivo da VM local, a avaliação do servidor agrega os dados transmitidos por segundo (MBps) do computador (saída de rede), em todos os adaptadores de rede, e aplica o fator de conforto. Ele usa esse número para encontrar uma VM do Azure que pode dar suporte ao desempenho de rede necessário.
- Junto com o desempenho da rede, a avaliação do servidor também considera se a VM do Azure pode dar suporte ao número necessário de adaptadores de rede.
- Se nenhum dado de desempenho de rede estiver disponível, a avaliação do servidor considerará apenas a contagem do adaptador de rede para o dimensionamento da VM.

**Dimensionamento de computação**: Depois de calcular os requisitos de armazenamento e de rede, a avaliação do servidor considera os requisitos de CPU e memória para encontrar um tamanho de VM adequado no Azure.
- As migrações para Azure analisam os núcleos e a memória utilizados em vigor para encontrar um tamanho de VM adequado no Azure.
- Se nenhum tamanho adequado for encontrado, o computador será marcado como inadequado para o Azure.
- Se um tamanho adequado for encontrado, as migrações para Azure aplicarão os cálculos de armazenamento e rede. Em seguida, ele aplica as configurações de localização e tipo de preço para a recomendação final de tamanho de VM.
- Se existirem vários tamanhos de VMs do Azure elegíveis, é recomendado aquele que tiver o custo mais baixo.

### <a name="as-on-premises-sizing"></a>Como dimensionamento local

Se você usar *como dimensionamento local*, a avaliação do servidor não considerará o histórico de desempenho das VMs e dos discos. Em vez disso, ele aloca um SKU de VM no Azure com base no tamanho alocado no local. Da mesma forma para o dimensionamento de disco, a avaliação do servidor examina o tipo de armazenamento especificado nas propriedades de avaliação (HDD Standard/SSD/Premium) e recomenda o tipo de disco de acordo. O tipo de armazenamento padrão é os discos Premium.

## <a name="confidence-ratings"></a>Classificações de confiança
Cada avaliação baseada em desempenho nas migrações para Azure está associada a uma classificação de confiança que varia de uma (mais baixa) a cinco estrelas (mais alta).
- A classificação de confiança é alocada a uma avaliação com base na disponibilidade dos pontos de dados necessários para calcular a avaliação.
- A classificação de confiança de uma avaliação ajuda a calcular a fiabilidade das recomendações de tamanho fornecidas pelo Azure Migrate.
- As classificações de confiança não são aplicáveis para *as avaliações locais* .
- Para o dimensionamento baseado em desempenho, as necessidades de avaliação do servidor:
    - Os dados de utilização da CPU e da memória da VM.
    - Os dados de taxa de transferência e IOPS de disco para cada disco anexado à VM.
    - A e/s de rede para lidar com o dimensionamento baseado em desempenho para cada adaptador de rede anexado a uma VM.

   Se qualquer um desses números de utilização estiver indisponível em vCenter Server, a recomendação de tamanho poderá não ser confiável.

Dependendo da porcentagem de pontos de dados disponíveis, a classificação de confiança para a avaliação é a seguinte.

   **Disponibilidade de pontos de dados** | **Classificação de confiança**
   --- | ---
   0-20% | 1 estrela
   21-40% | 2 estrelas
   41-60% | 3 estrelas
   61-80% | 4 estrelas
   81-100% | 5 estrelas

### <a name="low-confidence-ratings"></a>Classificações de baixa confiança

Aqui estão algumas razões pelas quais uma avaliação pode obter uma classificação de baixa confiança:

- Você não fez o profile do seu ambiente pela duração para a qual está criando a avaliação. Por exemplo, se você criar a avaliação com duração de desempenho definida como um dia, deverá aguardar pelo menos um dia depois de iniciar a descoberta para todos os pontos de dados a serem coletados.
- Algumas VMs foram desligadas durante o período para o qual a avaliação foi calculada. Se alguma VM for desativada por alguma duração, a avaliação do servidor não poderá coletar os dados de desempenho para esse período.
- Algumas VMs foram criadas durante o período para o qual a avaliação foi calculada. Por exemplo, se você criou uma avaliação para o histórico de desempenho do último mês, mas algumas VMs foram criadas no ambiente apenas uma semana atrás, o histórico de desempenho das novas VMs não existirá durante toda a duração.

> [!NOTE]
> Se a classificação de confiança de qualquer avaliação for menor que cinco estrelas, recomendamos que você aguarde pelo menos um dia para o dispositivo criar o perfil do ambiente e recalcular a avaliação. Caso contrário, o dimensionamento baseado em desempenho pode não ser confiável. Nesse caso, recomendamos que você alterne a avaliação para o dimensionamento local.

## <a name="monthly-cost-estimation"></a>Estimativa de custo mensal

Após a conclusão das recomendações de dimensionamento, as migrações para Azure calculam os custos de computação e armazenamento para após a migração.

- **Custo de computação**: Usando o tamanho recomendado da VM do Azure, as migrações para Azure usam a API de cobrança para calcular o custo mensal da VM.
    - O cálculo usa o sistema operacional, o Software Assurance, as instâncias reservadas, o tempo de atividade da VM, o local e as configurações de moeda em conta.
    - Ele agrega o custo em todas as máquinas para calcular o custo de computação mensal total.
- **Custo de armazenamento**: O custo de armazenamento mensal de um computador é calculado agregando-se o custo mensal de todos os discos anexados à máquina, da seguinte maneira:
    - A avaliação do servidor calcula o total de custos de armazenamento mensal agregando os custos de armazenamento de todas as máquinas.
    - Atualmente, o cálculo não considera as ofertas especificadas nas configurações de avaliação.

Os custos são exibidos na moeda especificada nas configurações de avaliação.


## <a name="next-steps"></a>Passos Seguintes

Crie uma avaliação para VMs [VMware](tutorial-assess-vmware.md) ou [VMs do Hyper-V](tutorial-assess-hyper-v.md).
