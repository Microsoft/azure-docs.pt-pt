---
title: Avaliações em Migração Azure
description: Conheça as avaliações em Azure Migrate.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 0cf933dd1c8c61edfcea20ea954c5813f3848b28
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78392869"
---
# <a name="about-assessments-in-azure-migrate"></a>Sobre avaliações em Azure Migrate

Este artigo descreve como as avaliações são calculadas em [Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool). Faz avaliações em grupos de máquinas no local, para descobrir se estão prontas para a migração para O Migração Azure.

## <a name="how-do-i-run-an-assessment"></a>Como faço uma avaliação?
Pode executar uma avaliação utilizando o Azure Migrate: Server Assessment, ou outra ferramenta Azure ou de terceiros. Depois de criar um projeto Azure Migrate, adicione a ferramenta de que necessita. [Saiba mais](how-to-add-tool-first-time.md)

### <a name="collect-compute-data"></a>Recolher dados computacionais

Os dados de desempenho das definições de cálculo são recolhidos da seguinte forma:

1. O [aparelho Azure Migrate](migrate-appliance.md) recolhe um ponto de amostra em tempo real:

    - **VMware VMs**: Para VMware VMs, o aparelho Azure Migrate recolhe um ponto de amostra em tempo real a cada intervalo de 20 segundos.
    - **Hiper-V VMs**: Para VMs hiper-V, o ponto de amostra em tempo real é recolhido a cada intervalo de 30 segundos.
    - **Servidores físicos**: Para servidores físicos, o ponto de amostra em tempo real é recolhido a cada intervalo de cinco minutos. 
    
2. O aparelho arregalhe os pontos de amostra (20 segundos, 30 segundos, cinco minutos) para criar um único ponto de dados a cada 10 minutos. Para criar o único ponto de dados, o aparelho seleciona o valor máximo de todas as amostras e, em seguida, envia-o para o Azure.
3. A Avaliação do Servidor armazena todos os pontos de amostra de 10 minutos do último mês.
4. Quando cria uma avaliação, a Avaliação do Servidor identifica o ponto de dados adequado para o tamanho certo, com base nos valores percentuais para o histórico de *desempenho* e *utilização do Percentil*.

    - Por exemplo, se o histórico de desempenho for definido para uma semana, e a utilização do percentil for o percentil 95, a Avaliação do Servidor classifica os pontos de amostra de 10 minutos para a última semana em ordem ascendente, e escolhe o 95º valor percentil para o tamanho certo. 
    - O valor do percentil 95 garante que ignora qualquer fora-de-série, que pode ser incluído se escolher o percentil 99.
    - Se quiser escolher o pico de utilização para o período e não quiser perder nenhum outliers, deve selecionar o percentil 99 para utilização do percentil.

5. Este valor é multiplicado pelo fator de conforto para obter os dados eficazes de utilização do desempenho para cada métrica (utilização de CPU, utilização da memória, IOPS do disco (ler e escrever), entrada de disco (ler e escrever) e entrada de rede (dentro e fora) que o recolhas de aparelhos.

Para eexecutar avaliações na Avaliação do Servidor, prepare-se para avaliação no local e em Azure, e instale o aparelho Azure Migrate para descobrir continuamente as máquinas no local. Depois de as máquinas serem descobertas, reúne-as em grupos para as avaliar. Para avaliações mais detalhadas e de alta confiança, pode visualizar e mapear dependências entre máquinas, para descobrir como migrar.

- Saiba mais sobre avaliações de execução de [VMware VMs,](tutorial-prepare-vmware.md) [VMs Hiper-V](tutorial-prepare-hyper-v.md)e [servidores físicos](tutorial-prepare-physical.md).
- Saiba avaliar os servidores [importados com um ficheiro CSV](tutorial-assess-import.md).
- Aprenda sobre a criação de [visualização da dependência.](concepts-dependency-visualization.md)

## <a name="assessments-in-server-assessment"></a>Avaliações na Avaliação do Servidor 

As avaliações que cria com a Avaliação do Servidor Migratório Azure são uma imagem pontual dos dados. A ferramenta de avaliação do servidor fornece dois tipos de avaliações.

**Tipo de avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Baseado no desempenho** | Avaliações que fazem recomendações com base em dados de desempenho recolhidos | A recomendação do tamanho vM baseia-se em dados de CPU e utilização da memória.<br/><br/> A recomendação do tipo disco (hDD/SSD padrão ou discos geridos por prémios) baseia-se no IOPS e na entrada dos discos no local.
**As-is no local** | Avaliações que não usam dados de desempenho para fazer recomendações. | Recomendação de tamanho VM baseia-se no tamanho vm no local<br/><br> O tipo de disco recomendado baseia-se no tipo de armazenamento selecionado para a avaliação.

## <a name="collecting-performance-data"></a>Recolha de dados de desempenho

Os dados de desempenho são recolhidos da seguinte forma:

1. O [aparelho Azure Migrate](migrate-appliance.md) recolhe um ponto de amostra em tempo real:

    - **VMware VMs**: Para VMware VMs, o aparelho Azure Migrate recolhe um ponto de amostra em tempo real a cada intervalo de 20 segundos.
    - **Hiper-V VMs**: Para VMs hiper-V, o ponto de amostra em tempo real é recolhido a cada intervalo de 30 segundos.
    - **Servidores físicos**: Para servidores físicos, o ponto de amostra em tempo real é recolhido a cada intervalo de cinco minutos. 
    
2. O aparelho arregalhe os pontos de amostra (20 segundos, 30 segundos, cinco minutos) para criar um único ponto de dados a cada 10 minutos. Para criar o único ponto de dados, o aparelho seleciona o valor máximo de todas as amostras e, em seguida, envia-o para o Azure.
3. A Avaliação do Servidor armazena todos os pontos de amostra de 10 minutos do último mês.
4. Quando cria uma avaliação, a Avaliação do Servidor identifica o ponto de dados adequado para o tamanho certo, com base nos valores percentuais para o histórico de *desempenho* e *utilização do Percentil*.

    - Por exemplo, se o histórico de desempenho for definido para uma semana, e a utilização do percentil for o percentil 95, a Avaliação do Servidor classifica os pontos de amostra de 10 minutos para a última semana em ordem ascendente, e escolhe o 95º valor percentil para o tamanho certo. 
    - O valor do percentil 95 garante que ignora qualquer fora-de-série, que pode ser incluído se escolher o percentil 99.
    - Se quiser escolher o pico de utilização para o período e não quiser perder nenhum outliers, deve selecionar o percentil 99 para utilização do percentil.

5. Este valor é multiplicado pelo fator de conforto para obter os dados eficazes de utilização do desempenho para cada métrica (utilização de CPU, utilização da memória, IOPS do disco (ler e escrever), entrada de disco (ler e escrever) e entrada de rede (dentro e fora) que o recolhas de aparelhos.
## <a name="whats-in-an-assessment"></a>Novidades nas avaliações

Aqui está o que incluído numa avaliação em Azure Migrate: Server Assessment.

**Propriedade** | **Detalhes**
--- | ---
**Localização de destino** | O local para onde quer migrar. A Avaliação do Servidor suporta atualmente estas regiões-alvo do Azure:<br/><br/> Austrália Leste, Austrália Sudeste, Brasil Sul, Canadá Central, Canadá Leste, Índia Central, Eua Central, China Leste, China Norte, Leste dos EUA, Leste DOS 2,00, Alemanha Central, Alemanha Nordeste, Japão Leste, Japão Oeste, Coreia Central, Coreia do Sul, Norte Centro dos EUA, Norte da Europa, Centro-Sul dos EUA, Sudeste Asiático, Índia do Sul, Reino Unido Sul, Reino Unido Oeste, US Gov Arizona, EUA Gov Texas, EUA Gov Virginia, West Central US, West Europe, West India, West US, e West US2.
*Disco de armazenamento alvo (as-is dimensionamento)* * | O tipo de discos a utilizar para armazenamento em Azure. <br/><br/> Especifique o disco de armazenamento-alvo como gerido por prémio, gerido por Norma sSD ou hDD padrão gerido.
**Disco de armazenamento de alvo (tamanho baseado no desempenho)** | Especifique o tipo de disco de armazenamento alvo como gerido automaticamente, gerido por prémios, gerido sem medidas normais ou geridas por SSD padrão.<br/><br/> **Automática**: A recomendação do disco baseia-se nos dados de desempenho dos discos (as operações de entrada/saída por segundo (IOPS) e a produção).<br/><br/>**Premium/standard**: A avaliação recomenda um SKU de disco dentro do tipo de armazenamento selecionado.<br/><br/> Se quiser atingir uma única instância VM SLA de 99,9%, considerando a utilização de discos geridos por prémios. Isto garante que todos os discos da avaliação são recomendados como discos geridos por prémios.<br/><br/> O Azure Migrate só suporta discos geridos para avaliação de migrações.
**Instâncias Reservadas (RIs)** | Especifique [as instâncias reservadas](https://azure.microsoft.com/pricing/reserved-vm-instances/) em Azure, de modo a que as estimativas de custos na avaliação tomem em consideração os descontos ri.<br/><br/> Atualmente, as RIs são suportadas apenas para ofertas pay-As-You-Go em Azure Migrate.
**Critérios de dimensionamento** | Usado para tamanho direito o VM em Azure.<br/><br/> Use o tamanho as-is, ou tamanho baseado no desempenho.
**Histórico de desempenho** | Usado com tamanho baseado no desempenho. Especifique a duração utilizada na avaliação dos dados de desempenho.
**Utilização de percentil** | Usado com tamanho baseado no desempenho. Especifica o valor percentil da amostra de desempenho a utilizar para o tamanho certo. 
**Série das VMs** | Especifique a série Azure VM que pretende considerar para o tamanho certo. Por exemplo, se não tiver um ambiente de produção que precise de VMs da série A em Azure, pode excluir séries A da lista ou série.
**Fator de conforto** | Tampão usado durante a avaliação. Aplicado em cima dos dados de utilização da máquina para VMs (CPU, memória, disco e rede). É responsável por questões como o uso sazonal, histórico de desempenho curto e provável aumento sustâneo.<br/><br/> Por exemplo, um VM de 10 núcleos com 20% de utilização normalmente resulta num VM de dois núcleos. Com um fator de conforto de 2,0x, o resultado é um VM de quatro núcleos.
**Oferta** | Exibe a [oferta Azure](https://azure.microsoft.com/support/legal/offer-details/) na qual está matriculado. A Avaliação do Servidor estima o custo em conformidade.
**Moeda** | Moeda de faturação para a sua conta.
**Desconto (%)** | Lista quaisquer descontos específicos para a subscrição que receber em cima da oferta do Azure. A predefinição é 0%.
**Tempo de atividade de VM** | Se os VMs Azure não funcionarem 24 horas por dia, 7 dias por semana, pode especificar a duração (dias por mês e horas por dia) que irão funcionar. As estimativas de custos são tratadas em conformidade.<br/><br/> O valor predefinido é de 31 dias por mês e 24 horas por dia.
**Benefício Híbrido do Azure** | Especifica se tem garantia de software e é elegível para [o Benefício Híbrido Azure.](https://azure.microsoft.com/pricing/hybrid-use-benefit/) Se definido para Sim (a definição predefinida), os preços não Windows Azure são considerados para VMs do Windows.

[Reveja as melhores práticas](best-practices-assessment.md) para a criação de avaliação com a Avaliação do Servidor.

## <a name="how-are-assessments-calculated"></a>Como são calculadas as avaliações? 

Avaliações em Azure Migrate: A avaliação do servidor é calculada utilizando os metadados recolhidos sobre as máquinas no local. Se fizer uma avaliação sobre máquinas importadas com a . Ficheiro CSV, fornece os metadados para o cálculo. Os cálculos ocorrem em três fases:

1. **Calcular a prontidão do Azure**: Avaliar se as máquinas são adequadas para a migração para Azure.
2. **Calcular recomendações**de dimensionamento : Calcular o cálculo, armazenamento e dimensionamento da rede. 
2. **Calcular os custos mensais**: Calcular os custos estimados mensais de cálculo e armazenamento para o funcionamento das máquinas em Azure após a migração.

Os cálculos estão em ordem, e um servidor de máquinas move-se para uma fase posterior apenas se passar na anterior. Por exemplo, se um servidor falhar a prontidão do Azure, é marcado como inadequado para o Azure, e o dimensionamento e o custo não são feitos para esse servidor.



## <a name="calculate-readiness"></a>Calcular prontidão

Nem todas as máquinas são adequadas para funcionar em Azure. A Avaliação do Servidor avalia cada máquina no local e atribui-lhe uma categoria de prontidão. 
- **Pronto para Azure**: A máquina pode ser migrada como está para Azure sem alterações. Começará em Azure com todo o apoio azure.
- **Preparado condicionalmente para O Azure**: A máquina pode arrancar em Azure, mas pode não ter suporte azure completo. Por exemplo, uma máquina que está a executar uma versão mais antiga do Windows Server não é suportada no Azure. Tens de ter cuidado antes de migrares estas máquinas para o Azure. Siga as orientações de reparação sugeridas na avaliação para corrigir as questões de prontidão.
- **Não está pronto para O Azure:** A máquina não arranca em Azure. Por exemplo, se um disco de máquinas no local for superior a 64-TBs, não pode ser hospedado em Azure. Siga a orientação de reparação para corrigir a questão antes da migração. 
- **Prontidão desconhecida**: O Azure Migrate não conseguiu determinar a prontidão de uma máquina, devido a metadados insuficientes.

Para calcular a prontidão, a Avaliação do Servidor analisa as propriedades da máquina e as definições do sistema operativo resumidas nas tabelas seguintes. 

### <a name="machine-properties"></a>Propriedades da máquina

A Avaliação do Servidor analisa as seguintes propriedades do VM no local para determinar se pode funcionar no Azure.

**Propriedade** | **Detalhes** | **Estado de prontidão azure**
--- | --- | ---
**Tipo de arranque** | O Azure suporta VMs com um tipo de bios de arranque, não UEFI. | Pronto condicionalmente se o tipo de arranque for UEFI.
**Núcleos** | O número de núcleos nas máquinas deve ser igual ou inferior ao número máximo de núcleos (128) suportados por um VM Azure.<br/><br/> Se o histórico de desempenho estiver disponível, a Azure Migrate considera os núcleos utilizados para comparação. Se um fator de conforto for especificado nas definições de avaliação, o número de núcleos utilizados é multiplicado pelo fator de conforto.<br/><br/> Se não houver histórico de desempenho, a Azure Migrate usa os núcleos atribuídos sem aplicar o fator de conforto. | Pronto se for inferior ou igual a limites.
**Memória** | O tamanho da memória da máquina deve ser igual ou inferior à memória máxima (3892 gigabytes [GB] na série Azure M Standard_M128m&nbsp;<sup>2</sup>) permitida para um VM Azure. [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Se o histórico de desempenho estiver disponível, a Azure Migrate considera a memória utilizada para comparação. Se for especificado um fator de conforto, a memória utilizada é multiplicada pelo fator de conforto.<br/><br/> Se não houver histórico, a memória atribuída é usada sem aplicar o fator de conforto.<br/><br/> | Pronto se dentro dos limites.
**Disco de armazenamento** | O tamanho atribuído de um disco deve ser de 32 TB ou menos. Embora o Azure suporte discos de 64 TB com discos Ultra SSD, o Azure Migrate: A Avaliação do Servidor verifica atualmente 32 TB, uma vez que o tamanho do disco limita,uma vez que ainda não suporta o Ultra SSD. <br/><br/> O número de discos ligados à máquina deve ser de 65 ou menos, incluindo o disco OS. | Pronto se dentro dos limites.
**Redes** | Uma máquina deve ter 32 ou menos interfaces de rede (NICs) ligadas a ela. | Pronto se dentro dos limites.

### <a name="guest-operating-system"></a>Sistema operativo convidado
Juntamente com as propriedades vM, a Server Assessment analisa o sistema operativo dos hóspedes das máquinas para determinar se pode funcionar no Azure.

> [!NOTE]
> Para VMware VMs, a Avaliação do Servidor utiliza o sistema operativo especificado para o VM no VCenter Server para lidar com a análise do OS do hóspede. Para os VMs Linux em VMware, atualmente não identifica a versão exata do kernel do oss odeiado convidado.

A seguinte lógica é usada pela Avaliação do Servidor para identificar a prontidão do Azure com base no sistema operativo.

**Sistema Operativo** | **Detalhes** | **Estado de prontidão azure**
--- | --- | ---
Windows Server 2016 e todos os SPs | O Azure oferece todo o apoio. | Preparado para o Azure
Windows Server 2012 R2 & todos os SPs | O Azure oferece todo o apoio. | Preparado para o Azure
Windows Server 2012 e todos os SPs | O Azure oferece todo o apoio. | Preparado para o Azure
Windows Server 2008 R2 com todos os SPs | O Azure oferece todo o apoio.| Preparado para o Azure
Windows Server 2008 (32-bits e 64-bits) | O Azure oferece todo o apoio. | Preparado para o Azure
Windows Server 2003, 2003 R2 | Estes sistemas operativos passaram a data de fim de suporte e precisam de um Acordo de [Apoio Personalizado (CSA)](https://aka.ms/WSosstatement) para apoio no Azure. | Preparado condicionalmente para Azure. Considere melhorar o Sistema operativo antes de migrar para Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Estes sistemas operativos passaram a data de fim de suporte. A máquina pode começar em Azure, mas azure não fornece suporte para os soários. | Preparado condicionalmente para Azure. Recomendamos que atualize o Sistema operativo antes de migrar para O Azure.
Windows Cliente 7, 8 e 10 | O Azure fornece suporte apenas com [subscrição do Estúdio Visual.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Condicionalmente preparado para o Azure
Windows 10 Pro Desktop | A Azure presta apoio com os Direitos de [Hospedagem Multitenant.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Condicionalmente preparado para o Azure
Windows Vista, XP Professional | Estes sistemas operativos passaram a data de fim de suporte. A máquina pode começar em Azure, mas azure não fornece suporte para os soários. | Preparado condicionalmente para Azure. Recomendamos que atualize o Sistema operativo antes de migrar para O Azure.
Linux | O Azure apoia estes [sistemas operativos Linux.](../virtual-machines/linux/endorsed-distros.md) Outros sistemas operativos Linux podem começar em Azure, mas recomendamos que atualize o SO para uma versão endossada antes de migrar para o Azure. | Pronto para o Azure se a versão for endossada.<br/><br/>Prepare-se condicionalmente se a versão não for endossada.
Outros sistemas operativos<br/><br/> Por exemplo, Oracle Solaris, Apple macOS etc., FreeBSD, etc. | O Azure não apoia estes sistemas operativos. A máquina pode começar em Azure, mas azure não fornece suporte para os soários. | Preparado condicionalmente para Azure. Recomendamos que instale um Sistema operativo suportado antes de migrar para o Azure.  
OS especificado como **Outros** no servidor vCenter | A Azure Migrate não consegue identificar o SO neste caso. | Prontidão desconhecida. Certifique-se de que o Sistema operativo que corre dentro do VM é suportado em Azure.
Sistemas operativos de 32 bits | A máquina pode começar em Azure, mas azure pode não fornecer suporte total. | Preparado condicionalmente para Azure. Considere melhorar o Os da máquina de 32 bits de Os para 64 bits so antes de migrar para Azure.

## <a name="calculate-sizing-as-is-on-premises"></a>Calcular o dimensionamento (as-is on-local)

Depois de a máquina estar marcada como pronta para o Azure, a Avaliação do Servidor faz recomendações de dimensionamento para identificar o Azure VM e o disco SKU. Se utilizar o dimensionamento como está no local, a Avaliação do Servidor não considera o histórico de desempenho dos VMs e discos.

**Tamanho computacional**: Atribui um SKU Azure VM com base no tamanho atribuído no local.
**Dimensionamento de armazenamento/disco:** A avaliação do servidor analisa o tipo de armazenamento especificado nas propriedades de avaliação (HDD/SSD/premium padrão), e recomenda o tipo de disco em conformidade. O tipo de armazenamento predefinido é de discos premium.
**Dimensionamento da rede**: A Avaliação do Servidor considera o adaptador de rede na máquina no local.


## <a name="calculate-sizing-performance-based"></a>Calcular o dimensionamento (baseado no desempenho)

Depois de uma máquina estar marcada como pronta para o Azure, se utilizar o dimensionamento de base de desempenho, a Avaliação do Servidor faz recomendações de dimensionamento da seguinte forma:

- A Avaliação do Servidor considera o histórico de desempenho da máquina para identificar o tamanho VM e o tipo de disco em Azure.
- Se os servidores tiverem sido importados utilizando um ficheiro CSV, os valores que especifica são utilizados. Este método é especialmente útil se tiver alocada excessivamente a máquina no local, a utilização é na verdade baixa, e você quer tamanho direito o VM em Azure para economizar custos. 
- Se não quiser utilizar os dados de desempenho, redefinir os critérios de dimensionamento para as-is no local, como descrito na secção anterior.

### <a name="calculate-storage-sizing"></a>Calcular o tamanho do armazenamento

Para o tamanho do armazenamento, a Azure Migrate tenta mapear todos os discos ligados à máquina a um disco em Azure, e funciona da seguinte forma:

1. A Avaliação do Servidor adiciona a leitura e a escrita de IOPS de um disco para obter o iOPS total necessário. Da mesma forma, adiciona os valores de leitura e escrita de produção para obter a produção total de cada disco.
2. Se especificou o tipo de armazenamento como Automático, com base nos valores eficazes do IOPS e dos valores de entrada, a Avaliação do Servidor determina se o disco deve ser mapeado para um HDD padrão, SSD padrão ou um disco premium em Azure. Se o tipo de armazenamento for definido para Standard HDD/SSD/Premium, a Avaliação do Servidor tenta encontrar um SKU de disco dentro do tipo de armazenamento selecionado (Discos Standard HDD/SSD/Premium).
3. Os discos são selecionados da seguinte forma:
    - Se a Avaliação do Servidor não encontrar um disco com o IOPS e a entrada necessários, marca a máquina como inadequada para o Azure.
    - Se a Avaliação do Servidor encontrar um conjunto de discos adequados, seleciona os discos que suportam a localização especificada nas definições de avaliação.
    - Se existirem vários discos elegíveis, a Avaliação do Servidor seleciona o disco com o menor custo.
    - Se os dados de desempenho de qualquer disco não estiverem disponíveis, os dados de configuração do disco (tamanho do disco) são utilizados para encontrar um disco SSD padrão em Azure.

### <a name="calculate-network-sizing"></a>Calcular o dimensionamento da rede

A Avaliação do Servidor tenta encontrar um VM Azure que possa suportar o número de adaptadores de rede ligados à máquina no local e o desempenho exigido por estes adaptadores de rede.
- Para obter o desempenho eficaz da rede do VM no local, a Avaliação do Servidor agrega os dados transmitidos por segundo (MBps) da máquina (rede fora), em todos os adaptadores de rede, e aplica o fator de conforto. Utiliza este número para encontrar um VM Azure que possa suportar o desempenho da rede necessário.
- Juntamente com o desempenho da rede, a Server Assessment também considera se o Azure VM pode suportar o número necessário de adaptadores de rede.
- Se não houver dados de desempenho da rede disponíveis, a Avaliação do Servidor considera apenas a contagem de adaptadores de rede para o dimensionamento de VM.


### <a name="calculate-compute-sizing"></a>Calcular o tamanho do cálculo

Depois de calcular os requisitos de armazenamento e rede, a Server Assessment considera os requisitos de CPU e memória para encontrar um tamanho VM adequado em Azure.
- A Azure Migrate olha para os núcleos e memória utilizados eficazes para encontrar um tamanho VM adequado em Azure.
- Se não for encontrado um tamanho adequado, a máquina está marcada como inadequada para o Azure.
- Se for encontrado um tamanho adequado, o Azure Migrate aplica os cálculos de armazenamento e rede. Em seguida, aplica definições de localização e nível de preços para a recomendação final do tamanho vm.
- Se existirem vários tamanhos de VMs do Azure elegíveis, é recomendado aquele que tiver o custo mais baixo.


### <a name="calculate-confidence-ratings"></a>Calcular classificações de confiança

Cada avaliação baseada no desempenho no Azure Migrate está associada a uma classificação de confiança que varia de uma (mais baixa) a cinco estrelas (mais alta).
- A classificação de confiança é alocada a uma avaliação com base na disponibilidade dos pontos de dados necessários para calcular a avaliação.
- A classificação de confiança de uma avaliação ajuda a calcular a fiabilidade das recomendações de tamanho fornecidas pelo Azure Migrate.
- As classificações de confiança não são aplicáveis como avaliações *no local.*
- Para dimensionamento baseado no desempenho, a Avaliação do Servidor necessita:
    - Os dados de utilização da memória CPU e VM.
    - O IOPS do disco e os dados de entrada de cada disco ligado ao VM.
    - A rede I/O para lidar com o dimensionamento baseado no desempenho de cada adaptador de rede ligado a um VM.

   Se algum destes números de utilização não estiver disponível no VCenter Server, a recomendação de tamanho pode não ser fiável.

Dependendo da percentagem de pontos de dados disponíveis, a classificação de confiança para a avaliação é a seguinte.

   **Disponibilidade de pontos de dados** | **Classificação de confiança**
   --- | ---
   0-20% | 1 estrela
   21-40% | 2 estrelas
   41-60% | 3 estrelas
   61-80% | 4 estrelas
   81-100% | 5 estrelas

> [!NOTE]
> As classificações de confiança não são atribuídas a avaliações de servidores importados utilizando . Ficheiro CSV no Azure Migrate. 

#### <a name="low-confidence-ratings"></a>Baixas classificações de confiança

Aqui estão algumas razões pelas quais uma avaliação poderia obter uma classificação de confiança baixa:

- Não perfilou o seu ambiente durante a duração pela qual está a criar a avaliação. Por exemplo, se criar a avaliação com a duração do desempenho definida para um dia, deve esperar pelo menos um dia depois de começar a descobrir para que todos os pontos de dados sejam recolhidos.
- Alguns VMs foram encerrados durante o período para o qual a avaliação foi calculada. Se algum VMs for desligado por alguma duração, a Avaliação do Servidor não pode recolher os dados de desempenho desse período.
- Foram criados alguns VMs durante o período para o qual a avaliação foi calculada. Por exemplo, se criou uma avaliação para o histórico de desempenho do mês passado, mas alguns VMs foram criados no ambiente há apenas uma semana, o histórico de desempenho dos novos VMs não existirá durante toda a duração.

> [!NOTE]
> Se a classificação de confiança de qualquer avaliação for inferior a cinco estrelas, recomendamos que espere pelo menos um dia para que o aparelho perfile o ambiente e, em seguida, recalcule a avaliação. Se não o fizeres, o tamanho baseado no desempenho pode não ser fiável. Nesse caso, recomendamos que altere a avaliação para o tamanho no local.

## <a name="calculate-monthly-costs"></a>Calcular os custos mensais

Depois de as recomendações de dimensionamento estarem completas, a Azure Migrate calcula os custos de cálculo e armazenamento para após a migração.

- **Custo da computação**: Utilizando o tamanho de VM Azure recomendado, o Azure Migrate utiliza a API de faturação para calcular o custo mensal para o VM.
    - O cálculo leva em conta as definições de sistema operativo, garantia de software, instâncias reservadas, uptime, localização e configurações de moeda vm.
    - Agrega o custo em todas as máquinas para calcular o custo total mensal do cálculo do cálculo.
- **Custo de armazenamento**: O custo mensal de armazenamento de uma máquina é calculado agregando o custo mensal de todos os discos ligados à máquina, da seguinte forma:
    - A Avaliação do Servidor calcula os custos totais mensais de armazenamento agregando os custos de armazenamento de todas as máquinas.
    - Atualmente, o cálculo não considera as ofertas especificadas nas definições de avaliação.

Os custos são apresentados na moeda especificada nas definições de avaliação.


## <a name="next-steps"></a>Passos seguintes

[Reveja](best-practices-assessment.md) as melhores práticas para a criação de avaliações. 
