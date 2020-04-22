---
title: Avaliações na Avaliação do Servidor Migratório Azure
description: Conheça avaliações na Avaliação do Servidor Migratório Azure
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 2f76ea5f195be2914cdcdb4de9e93af38504d66e
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769928"
---
# <a name="assessments-in-azure-migrate-server-assessment"></a>Avaliações em Azure Migrate: Avaliação do Servidor

Este artigo fornece uma visão geral das avaliações na ferramenta De avaliação do [servidor Azure Migrate: Server.](migrate-services-overview.md#azure-migrate-server-assessment-tool) A ferramenta pode avaliar no local máquinas virtuais VMware, VMs Hiper-V e servidores físicos para migração para Azure.

## <a name="whats-an-assessment"></a>O que é uma avaliação?

Uma avaliação com a ferramenta de avaliação do servidor mede a prontidão e estima o efeito da migração de servidores no local para o Azure.

> [!NOTE]
> No Governo de Azure, reveja os locais de avaliação [de alvos apoiados.](migrate-support-matrix.md#supported-geographies-azure-government) Note que as recomendações de tamanho VM em avaliações usarão a série VM especificamente para as regiões da Nuvem governamental. [Saiba mais](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) sobre os tipos de VM.

## <a name="types-of-assessments"></a>Tipos de avaliações

As avaliações que cria com a Avaliação do Servidor são uma imagem pontual dos dados. A Avaliação do Servidor fornece dois tipos de avaliações.

**Tipo de avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Baseado no desempenho** | Avaliações que fazem recomendações com base em dados de desempenho recolhidos | A recomendação de tamanho VM baseia-se em dados de CPU e de utilização de RAM.<br/><br/> A recomendação do tipo disco baseia-se nas operações de entrada/saída por segundo (IOPS) e na produção dos discos no local. Os tipos de discos são discos Azure Standard HDD, Azure Standard SSD e Azure Premium.
**As-is no local** | Avaliações que não usam dados de desempenho para fazer recomendações | A recomendação de tamanho VM baseia-se no tamanho vm no local.<br/><br> O tipo de disco recomendado baseia-se no tipo de armazenamento selecionado para a avaliação.

## <a name="how-do-i-run-an-assessment"></a>Como faço uma avaliação?

Há algumas maneiras de fazer uma avaliação.

- Avaliar as máquinas utilizando metadados de servidores recolhidos por um aparelho ligeiro de migração Azure. O aparelho descobre máquinas no local. Em seguida, envia metadados de máquinas e dados de desempenho para o Azure Migrate.
- Avaliar as máquinas utilizando metadados de servidores importados num formato de valores separados pela vírvia (CSV).

## <a name="how-do-i-assess-with-the-appliance"></a>Como posso avaliar com o aparelho?

Se estiver a implantar um aparelho Azure Migrate para descobrir servidores no local, faça os seguintes passos:

1. Instale o Azure e o seu ambiente no local para trabalhar com a Avaliação do Servidor.
1. Para a sua primeira avaliação, crie um projeto Azure e adicione-lhe a ferramenta de Avaliação do Servidor.
1. Implante um aparelho ligeiro de migração Azure. O aparelho descobre continuamente as máquinas no local e envia metadados de máquinas e dados de desempenho para o Azure Migrate. Implante o aparelho como VM ou máquina física. Não precisas de instalar nada nas máquinas que queiras avaliar.

Depois de o aparelho começar a ser descoberto, pode recolher máquinas que pretende avaliar em grupo e fazer uma avaliação para o grupo.

Siga os nossos tutoriais para [VMware,](tutorial-prepare-vmware.md) [Hyper-V](tutorial-prepare-hyper-v.md)ou [servidores físicos](tutorial-prepare-physical.md) para experimentar estes passos.

## <a name="how-do-i-assess-with-imported-data"></a>Como posso avaliar com os dados importados?

Se estiver a avaliar os servidores utilizando um ficheiro CSV, não precisa de um aparelho. Em vez disso, faça os seguintes passos:

1. Configurar o Azure para trabalhar com a Avaliação do Servidor.
1. Para a sua primeira avaliação, crie um projeto Azure e adicione-lhe a ferramenta de Avaliação do Servidor.
1. Descarregue um modelo CSV e adicione-lhe dados do servidor.
1. Importa o modelo para a Avaliação do Servidor.
1. Descubra os servidores adicionados com a importação, reúna-os em um grupo, e eexecute uma avaliação para o grupo.

## <a name="what-data-does-the-appliance-collect"></a>Que dados o aparelho recolhe?

Se estiver a utilizar o aparelho Azure Migrate para avaliação, conheça os dados de metadados e de desempenho recolhidos para [VMware](migrate-appliance.md#collected-data---vmware) e [Hyper-V](migrate-appliance.md#collected-data---hyper-v).

## <a name="how-does-the-appliance-calculate-performance-data"></a>Como é que o aparelho calcula os dados de desempenho?

Se utilizar o aparelho para ser descoberto, recolhe dados de desempenho para definições de cálculo com estes passos:

1. O aparelho recolhe um ponto de amostra em tempo real.

    - **VMs vMware**: Um ponto de amostra é recolhido a cada 20 segundos.
    - **VMs hiper-V**: Um ponto de amostra é recolhido a cada 30 segundos.
    - **Servidores físicos**: Um ponto de amostra é recolhido a cada cinco minutos.

1. O aparelho combina os pontos de amostra para criar um único ponto de dados a cada 10 minutos. Para criar o ponto de dados, o aparelho seleciona os valores de pico de todas as amostras. Em seguida, envia os dados apontar para Azure.
1. A Avaliação do Servidor armazena todos os pontos de dados de 10 minutos do último mês.
1. Quando cria uma avaliação, a Avaliação do Servidor identifica o ponto de dados adequado a utilizar para a detenção. A identificação baseia-se nos valores percentuais para o histórico de *desempenho* e *utilização do percentil.*

    - Por exemplo, se o histórico de desempenho é de uma semana e a utilização do percentil é o percentil 95, a Avaliação do Servidor classifica os pontos de amostra de 10 minutos para a última semana. Classifica-os em ordem ascendente e escolhe o 95º valor por cento por direitos.
    - O valor do percentil 95 garante que ignora qualquer fora-de-jogo, que pode ser incluído se escolher o percentil 99.
    - Se quiser escolher o pico de utilização para o período e não quiser perder nenhum outliers, selecione o percentil 99 para utilização do percentil.

1. Este valor é multiplicado pelo fator de conforto para obter os dados eficazes de utilização do desempenho destas métricas que o aparelho recolhe:

    - Utilização da CPU
    - Utilização de RAM
    - IOPS do disco (ler e escrever)
    - Entrada em disco (ler e escrever)
    - Entrada de rede (dentro e fora)

## <a name="how-are-assessments-calculated"></a>Como são calculadas as avaliações?

A Avaliação do Servidor utiliza os metadados e os dados de desempenho das máquinas no local para calcular as avaliações. Se utilizar o aparelho Azure Migrate, a avaliação utiliza os dados recolhidos pelo aparelho. Mas se fizer uma avaliação importada usando um ficheiro CSV, fornece os metadados para o cálculo.

Os cálculos ocorrem nestas três fases:

1. **Calcular a prontidão do Azure**: Avaliar se as máquinas são adequadas para a migração para Azure.
1. **Calcular recomendações**de dimensionamento : Calcular o cálculo, armazenamento e dimensionamento da rede.
1. **Calcular os custos mensais**: Calcular os custos estimados mensais de cálculo e armazenamento para o funcionamento das máquinas em Azure após a migração.

Os cálculos estão na ordem anterior. Um servidor de máquinas passa para uma fase posterior apenas se passar na anterior. Por exemplo, se um servidor falhar na fase de prontidão do Azure, está marcado como inadequado para o Azure. Dimensionamento e cálculos de custos não são feitos para o servidor.

## <a name="whats-in-an-assessment"></a>Novidades nas avaliações

Aqui está o que está incluído numa avaliação na Avaliação do Servidor:

Propriedade | Detalhes
--- | ---
**Localização de destino** | O local para onde quer migrar. A Avaliação do Servidor suporta atualmente estas regiões-alvo do Azure:<br/><br/> Austrália Leste, Austrália Sudeste, Brasil Sul, Canadá Central, Canadá Leste, Índia Central, Eua Central, China Leste, China Norte, Leste dos EUA, Leste dos EUA 2, Alemanha Central, Alemanha Nordeste, Japão Leste, Japão Oeste, Coreia Central, Coreia do Sul, Norte-Central EUA, Norte Da Europa, Centro-Sul dos EUA, Sudeste Asiático, Índia do Sul, Reino Unido Sul, Reino Unido Ocidental, EUA Gov Arizona, EUA Gov , West Central US, Europa Ocidental, Índia Ocidental, Oeste dos EUA e US 2 Ocidental.
**Disco de armazenamento de alvo (as-is dimensionamento)** | O tipo de disco a utilizar para armazenamento em Azure. <br/><br/> Especifique o disco de armazenamento do alvo como gerido por Prémios, gerido por Standard SSD ou gerido por Standard HDD.
**Disco de armazenamento de alvo (tamanho baseado no desempenho)** | Especifica o tipo de disco de armazenamento alvo como automático, gerido por Prémios, gerido por HDD ou gerido por Standard SSD.<br/><br/> **Automática**: A recomendação do disco baseia-se nos dados de desempenho dos discos, ou seja, o IOPS e a entrada.<br/><br/>**Premium ou Standard**: A avaliação recomenda um SKU de disco dentro do tipo de armazenamento selecionado.<br/><br/> Se quiser um acordo de serviço vm de uma instância única (SLA) de 99,9%, considere utilizar discos geridos por Prémios. Esta utilização garante que todos os discos da avaliação são recomendados como discos geridos por Prémio.<br/><br/> A Azure Migrate apoia apenas discos geridos para avaliação da migração.
**Instâncias de máquinavirtual reservada seleto saqueadas de Azure** | Especifica [as instâncias reservadas](https://azure.microsoft.com/pricing/reserved-vm-instances/) de modo a que as estimativas de custos na avaliação as tomem em consideração.<br/><br/> A Tualmente, a Azure Migrate apoia os Casos De VM Reservados Azure apenas para ofertas pay-as-you-go.
**Critérios de dimensionamento** | Usado para corrigir o VM Azure.<br/><br/> Utilize o dimensionamento as-is ou o tamanho baseado no desempenho.
**Histórico de desempenho** | Usado com tamanho baseado no desempenho. O histórico de desempenho especifica a duração utilizada quando os dados de desempenho são avaliados.
**Utilização de percentil** | Usado com tamanho baseado no desempenho. A utilização do percentil especifica o valor percentil da amostra de desempenho utilizada para a desdireito.
**Série das VMs** | A série Azure VM que pretende considerar para a destitução de direitos. Por exemplo, se não tiver um ambiente de produção que precise de VMs da série A em Azure, pode excluir sérieS A da lista de séries.
**Fator de conforto** | O tampão utilizado durante a avaliação. É aplicado aos dados de CPU, RAM, disk e utilização da rede para VMs. É responsável por questões como o uso sazonal, histórico de desempenho curto e provável aumento sustâneo.<br/><br/> Por exemplo, um VM de 10 núcleos com 20% de utilização normalmente resulta num VM de dois núcleos. Com um fator de conforto de 2.0, o resultado é um VM de quatro núcleos.
**Oferta** | A [oferta azure](https://azure.microsoft.com/support/legal/offer-details/) na qual está matriculado. A Avaliação do Servidor estima o custo dessa oferta.
**Moeda** | A moeda de faturação para a sua conta.
**Desconto (%)** | Quaisquer descontos específicos para a subscrição que receber em cima da oferta azure. A predefinição é 0%.
**Tempo de atividade de VM** | A duração dos dias por mês e horas por dia para Os VMs Azure que não funcionam continuamente. As estimativas de custos baseiam-se nessa duração.<br/><br/> Os valores predefinidos são de 31 dias por mês e 24 horas por dia.
**Benefício Híbrido do Azure** | Especifica se tem garantia de software e é elegível para [o Benefício Híbrido Azure.](https://azure.microsoft.com/pricing/hybrid-use-benefit/) Se a definição tiver o valor padrão "Sim", os preços do Azure para sistemas operativos que não o Windows são considerados para VMs do Windows.

[Reveja as melhores práticas](best-practices-assessment.md) para criar uma avaliação com a Avaliação do Servidor.

## <a name="calculate-readiness"></a>Calcular prontidão

Nem todas as máquinas são adequadas para funcionar em Azure. A Avaliação do Servidor avalia todas as máquinas no local e atribui-lhes uma categoria de prontidão.

- **Pronto para Azure**: A máquina pode ser migrada como está para Azure sem alterações. Começará em Azure com todo o apoio azure.
- **Pronta condicionalmente para O Azure**: A máquina pode arrancar em Azure, mas pode não ter suporte azure completo. Por exemplo, o Azure não suporta uma máquina que esteja a executar uma versão antiga do Windows Server. Tens de ter cuidado antes de migrares estas máquinas para o Azure. Para corrigir quaisquer problemas de prontidão, siga a orientação de reparação que a avaliação sugere.
- **Não está pronto para O Azure:** A máquina não arranca em Azure. Por exemplo, se o disco de uma máquina no local armazena mais de 64 TB, o Azure não consegue alojar a máquina. Siga a orientação de reparação para corrigir o problema antes da migração.
- **Prontidão desconhecida**: O Azure Migrate não consegue determinar a prontidão da máquina devido a metadados insuficientes.

Para calcular a prontidão, a Avaliação do Servidor analisa as propriedades da máquina e as definições do sistema operativo resumidas nas tabelas seguintes.

### <a name="machine-properties"></a>Propriedades da máquina

A Avaliação do Servidor analisa as seguintes propriedades de um VM no local para determinar se pode funcionar no Azure.

Propriedade | Detalhes | Estado de prontidão azure
--- | --- | ---
**Tipo de arranque** | O Azure suporta VMs com um tipo de bios de arranque, não UEFI. | Preparado condicionalmente se o tipo de arranque for UEFI
**Núcleos** | Cada máquina não deve ter mais de 128 núcleos, que é o número máximo que um Azure VM suporta.<br/><br/> Se o histórico de desempenho estiver disponível, a Azure Migrate considera os núcleos utilizados para comparação. Se as definições de avaliação especificarem um fator de conforto, o número de núcleos utilizados é multiplicado pelo fator de conforto.<br/><br/> Se não houver histórico de desempenho, a Azure Migrate usa os núcleos atribuídos sem aplicar o fator de conforto. | Pronto se o número de núcleos estiver dentro do limite
**RAM** | Cada máquina não deve ter mais de 3.892 GB de RAM, que&nbsp;é o tamanho máximo que uma série Azure M Standard_M128m<sup>2</sup> Suportes VM. [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Se o histórico de desempenho estiver disponível, a Azure Migrate considera a RAM utilizada para comparação. Se for especificado um fator de conforto, a RAM utilizada é multiplicada pelo fator de conforto.<br/><br/> Se não houver histórico, a RAM atribuída é usada sem aplicação de um fator de conforto.<br/><br/> | Pronto se a quantidade de RAM estiver dentro do limite
**Disco de armazenamento** | O tamanho atribuído de um disco não deve ser superior a 32 TB. Embora o Azure suporte discos de 64 TB com discos Azure Ultra SSD, o Azure Migrate: Server Assessment verifica atualmente 32 TB como o limite de tamanho do disco porque ainda não suporta Ultra SSD. <br/><br/> O número de discos ligados à máquina, incluindo o disco OS, deve ser de 65 ou menos. | Pronto se o tamanho e o número do disco estiverem dentro dos limites
**Redes** | Uma máquina não deve ter mais de 32 interfaces de rede (NICs) ligadas a ela. | Pronto se o número de NICs estiver dentro do limite

### <a name="guest-operating-system"></a>Sistema operativo convidado

Juntamente com a revisão das propriedades vm, server Assessment olha para o sistema operativo de um hóspede de uma máquina para determinar se pode funcionar em Azure.

> [!NOTE]
> Para lidar com a análise dos hóspedes para VMware VMs, a Avaliação do Servidor utiliza o sistema operativo especificado para o VM no VCenter Server. Para os VMs Linux em vMware, a Avaliação do Servidor não identifica aversão do kernel do OS convidado.

A Avaliação do Servidor utiliza a seguinte lógica para identificar a prontidão do Azure com base no sistema operativo:

**Sistema Operativo** | **Detalhes** | **Estado de prontidão azure**
--- | --- | ---
Windows Server 2016 e todos os SPs | O Azure oferece todo o apoio. | Pronto para o Azure.
Windows Server 2012 R2 e todos os SPs | O Azure oferece todo o apoio. | Pronto para o Azure.
Windows Server 2012 e todos os SPs | O Azure oferece todo o apoio. | Pronto para o Azure.
Windows Server 2008 R2 com todos os SPs | O Azure oferece todo o apoio.| Pronto para o Azure.
Windows Server 2008 (32-bits e 64-bits) | O Azure oferece todo o apoio. | Pronto para o Azure.
Windows Server 2003 e Windows Server 2003 R2 | Estes sistemas operativos passaram as suas datas de fim de suporte e precisam de um Acordo de [Apoio Personalizado (CSA)](https://aka.ms/WSosstatement) para apoio no Azure. | Preparado condicionalmente para Azure. Considere melhorar o Sistema operativo antes de migrar para Azure.
Windows 2000, Windows 98, Windows 95, Windows NT, Windows 3.1 e MS-DOS | Estes sistemas operativos passaram as datas de fim de suporte. A máquina pode começar em Azure, mas azure não fornece suporte para os soários. | Preparado condicionalmente para Azure. Recomendamos que atualize o Sistema operativo antes de migrar para O Azure.
Windows 7, Windows 8 e Windows 10 | O Azure fornece suporte apenas com uma subscrição do [Estúdio Visual.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Preparado condicionalmente para Azure.
Windows 10 Pro | A Azure presta apoio com os Direitos de [Hospedagem Multitenant.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Preparado condicionalmente para Azure.
Windows Vista e Windows XP Professional | Estes sistemas operativos passaram as datas de fim de suporte. A máquina pode começar em Azure, mas azure não fornece suporte para os soários. | Preparado condicionalmente para Azure. Recomendamos que atualize o Sistema operativo antes de migrar para O Azure.
Linux | Veja os [sistemas operativos Linux](../virtual-machines/linux/endorsed-distros.md) que o Azure apoia. Outros sistemas operativos Linux podem começar em Azure. Mas recomendamos que atualize o Spara para uma versão endossada antes de migrar para o Azure. | Pronto para o Azure se a versão for endossada.<br/><br/>Pronta condicionalmente se a versão não for endossada.
Outros sistemas operativos como o Oracle Solaris, apple macOS e FreeBSD | O Azure não apoia estes sistemas operativos. A máquina pode começar em Azure, mas azure não fornece suporte para os soários. | Preparado condicionalmente para Azure. Recomendamos que instale um Sistema operativo suportado antes de migrar para o Azure.  
OS especificado como **Outros** no servidor vCenter | O Azure Migrate não consegue identificar o SO neste caso. | Prontidão desconhecida. Certifique-se de que o Azure suporta o Sistema operativo que funciona dentro do VM.
Sistemas operativos de 32 bits | A máquina pode começar em Azure, mas azure pode não fornecer suporte total. | Preparado condicionalmente para Azure. Considere a atualização para um Sistema operativo de 64 bits antes de migrar para Azure.

## <a name="calculating-sizing"></a>Cálculo do dimensionamento

Depois de a máquina estar marcada como pronta para o Azure, a Avaliação do Servidor faz recomendações de dimensionamento. Estas recomendações identificam o VM Azure e o disco SKU. Os cálculos de dimensionamento dependem do facto de estar a usar o tamanho como está no local ou o tamanho baseado no desempenho.

### <a name="calculate-sizing-as-is-on-premises"></a>Calcular o dimensionamento (as-is on-local)

 Se utilizar o dimensionamento como está no local, a Avaliação do Servidor não considera o histórico de desempenho dos VMs e discos.

- **Dimensionamento computacional**: A Avaliação do Servidor atribui um SKU Azure VM com base no tamanho atribuído no local.
- **Armazenamento e dimensionamento**do disco: A Avaliação do Servidor analisa o tipo de armazenamento especificado nas propriedades de avaliação e recomenda o tipo de disco apropriado. Os possíveis tipos de armazenamento são Standard HDD, Standard SSD e Premium. O tipo de armazenamento predefinido é Premium.
- **Dimensionamento da rede**: A Avaliação do Servidor considera o adaptador de rede na máquina no local.

### <a name="calculate-sizing-performance-based"></a>Calcular o dimensionamento (baseado no desempenho)

Se utilizar o dimensionamento baseado no desempenho, a Avaliação do Servidor faz recomendações de dimensionamento da seguinte forma:

- A Avaliação do Servidor considera o histórico de desempenho da máquina para identificar o tamanho VM e o tipo de disco em Azure.
- Se importar servidores utilizando um ficheiro CSV, os valores que especifica são utilizados. Este método é especialmente útil se tiver alocada excessivamente a máquina no local, a utilização é baixa e você quer corrigir o VM Azure para economizar custos.
- Se não quiser utilizar os dados de desempenho, redefinir os critérios de dimensionamento para as-is no local, como descrito na secção anterior.

#### <a name="calculate-storage-sizing"></a>Calcular o tamanho do armazenamento

Para o tamanho do armazenamento, a Azure Migrate tenta mapear cada disco que está ligado à máquina a um disco Azure. Dimensionamento funciona da seguinte forma:

1. A Avaliação do Servidor adiciona a leitura e a escrita de IOPS de um disco para obter o iOPS total necessário. Da mesma forma, adiciona os valores de leitura e escrita de produção para obter a produção total de cada disco.
1. Se especificou o tipo de armazenamento como automático, o tipo selecionado baseia-se nos iOPS e valores de entrada eficazes. A Avaliação do Servidor determina se deve mapear o disco para um HDD standard, SSD standard ou disco Premium em Azure. Se o tipo de armazenamento estiver definido para um desses tipos de disco, a Avaliação do Servidor tenta encontrar um SKU de disco dentro do tipo de armazenamento selecionado.
1. Os discos são selecionados da seguinte forma:
    - Se a Avaliação do Servidor não encontrar um disco com o IOPS e a entrada necessários, marca a máquina como inadequada para o Azure.
    - Se a Avaliação do Servidor encontrar um conjunto de discos adequados, seleciona os discos que suportam a localização especificada nas definições de avaliação.
    - Se existirem vários discos elegíveis, a Avaliação do Servidor seleciona o disco com o menor custo.
    - Se os dados de desempenho de qualquer disco não estiverem disponíveis, o tamanho do disco de configuração é utilizado para encontrar um disco SSD Standard em Azure.

#### <a name="calculate-network-sizing"></a>Calcular o dimensionamento da rede

A Avaliação do Servidor tenta encontrar um VM Azure que suporte o número e o desempenho exigido dos adaptadores de rede ligados à máquina no local.

- Para obter o desempenho eficaz da rede do VM no local, a Avaliação do Servidor agrega a taxa de transmissão de dados da máquina (rede fora) em todos os adaptadores de rede. Em seguida, aplica o fator de conforto. Utiliza o valor resultante para encontrar um VM Azure que possa suportar o desempenho da rede necessário.
- Juntamente com o desempenho da rede, a Server Assessment também considera se o Azure VM pode suportar o número necessário de adaptadores de rede.
- Se os dados de desempenho da rede não estiverem disponíveis, a Avaliação do Servidor considera apenas a contagem de adaptadores de rede para dimensionamento vm.

#### <a name="calculate-compute-sizing"></a>Calcular o tamanho do cálculo

Depois de calcular os requisitos de armazenamento e rede, a Server Assessment considera os requisitos de CPU e RAM para encontrar um tamanho VM adequado em Azure.

- A Azure Migrate olha para os núcleos utilizados eficazes e ram para encontrar um tamanho Azure VM adequado.
- Se não for encontrado um tamanho adequado, a máquina está marcada como inadequada para o Azure.
- Se for encontrado um tamanho adequado, o Azure Migrate aplica os cálculos de armazenamento e rede. Em seguida, aplica definições de localização e nível de preços para a recomendação final do tamanho vm.
- Se existirem vários tamanhos de VMs do Azure elegíveis, é recomendado aquele que tiver o custo mais baixo.

## <a name="confidence-ratings-performance-based"></a>Classificações de confiança (baseadas no desempenho)

Cada avaliação baseada no desempenho no Azure Migrate está associada a uma classificação de confiança. A classificação varia de uma (mais baixa) a cinco (mais altas) estrelas. A classificação de confiança ajuda-o a estimar a fiabilidade das recomendações de tamanho que a Azure Migrate fornece.

- A classificação de confiança é atribuída a uma avaliação. A classificação baseia-se na disponibilidade de pontos de dados necessários para calcular a avaliação.
- Para dimensionamento baseado no desempenho, a Avaliação do Servidor necessita:
    - Os dados de utilização da CPU e da VM RAM.
    - O IOPS do disco e os dados de entrada de cada disco ligado ao VM.
    - A rede I/O para lidar com o dimensionamento baseado no desempenho de cada adaptador de rede ligado a um VM.

Se algum destes números de utilização não estiver disponível, as recomendações de tamanho podem não ser fiáveis.

> [!NOTE]
> As classificações de confiança não são atribuídas para servidores avaliados usando um ficheiro CSV importado. As classificações também não são aplicáveis para a avaliação como está no local.

### <a name="ratings"></a>Classificações

Este quadro mostra as classificações de confiança da avaliação, que dependem da percentagem de pontos de dados disponíveis:

   **Disponibilidade de pontos de dados** | **Classificação de confiança**
   --- | ---
   0-20% | 1 estrela
   21-40% | 2 estrelas
   41-60% | 3 estrelas
   61-80% | 4 estrelas
   81-100% | 5 estrelas

### <a name="low-confidence-ratings"></a>Baixas classificações de confiança

Aqui estão algumas razões pelas quais uma avaliação poderia obter uma classificação de confiança baixa:

- Não perfilou o seu ambiente durante a duração pela qual está a criar a avaliação. Por exemplo, se criar a avaliação com a duração do desempenho definida para um dia, deve esperar pelo menos um dia depois de começar a descobrir para que todos os pontos de dados sejam recolhidos.
- Alguns VMs foram encerrados durante o período para o qual a avaliação foi calculada. Se algum VMs for desligado por alguma duração, a Avaliação do Servidor não pode recolher os dados de desempenho desse período.
- Foram criados alguns VMs durante o período para o qual a avaliação foi calculada. Por exemplo, assuma que criou uma avaliação para o histórico de desempenho do mês passado, mas alguns VMs foram criados apenas há uma semana. O histórico de desempenho dos novos VMs não existirá durante toda a duração.

> [!NOTE]
> Se a classificação de confiança de qualquer avaliação for inferior a cinco estrelas, recomendamos que espere pelo menos um dia para que o aparelho perfile o ambiente e, em seguida, recalcule a avaliação. Caso contrário, o tamanho baseado no desempenho pode não ser fiável. Nesse caso, recomendamos que altere a avaliação para o tamanho no local.

## <a name="calculate-monthly-costs"></a>Calcular os custos mensais

Depois de as recomendações de dimensionamento estarem completas, a Azure Migrate calcula os custos de cálculo e armazenamento para após a migração.

- **Custo da computação**: A Azure Migrate utiliza o tamanho de VM Azure recomendado e a API de faturação Azure para calcular o custo mensal para o VM.

    O cálculo tem em conta:
    - Sistema operativo
    - Garantia de software
    - Instâncias reservadas
    - Tempo de atividade de VM
    - Localização
    - Definições cambiais

    A Avaliação do Servidor agrega o custo em todas as máquinas para calcular o custo total mensal da computação.

- **Custo de armazenamento**: O custo mensal de armazenamento de uma máquina é calculado agregando o custo mensal de todos os discos que estão ligados à máquina.

    A Avaliação do Servidor calcula os custos totais mensais de armazenamento agregando os custos de armazenamento de todas as máquinas. Atualmente, o cálculo não considera as ofertas especificadas nas definições de avaliação.

Os custos são apresentados na moeda especificada nas definições de avaliação.

## <a name="next-steps"></a>Passos seguintes

[Reveja](best-practices-assessment.md) as melhores práticas para a criação de avaliações. 

- Saiba mais sobre avaliações de execução de [VMware VMs,](tutorial-prepare-vmware.md) [VMs Hiper-V](tutorial-prepare-hyper-v.md)e [servidores físicos](tutorial-prepare-physical.md).
- Saiba avaliar os servidores [importados com um ficheiro CSV](tutorial-assess-import.md).
- Aprenda sobre a criação de [visualização da dependência.](concepts-dependency-visualization.md)
