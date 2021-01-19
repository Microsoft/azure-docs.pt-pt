---
title: Cálculos de avaliação do AVS em Azure Migrate | Microsoft Docs
description: Fornece uma visão geral dos cálculos de avaliação do AVS no serviço Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/25/2020
ms.openlocfilehash: f52c0296023098c755feb1bf0baba980f2988bd7
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567706"
---
# <a name="server-assessment-overview-migrate-to-azure-vmware-solution"></a>Visão geral da avaliação do servidor (migrar para a Solução VMware Azure)

[A Azure Migrate](migrate-services-overview.md) fornece um centro central para acompanhar a descoberta, avaliação e migração das suas aplicações e cargas de trabalho no local. Também rastreia as suas instâncias de nuvem privada e pública para Azure. O hub oferece ferramentas Azure Migrate para avaliação e migração, bem como ofertas de fornecedores de software independentes de terceiros (ISV).

A Avaliação do Servidor é uma ferramenta no Azure Migrate que avalia servidores no local para migração para máquinas virtuais Azure IaaS e Solução VMware Azure (AVS). Este artigo fornece informações sobre como as avaliações da Azure VMware Solution (AVS) são calculadas.

> [!NOTE]
> A avaliação da Solução VMware (AVS) da Azure está atualmente em pré-visualização e pode ser criada apenas para VMware VMs.

## <a name="types-of-assessments"></a>Tipos de avaliações

As avaliações que cria com a Avaliação do Servidor são uma imagem pontual dos dados. Existem dois tipos de avaliações que pode criar usando Azure Migrate: Avaliação do servidor.

**Tipo de Avaliação** | **Detalhes**
--- | --- 
**VM do Azure** | Avaliações para migrar os seus servidores no local para máquinas virtuais do Azure. <br/><br/> Pode avaliar os seus [VMS VMware](how-to-set-up-appliance-vmware.md)no local, [VMs hiper-V](how-to-set-up-appliance-hyper-v.md)e [servidores físicos](how-to-set-up-appliance-physical.md) para migração para Azure utilizando este tipo de avaliação. [Saiba mais](concepts-assessment-calculation.md)
**Solução VMware no Azure (AVS)** | Avaliações para migrar os seus servidores no local para o [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Pode avaliar as [VMs VMware](how-to-set-up-appliance-vmware.md) no local para a migração para o Azure VMware Solution (AVS) com este tipo de avaliação.[Saiba mais](concepts-azure-vmware-solution-assessment-calculation.md)

A avaliação da Solução VMware (AVS) em Avaliação do Servidor fornece duas opções de critérios de dimensionamento:

**Avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Com base no desempenho** | Avaliações baseadas em dados de desempenho recolhidos de VMs no local. | **Tamanho recomendado do nó**: Com base em dados de utilização da CPU e da memória, juntamente com o tipo de nó, o tipo de armazenamento e a definição de FTT que seleciona para a avaliação.
**Como no local** | Avaliações baseadas no dimensionamento no local. | **Tamanho do nó recomendado**: Com base no tamanho VM no local, juntamente com o tipo de nó, tipo de armazenamento e definição FTT que seleciona para a avaliação.

## <a name="how-do-i-run-an-assessment"></a>Como faço uma avaliação?

Há algumas maneiras de fazer uma avaliação.

- Avaliar as máquinas utilizando metadados do servidor recolhidos por um aparelho Azure Migrate leve. O aparelho descobre máquinas no local. Em seguida, envia metadados de máquina e dados de desempenho para Azure Migrate.
- Avaliar as máquinas utilizando metadados de servidor que são importados num formato de valores separados por vírgula (CSV).

## <a name="how-do-i-assess-with-the-appliance"></a>Como avalio com o aparelho?

Se estiver a implantar um aparelho Azure Migrate para descobrir servidores no local, faça os seguintes passos:

1. Confiússe o Azure e o seu ambiente no local para trabalhar com a Avaliação do Servidor.
2. Para a sua primeira avaliação, crie um projeto Azure e adicione-lhe a ferramenta de Avaliação do Servidor.
3. Desloque um aparelho Azure Migrate leve. O aparelho descobre continuamente máquinas no local e envia metadados e dados de desempenho da máquina para a Azure Migrate. Desloque o aparelho como VM. Não precisa de instalar nada em máquinas que queira avaliar.

Após o início da descoberta da máquina, pode recolher máquinas que pretende avaliar em grupo e fazer uma avaliação para o grupo com a avaliação do tipo **Azure VMware Solution (AVS)**.

Crie a sua primeira avaliação da Solução Azure VMware (AVS) seguindo os passos [aqui.](how-to-create-azure-vmware-solution-assessment.md)

## <a name="how-do-i-assess-with-imported-data"></a>Como avalio com dados importados?

Se estiver a avaliar os servidores utilizando um ficheiro CSV, não precisa de um aparelho. Em vez disso, faça os seguintes passos:

1. Configurar o Azure para trabalhar com a Avaliação do Servidor.
2. Para a sua primeira avaliação, crie um projeto Azure e adicione-lhe a ferramenta de Avaliação do Servidor.
3. Descarregue um modelo de CSV e adicione dados do servidor.
4. Importe o modelo na Avaliação do Servidor.
5. Descubra os servidores adicionados com a importação, reúna-os em um grupo e execute uma avaliação para o grupo com avaliação tipo **Azure VMware Solution (AVS)**.

## <a name="what-data-does-the-appliance-collect"></a>Que dados o aparelho recolhe?

Se estiver a utilizar o aparelho Azure Migrate para avaliação, saiba mais sobre os metadados e dados de desempenho recolhidos para [vMware](migrate-appliance.md#collected-data---vmware).

## <a name="how-does-the-appliance-calculate-performance-data"></a>Como calcula o aparelho dados de desempenho?

Se utilizar o aparelho para ser descoberto, recolhe dados de desempenho para configurações de cálculo com estes passos:

1. O aparelho recolhe um ponto de amostra em tempo real.

    - **VMware VMs**: Um ponto de amostra é recolhido a cada 20 segundos.

2. O aparelho combina os pontos de amostra para criar um único ponto de dados a cada 10 minutos. Para criar o ponto de dados, o aparelho seleciona os valores de pico de todas as amostras. Em seguida, envia o ponto de dados para Azure.
3. A Avaliação do Servidor armazena todos os pontos de dados de 10 minutos do último mês.
4. Quando cria uma avaliação, a Avaliação do Servidor identifica o ponto de dados apropriado a utilizar para a utilização de direitos. A identificação baseia-se nos valores percentil para o *histórico de desempenho* e *utilização do percentil.*

    - Por exemplo, se o histórico de desempenho for de uma semana e a utilização percentil for o percentil 95, a Avaliação do Servidor classifica os pontos de amostra de 10 minutos da última semana. Classifica-os por ordem ascendente e escolhe o valor percentil 95 para a deposição de direitos.
    - O valor percentil 95 faz com que ignore qualquer outliers, que podem ser incluídos se escolher o percentil 99.
    - Se quiser escolher o pico de utilização para o período e não quiser perder nenhum outliers, selecione o percentil 99 para utilização percentil.

5. Este valor é multiplicado pelo fator de conforto para obter os dados de utilização eficazes do desempenho para estas métricas que o aparelho recolhe:

    - Utilização da CPU
    - Utilização do RAM
    - IOPS de disco (ler e escrever)
    - Produção de disco (ler e escrever)
    - Produção de rede (dentro e fora)

Os seguintes dados de desempenho são recolhidos, mas não utilizados em recomendações de dimensionamento para avaliações avs:
  - O disco IOPS e os dados de produção de cada disco ligado ao VM.
  - A rede I/O para lidar com o tamanho baseado no desempenho de cada adaptador de rede ligado a um VM.

## <a name="how-are-avs-assessments-calculated"></a>Como são calculadas as avaliações do AVS?

A Avaliação do Servidor utiliza os metadados e dados de desempenho das máquinas no local para calcular as avaliações. Se utilizar o aparelho Azure Migrate, a avaliação utiliza os dados que o aparelho recolhe. Mas se fizer uma avaliação importada usando um ficheiro CSV, fornece os metadados para o cálculo.

Os cálculos ocorrem nestas três fases:

1. **Calcular a prontidão da Solução VMware Azure (AVS):** Se os VMs no local são adequados para migração para Azure VMware Solution (AVS).
2. **Calcular o número de nós AVS e utilização através** de nós : Número estimado de nós AVS necessários para executar os VMs e cpu projetado, memória e utilização de armazenamento em todos os nós.
3. **Estimativa mensal dos custos**: Os custos mensais estimados para todos os nós da Azure VMware Solution (AVS) que executam os VMs no local.

Os cálculos estão na ordem anterior. Um servidor de máquinas move-se para uma fase posterior apenas se passar o anterior. Por exemplo, se um servidor falhar na fase de prontidão do AVS, está marcado como inadequado para o Azure. O tamanho e os cálculos de custos não são feitos para o servidor

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>O que está numa avaliação da Solução VMware Azure (AVS) ?

Aqui está o que está incluído numa avaliação avs na avaliação do servidor:


| **Propriedade** | **Detalhes** 
| - | - 
| **Localização de destino** | Especifica a localização da nuvem privada AVS para a qual pretende migrar.<br/><br/> A avaliação do AVS na avaliação do servidor suporta atualmente estas regiões-alvo: Leste dos EUA, Europa Ocidental, EUA Ocidentais. 
| **Tipo de armazenamento** | Especifica o motor de armazenamento a utilizar em AVS.<br/><br/> As avaliações AVS suportam apenas o vSAN como um tipo de armazenamento predefinido. 
**Instâncias Reservadas (RIs)** | Esta propriedade ajuda-o a especificar Instâncias Reservadas em AVS. Os RIs não são atualmente suportados para os nós AVS. 
**Tipo de nó** | Especifica o [tipo de nó AVS](../azure-vmware/concepts-private-clouds-clusters.md) utilizado para mapear os VMs no local. O tipo de nó predefinido é AV36. <br/><br/> A Azure Migrate recomendará um número necessário de nós para que os VMs sejam migrados para AVS. 
**Definição FTT, nível raid** | Especifica as combinações de Falha de Tolerar e Raid aplicáveis. A opção FTT selecionada combinada com o requisito do disco VM no local determinará o armazenamento total de vSAN exigido em AVS. 
**Critério de dimensionamento** | Define os critérios a utilizar para VMs *de tamanho correto* para AVS. Pode optar pelo dimensionamento *baseado no desempenho* ou como no local sem considerar o histórico *de* desempenho. 
**Histórico de desempenho** | Define a duração a ter em conta na avaliação dos dados de desempenho das máquinas. Esta propriedade só é aplicável quando os critérios de dimensionamento são *baseados no desempenho.* 
**Utilização de percentil** | Especifica o valor percentil do conjunto de amostras de desempenho a considerar para o tamanho certo. Esta propriedade só é aplicável quando o dimensionamento é baseado no desempenho.
**Fator de conforto** | A avaliação do servidor Azure Migrate considera um tampão (fator de conforto) durante a avaliação. Esta memória intermédia é aplicada em cima dos dados de utilização das VMs (CPU, memória, disco e rede). O fator de conforto dá conta de problemas como utilização sazonal, histórico de desempenho breve e prováveis aumentos na utilização futura.<br/><br/> Por exemplo, uma VM com 10 núcleos e 20% de utilização resulta, normalmente, numa VM de 2 núcleos. No entanto, com um fator de conforto de 2,0 x, o resultado é uma VM de 4 núcleos. 
**Oferta** | Exibe a oferta do [Azure](https://azure.microsoft.com/support/legal/offer-details/) em que está matriculado. O Azure Migrate calcula o custo em conformidade.
**Moeda** | Mostra a moeda de faturação da sua conta. 
**Desconto (%)** | Lista qualquer desconto específico por subscrição que receba em cima da oferta Azure. A predefinição é 0%. 
**Benefício Híbrido do Azure** | Especifica se tem garantia de software e é elegível para [benefício híbrido Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Embora não tenha qualquer impacto nos preços das soluções Azure VMware devido ao preço baseado no nó, os clientes ainda podem aplicar as licenças de OS no local (com base na Microsoft) em AVS utilizando benefícios híbridos Azure. Outros fornecedores de SISTEMA de software terão de fornecer os seus próprios termos de licenciamento, como o RHEL, por exemplo. 
**vCPU Oversubscription** | Especifica a relação de número de núcleos virtuais ligados a um núcleo físico no nó AVS. O valor predefinido nos cálculos é de 4 vCPU:1 núcleo físico em AVS. <br/><br/> Os utilizadores da API podem definir este valor como um inteiro. Note que a subscrição excessiva do vCPU > 4:1 pode ter impacto nas cargas de trabalho dependendo da sua utilização do CPU. 


## <a name="azure-vmware-solution-avs-suitability-analysis"></a>Análise de adequação da Solução VMware Azure (AVS)

As avaliações avs na avaliação do servidor avaliam cada VMs no local pela sua adequação para AVS, revendo as propriedades da máquina. Atribui igualmente cada máquina avaliada a uma das seguintes categorias de adequação:

- **Pronto para AVS**: A máquina pode ser migrada como-é para Azure (AVS) sem alterações. Começará em AVS com suporte AVS completo.
- **Pronto com condições**: O VM pode ter problemas de compatibilidade com a versão atual do vSphere, bem como exigir ferramentas e outras configurações de VMware e ou outras configurações antes de a funcionalidade completa do VM poder ser alcançada em AVS.
- **Não está pronto para AVS**: O VM não começará em AVS. Por exemplo, se o VMware VMware VMware no local tiver um dispositivo externo ligado, como um cd-rom, a operação VMotion VMware falhará (se utilizar vMware VMotion).
- **Prontidão desconhecida**: A Azure Migrate não conseguiu determinar a prontidão da máquina devido a metadados insuficientes recolhidos do ambiente no local.

A Avaliação do Servidor revê as propriedades da máquina para determinar a prontidão do Azure da máquina no local.

### <a name="machine-properties"></a>Propriedades da máquina

A Avaliação do Servidor revê a seguinte propriedade do VM no local para determinar se pode funcionar na Solução VMware Azure (AVS).


| **Propriedade** | **Detalhes** | **Estado de prontidão do AVS** 
| - | - | - 
| **Protocolo de Internet** | Atualmente, o AVS não suporta o endereçamento de Internet IPv6.<br/><br/> Contacte a equipa MSFT AVS GBB local para obter orientações de remediação se a máquina virtual for detetada com o IPv6.| Protocolo de Internet pronto condicionalmente


### <a name="guest-operating-system"></a>Sistema operativo convidado

Atualmente, as avaliações avs não analisam o sistema operativo como parte da análise de adequação. Todos os sistemas operativos em funcionamento em VMs no local são suscetíveis de funcionar na Solução VMware Azure (AVS).

Juntamente com as propriedades de VM, a Avaliação do Servidor olha para o sistema operativo dos hóspedes das máquinas para determinar se pode funcionar no Azure.


## <a name="sizing"></a>Dimensionamento

Depois de uma máquina estar marcada como pronta para o AVS, a Avaliação avs na avaliação do servidor faz recomendações de dimensionamento de nó, que envolvem identificar os requisitos VM adequados no local e encontrar o número total de nós AVS necessários. Estas recomendações variam, dependendo das propriedades de avaliação especificadas.

- Se a avaliação utilizar *o dimensionamento baseado no desempenho,* a Azure Migrate considera o histórico de desempenho da máquina para fazer a recomendação de dimensionamento adequada para o AVS. Este método é especialmente útil se você tiver alocado o VM no local, mas a utilização é baixa e você quer tamanho direito o VM em AVS para economizar custos. Este método irá ajudá-lo a otimizar os tamanhos durante a migração.
- Se não quiser considerar os dados de desempenho para o tamanho de VM e quiser levar as máquinas no local como é para o AVS, pode definir os *critérios* de dimensionamento como no local . Em seguida, a Avaliação do Servidor irá dimensionar os VMs com base na configuração no local sem considerar os dados de utilização. 


### <a name="ftt-sizing-parameters"></a>Parâmetros de dimensionamento ftt

O motor de armazenamento utilizado em AVS é vSAN. as políticas de armazenamento vSAN definem os requisitos de armazenamento para as suas máquinas virtuais. Estas políticas garantem o nível de serviço necessário das VMs, porque determinam como o armazenamento é alocado à VM. As combinações FTT-Raid disponíveis são: 

**Falhas a Tolerar (FTT)** | **Configuração do RAID** | **Anfitriões Mínimos Necessários** | **Consideração sobre o dimensionamento**
--- | --- | --- | --- 
1 | RAID-1 (Espelhamento) | 3 | Uma VM de 100 GB consumiria 200 GB.
1 | RAID-5 (Codificação de Eliminação) | 4 | Uma VM de 100 GB consumiria 133,33 GB
2 | RAID-1 (Espelhamento) | 5 | Uma VM de 100 GB consumiria 300 GB.
2 | RAID-6 (Codificação de Eliminação) | 6 | Uma VM de 100 GB consumiria 150 GB.
3 | RAID-1 (Espelhamento) | 7 | Uma VM de 100 GB consumiria 400 GB.

### <a name="performance-based-sizing"></a>Dimensionamento baseado no desempenho

Para dimensionamento baseado no desempenho, a Avaliação do Servidor começa com os discos ligados ao VM, seguidos por adaptadores de rede. Em seguida, mapeia os requisitos de VM para um não adequado de nós para AVS. O aparelho Azure Migrate traça o perfil do ambiente no local para recolher dados de desempenho para CPU, memória, discos e adaptador de rede.

**Etapas de recolha de dados de desempenho:**

1. Para VMware VMs, o aparelho Azure Migrate recolhe um ponto de amostra em tempo real a cada intervalo de 20 segundos. 
2. O aparelho enrola os pontos de amostra recolhidos a cada 10 minutos e envia o valor máximo dos últimos 10 minutos para a Avaliação do Servidor.
3. A Avaliação do Servidor armazena todos os pontos de amostra de 10 minutos do último mês. Em seguida, dependendo das propriedades de avaliação especificadas para o *histórico de desempenho* e *utilização por percentil,* identifica o ponto de dados apropriado a utilizar para o tamanho certo. Por exemplo, se o histórico de desempenho estiver definido para 1 dia e a utilização por percentil for o percentil 95, a Avaliação do Servidor utiliza os pontos de amostra de 10 minutos para o último dia, classifica-os em ordem ascendente e escolhe o valor percentil 95 para o tamanho certo.
4. Este valor é multiplicado pelo fator de conforto para obter os dados de utilização eficazes do desempenho para cada métrica (utilização de CPU, utilização da memória, IOPS do disco (ler e escrever), produção de disco (ler e escrever) e produção de rede (dentro e fora) que o aparelho recolhe.

Após a determinação do valor de utilização efetiva, o armazenamento, a rede e o tamanho do cálculo são manuseados da seguinte forma.

**Dimensionamento de armazenamento**: A Azure Migrate utiliza o espaço total do disco VM no local como parâmetro de cálculo para determinar os requisitos de armazenamento AVS vSAN, para além da definição ftt selecionada pelo cliente. FTT - Falhas na toleração, bem como a necessidade de um mínimo de nós por opção FTT determinarão o armazenamento total de vSAN necessário combinado com o requisito do disco VM.

**Dimensionamento da rede**: atualmente, a Avaliação do Servidor não leva em consideração quaisquer definições de rede para avaliações do AVS.

**Tamanho do cálculo**: Depois de calcular os requisitos de armazenamento, a Avaliação do Servidor considera os requisitos de CPU e memória para determinar o número de nós necessários para o AVS com base no tipo de nó.

- Com base nos critérios de dimensionamento, a Avaliação do Servidor analisa os dados VM baseados no desempenho ou a configuração VM no local. A definição do fator de conforto permite especificar o fator de crescimento do cluster. Atualmente, por predefinição, o hyperthreading está ativado e, portanto, um nó de 36 núcleos terá 72 vCores. São utilizados 4 vCores por máquina física para determinar os limiares da CPU por cluster através da norma de VMware de não exceder 80% de utilização para permitir que a manutenção ou as falhas sejam processadas sem comprometer a disponibilidade do cluster. Não existe atualmente nenhuma sobreposição disponível para alterar os valores de sobresubscrição e podemos tê-lo em futuras versões.

### <a name="as-on-premises-sizing"></a>Como dimensionamento no local

Se utilizar *como dimensionamento no local,* a Avaliação do Servidor não considera o histórico de desempenho dos VMs e discos. Em vez disso, atribui nós AVS com base no tamanho atribuído no local. O tipo de armazenamento predefinido é vSAN em AVS.

[Saiba mais](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware-azure-vmware-solution#review-an-assessment) sobre como rever uma avaliação da Solução VMware Azure.

## <a name="confidence-ratings"></a>Classificações de confiança

Cada avaliação baseada no desempenho em Azure Migrate está associada a uma classificação de confiança que varia de uma (mais baixa) a cinco estrelas (mais alta).

- A classificação de confiança é alocada a uma avaliação com base na disponibilidade dos pontos de dados necessários para calcular a avaliação.
- A classificação de confiança de uma avaliação ajuda a calcular a fiabilidade das recomendações de tamanho fornecidas pelo Azure Migrate.
- As classificações de confiança não são aplicáveis *como avaliações no local.*
- Para dimensionamento baseado no desempenho, as avaliações de AVS na Avaliação do Servidor precisam dos dados de utilização da memória CPU e VM. Os seguintes dados são recolhidos, mas não utilizados em recomendações de dimensionamento para a AVS:
  - O disco IOPS e os dados de produção de cada disco ligado ao VM.
  - A rede I/O para lidar com o tamanho baseado no desempenho de cada adaptador de rede ligado a um VM.

  Se algum destes números de utilização não estiver disponível no servidor vCenter, a recomendação de tamanho pode não ser fiável.

Dependendo da percentagem de pontos de dados disponíveis, a classificação de confiança para a avaliação é a seguinte.


| **Disponibilidade de pontos de dados** | **Classificação de confiança** |
| - | - |
| 0-20% | 1 estrela |
| 21-40% | 2 estrelas |
| 41-60% | 3 estrelas |
| 61-80% | 4 estrelas |
| 81-100% | 5 estrelas |

### <a name="low-confidence-ratings"></a>Baixas classificações de confiança

Eis algumas razões pelas quais uma avaliação pode obter uma classificação de baixa confiança:

- Não perfilou o seu ambiente durante a duração pela qual está a criar a avaliação. Por exemplo, se criar a avaliação com a duração de desempenho definida para um dia, deve esperar pelo menos um dia depois de começar a descobrir todos os pontos de dados para ser recolhido.
- A avaliação não é capaz de recolher os dados de desempenho para alguns ou todos os VMs no período de avaliação. Para uma alta classificação de confiança, certifique-se de que: 
    - Os VM são alimentados durante a duração da avaliação
    - São permitidas ligações de saída nas portas 443
    - Para a memória dinâmica Hiper-VMs está ativada 
    
    “Recalcule” a avaliação para refletir as últimas alterações na classificação de confiança.

- Foram criados alguns VM durante o período para o qual a avaliação foi calculada. Por exemplo, assuma que criou uma avaliação para o histórico de desempenho do mês passado, mas alguns VMs foram criados apenas há uma semana. Neste caso, os dados de desempenho das novas VMs não vão estar disponíveis durante todo este período e a classificação de confiança seria baixa.

> [!NOTE]
> Se a classificação de confiança de qualquer avaliação for inferior a cinco estrelas, recomendamos que espere pelo menos um dia para que o aparelho perfile o ambiente e, em seguida, recalcule a avaliação. Se não o fizeres, o tamanho baseado no desempenho pode não ser fiável. Nesse caso, recomendamos que altere a avaliação para o tamanho do local.

## <a name="monthly-cost-estimation"></a>Estimativa mensal dos custos

Após a conclusão das recomendações de dimensionamento, a Azure Migrate calcula o custo total de execução das cargas de trabalho no local em AVS multiplicando o número de nós AVS exigidos pelo preço do nó. O custo por VM é calculado dividindo o custo total pelo número de VMs na avaliação. 
- O cálculo tem em conta o número de nós necessários, tipo de nó e localização.
- Agrega o custo em todos os nós para calcular o custo mensal total.
- Os custos são apresentados na moeda especificada nas definições de avaliação.

Como o preço da Azure VMware Solution (AVS) é por nó, o custo total não tem custo de computação e distribuição de custos de armazenamento. [Saiba mais](../azure-vmware/introduction.md)

Note que como a Azure VMware Solution (AVS) está em Pré-visualização, os preços do nó na avaliação são os preços de pré-visualização. Contacte a sua equipa local da MSFT AVS GBB para obter orientação.

## <a name="migration-tool-guidance"></a>Orientação da ferramenta de migração

No relatório de preparação para o Azure da avaliação do Azure VMware Solution (AVS), pode ver as seguintes ferramentas sugeridas: 
- **VMware HCX ou Enterprise**: Para máquinas VMware, a solução VMware Hybrid Cloud Extension (HCX) é a ferramenta de migração sugerida para migrar a sua carga de trabalho no local para a sua nuvem privada Azure VMware Solution (AVS). [Saiba Mais](../azure-vmware/tutorial-deploy-vmware-hcx.md).
- **Desconhecida**: para as máquinas virtuais importadas através de um ficheiro CSV, a ferramenta de migração predefinida é desconhecida. Embora para máquinas VMware, é aconselhável utilizar a solução VMware Hybrid Cloud Extension (HCX).

## <a name="next-steps"></a>Passos seguintes

Criar uma avaliação para [VMware VMware AVS](how-to-create-azure-vmware-solution-assessment.md).
