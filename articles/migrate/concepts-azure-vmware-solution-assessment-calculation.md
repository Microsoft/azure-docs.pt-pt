---
title: Cálculos de avaliação do AVS em Azure Migrate | Microsoft Docs
description: Fornece uma visão geral dos cálculos de avaliação do AVS no serviço Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/25/2020
ms.openlocfilehash: 1d9918786b22faddaeb07a12f0840b36a11ffe4d
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778386"
---
# <a name="assessment-overview-migrate-to-azure-vmware-solution"></a>Visão geral da avaliação (migrar para a Solução VMware Azure)

[A Azure Migrate](migrate-services-overview.md) fornece um centro central para acompanhar a descoberta, avaliação e migração das suas aplicações e cargas de trabalho no local. Também rastreia as suas instâncias de nuvem privada e pública para Azure. O hub oferece ferramentas Azure Migrate para avaliação e migração, bem como ofertas de fornecedores de software independentes de terceiros (ISV).

A ferramenta de descoberta e avaliação em Azure Migrate avalia servidores no local para migração para máquinas virtuais Azure e Solução VMware Azure (AVS). Este artigo fornece informações sobre como as avaliações da Azure VMware Solution (AVS) são calculadas.

> [!NOTE]
> A avaliação da Solução VMware (AVS) do Azure VMware pode ser criada apenas para VMware VMs.

## <a name="types-of-assessments"></a>Tipos de avaliações

As avaliações que cria com a Azure Migrate são uma imagem pontual dos dados. Existem dois tipos de avaliações que pode criar usando a Azure Migrate:

**Tipo de Avaliação** | **Detalhes**
--- | --- 
**VM do Azure** | Avaliações para migrar os seus servidores no local para máquinas virtuais do Azure. Pode avaliar os seus servidores no local em ambiente [VMware](how-to-set-up-appliance-vmware.md) e [Hiper-V](how-to-set-up-appliance-hyper-v.md) e [servidores físicos](how-to-set-up-appliance-physical.md) para migração para VMs Azure usando este tipo de avaliação.
**SQL do Azure** | Avaliações para migrar os seus servidores SQL no local do seu ambiente VMware para Azure SQL Database ou Azure SQL Managed Instance.
**Solução VMware no Azure (AVS)** | Avaliações para migrar os seus servidores no local para o [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). Pode avaliar os seus [VMS VMware](how-to-set-up-appliance-vmware.md) no local para migração para Azure VMware Solution (AVS) utilizando este tipo de avaliação. [Saiba mais](concepts-azure-vmware-solution-assessment-calculation.md)

A avaliação da Solução VMware Azure (AVS) fornece duas opções de critérios de dimensionamento:

| **Avaliação** | **Detalhes** | **Dados** |
| - | - | - |
| **Com base no desempenho** | Avaliações baseadas em dados de desempenho recolhidos de VMs no local. | **Tamanho recomendado do nó**: Com base em dados de utilização da CPU e da memória, juntamente com o tipo de nó, o tipo de armazenamento e a definição de FTT que seleciona para a avaliação. |
| **Como no local** | Avaliações baseadas no dimensionamento no local. | **Tamanho do nó recomendado**: Com base no tamanho VM no local, juntamente com o tipo de nó, tipo de armazenamento e definição FTT que seleciona para a avaliação. |

## <a name="how-do-i-run-an-assessment"></a>Como faço uma avaliação?

Há algumas maneiras de fazer uma avaliação.

- Avaliar os servidores utilizando metadados do servidor recolhidos por um aparelho Azure Migrate leve. O aparelho descobre servidores no local. Em seguida, envia metadados de servidor e dados de desempenho para Azure Migrate. Isto permite uma maior precisão.
- Avaliar os servidores utilizando metadados de servidores importados num formato de valores separados por vírgula (CSV).

## <a name="how-do-i-assess-with-the-appliance"></a>Como avalio com o aparelho?

Se estiver a implantar um aparelho Azure Migrate para descobrir servidores no local, faça os seguintes passos:

1. Instale o Azure e o seu ambiente no local para trabalhar com a Azure Migrate.
2. Para a sua primeira avaliação, crie um projeto Azure e adicione-lhe a ferramenta Discovery e de avaliação.
3. Desloque um aparelho Azure Migrate leve. O aparelho descobre continuamente servidores no local e envia metadados de servidores e dados de desempenho para a Azure Migrate. Desloque o aparelho como VM. Não precisa de instalar nada nos servidores que queira avaliar.

Após o início da descoberta do servidor do aparelho, pode recolher servidores que pretende avaliar num grupo e fazer uma avaliação para o grupo com a avaliação do tipo **Azure VMware Solution (AVS)**.

Crie a sua primeira avaliação da Solução Azure VMware (AVS) seguindo os passos [aqui.](how-to-create-azure-vmware-solution-assessment.md)

## <a name="how-do-i-assess-with-imported-data"></a>Como avalio com dados importados?

Se estiver a avaliar os servidores utilizando um ficheiro CSV, não precisa de um aparelho. Em vez disso, faça os seguintes passos:

1. Instale o Azure para trabalhar com a Azure Migrate.
2. Para a sua primeira avaliação, crie um projeto Azure e adicione-lhe a ferramenta Discovery e de avaliação.
3. Descarregue um modelo de CSV e adicione dados do servidor.
4. Importe o modelo em Azure Migrate.
5. Descubra os servidores adicionados com a importação, reúna-os em um grupo e execute uma avaliação para o grupo com avaliação tipo **Azure VMware Solution (AVS)**.

## <a name="what-data-does-the-appliance-collect"></a>Que dados o aparelho recolhe?

Se estiver a utilizar o aparelho Azure Migrate para avaliação, saiba mais sobre os metadados e dados de desempenho recolhidos para [vMware](migrate-appliance.md#collected-data---vmware).

## <a name="how-does-the-appliance-calculate-performance-data"></a>Como calcula o aparelho dados de desempenho?

Se utilizar o aparelho para ser descoberto, recolhe dados de desempenho para configurações de cálculo com estes passos:

1. O aparelho recolhe um ponto de amostra em tempo real.

   - **VMware VMs**: Um ponto de amostra é recolhido a cada 20 segundos.
2. O aparelho combina os pontos de amostra para criar um único ponto de dados a cada 10 minutos. Para criar o ponto de dados, o aparelho seleciona os valores de pico de todas as amostras. Em seguida, envia o ponto de dados para Azure.
3. A Azure Migrate armazena todos os pontos de dados de 10 minutos do último mês.
4. Ao criar uma avaliação, a avaliação identifica o ponto de dados apropriado a utilizar para a corretaização. A identificação baseia-se nos valores percentil para o *histórico de desempenho* e *utilização do percentil.*

   - Por exemplo, se o histórico de desempenho for de uma semana e a utilização percentil for o percentil 95, a avaliação classifica os pontos de amostra de 10 minutos da última semana. Classifica-os por ordem ascendente e escolhe o valor percentil 95 para a deposição de direitos.
   - O valor percentil 95 faz com que ignore qualquer outliers, que podem ser incluídos se escolher o percentil 99.
   - Se quiser escolher o pico de utilização para o período e não quiser perder nenhum outliers, selecione o percentil 99 para utilização percentil.
5. Este valor é multiplicado pelo fator de conforto para obter os dados de utilização eficazes do desempenho para estas métricas que o aparelho recolhe:

   - Utilização da CPU
   - Utilização do RAM

Os seguintes dados de desempenho são recolhidos, mas não utilizados em recomendações de dimensionamento para avaliações avs:

- O disco IOPS e os dados de produção de cada disco ligado ao VM.
- A rede I/O para lidar com o tamanho baseado no desempenho de cada adaptador de rede ligado a um VM.

## <a name="how-are-avs-assessments-calculated"></a>Como são calculadas as avaliações do AVS?

A avaliação do AVS utiliza os metadados e dados de desempenho dos servidores no local para calcular as avaliações. Se utilizar o aparelho Azure Migrate, a avaliação utiliza os dados que o aparelho recolhe. Mas se fizer uma avaliação importada usando um ficheiro CSV, fornece os metadados para o cálculo.

Os cálculos ocorrem nestas três fases:

1. **Calcular a prontidão da Solução VMware Azure (AVS):** Se os VMs no local são adequados para migração para Azure VMware Solution (AVS).
2. **Calcular o número de nós AVS e utilização através** de nós : Número estimado de nós AVS necessários para executar os VMs VMware e cpu projetado, memória e utilização de armazenamento em todos os nós.
3. **Estimativa mensal dos custos**: Os custos mensais estimados para todos os nós da Azure VMware Solution (AVS) que executam os VMs no local.

Os cálculos estão na ordem anterior. Um servidor move-se para uma fase posterior apenas se passar o anterior. Por exemplo, se um servidor falhar na fase de prontidão do AVS, está marcado como inadequado para o Azure. O tamanho e os cálculos de custos não são feitos para o servidor

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>O que está numa avaliação da Solução VMware Azure (AVS) ?

Aqui está o que está incluído numa avaliação de AVS:

| **Propriedade** | **Detalhes** |
| - | - |
| **Localização de destino** | Especifica a localização da nuvem privada AVS para a qual pretende migrar. |
| **Tipo de armazenamento** | Especifica o motor de armazenamento a utilizar em AVS. Atualmente, o AVS apenas suporta o vSAN como um tipo de armazenamento predefinido, mas mais opções de armazenamento virão de acordo com o roteiro. |
| **Instâncias Reservadas (RIs)** | Este imóvel ajuda-o a especificar Instâncias Reservadas em AVS se adquirido e o termo da Instância Reservada. Usado para calcular custos. |
| **Tipo de nó** | Especifica o [tipo de nó AVS](../azure-vmware/concepts-private-clouds-clusters.md) utilizado para ser utilizado em Azure. O tipo de nó predefinido é AV36. Mais tipos de nó podem estar disponíveis no futuro.  A Azure Migrate recomendará um número necessário de nós para que os VMs sejam migrados para AVS. |
| **Definição FTT, nível raid** | Especifica a combinação válida de combinações de Falhas para Tolerar e Raid. A opção FTT selecionada combinada com o nível RAID e o requisito do disco VM no local determinará o armazenamento total de vSAN exigido em AVS. O armazenamento total disponível após os cálculos também inclui a) espaço reservado para objetos de gestão como vCenter e b) 25% de folga de armazenamento necessária para operações vSAN. |
| **Critério de dimensionamento** | Define os critérios a utilizar para determinar os requisitos de memória, cpu e armazenamento dos nós AVS. Pode optar pelo dimensionamento *baseado no desempenho* ou como no local sem considerar o histórico *de* desempenho. Simplesmente levantar e deslocar escolha como no local. Para obter dimensionamento baseado em utilização escolha desempenho baseado. |
| **Histórico de desempenho** | Define a duração a ter em conta na avaliação dos dados de desempenho dos servidores. Esta propriedade só é aplicável quando os critérios de dimensionamento são *baseados no desempenho.* |
| **Utilização de percentil** | Especifica o valor percentil do conjunto de amostras de desempenho a considerar para o tamanho certo. Esta propriedade só é aplicável quando o dimensionamento é baseado no desempenho. |
| **Fator de conforto** | O Azure Migrate considera uma memória intermédia (fator de conforto) durante a avaliação. Este tampão é aplicado em cima dos dados de utilização do servidor para VMs (CPU, memória e disco). O fator de conforto dá conta de problemas como utilização sazonal, histórico de desempenho breve e prováveis aumentos na utilização futura. Por exemplo, uma VM com 10 núcleos e 20% de utilização resulta, normalmente, numa VM de 2 núcleos. No entanto, com um fator de conforto de 2,0 x, o resultado é uma VM de 4 núcleos. |
| **Oferta** | Exibe a oferta do [Azure](https://azure.microsoft.com/support/legal/offer-details/) em que está matriculado. O Azure Migrate calcula o custo em conformidade. |
| **Moeda** | Mostra a moeda de faturação da sua conta. |
| **Desconto (%)** | Lista qualquer desconto específico por subscrição que receba em cima da oferta Azure. A predefinição é 0%. |
| **Benefício Híbrido do Azure** | Especifica se tem garantia de software e é elegível para [benefício híbrido Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Embora não tenha qualquer impacto nos preços das soluções Azure VMware devido ao preço baseado no nó, os clientes ainda podem aplicar as licenças DE OU OU SQL no local (com base na Microsoft) em AVS usando benefícios híbridos Azure. Outros fornecedores de SISTEMA de software terão de fornecer os seus próprios termos de licenciamento, como o RHEL, por exemplo. |
| **vCPU Oversubscription** | Especifica a relação de número de núcleos virtuais ligados a um núcleo físico no nó AVS. O valor predefinido nos cálculos é de 4 vCPU:1 núcleo físico em AVS. Os utilizadores da API podem definir este valor como um inteiro. Note que a subscrição excessiva do vCPU > 4:1 pode ter impacto nas cargas de trabalho dependendo da sua utilização do CPU. Quando o tamanho assumimos sempre 100% de utilização dos núcleos escolhidos. |
| **Fator de sobrecompromisso da memória** | Especifica a relação de memória sobre o compromisso no cluster. Um valor de 1 representa 100% de uso de memória, 0,5 por exemplo é 50%, e 2 usaria 200% da memória disponível. Só pode adicionar valores de 0,5 a 10 até uma casa decimal. |
| **Dedupe e fator de compressão** | Especifica o fator de dedupe e compressão previsto para as suas cargas de trabalho. O valor real pode ser obtido a partir de vSAN no local ou config de armazenamento. Estes variam por carga de trabalho. Um valor de 3 significaria 3x, pelo que para o disco de 300GB apenas seria usado um armazenamento de 100GB. Um valor de 1 significaria não dedupe ou compressão. Só é possível adicionar valores de 1 a 10 até uma casa decimal. |

## <a name="azure-vmware-solution-avs-suitability-analysis"></a>Análise de adequação da Solução VMware Azure (AVS)

As avaliações avs avaliam cada VMs no local pela sua adequação para AVS, revendo as propriedades do servidor. Atribui também cada servidor avaliado a uma das seguintes categorias de adequação:

- **Pronto para AVS**: O servidor pode ser migrado como-é para Azure (AVS) sem alterações. Começará em AVS com suporte AVS completo.
- **Pronto com condições**: O VM pode ter problemas de compatibilidade com a versão atual do vSphere, bem como exigir ferramentas e outras configurações de VMware e ou outras configurações antes de a funcionalidade completa do VM poder ser alcançada em AVS.
- **Não está pronto para AVS**: O VM não começará em AVS. Por exemplo, se o VMware VMware VMware no local tiver um dispositivo externo ligado, como um cd-rom, a operação VMotion VMware falhará (se utilizar vMware VMotion).
- **Prontidão desconhecida**: A Azure Migrate não conseguiu determinar a prontidão do servidor devido a metadados insuficientes recolhidos do ambiente no local.

A avaliação revê as propriedades do servidor para determinar a prontidão do Azure do servidor no local.

### <a name="server-properties"></a>Propriedades do servidor

A avaliação revê a seguinte propriedade do VM no local para determinar se pode funcionar na Azure VMware Solution (AVS).

| **Propriedade** | **Detalhes** | **Estado de prontidão do AVS** |
| - | - | - |
| **Protocolo de Internet** | A Azure não suporta atualmente o fim do endereço de internet IPv6. Contacte a sua equipa local msft AVS GBB para obter orientação sobre a orientação de remediação se o seu servidor for detetado com o IPv6. | Protocolo de Internet pronto condicionalmente |

### <a name="guest-operating-system"></a>Sistema operativo convidado

Atualmente, as avaliações avs não analisam o sistema operativo como parte da análise de adequação. Todos os sistemas operativos em funcionamento em VMs no local são suscetíveis de funcionar na Solução VMware Azure (AVS).

Juntamente com as propriedades VM, a avaliação olha para o sistema operativo dos servidores para determinar se pode funcionar no Azure.

## <a name="sizing"></a>Dimensionamento

Depois de um servidor estar marcado como pronto para o AVS, a Avaliação avs faz recomendações de dimensionamento de nó, que envolvem identificar os requisitos VM adequados no local e encontrar o número total de nós AVS necessários. Estas recomendações variam, dependendo das propriedades de avaliação especificadas.

- Se a avaliação utilizar *o dimensionamento baseado no desempenho,* a Azure Migrate considera o histórico de desempenho do servidor a fazer a recomendação de dimensionamento adequada para o AVS. Este método é especialmente útil se você tiver alocado o VM no local, mas a utilização é baixa e você quer tamanho direito o VM em AVS para economizar custos. Este método irá ajudá-lo a otimizar os tamanhos durante a migração.
- Se não quiser considerar os dados de desempenho para o tamanho de VM e quiser levar os servidores no local como é para o AVS, pode definir os critérios de dimensionamento para* como no local*. Em seguida, a avaliação irá dimensionar os VMs com base na configuração no local sem considerar os dados de utilização.

### <a name="ftt-sizing-parameters"></a>Parâmetros de dimensionamento ftt

O motor de armazenamento utilizado em AVS é vSAN. as políticas de armazenamento vSAN definem os requisitos de armazenamento para os seus servidores. Estas políticas garantem o nível de serviço necessário das VMs, porque determinam como o armazenamento é alocado à VM. As combinações FTT-Raid disponíveis são:

| **Falhas a Tolerar (FTT)** | **Configuração do RAID** | **Anfitriões Mínimos Necessários** | **Consideração sobre o dimensionamento** |
| - | - | - | - |
| 1 | RAID-1 (Espelhamento) | 3 | Uma VM de 100 GB consumiria 200 GB. |
| 1 | RAID-5 (Codificação de Eliminação) | 4 | Uma VM de 100 GB consumiria 133,33 GB |
| 2 | RAID-1 (Espelhamento) | 5 | Uma VM de 100 GB consumiria 300 GB. |
| 2 | RAID-6 (Codificação de Eliminação) | 6 | Uma VM de 100 GB consumiria 150 GB. |
| 3 | RAID-1 (Espelhamento) | 7 | Uma VM de 100 GB consumiria 400 GB. |

### <a name="performance-based-sizing"></a>Dimensionamento baseado no desempenho

Para dimensionamento baseado no desempenho, o aparelho Azure Migrate perfis o ambiente no local para recolher dados de desempenho para CPU, memória e disco. Assim, o dimensionamento baseado no desempenho para AVS terá em conta o espaço do disco atribuído e utilizando a utilização percentil escolhida da memória e da CPU. Por exemplo, se um VM tiver 4vCores atribuídos mas apenas usando 25% então o AVS será dimensionado para 1vCore para esse VM.

**Etapas de recolha de dados de desempenho:**

1. Para VMware VMs, o aparelho Azure Migrate recolhe um ponto de amostra em tempo real a cada intervalo de 20 segundos.
2. O aparelho enrola os pontos de amostra recolhidos a cada 10 minutos e envia o valor máximo dos últimos 10 minutos para a Azure Migrate.
3. A Azure Migrate armazena todos os pontos de amostra de 10 minutos do último mês. Em seguida, dependendo das propriedades de avaliação especificadas para o *histórico de desempenho* e *utilização por percentil,* identifica o ponto de dados apropriado a utilizar para o tamanho certo. Por exemplo, se o histórico de desempenho estiver definido para 1 dia e a utilização por percentil for o percentil 95, Azure Migrate usa os pontos de amostra de 10 minutos para o último dia, classifica-os em ordem ascendente, e escolhe o valor percentil 95 para o tamanho certo.
4. Este valor é multiplicado pelo fator de conforto para obter os dados eficazes de utilização do desempenho para cada métrica (utilização de CPU e utilização da memória) que o aparelho recolhe.

Após a determinação do valor de utilização efetiva, o armazenamento, a rede e o tamanho do cálculo são manuseados da seguinte forma.

**Dimensionamento de armazenamento**: A Azure Migrate utiliza o espaço total do disco VM no local como parâmetro de cálculo para determinar os requisitos de armazenamento AVS vSAN, para além da definição ftt selecionada pelo cliente. FTT - Falhas na toleração, bem como a necessidade de um mínimo de nós por opção FTT determinarão o armazenamento total de vSAN necessário combinado com o requisito do disco VM. Atualmente, a utilização do armazenamento não é tida em conta e a lógica apenas analisa o armazenamento atribuído por VM.

**Dimensionamento da rede**: As avaliações avs atualmente não têm em conta quaisquer definições de rede.

**Tamanho do cálculo**: Após calcular os requisitos de armazenamento, a avaliação do AVS considera os requisitos de CPU e memória para determinar o número de nós necessários para o AVS com base no tipo de nó.

- Com base nos critérios de dimensionamento, a avaliação do AVS analisa os dados VM baseados no desempenho ou a configuração VM no local. A definição do fator de conforto permite especificar o fator de crescimento do cluster. Atualmente, por predefinição, o hyperthreading está ativado e, portanto, um nó de 36 núcleos terá 72 vCores. São utilizados 4 vCores por máquina física para determinar os limiares da CPU por cluster através da norma de VMware de não exceder 80% de utilização para permitir que a manutenção ou as falhas sejam processadas sem comprometer a disponibilidade do cluster. Não existe atualmente nenhuma sobreposição disponível para alterar os valores de sobresubscrição e podemos tê-lo em futuras versões.

### <a name="as-on-premises-sizing"></a>Como dimensionamento no local

Se utilizar *como dimensionamento no local,* a avaliação do AVS não considera o histórico de desempenho dos VMs e discos. Em vez disso, atribui nós AVS com base no tamanho atribuído no local. O tipo de armazenamento predefinido é vSAN em AVS.

[Saiba mais](./tutorial-assess-vmware-azure-vmware-solution.md#review-an-assessment) sobre como rever uma avaliação da Solução VMware Azure.

### <a name="cpu-utilization-on-avs-nodes"></a>Utilização do CPU nos nos acenos AVS

A utilização do CPU pressupõe uma utilização 100% dos núcleos disponíveis. Para reduzir o não dos nós necessários, pode-se aumentar a subscrição excessiva a partir de 4:1 para dizer 6:1 com base nas características da carga de trabalho e na experiência no local. Ao contrário do disco, o AVS não coloca limites na utilização do CPU, cabe aos clientes garantir que o seu cluster funciona da melhor forma, por isso, se for necessário ajustar -se "correr quente". Para permitir mais espaço para o crescimento, reduza a subscrição excessiva ou aumente o valor para o fator de crescimento.

A utilização do CPU também já é responsável pela gestão da despesa com o vCenter, o gestor NSX e outros recursos menores.

### <a name="memory-utilization-on-avs-nodes"></a>Utilização da memória nos nos acenos AVS

A utilização da memória mostra a memória total de todos os nós vs. requisitos do Servidor ou cargas de trabalho. A memória pode ser sobresscrita e, novamente, o AVS não coloca limites e cabe ao cliente executar o melhor desempenho do cluster para as suas cargas de trabalho.

A utilização da memória também já é responsável pela gestão da sobrecarga do vCenter, do gestor NSX e de outros recursos menores.

### <a name="storage-utilization-on-avs-nodes"></a>Utilização do armazenamento nos nosmes AVS

A utilização do armazenamento é calculada com base na seguinte sequência:

1. Tamanho necessário para vm's (atribuído como é ou espaço usado baseado no desempenho)
2. Aplicar o fator de crescimento se houver
3. Adicione a sobrecarga de gestão e aplique rácio FTT
4. Aplicar dedupe e fator de compressão
5. Aplicar a folga de 25% necessária para vSAN
6. Resultado armazenamento disponível para VMs fora do armazenamento total, incluindo sobrecarga de gestão.

O armazenamento disponível num cluster de nó 3 basear-se-á na política de armazenamento padrão que é Raid-1 e utiliza um fornecimento grosso. Ao calcular para codificação de apagamento ou Raid-5, por exemplo, é necessário um mínimo de 4 nós. Note que no AVS a política de armazenamento tem de ser alterada pelo administrador para permitir mais espaço.

## <a name="confidence-ratings"></a>Classificações de confiança

Cada avaliação baseada no desempenho em Azure Migrate está associada a uma classificação de confiança que varia de uma (mais baixa) a cinco estrelas (mais alta).

- A classificação de confiança é alocada a uma avaliação com base na disponibilidade dos pontos de dados necessários para calcular a avaliação.
- A classificação de confiança de uma avaliação ajuda a calcular a fiabilidade das recomendações de tamanho fornecidas pelo Azure Migrate.
- As classificações de confiança não são aplicáveis *como avaliações no local.*
- Para o dimensionamento baseado no desempenho, as avaliações avs precisam dos dados de utilização para a memória cpu e VM. Os seguintes dados são recolhidos, mas não utilizados em recomendações de dimensionamento para a AVS:

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

- Não analisou o ambiente durante o período para o qual está a criar a avaliação. Por exemplo, se criar a avaliação com a duração de desempenho definida para um dia, deve esperar pelo menos um dia depois de começar a descobrir todos os pontos de dados para ser recolhido.
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

## <a name="migration-tool-guidance"></a>Orientação da ferramenta de migração

No relatório de preparação para o Azure da avaliação do Azure VMware Solution (AVS), pode ver as seguintes ferramentas sugeridas:

- **VMware HCX ou Enterprise**: Para servidores VMware, a solução VMware Hybrid Cloud Extension (HCX) é a ferramenta de migração sugerida para migrar a sua carga de trabalho no local para a sua nuvem privada Azure VMware Solution (AVS). [Saiba Mais](../azure-vmware/tutorial-deploy-vmware-hcx.md).
- **Desconhecido**: Para os servidores importados através de um ficheiro CSV, a ferramenta de migração padrão é desconhecida. Embora para servidores VMware, é aconselhável utilizar a solução VMware Hybrid Cloud Extension (HCX).

## <a name="next-steps"></a>Passos seguintes

Criar uma avaliação para [VMware VMware AVS](how-to-create-azure-vmware-solution-assessment.md).
