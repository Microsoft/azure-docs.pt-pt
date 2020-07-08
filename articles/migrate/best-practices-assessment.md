---
title: Avaliação das melhores práticas na avaliação do servidor Azure Migrate
description: Dicas para criar avaliações com a Avaliação do Servidor Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 1a3735180d72496d58cdd22d0aa34c8a6f88a6a3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559853"
---
# <a name="best-practices-for-creating-assessments"></a>Melhores práticas para criar avaliações

[A Azure Migrate](migrate-overview.md) fornece um centro de ferramentas que o ajudam a descobrir, avaliar e migrar aplicações, infraestruturas e cargas de trabalho para o Microsoft Azure. O hub inclui ferramentas Azure Migrate e ofertas de fornecedores de software independentes de terceiros (ISV).

Este artigo resume as melhores práticas ao criar avaliações utilizando a ferramenta de avaliação do servidor Azure Migrate.

## <a name="about-assessments"></a>Sobre avaliações

As avaliações que cria com a Avaliação do Servidor Azure Migrate são uma imagem pontual dos dados. Existem dois tipos de avaliações que pode criar usando Azure Migrate: Avaliação do servidor:

**Tipo de Avaliação** | **Detalhes**
--- | --- 
**VM do Azure** | Avaliações para migrar os seus servidores no local para máquinas virtuais Azure. <br/><br/> Pode avaliar os seus [VMS VMware](how-to-set-up-appliance-vmware.md)no local, [VMs hiper-V](how-to-set-up-appliance-hyper-v.md)e [servidores físicos](how-to-set-up-appliance-physical.md) para migração para Azure utilizando este tipo de avaliação. [Saiba mais](concepts-assessment-calculation.md)
**Solução VMware no Azure (AVS)** | Avaliações para migrar os seus servidores no local para [Azure VMware Solution (AVS)](https://docs.microsoft.com/azure/azure-vmware/introduction). <br/><br/> Pode avaliar os seus [VMS VMware](how-to-set-up-appliance-vmware.md) no local para migração para Azure VMware Solution (AVS) utilizando este tipo de avaliação. [Saiba mais](concepts-azure-vmware-solution-assessment-calculation.md)


### <a name="sizing-criteria"></a>Critérios de dimensionamento
A Avaliação do Servidor fornece duas opções de critérios de dimensionamento:

**Critérios de dimensionamento** | **Detalhes** | **Dados**
--- | --- | ---
**Baseado no desempenho** | Avaliações que fazem recomendações com base em dados de desempenho recolhidos | **Avaliação Azure VM**: A recomendação do tamanho de VM baseia-se em dados de cpu e utilização da memória.<br/><br/> A recomendação do tipo de disco (hdd/SSD padrão ou discos geridos por prémios) baseia-se no IOPS e na produção dos discos no local.<br/><br/> **Avaliação da Solução Azure VMware (AVS):** A recomendação dos nós AVS baseia-se em dados de CPU e utilização da memória.
**As-is in-ins** | Avaliações que não usam dados de desempenho para fazer recomendações. | **Avaliação Azure VM**: A recomendação do tamanho de VM baseia-se no tamanho VM no local<br/><br> O tipo de disco recomendado baseia-se no que seleciona na definição do tipo de armazenamento para a avaliação.<br/><br/> **Avaliação da Solução Azure VMware (AVS):** A recomendação dos nós AVS baseia-se no tamanho VM no local.

#### <a name="example"></a>Exemplo
A título de exemplo, se tiver um VM no local com quatro núcleos a 20% de utilização, e memória de 8 GB com 10% de utilização, a avaliação do Azure VM será a seguinte:

- **Avaliação baseada no desempenho:**
    - Identifica núcleos e memória eficazes com base no núcleo (4 x 0,20 = 0,8) e na utilização da memória (8 GB x 0,10 = 0,8).
    - Aplica o fator de conforto especificado nas propriedades de avaliação (digamos 1,3x) para obter os valores a serem usados para dimensionamento. 
    - Recomenda o tamanho VM mais próximo em Azure que pode suportar ~1,04 núcleos (0,8 x 1.3) e ~1,04 GB (0,8 x 1.3) de memória.

- **Avaliação as-é (como no local):**
    -  Recomenda um VM com quatro núcleos; 8 GB de memória.


## <a name="best-practices-for-creating-assessments"></a>Melhores práticas para criar avaliações

O aparelho Azure Migrate traça continuamente o seu ambiente no local e envia metadados e dados de desempenho para o Azure. Siga estas boas práticas para avaliações de servidores descobertos com um aparelho:

- **Crie avaliações como-is**: Pode criar avaliações como-is imediatamente assim que as suas máquinas aparecerem no portal Azure Migrate.
- **Criar uma avaliação baseada no desempenho**: Depois de configurar a descoberta, recomendamos que espere pelo menos um dia antes de realizar uma avaliação baseada no desempenho:
    - Recolher dados de desempenho leva tempo. Esperar pelo menos um dia garante que há pontos de dados de desempenho suficientes antes de executar a avaliação.
    - Quando estiver a fazer avaliações baseadas no desempenho, certifique-se de que perfis o seu ambiente para a duração da avaliação. Por exemplo, se criar uma avaliação com uma duração de desempenho definida para uma semana, precisa esperar pelo menos uma semana após começar a descoberta, para que todos os pontos de dados sejam recolhidos. Se não o fizeres, a avaliação não terá uma classificação de cinco estrelas.
- **Recalcular avaliações**: Uma vez que as avaliações são instantâneos pontuais, não são automaticamente atualizados com os dados mais recentes. Para atualizar uma avaliação com os dados mais recentes, é necessário recalculá-lo.

Siga estas melhores práticas para avaliações de servidores importados para a Azure Migrate via . Ficheiro CSV:

- **Crie avaliações como-is**: Pode criar avaliações como-is imediatamente assim que as suas máquinas aparecerem no portal Azure Migrate.
- **Crie uma avaliação baseada no desempenho**: Isto ajuda a obter uma melhor estimativa de custos, especialmente se tiver uma capacidade de servidor sobreprovisionada no local. No entanto, a precisão da avaliação baseada no desempenho depende dos dados de desempenho especificados por si para os servidores. 
- **Recalcular avaliações**: Uma vez que as avaliações são instantâneos pontuais, não são automaticamente atualizados com os dados mais recentes. Para atualizar uma avaliação com os dados mais recentes importados, é necessário recalculá-lo.
 
### <a name="ftt-sizing-parameters-for-avs-assessments"></a>Parâmetros de dimensionamento ftt para avaliações de AVS

O motor de armazenamento utilizado em AVS é vSAN. as polícias de armazenamento vSAN definem os requisitos de armazenamento para as suas máquinas virtuais. Estas políticas garantem o nível de serviço necessário para os seus VMs porque determinam como o armazenamento é atribuído ao VM. Estas são as combinações FTT-Raid disponíveis: 

**Falhas na toleração (FTT)** | **Configuração RAID** | **Anfitriões mínimos necessários** | **Consideração de dimensionamento**
--- | --- | --- | --- 
1 | RAID-1 (Espelhamento) | 3 | Um VM de 100GB consumiria 200GB.
1 | RAID-5 (Codificação de Apagação) | 4 | Um VM de 100GB consumiria 133.33GB
2 | RAID-1 (Espelhamento) | 5 | Um VM de 100GB consumiria 300GB.
2 | RAID-6 (Codificação de Apagação) | 6 | Um VM de 100GB consumiria 150GB.
3 | RAID-1 (Espelhamento) | 7 | Um VM de 100GB consumiria 400GB.


## <a name="best-practices-for-confidence-ratings"></a>Melhores práticas para avaliações de confiança

Quando se faz avaliações baseadas no desempenho, é atribuída uma classificação de confiança de 1 estrela (mais baixa) a 5 estrelas (mais alta) para a avaliação. Para utilizar as classificações de confiança de forma eficaz:
- A avaliação do servidor Azure Migrate necessita dos dados de utilização do CPU/Memória VM.
- Para cada disco ligado ao VM no local, necessita dos dados de leitura/escrita IOPS/produção.
- Para cada adaptador de rede ligado ao VM, necessita dos dados de in/out da rede.

Dependendo da percentagem de pontos de dados disponíveis para a duração selecionada, a classificação de confiança para uma avaliação é fornecida como resumida no quadro seguinte.

   **Disponibilidade de pontos de dados** | **Classificação de confiança**
   --- | ---
   0%-20% | 1 Estrela
   21%-40% | 2 Estrelas
   41%-60% | 3 Estrelas
   61%-80% | 4 Estrelas
   81%-100% | 5 Estrelas


## <a name="common-assessment-issues"></a>Questões comuns de avaliação

Aqui está como abordar algumas questões ambientais comuns que afetam as avaliações.

###  <a name="out-of-sync-assessments"></a>Avaliações fora de sincronização

Se adicionar ou remover máquinas de um grupo depois de criar uma avaliação, a avaliação que criou será marcada **fora de sincronização**. Volte a fazer a avaliação (**Recalculare)** para refletir as alterações de grupo.

### <a name="outdated-assessments"></a>Avaliações desatualizadas

Se houver alterações no local para os VMs que estão num grupo que foi avaliado, a avaliação está marcada **desatualizada.** Uma avaliação pode ser marcada como "Ultrapassada" devido a uma ou mais alterações nas propriedades abaixo:

- Número de núcleos de processador
- Memória alocada
- Tipo de bota ou firmware
- Nome do sistema operativo, versão e arquitetura
- Número de discos
- Número de adaptador de rede
- Alteração do tamanho do disco (GB Atribuído)
- Atualização de propriedades nic. Exemplo: Alterações no endereço mac, adição de endereço IP, etc.

Volte a fazer a avaliação (**Recalcular)** para refletir as alterações.

### <a name="low-confidence-rating"></a>Baixa classificação de confiança

Uma avaliação pode não ter todos os pontos de dados por uma série de razões:

- Não analisou o ambiente durante o tempo para a qual está a criar a avaliação. Por exemplo, se estiver a criar uma *avaliação baseada no desempenho* com duração de desempenho definida para uma semana, precisa de esperar pelo menos uma semana depois de iniciar a descoberta de todos os pontos de dados para serem recolhidos. Pode sempre clicar em **Recalcularte** para ver a mais recente classificação de confiança aplicável. A classificação de confiança só é aplicável quando se cria uma avaliação *baseada no desempenho.*

- Poucas VMs foram encerradas durante o período para o qual a avaliação é calculada. Se houver VMs desligadas durante algum tempo, a Avaliação do Servidor não vai conseguir recolher os dados de desempenho correspondentes a esse período.

- Algumas VMs foram criadas após a deteção numa Avaliação do Servidor ter sido iniciada. Por exemplo, se estiver a criar uma avaliação para o histórico de desempenho do último mês, mas poucas VMs tiverem sido criadas no ambiente há apenas uma semana. Neste caso, os dados de desempenho das novas VMs não vão estar disponíveis durante todo este período e a classificação de confiança seria baixa.

### <a name="migration-tool-guidance-for-avs-assessments"></a>Orientação da Ferramenta de Migração para avaliações de AVS

No relatório de prontidão Azure para a avaliação da Solução VMware (AVS) do Azure VMware, pode ver as seguintes ferramentas sugeridas: 
- **VMware HCX ou Enterprise**: Para máquinas VMware, a solução VMWare Hybrid Cloud Extension (HCX) é a ferramenta de migração sugerida para migrar a sua carga de trabalho no local para a sua nuvem privada Azure VMWare Solution (AVS). [Saiba mais.](https://docs.microsoft.com/azure/azure-vmware/hybrid-cloud-extension-installation)
- **Desconhecido**: Para as máquinas importadas através de um ficheiro CSV, a ferramenta de migração padrão é desconhecida. No entanto, para máquinas VMware, recomenda-se a utilização da solução VMWare Hybrid Cloud Extension (HCX).


## <a name="next-steps"></a>Próximos passos

- [Saiba](concepts-assessment-calculation.md) como as avaliações são calculadas.
- [Saiba](how-to-modify-assessment.md) como personalizar uma avaliação.
