---
title: Avaliações de Azure VM na Avaliação do Servidor Migratório Azure
description: Conheça as avaliações na Avaliação do Servidor Azure Migrate
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: 4020df3ef77e4b8ae0618108f539322092b93079
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91275528"
---
# <a name="server-assessment-overview-migrate-to-azure-vms"></a>Visão geral da avaliação do servidor (migrar para VMs Azure)

Este artigo fornece uma visão geral das avaliações na ferramenta [Azure Migrate: Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool) A ferramenta pode avaliar no local máquinas virtuais VMware, VMs hiper-V e servidores físicos para migração para Azure.

## <a name="whats-an-assessment"></a>O que é uma avaliação?

Uma avaliação com a ferramenta de avaliação do servidor mede a prontidão e estima o efeito da migração dos servidores no local para Ozure.

> [!NOTE]
> No Governo de Azure, reveja os locais de avaliação [de alvos apoiados.](migrate-support-matrix.md#supported-geographies-azure-government) Note que as recomendações de tamanho VM nas avaliações utilizarão a série VM especificamente para as regiões da Nuvem governamental. [Saiba mais](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) sobre os tipos de VM.

## <a name="types-of-assessments"></a>Tipos de avaliações

Existem dois tipos de avaliações que pode criar usando Azure Migrate: Avaliação do servidor.

**Tipo de Avaliação** | **Detalhes**
--- | --- 
**VM do Azure** | Avaliações para migrar os seus servidores no local para máquinas virtuais do Azure. <br/><br/> Pode avaliar as [VMs VMware](how-to-set-up-appliance-vmware.md) no local, as [VMs Hyper-V](how-to-set-up-appliance-hyper-v.md) e os [servidores físicos](how-to-set-up-appliance-physical.md) para a migração para o Azure com este tipo de avaliação.
**Solução VMware no Azure (AVS)** | Avaliações para migrar os seus servidores no local para o [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Pode avaliar as [VMs VMware](how-to-set-up-appliance-vmware.md) no local para a migração para o Azure VMware Solution (AVS) com este tipo de avaliação.[Saiba mais](concepts-azure-vmware-solution-assessment-calculation.md)

As avaliações que cria com a Avaliação do Servidor são uma imagem pontual dos dados. Uma avaliação Azure VM na Avaliação do Servidor fornece duas opções de critérios de dimensionamento:

**Tipo de avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Com base no desempenho** | Avaliações que fazem recomendações com base nos dados de desempenho recolhidos | A recomendação sobre o tamanho dos VM baseia-se em dados de utilização de CPU e RAM.<br/><br/> A recomendação do tipo disco baseia-se nas operações de entrada/saída por segundo (IOPS) e na produção dos discos no local. Os tipos de discos são discos Azure Standard HDD, Azure Standard SSD e Azure Premium.
**Como está no local** | Avaliações que não usam dados de desempenho para fazer recomendações | A recomendação de tamanho VM baseia-se no tamanho vM no local.<br/><br> O tipo de disco recomendado baseia-se no tipo de armazenamento selecionado para a avaliação.

## <a name="how-do-i-run-an-assessment"></a>Como faço uma avaliação?

Há algumas maneiras de fazer uma avaliação.

- Avaliar as máquinas utilizando metadados do servidor recolhidos por um aparelho Azure Migrate leve. O aparelho descobre máquinas no local. Em seguida, envia metadados de máquina e dados de desempenho para Azure Migrate.
- Avaliar as máquinas utilizando metadados de servidor que são importados num formato de valores separados por vírgula (CSV).

## <a name="how-do-i-assess-with-the-appliance"></a>Como avalio com o aparelho?

Se estiver a implantar um aparelho Azure Migrate para descobrir servidores no local, faça os seguintes passos:

1. Confiússe o Azure e o seu ambiente no local para trabalhar com a Avaliação do Servidor.
1. Para a sua primeira avaliação, crie um projeto Azure e adicione-lhe a ferramenta de Avaliação do Servidor.
1. Desloque um aparelho Azure Migrate leve. O aparelho descobre continuamente máquinas no local e envia metadados e dados de desempenho da máquina para a Azure Migrate. Desloque o aparelho como VM ou máquina física. Não precisa de instalar nada em máquinas que queira avaliar.

Após o início da descoberta da máquina, pode recolher máquinas que pretende avaliar em grupo e fazer uma avaliação para o grupo com o tipo de avaliação **Azure VM**.

Siga os nossos tutoriais para [VMware,](tutorial-prepare-vmware.md) [Hyper-V](tutorial-prepare-hyper-v.md)ou [servidores físicos](tutorial-prepare-physical.md) para experimentar estes passos.

## <a name="how-do-i-assess-with-imported-data"></a>Como avalio com dados importados?

Se estiver a avaliar os servidores utilizando um ficheiro CSV, não precisa de um aparelho. Em vez disso, faça os seguintes passos:

1. Configurar o Azure para trabalhar com a Avaliação do Servidor.
1. Para a sua primeira avaliação, crie um projeto Azure e adicione-lhe a ferramenta de Avaliação do Servidor.
1. Descarregue um modelo de CSV e adicione dados do servidor.
1. Importe o modelo na Avaliação do Servidor.
1. Descubra os servidores adicionados com a importação, reúna-os em um grupo e execute uma avaliação para o grupo com o tipo de avaliação **Azure VM**.

## <a name="what-data-does-the-appliance-collect"></a>Que dados o aparelho recolhe?

Se estiver a utilizar o aparelho Azure Migrate para avaliação, saiba mais sobre os metadados e dados de desempenho recolhidos para [VMware](migrate-appliance.md#collected-data---vmware) e [Hyper-V](migrate-appliance.md#collected-data---hyper-v).

## <a name="how-does-the-appliance-calculate-performance-data"></a>Como calcula o aparelho dados de desempenho?

Se utilizar o aparelho para ser descoberto, recolhe dados de desempenho para configurações de cálculo com estes passos:

1. O aparelho recolhe um ponto de amostra em tempo real.

    - **VMware VMs**: Um ponto de amostra é recolhido a cada 20 segundos.
    - **Hiper-V VMs**: Um ponto de amostra é recolhido a cada 30 segundos.
    - **Servidores físicos**: Um ponto de amostra é recolhido a cada cinco minutos.

1. O aparelho combina os pontos de amostra para criar um único ponto de dados a cada 10 minutos para servidores VMware e Hiper-V, e a cada 5 minutos para servidores físicos. Para criar o ponto de dados, o aparelho seleciona os valores de pico de todas as amostras. Em seguida, envia o ponto de dados para Azure.
1. A Avaliação do Servidor armazena todos os pontos de dados de 10 minutos do último mês.
1. Quando cria uma avaliação, a Avaliação do Servidor identifica o ponto de dados apropriado a utilizar para a utilização de direitos. A identificação baseia-se nos valores percentil para o *histórico de desempenho* e *utilização do percentil.*

    - Por exemplo, se o histórico de desempenho for de uma semana e a utilização percentil for o percentil 95, a Avaliação do Servidor classifica os pontos de amostra de 10 minutos da última semana. Classifica-os por ordem ascendente e escolhe o valor percentil 95 para a deposição de direitos.
    - O valor percentil 95 faz com que ignore qualquer outliers, que podem ser incluídos se escolher o percentil 99.
    - Se quiser escolher o pico de utilização para o período e não quiser perder nenhum outliers, selecione o percentil 99 para utilização percentil.

1. Este valor é multiplicado pelo fator de conforto para obter os dados de utilização eficazes do desempenho para estas métricas que o aparelho recolhe:

    - Utilização da CPU
    - Utilização do RAM
    - IOPS de disco (ler e escrever)
    - Produção de disco (ler e escrever)
    - Produção de rede (dentro e fora)

## <a name="how-are-azure-vm-assessments-calculated"></a>Como são calculadas as avaliações do Azure VM?

A Avaliação do Servidor utiliza os metadados e dados de desempenho das máquinas no local para calcular as avaliações. Se utilizar o aparelho Azure Migrate, a avaliação utiliza os dados que o aparelho recolhe. Mas se fizer uma avaliação importada usando um ficheiro CSV, fornece os metadados para o cálculo.

Os cálculos ocorrem nestas três fases:

1. **Calcular prontidão Azure**: Avaliar se as máquinas são adequadas para a migração para Azure.
1. **Calcular recomendações de dimensionamento**: Calcular, armazenar e dimensionar a rede.
1. **Calcular os custos mensais**: Calcular os custos estimados mensalmente de cálculo e armazenamento para a execução das máquinas em Azure após a migração.

Os cálculos estão na ordem anterior. Um servidor de máquinas move-se para uma fase posterior apenas se passar o anterior. Por exemplo, se um servidor falhar na fase de prontidão do Azure, está marcado como inadequado para o Azure. O tamanho e os cálculos de custos não são feitos para o servidor.

## <a name="whats-in-an-azure-vm-assessment"></a>O que há numa avaliação do Azure VM?

Aqui está o que está incluído numa avaliação de VM Azure na avaliação do servidor:

**Propriedade** | **Detalhes**
--- | ---
**Localização de destino** | O local para onde quer migrar. A Avaliação do Servidor suporta atualmente estas regiões-alvo do Azure:<br/><br/> Austrália Leste, Austrália Sudeste, Brasil Sul, Canadá Central, Canadá Leste, Índia Central, Eua Central, China Oriental, China Norte, Leste asiático, Leste dos EUA 2, Alemanha Central, Alemanha Nordeste, Japão Leste, Japão Oeste, Coreia Central, Coreia do Sul, Norte-Americano, Norte da Europa, Centro-Sul dos EUA, Sudeste Asiático, Sudeste asiático, Reino Unido, Reino Unido , Centro Oeste dos EUA, Europa Ocidental, Índia Ocidental, Eua Ocidental e Oeste dos EUA 2.
**Disco de armazenamento de destino (as-is dimensionamento)** | O tipo de disco a utilizar para armazenamento em Azure. <br/><br/> Especifique o disco de armazenamento alvo como gerido por Premium, gerido standard SSD ou gerido por HDD standard.
**Disco de armazenamento de destino (dimensionamento baseado no desempenho)** | Especifica o tipo de disco de armazenamento alvo como automático, gerido com Premium, gerido por HDD padrão ou gerido por SSD standard.<br/><br/> **Automático**: A recomendação do disco baseia-se nos dados de desempenho dos discos, ou seja, no IOPS e no produção.<br/><br/>**Premium ou Standard**: A avaliação recomenda um SKU de disco dentro do tipo de armazenamento selecionado.<br/><br/> Se pretender um acordo de nível de serviço VM de uma só instância (SLA) de 99,9%, considere a utilização de discos geridos com Premium. Esta utilização garante que todos os discos da avaliação são recomendados como discos geridos por Premium.<br/><br/> A azure Migrate suporta apenas discos geridos para avaliação de migração.
**Azure Reservado VM Instances** | Especifica [casos reservados](https://azure.microsoft.com/pricing/reserved-vm-instances/) de modo a que as estimativas de custos na avaliação as levem em conta.<br/><br/> Quando seleciona 'Instâncias Reservadas', o 'Desconto (%)» e as propriedades de 'uptime' VM não são aplicáveis.<br/><br/> A Azure Migrate suporta atualmente a Azure Reserved VM Instances apenas para ofertas pay-as-you-go.
**Critérios de dimensionamento** | Usado para direitos de direito o Azure VM.<br/><br/> Use o tamanho de tamanho ou tamanho baseado no desempenho.
**Histórico de desempenho** | Usado com tamanho baseado no desempenho. O histórico de desempenho especifica a duração utilizada quando os dados de desempenho são avaliados.
**Utilização de percentil** | Usado com tamanho baseado no desempenho. A utilização percentil especifica o valor percentil da amostra de desempenho utilizada para a direito.
**Série das VMs** | A série Azure VM que pretende considerar para a sua demissão. Por exemplo, se não tiver um ambiente de produção que precise de VMs da série A em Azure, pode excluir a série A da lista de séries.
**Fator de conforto** | O tampão utilizado durante a avaliação. É aplicado ao CPU, RAM, disco e dados de rede para VMs. Explica questões como o uso sazonal, o histórico de desempenho curto e, provavelmente, o aumento do uso futuro.<br/><br/> Por exemplo, um VM de 10 núcleos com 20% de utilização normalmente resulta num VM de dois núcleos. Com um fator de conforto de 2.0, o resultado é um VM de quatro núcleos em vez disso.
**Oferta** | A [oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/) em que está matriculado. A Avaliação do Servidor estima o custo dessa oferta.
**Moeda** | A moeda de faturação da sua conta.
**Desconto (%)** | Quaisquer descontos específicos por subscrição que receba em cima da oferta Azure. A predefinição é 0%.
**Tempo de atividade de VM** | A duração em dias por mês e horas diárias para VMs Azure que não funcionarão continuamente. As estimativas de custos baseiam-se nessa duração.<br/><br/> Os valores predefinidos são de 31 dias por mês e 24 horas por dia.
**Benefício Híbrido do Azure** | Especifica se tem garantia de software e é elegível para [benefício híbrido Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Se a definição tiver o valor padrão "Sim", os preços do Azure para sistemas operativos que não o Windows são considerados para VMs windows.
**Assinatura EA** | Especifica que uma subscrição do Acordo de Empresa (EA) é utilizada para a estimativa de custos. Tem em conta o desconto aplicável à subscrição. <br/><br/> Deixe as configurações para instâncias reservadas, desconto (%) e propriedades de uptime VM com as suas definições predefinidos.


[Reveja as melhores práticas](best-practices-assessment.md) para criar uma avaliação com a Avaliação do Servidor.

## <a name="calculate-readiness"></a>Calcular prontidão

Nem todas as máquinas são adequadas para funcionar em Azure. Uma Avaliação VM Azure avalia todas as máquinas no local e atribui-lhes uma categoria de prontidão.

- **Pronto para O Azure**: A máquina pode ser migrada como está para Azure sem alterações. Começará em Azure com todo o apoio do Azure.
- **Condicionalmente pronto para O Azure**: A máquina pode arrancar em Azure, mas pode não ter suporte Azure completo. Por exemplo, o Azure não suporta uma máquina que esteja a executar uma versão antiga do Windows Server. Tem de ter cuidado antes de migrar estas máquinas para Azure. Para corrigir quaisquer problemas de prontidão, siga a orientação de reparação que a avaliação sugere.
- **Não está pronto para O Azure:** A máquina não arranca em Azure. Por exemplo, se o disco de uma máquina no local armazena mais de 64 TB, o Azure não pode hospedar a máquina. Siga as orientações de reparação para corrigir o problema antes da migração.
- **Prontidão desconhecida**: A Azure Migrate não pode determinar a prontidão da máquina devido a metadados insuficientes.

Para calcular a prontidão, a Avaliação do Servidor revê as propriedades da máquina e as definições do sistema operativo resumidas nas seguintes tabelas.

### <a name="machine-properties"></a>Propriedades da máquina

Para uma Avaliação VM Azure, a Avaliação do Servidor revê as seguintes propriedades de um VM no local para determinar se pode funcionar em VMs Azure.

Propriedade | Detalhes | Estatuto de prontidão Azure
--- | --- | ---
**Tipo de bota** | O Azure suporta VMs com um tipo de botão de BIOS, não UEFI. | Condicionalmente pronto se o tipo de bota for UEFI
**Núcleos** | Cada máquina não deve ter mais de 128 núcleos, que é o número máximo que um Azure VM suporta.<br/><br/> Se o histórico de desempenho estiver disponível, Azure Migrate considera os núcleos utilizados para comparação. Se as definições de avaliação especificarem um fator de conforto, o número de núcleos utilizados é multiplicado pelo fator de conforto.<br/><br/> Se não houver histórico de desempenho, a Azure Migrate utiliza os núcleos atribuídos para aplicar o fator de conforto. | Pronto se o número de núcleos estiver dentro do limite
**RAM** | Cada máquina não deve ter mais de 3.892 GB de RAM, que é o tamanho máximo que uma série M Azure Standard_M128m &nbsp; <sup>suportes de 2</sup> VM. [Saiba mais](../virtual-machines/sizes.md).<br/><br/> Se o histórico de desempenho estiver disponível, a Azure Migrate considera a RAM utilizada para comparação. Se for especificado um fator de conforto, a RAM utilizada é multiplicada pelo fator de conforto.<br/><br/> Se não houver história, a RAM atribuída é usada para aplicar um fator de conforto.<br/><br/> | Pronto se a quantidade de RAM estiver dentro do limite
**Disco de armazenamento** | O tamanho atribuído de um disco não deve ser superior a 32 TB. Embora o Azure suporte discos de 64-TB com discos Azure Ultra SSD, Azure Migrate: A Avaliação do Servidor verifica atualmente 32 TB como limite de tamanho do disco porque ainda não suporta Ultra SSD. <br/><br/> O número de discos ligados à máquina, incluindo o disco SO, deve ser de 65 ou menos. | Pronto se o tamanho e o número do disco estiverem dentro dos limites
**Redes** | Uma máquina não deve ter mais de 32 interfaces de rede (NICs) ligadas a ela. | Pronto se o número de NICs estiver dentro do limite

### <a name="guest-operating-system"></a>Sistema operativo convidado

Para uma Avaliação VM Azure, juntamente com a revisão das propriedades de VM, a Avaliação do Servidor olha para o sistema operativo de um hóspede para determinar se pode funcionar no Azure.

> [!NOTE]
> Para lidar com a análise dos VMware VMs, a Avaliação do Servidor utiliza o sistema operativo especificado para o VM no vCenter Server. No entanto, o vCenter Server não fornece a versão kernel para sistemas operativos Linux VM. Para descobrir a versão, é necessário configurar a [descoberta da aplicação.](./how-to-discover-applications.md) Em seguida, o aparelho descobre informações de versão usando as credenciais de hóspedes que especifica quando configura a descoberta de aplicações.


A Avaliação do Servidor utiliza a seguinte lógica para identificar a prontidão do Azure com base no sistema operativo:

**Sistema operativo** | **Detalhes** | **Estatuto de prontidão Azure**
--- | --- | ---
Windows Server 2016 e todos os SPs | O Azure oferece todo o apoio. | Pronto para O Azure.
Windows Server 2012 R2 e todos os SPs | O Azure oferece todo o apoio. | Pronto para O Azure.
Windows Server 2012 e todos os SPs | O Azure oferece todo o apoio. | Pronto para O Azure.
Windows Server 2008 R2 com todos os SPs | O Azure oferece todo o apoio.| Pronto para O Azure.
Windows Server 2008 (32 bits e 64 bits) | O Azure oferece todo o apoio. | Pronto para O Azure.
Windows Server 2003 e Windows Server 2003 R2 | Estes sistemas operativos passaram as suas datas de fim de suporte e precisam de um Acordo de [Apoio Personalizado (CSA)](https://aka.ms/WSosstatement) para apoio em Azure. | Condicionalmente pronto para O Azure. Considere melhorar o SO antes de migrar para Azure.
Windows 2000, Windows 98, Windows 95, Windows NT, Windows 3.1 e MS-DOS | Estes sistemas operativos passaram as datas de fim de suporte. A máquina pode começar em Azure, mas a Azure não fornece suporte ao SISTEMA. | Condicionalmente pronto para O Azure. Recomendamos que atualize o SO antes de migrar para Azure.
Windows 7, Windows 8 e Windows 10 | O Azure fornece suporte apenas com uma [subscrição do Visual Studio.](../virtual-machines/windows/client-images.md) | Condicionalmente pronto para O Azure.
Windows 10 Pro | A Azure fornece suporte com [direitos de hospedagem multitenant.](../virtual-machines/windows/windows-desktop-multitenant-hosting-deployment.md) | Condicionalmente pronto para O Azure.
Windows Vista e Windows XP Professional | Estes sistemas operativos passaram as datas de fim de suporte. A máquina pode começar em Azure, mas a Azure não fornece suporte ao SISTEMA. | Condicionalmente pronto para O Azure. Recomendamos que atualize o SO antes de migrar para Azure.
Linux | Veja os [sistemas operativos Linux](../virtual-machines/linux/endorsed-distros.md) que o Azure apoia. Outros sistemas operativos Linux podem começar em Azure. Mas recomendamos que atualize o SO para uma versão endossada antes de migrar para Azure. | Pronto para o Azure se a versão for endossada.<br/><br/>Condicionalmente pronto se a versão não for endossada.
Outros sistemas operativos como o Oracle Solaris, Apple macOS e FreeBSD | O Azure não apoia estes sistemas operativos. A máquina pode começar em Azure, mas a Azure não fornece suporte ao SISTEMA. | Condicionalmente pronto para O Azure. Recomendamos que instale um SISTEMA suportado antes de migrar para Azure.  
OS especificado como **Outros** no servidor vCenter | Azure Migrate não consegue identificar o SO neste caso. | Prontidão desconhecida. Certifique-se de que o Azure suporta o sistema operativo que funciona dentro do VM.
Sistemas operativos de 32 bits | A máquina pode começar em Azure, mas Azure pode não fornecer suporte total. | Condicionalmente pronto para O Azure. Considere atualizar para um SISTEMA de 64 bits antes de migrar para Azure.

## <a name="calculating-sizing"></a>Cálculo do dimensionamento

Depois de a máquina estar marcada como pronta para o Azure, a Avaliação do Servidor faz recomendações de dimensionamento na avaliação do Azure VM. Estas recomendações identificam o Azure VM e o disco SKU. Os cálculos de dimensionamento dependem se está a usar o tamanho do tamanho no local ou o tamanho baseado no desempenho.

### <a name="calculate-sizing-as-is-on-premises"></a>Calcular o dimensionamento (como-está no local)

 Se utilizar o tamanho do as-is no local, a Avaliação do Servidor não considera o histórico de desempenho dos VMs e discos na avaliação do VM Azure.

- **Tamanho do cálculo**: A Avaliação do Servidor atribui um Azure VM SKU com base no tamanho atribuído no local.
- **Armazenamento e dimensionamento de discos**: A avaliação do servidor analisa o tipo de armazenamento especificado nas propriedades de avaliação e recomenda o tipo de disco apropriado. Os tipos de armazenamento possíveis são Standard HDD, Standard SSD e Premium. O tipo de armazenamento predefinido é Premium.
- **Dimensionamento da rede**: A avaliação do servidor considera o adaptador de rede na máquina no local.

### <a name="calculate-sizing-performance-based"></a>Calcular dimensionamento (baseado no desempenho)

Se utilizar o tamanho baseado no desempenho numa avaliação Azure VM, a Avaliação do Servidor faz recomendações de dimensionamento da seguinte forma:

- A Avaliação do Servidor considera o histórico de desempenho da máquina para identificar o tamanho e o tipo de disco VM em Azure.
- Se importar servidores utilizando um ficheiro CSV, os valores especificados são utilizados. Este método é especialmente útil se tiver globalizado a máquina no local, a utilização é baixa, e você quer direitos de direito o Azure VM para economizar custos.
- Se não quiser utilizar os dados de desempenho, repor os critérios de dimensionamento para as-is-in, conforme descrito na secção anterior.

#### <a name="calculate-storage-sizing"></a>Calcular o tamanho do armazenamento

Para o armazenamento numa avaliação Azure VM, a Azure Migrate tenta mapear cada disco que está ligado à máquina a um disco Azure. As obras de dimensionamento são as seguintes:

1. A Avaliação do Servidor adiciona a leitura e a escrita de IOPS de um disco para obter o total de IOPS necessário. Da mesma forma, adiciona os valores de leitura e de produção para obter a produção total de cada disco. No caso de avaliações baseadas em importações, tem a opção de fornecer o total de IOPS, produção total e total nº. de discos no ficheiro importado sem especificar as definições individuais do disco. Se o fizer, o tamanho do disco individual é ignorado e os dados fornecidos são utilizados diretamente para calcular o tamanho e selecione um VM SKU apropriado.

1. Se especificou o tipo de armazenamento como automático, o tipo selecionado baseia-se nos valores efetivos de IOPS e de produção. A Avaliação do Servidor determina se mapeia o disco para um disco Standard HDD, Standard SSD ou Premium em Azure. Se o tipo de armazenamento for definido para um desses tipos de disco, a Avaliação do Servidor tenta encontrar um SKU de disco dentro do tipo de armazenamento selecionado.
1. Os discos são selecionados da seguinte forma:
    - Se a Avaliação do Servidor não conseguir encontrar um disco com o IOPS e a produção necessários, ele marca a máquina como inadequada para o Azure.
    - Se a Avaliação do Servidor encontrar um conjunto de discos adequados, seleciona os discos que suportam a localização especificada nas definições de avaliação.
    - Se existirem vários discos elegíveis, a Avaliação do Servidor seleciona o disco com o menor custo.
    - Se os dados de desempenho de qualquer disco não estiverem disponíveis, o tamanho do disco de configuração é utilizado para encontrar um disco SSD standard em Azure.

#### <a name="calculate-network-sizing"></a>Calcular o tamanho da rede

Para uma avaliação do Azure VM, a Avaliação do Servidor tenta encontrar um VM Azure que suporte o número e exija o desempenho dos adaptadores de rede ligados à máquina no local.

- Para obter o desempenho eficaz da rede dos VM no local, a Avaliação do Servidor agrega a taxa de transmissão de dados da máquina (rede para fora) em todos os adaptadores de rede. Em seguida, aplica o fator de conforto. Utiliza o valor resultante para encontrar um VM Azure que possa suportar o desempenho de rede necessário.
- Juntamente com o desempenho da rede, a Avaliação do Servidor também considera se o Azure VM pode suportar o número necessário de adaptadores de rede.
- Se os dados de desempenho da rede não estiverem disponíveis, a Avaliação do Servidor considera apenas a contagem do adaptador de rede para o tamanho de VM.

#### <a name="calculate-compute-sizing"></a>Calcular o tamanho do cálculo

Depois de calcular os requisitos de armazenamento e rede, a Avaliação do Servidor considera os requisitos de CPU e RAM para encontrar um tamanho VM adequado em Azure.

- Azure Migrate olha para os núcleos e RAM utilizados eficazes para encontrar um tamanho Azure VM adequado.
- Se não for encontrado um tamanho adequado, a máquina está marcada como inadequada para o Azure.
- Se for encontrado um tamanho adequado, a Azure Migrate aplica os cálculos de armazenamento e rede. Em seguida, aplica definições de localização e de nível de preços para a recomendação final do tamanho do VM.
- Se existirem vários tamanhos de VMs do Azure elegíveis, é recomendado aquele que tiver o custo mais baixo.

## <a name="confidence-ratings-performance-based"></a>Classificações de confiança (baseadas no desempenho)

Cada avaliação Azure VM baseada no desempenho em Azure Migrate está associada a uma classificação de confiança. A classificação varia entre uma (mais baixa) e cinco estrelas (mais altas). A classificação de confiança ajuda-o a estimar a fiabilidade das recomendações de tamanho que a Azure Migrate fornece.

- A classificação de confiança é atribuída a uma avaliação. O rating baseia-se na disponibilidade de pontos de dados necessários para calcular a avaliação.
- Para dimensionamento baseado no desempenho, a Avaliação do Servidor precisa:
    - Os dados de utilização da CPU e da VM RAM.
    - O disco IOPS e os dados de produção de cada disco ligado ao VM.
    - A rede I/O para lidar com o tamanho baseado no desempenho de cada adaptador de rede ligado a um VM.

Se algum destes números de utilização não estiver disponível, as recomendações de tamanho podem não ser fiáveis.

> [!NOTE]
> As classificações de confiança não são atribuídas para servidores avaliados usando um ficheiro CSV importado. Ratings also aren't applicable for as-is on-premises assessment.

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

Eis algumas razões pelas quais uma avaliação pode obter uma classificação de baixa confiança:

- Não perfilou o seu ambiente durante a duração pela qual está a criar a avaliação. Por exemplo, se criar a avaliação com a duração de desempenho definida para um dia, deve esperar pelo menos um dia depois de começar a descobrir todos os pontos de dados para ser recolhido.
- Alguns VM foram encerrados durante o período para o qual a avaliação foi calculada. Se quaisquer VMs forem desligados durante algum tempo, a Avaliação do Servidor não pode recolher os dados de desempenho desse período.
- Foram criados alguns VM durante o período para o qual a avaliação foi calculada. Por exemplo, assuma que criou uma avaliação para o histórico de desempenho do mês passado, mas alguns VMs foram criados apenas há uma semana. O histórico de desempenho dos novos VMs não existirá durante toda a duração.

> [!NOTE]
> Se a classificação de confiança de qualquer avaliação for inferior a cinco estrelas, recomendamos que espere pelo menos um dia para que o aparelho perfile o ambiente e, em seguida, recalcule a avaliação. Caso contrário, o tamanho baseado no desempenho pode não ser fiável. Nesse caso, recomendamos que altere a avaliação para o tamanho do local.

## <a name="calculate-monthly-costs"></a>Calcular os custos mensais

Após a conclusão das recomendações de dimensionamento, uma avaliação do Azure VM em Azure Migrate calcula os custos de cálculo e armazenamento para após a migração.

- **Custo do cálculo**: A Azure Migrate utiliza o tamanho Azure VM recomendado e a API de Faturação Azure para calcular o custo mensal do VM.

    O cálculo tem em conta:
    - Sistema operativo
    - Garantia de software
    - Instâncias reservadas
    - Tempo de atividade de VM
    - Localização
    - Definições de moeda

    A Avaliação do Servidor agrega o custo em todas as máquinas para calcular o custo total mensal do cálculo.

- **Custo de armazenamento**: O custo de armazenamento mensal de uma máquina é calculado agregando o custo mensal de todos os discos que estão ligados à máquina.

    A Avaliação do Servidor calcula os custos totais de armazenamento mensal agregando os custos de armazenamento de todas as máquinas. Atualmente, o cálculo não considera ofertas especificadas nas definições de avaliação.

Os custos são apresentados na moeda especificada nas definições de avaliação.

## <a name="next-steps"></a>Passos seguintes

[Reveja as](best-practices-assessment.md) melhores práticas para criar avaliações. 

- Saiba mais sobre avaliações de execução de [VMware VMs,](tutorial-prepare-vmware.md) [Hiper-VMs](tutorial-prepare-hyper-v.md)e [servidores físicos](tutorial-prepare-physical.md).
- Saiba mais sobre a avaliação dos servidores [importados com um ficheiro CSV](tutorial-assess-import.md).
- Saiba como configurar [a visualização da dependência.](concepts-dependency-visualization.md)