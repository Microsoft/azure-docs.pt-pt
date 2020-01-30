---
title: Avaliações em migrações para Azure
description: Saiba mais sobre as avaliações nas migrações para Azure.
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 5fc61d9987c9e728a5d83cb3ab3f91b8e8f5f740
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76833333"
---
# <a name="about-assessments-in-azure-migrate"></a>Sobre avaliações em migrações para Azure

Este artigo descreve como as avaliações são calculadas nas [migrações para Azure: avaliação do servidor](migrate-services-overview.md#azure-migrate-server-assessment-tool). Você executa avaliações em grupos de computadores locais, para descobrir se eles estão prontos para migração para migrações para Azure.

## <a name="how-do-i-run-an-assessment"></a>Como fazer executar uma avaliação?
Você pode executar uma avaliação usando as migrações para Azure: avaliação do servidor ou outra ferramenta do Azure ou de terceiros. Depois de criar um projeto de migrações para Azure, você adiciona a ferramenta necessária. [Saiba mais](how-to-add-tool-first-time.md)

### <a name="collect-compute-data"></a>Coletar dados de computação

Os dados de desempenho para configurações de computação são coletados da seguinte maneira:

1. O [dispositivo de migrações para Azure](migrate-appliance.md) coleta um ponto de exemplo em tempo real:

    - **VMs VMware**: para VMs VMware, o dispositivo migrações para Azure coleta um ponto de exemplo em tempo real em cada intervalo de 20 segundos.
    - **VMs do Hyper-v**: para VMs do Hyper-v, o ponto de exemplo em tempo real é coletado a cada intervalo de 30 segundos.
    - **Servidores físicos**: para servidores físicos, o ponto de exemplo em tempo real é coletado a cada intervalo de cinco minutos. 
    
2. O dispositivo acumula os pontos de exemplo (20 segundos, 30 segundos, cinco minutos) para criar um único ponto de dados a cada 10 minutos. Para criar o ponto de dados único, o dispositivo seleciona o valor de pico de todos os exemplos e, em seguida, o envia para o Azure.
3. A avaliação do servidor armazena todos os pontos de exemplo de 10 minutos do último mês.
4. Quando você cria uma avaliação, a avaliação do servidor identifica o ponto de dados apropriado a ser usado para o dimensionamento correto, com base nos valores de percentil para o *histórico de desempenho* e *utilização de percentil*.

    - Por exemplo, se o histórico de desempenho for definido como uma semana e a utilização do percentil for o 95 º percentil, a avaliação do servidor classificará os pontos de exemplo de 10 minutos da última semana em ordem crescente e selecionará o valor de 95 º percentil para o dimensionamento correto. 
    - O valor do 95 º percentil garante que você ignore quaisquer exceções, que poderão ser incluídas se você escolher o 99 º percentil.
    - Se você quiser escolher o pico de uso do período e não quiser perder as exceções, deverá selecionar o 99 º percentil para utilização de percentil.

5. Esse valor é multiplicado pelo fator de conforto para obter os dados de utilização de desempenho efetivos de cada métrica (utilização de CPU, utilização de memória, IOPS de disco (leitura e gravação), taxa de transferência de disco (leitura e gravação) e taxa de transferência de rede (dentro e fora) que o o dispositivo coleta.

Para executar avaliações na avaliação do servidor, você se prepara para a avaliação local e no Azure e configura o dispositivo de migrações para Azure para descobrir continuamente computadores locais. Depois que os computadores são descobertos, você os reúne em grupos para avaliá-los. Para obter avaliações mais detalhadas e de alta confiança, você pode visualizar e mapear as dependências entre as máquinas, para descobrir como migrá-las.

- Saiba mais sobre a execução de Avaliações para [VMs VMware](tutorial-prepare-vmware.md), [VMs Hyper-V](tutorial-prepare-hyper-v.md)e [servidores físicos](tutorial-prepare-physical.md).
- Saiba mais sobre como avaliar servidores [importados com um arquivo CSV](tutorial-assess-import.md).
- Saiba mais sobre como configurar a [visualização de dependência](concepts-dependency-visualization.md).

## <a name="assessments-in-server-assessment"></a>Avaliações na avaliação do servidor 

As avaliações criadas com a avaliação do servidor de migrações para Azure são um instantâneo de dados pontual. A ferramenta de avaliação do servidor fornece dois tipos de avaliações.

**Tipo de avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Baseado em desempenho** | Avaliações que fazem recomendações com base nos dados de desempenho coletados | A recomendação de tamanho da VM baseia-se nos dados de utilização de CPU e memória.<br/><br/> A recomendação de tipo de disco (HD padrão/SSD ou discos gerenciados Premium) baseia-se na IOPS e na taxa de transferência dos discos locais.
**No estado em que se encontra no local** | Avaliações que não usam dados de desempenho para fazer recomendações. | A recomendação de tamanho da VM é baseada no tamanho da VM local<br/><br> O tipo de disco recomendado é baseado no tipo de armazenamento selecionado para a avaliação.

## <a name="collecting-performance-data"></a>Coletando dados de desempenho

Os dados de desempenho são coletados da seguinte maneira:

1. O [dispositivo de migrações para Azure](migrate-appliance.md) coleta um ponto de exemplo em tempo real:

    - **VMs VMware**: para VMs VMware, o dispositivo migrações para Azure coleta um ponto de exemplo em tempo real em cada intervalo de 20 segundos.
    - **VMs do Hyper-v**: para VMs do Hyper-v, o ponto de exemplo em tempo real é coletado a cada intervalo de 30 segundos.
    - **Servidores físicos**: para servidores físicos, o ponto de exemplo em tempo real é coletado a cada intervalo de cinco minutos. 
    
2. O dispositivo acumula os pontos de exemplo (20 segundos, 30 segundos, cinco minutos) para criar um único ponto de dados a cada 10 minutos. Para criar o ponto de dados único, o dispositivo seleciona o valor de pico de todos os exemplos e, em seguida, o envia para o Azure.
3. A avaliação do servidor armazena todos os pontos de exemplo de 10 minutos do último mês.
4. Quando você cria uma avaliação, a avaliação do servidor identifica o ponto de dados apropriado a ser usado para o dimensionamento correto, com base nos valores de percentil para o *histórico de desempenho* e *utilização de percentil*.

    - Por exemplo, se o histórico de desempenho for definido como uma semana e a utilização do percentil for o 95 º percentil, a avaliação do servidor classificará os pontos de exemplo de 10 minutos da última semana em ordem crescente e selecionará o valor de 95 º percentil para o dimensionamento correto. 
    - O valor do 95 º percentil garante que você ignore quaisquer exceções, que poderão ser incluídas se você escolher o 99 º percentil.
    - Se você quiser escolher o pico de uso do período e não quiser perder as exceções, deverá selecionar o 99 º percentil para utilização de percentil.

5. Esse valor é multiplicado pelo fator de conforto para obter os dados de utilização de desempenho efetivos de cada métrica (utilização de CPU, utilização de memória, IOPS de disco (leitura e gravação), taxa de transferência de disco (leitura e gravação) e taxa de transferência de rede (dentro e fora) que o o dispositivo coleta.
## <a name="whats-in-an-assessment"></a>Novidades nas avaliações

Aqui está o que está incluído em uma avaliação em migrações para Azure: avaliação do servidor.

**Propriedade** | **Detalhes**
--- | ---
**Localização de destino** | O local para o qual você deseja migrar. Atualmente, a avaliação do servidor dá suporte a essas regiões do Azure de destino:<br/><br/> Leste da Austrália, sudeste da Austrália, sul do Brasil, centro do Canadá, leste do Canadá, Índia central, EUA Central, Leste da China, Norte da China, Ásia Oriental, leste dos EUA, leste dos dos EUA 2, Alemanha central, Alemanha nordeste, leste do Japão, oeste do Japão, Coreia central, sul da Coreia, norte EUA Central, Europa Setentrional, Sul EUA Central, Sudeste Asiático, sul da Índia, Sul do Reino Unido, Oeste do Reino Unido, US Gov Arizona, US Gov Texas, US Gov-Virgínia, West EUA Central, Europa Ocidental, oeste da Índia, oeste dos EUA e West dos EUA 2.
*Disco de armazenamento de destino (no estado em que está sendo dimensionado)* * | O tipo de discos a ser usado para armazenamento no Azure. <br/><br/> Especifique o disco de armazenamento de destino como gerenciado Premium, padrão de SSD gerenciado ou HDD padrão gerenciado.
**Disco de armazenamento de destino (dimensionamento baseado em desempenho)** | Especifique o tipo de disco de armazenamento de destino como automático, gerenciado Premium, HDD padrão gerenciado ou SSD padrão gerenciado.<br/><br/> **Automático**: a recomendação de disco baseia-se nos dados de desempenho dos discos (as operações de entrada/saída por segundo (IOPS) e taxa de transferência).<br/><br/>**Premium/Standard**: a avaliação recomenda um SKU de disco dentro do tipo de armazenamento selecionado.<br/><br/> Se quiser atingir uma única instância VM SLA de 99,9%, considerando a utilização de discos geridos por prémios. Isso garante que todos os discos na avaliação sejam recomendados como discos gerenciados Premium.<br/><br/> O Azure Migrate só suporta discos geridos para avaliação de migrações.
**Instâncias reservadas (RIs)** | Especifique [instâncias reservadas](https://azure.microsoft.com/pricing/reserved-vm-instances/) no Azure, para que as estimativas de custo na avaliação tomem os descontos de ri em conta.<br/><br/> Atualmente, o RIs tem suporte apenas para ofertas pagas conforme o uso nas migrações para Azure.
**Critérios de dimensionamento** | Usado para dimensionar o tamanho correto da VM no Azure.<br/><br/> Use como está o dimensionamento, ou o dimensionamento baseado em desempenho.
**Histórico de desempenho** | Usado com o dimensionamento baseado em desempenho. Especifique a duração usada ao avaliar dados de desempenho.
**Utilização de percentil** | Usado com o dimensionamento baseado em desempenho. Especifica o valor percentual do exemplo de desempenho a ser usado para o dimensionamento correto. 
**Série das VMs** | Especifique a série de VMs do Azure que você deseja considerar para o dimensionamento correto. Por exemplo, se você não tiver um ambiente de produção que precise de VMs série A no Azure, poderá excluir uma série da lista ou série.
**Fator de conforto** | Buffer usado durante a avaliação. Aplicado na parte superior dos dados de utilização do computador para VMs (CPU, memória, disco e rede). Ele conta com problemas como uso sazonal, histórico de desempenho curto e, provavelmente, aumenta o uso futuro.<br/><br/> Por exemplo, um VM de 10 núcleos com 20% de utilização normalmente resulta num VM de dois núcleos. Com um fator de conforto de 2,0 x, o resultado é uma VM de quatro núcleos.
**Oferta** | Exibe a [oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/) na qual você está registrado. A avaliação do servidor estima o custo de acordo.
**Moeda** | Moeda de cobrança da sua conta.
**Desconto (%)** | Lista quaisquer descontos específicos de assinatura recebidos por cima da oferta do Azure. A predefinição é 0%.
**Tempo de atividade de VM** | Se as VMs do Azure não forem executadas 24 horas por dia, 7 dias por semana, você poderá especificar a duração (dias por mês e horas por dia) em que serão executadas. As estimativas de custo são manipuladas de acordo.<br/><br/> O valor padrão é 31 dias por mês e 24 horas por dia.
**Benefício Híbrido do Azure** | Especifica se você tem o Software Assurance e se está qualificado para [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Se definido como Sim (a configuração padrão), os preços não Windows Azure são considerados para VMs do Windows.

[Examine as práticas recomendadas](best-practices-assessment.md) para criar a avaliação com a avaliação do servidor.

## <a name="how-are-assessments-calculated"></a>Como as avaliações são calculadas? 

Avaliações nas migrações para Azure: a avaliação do servidor é calculada usando os metadados coletados sobre os computadores locais. Se você executar uma avaliação em computadores importados usando um. Arquivo CSV, você fornece os metadados para o cálculo. Os cálculos ocorrem em três estágios:

1. **Calcular a preparação do Azure**: avalie se as máquinas são adequadas para a migração para o Azure.
2. **Calcular recomendações de dimensionamento**: estimar a computação, o armazenamento e o dimensionamento de rede. 
2. **Calcular os custos mensais**: Calcule a computação mensal estimada e os custos de armazenamento para executar os computadores no Azure após a migração.

Os cálculos estão em ordem, e um servidor de computador será movido para um estágio posterior somente se ele passar o anterior. Por exemplo, se um servidor falhar na preparação do Azure, ele será marcado como inadequado para o Azure, e o dimensionamento e o custo não serão feitos para esse servidor.



## <a name="calculate-readiness"></a>Calcular preparação

Nem todos os computadores são adequados para execução no Azure. A avaliação do servidor avalia cada computador local e atribui a ela uma categoria de preparação. 
- **Pronto para o Azure**: o computador pode ser migrado no estado em que se encontra no Azure sem nenhuma alteração. Ele será iniciado no Azure com o suporte completo do Azure.
- **Condicionalmente pronto para o Azure**: o computador pode iniciar no Azure, mas pode não ter suporte completo do Azure. Por exemplo, um computador que está executando uma versão mais antiga do Windows Server não tem suporte no Azure. Você deve ter cuidado antes de migrar esses computadores para o Azure. Siga as diretrizes de correção sugeridas na avaliação para corrigir os problemas de preparação.
- **Não está pronto para o Azure**: o computador não será iniciado no Azure. Por exemplo, se um disco do computador local for maior que 64-TBs, ele não poderá ser hospedado no Azure. Siga as diretrizes de correção para corrigir o problema antes da migração. 
- **Preparação desconhecida**: as migrações para Azure não puderam determinar a prontidão de um computador, devido a metadados insuficientes.

Para calcular a preparação, a avaliação do servidor revisa as propriedades do computador e as configurações do sistema operacional resumidas nas tabelas a seguir. 

### <a name="machine-properties"></a>Propriedades da máquina

A avaliação do servidor revisa as seguintes propriedades da VM local para determinar se ela pode ser executada no Azure.

**Propriedade** | **Detalhes** | **Status de preparação do Azure**
--- | --- | ---
**Tipo de inicialização** | O Azure dá suporte a VMs com um tipo de inicialização de BIOS, não UEFI. | Condicionalmente pronto se o tipo de inicialização for UEFI.
**Núcleos** | O número de núcleos nos computadores deve ser igual ou menor que o número máximo de núcleos (128) com suporte para uma VM do Azure.<br/><br/> Se o histórico de desempenho estiver disponível, as migrações para Azure considerarão os núcleos utilizados para comparação. Se um fator de conforto for especificado nas configurações de avaliação, o número de núcleos utilizados será multiplicado pelo fator de conforto.<br/><br/> Se não houver histórico de desempenho, as migrações para Azure usarão os núcleos alocados sem aplicar o fator de conforto. | Pronto se for menor ou igual a limites.
**Memória** | O tamanho da memória da máquina deve ser igual ou inferior à memória máxima (3892 gigabytes [GB] na série Azure M Standard_M128m&nbsp;<sup>2</sup>) permitida para um VM Azure. [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Se o histórico de desempenho estiver disponível, as migrações para Azure considerarão a memória utilizada para comparação. Se um fator de conforto for especificado, a memória utilizada será multiplicada pelo fator de conforto.<br/><br/> Se não houver nenhum histórico, a memória alocada será usada sem aplicar o fator de conforto.<br/><br/> | Pronto se estiver dentro dos limites.
**Disco de armazenamento** | O tamanho alocado de um disco deve ser de 32 TB ou menos. Embora o Azure dê suporte a discos de 64 TB com discos SSD Ultra, migre o Azure: a avaliação do servidor atualmente verifica 32 TB como os limites de tamanho do disco, pois ele ainda não oferece suporte ao SSD Ultra. <br/><br/> O número de discos anexados ao computador deve ser de 65 ou menos, incluindo o disco do sistema operacional. | Pronto se estiver dentro dos limites.
**Redes** | Um computador deve ter 32 ou menos NICs (interfaces de rede) conectados a ele. | Pronto se estiver dentro dos limites.

### <a name="guest-operating-system"></a>Sistema operativo convidado
Juntamente com as propriedades da VM, a avaliação do servidor examina o sistema operacional convidado das máquinas para determinar se ele pode ser executado no Azure.

> [!NOTE]
> Para VMs VMware, a avaliação do servidor usa o sistema operacional especificado para a VM no vCenter Server para lidar com a análise do SO convidado. Para VMs do Linux em execução no VMware, atualmente ele não identifica a versão exata do kernel do sistema operacional convidado.

A lógica a seguir é usada pela avaliação do servidor para identificar a prontidão do Azure com base no sistema operacional.

**Sistema Operativo** | **Detalhes** | **Status de preparação do Azure**
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

## <a name="calculate-sizing-as-is-on-premises"></a>Calcular o dimensionamento (como está no local)

Depois que o computador estiver marcado como pronto para o Azure, a avaliação do servidor fará recomendações de dimensionamento para identificar a VM do Azure e o SKU do disco. Se você usar como está o dimensionamento local, a avaliação do servidor não considerará o histórico de desempenho das VMs e dos discos.

**Dimensionamento de computação**: ALOCA uma SKU de VM do Azure com base no tamanho alocado no local.
**Dimensionamento de armazenamento/disco**: a avaliação do servidor examina o tipo de armazenamento especificado nas propriedades de avaliação (HDD padrão/SSD/Premium) e recomenda o tipo de disco de acordo. O tipo de armazenamento padrão é os discos Premium.
**Dimensionamento de rede**: a avaliação do servidor considera o adaptador de rede no computador local.


## <a name="calculate-sizing-performance-based"></a>Calcular o dimensionamento (baseado em desempenho)

Depois que um computador estiver marcado como pronto para o Azure, se você usar o dimensionamento de base de desempenho, avaliação do servidor fazendo recomendações de dimensionamento da seguinte maneira:

- A avaliação do servidor considera o histórico de desempenho do computador para identificar o tamanho da VM e o tipo de disco no Azure.
- Se os servidores tiverem sido importados usando um arquivo CSV, os valores especificados serão usados. Esse método é especialmente útil se você tiver alocado o computador local com excesso de alocação, a utilização é realmente baixa e você deseja dimensionar a VM no Azure para economizar custos. 
- Se você não quiser usar os dados de desempenho, redefina os critérios de dimensionamento como estão no local, conforme descrito na seção anterior.

### <a name="calculate-storage-sizing"></a>Calcular o dimensionamento de armazenamento

Para o dimensionamento de armazenamento, as migrações para Azure tentam Mapear todos os discos anexados à máquina para um disco no Azure e funcionam da seguinte maneira:

1. A avaliação do servidor adiciona o IOPS de leitura e gravação de um disco para obter o IOPS total necessário. Da mesma forma, ele adiciona os valores de taxa de transferência de leitura e gravação para obter a taxa de transferência total de cada disco.
2. Se você tiver especificado o tipo de armazenamento como automático, com base nos valores de taxa de transferência e IOPS efetivos, a avaliação do servidor determinará se o disco deve ser mapeado para um HDD padrão, SSD Standard ou um disco Premium no Azure. Se o tipo de armazenamento estiver definido como HDD Standard/SSD/Premium, a avaliação do servidor tentará encontrar um SKU de disco dentro do tipo de armazenamento selecionado (discos HDD Standard/SSD/Premium).
3. Os discos são selecionados da seguinte maneira:
    - Se a avaliação do servidor não conseguir localizar um disco com a IOPS e a taxa de transferência necessárias, ela marcará a máquina como inadequada para o Azure.
    - Se a avaliação do servidor encontrar um conjunto de discos adequados, ela selecionará os discos que dão suporte ao local especificado nas configurações de avaliação.
    - Se houver vários discos qualificados, a avaliação do servidor selecionará o disco com o menor custo.
    - Se os dados de desempenho de qualquer disco não estiverem disponíveis, os dados de configuração do disco (tamanho do disco) serão usados para localizar um disco SSD padrão no Azure.

### <a name="calculate-network-sizing"></a>Calcular o dimensionamento da rede

A avaliação do servidor tenta encontrar uma VM do Azure que pode dar suporte ao número de adaptadores de rede anexados à máquina local e o desempenho exigido por esses adaptadores de rede.
- Para obter o desempenho de rede efetivo da VM local, a avaliação do servidor agrega os dados transmitidos por segundo (MBps) do computador (saída de rede), em todos os adaptadores de rede, e aplica o fator de conforto. Ele usa esse número para encontrar uma VM do Azure que pode dar suporte ao desempenho de rede necessário.
- Junto com o desempenho da rede, a avaliação do servidor também considera se a VM do Azure pode dar suporte ao número necessário de adaptadores de rede.
- Se nenhum dado de desempenho de rede estiver disponível, a avaliação do servidor considerará apenas a contagem do adaptador de rede para o dimensionamento da VM.


### <a name="calculate-compute-sizing"></a>Calcular o dimensionamento de computação

Depois de calcular os requisitos de armazenamento e de rede, a avaliação do servidor considera os requisitos de CPU e memória para encontrar um tamanho de VM adequado no Azure.
- As migrações para Azure analisam os núcleos e a memória utilizados em vigor para encontrar um tamanho de VM adequado no Azure.
- Se nenhum tamanho adequado for encontrado, o computador será marcado como inadequado para o Azure.
- Se um tamanho adequado for encontrado, as migrações para Azure aplicarão os cálculos de armazenamento e rede. Em seguida, ele aplica as configurações de localização e tipo de preço para a recomendação final de tamanho de VM.
- Se existirem vários tamanhos de VMs do Azure elegíveis, é recomendado aquele que tiver o custo mais baixo.


### <a name="calculate-confidence-ratings"></a>Calcular classificações de confiança

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

> [!NOTE]
> As classificações de confiança não são atribuídas a avaliações de servidores importados usando o. Arquivo CSV para migrações para Azure. 

#### <a name="low-confidence-ratings"></a>Classificações de baixa confiança

Aqui estão algumas razões pelas quais uma avaliação pode obter uma classificação de baixa confiança:

- Você não fez o profile do seu ambiente pela duração para a qual está criando a avaliação. Por exemplo, se você criar a avaliação com duração de desempenho definida como um dia, deverá aguardar pelo menos um dia depois de iniciar a descoberta para todos os pontos de dados a serem coletados.
- Algumas VMs foram desligadas durante o período para o qual a avaliação foi calculada. Se alguma VM for desativada por alguma duração, a avaliação do servidor não poderá coletar os dados de desempenho para esse período.
- Algumas VMs foram criadas durante o período para o qual a avaliação foi calculada. Por exemplo, se você criou uma avaliação para o histórico de desempenho do último mês, mas algumas VMs foram criadas no ambiente apenas uma semana atrás, o histórico de desempenho das novas VMs não existirá durante toda a duração.

> [!NOTE]
> Se a classificação de confiança de qualquer avaliação for menor que cinco estrelas, recomendamos que você aguarde pelo menos um dia para o dispositivo criar o perfil do ambiente e recalcular a avaliação. Caso contrário, o dimensionamento baseado em desempenho pode não ser confiável. Nesse caso, recomendamos que você alterne a avaliação para o dimensionamento local.

## <a name="calculate-monthly-costs"></a>Calcular custos mensais

Após a conclusão das recomendações de dimensionamento, as migrações para Azure calculam os custos de computação e armazenamento para após a migração.

- **Custo de computação**: usando o tamanho recomendado da VM do Azure, as migrações para Azure usam a API de cobrança para calcular o custo mensal da VM.
    - O cálculo usa o sistema operacional, o Software Assurance, as instâncias reservadas, o tempo de atividade da VM, o local e as configurações de moeda em conta.
    - Ele agrega o custo em todas as máquinas para calcular o custo de computação mensal total.
- **Custo de armazenamento**: o custo de armazenamento mensal de um computador é calculado pela agregação do custo mensal de todos os discos anexados à máquina, da seguinte maneira:
    - A avaliação do servidor calcula o total de custos de armazenamento mensal agregando os custos de armazenamento de todas as máquinas.
    - Atualmente, o cálculo não considera as ofertas especificadas nas configurações de avaliação.

Os custos são exibidos na moeda especificada nas configurações de avaliação.


## <a name="next-steps"></a>Passos seguintes

[Examine](best-practices-assessment.md) as práticas recomendadas para a criação de avaliações. 
