---
title: Avaliação das melhores práticas na Avaliação do Servidor Migratório Azure
description: Dicas para a criação de avaliações com a Avaliação do Servidor Migratório Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: de6953b6648613595bc9975b17941b3a453a6d60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185983"
---
# <a name="best-practices-for-creating-assessments"></a>Boas práticas para a criação de avaliações

[A Azure Migrate](migrate-overview.md) fornece um centro de ferramentas que o ajudam a descobrir, avaliar e migrar apps, infraestruturas e cargas de trabalho para o Microsoft Azure. O hub inclui ferramentas Azure Migrate e ofertas de fornecedores de software independentes de terceiros (ISV).

Este artigo resume as melhores práticas ao criar avaliações utilizando a ferramenta de avaliação do servidor de migração Azure.

## <a name="about-assessments"></a>Sobre avaliações

As avaliações que cria com a Avaliação do Servidor Migratório Azure são uma imagem pontual dos dados. Existem dois tipos de avaliações em Azure Migrate.

**Tipo de avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Baseado no desempenho** | Avaliações que fazem recomendações com base em dados de desempenho recolhidos | A recomendação do tamanho vM baseia-se em dados de CPU e utilização da memória.<br/><br/> A recomendação do tipo disco (hDD/SSD padrão ou discos geridos por prémios) baseia-se no IOPS e na entrada dos discos no local.
**As-is no local** | Avaliações que não usam dados de desempenho para fazer recomendações. | Recomendação de tamanho VM baseia-se no tamanho vm no local<br/><br> O tipo de disco recomendado baseia-se no que seleciona na definição do tipo de armazenamento para a avaliação.

### <a name="example"></a>Exemplo
Como exemplo, se tiver um VM no local com quatro núcleos a 20% de utilização, e memória de 8 GB com utilização de 10%, as avaliações serão as seguintes:

- **Avaliação baseada no desempenho:**
    - Identifica núcleos e memória eficazes com base no núcleo (4 x 0,20 = 0,8) e na utilização da memória (8 GB x 0,10 = 0,8).
    - Aplica o fator de conforto especificado nas propriedades de avaliação (digamos 1,3x) para obter os valores a utilizar para dimensionamento. 
    - Recomenda o tamanho vm mais próximo em Azure que pode suportar ~1,04 núcleos (0,8 x 1,3) e ~1,04 GB (0,8 x 1,3) memória.

- **Avaliação as-is (as-in-local):**
    -  Recomenda um VM com quatro núcleos; 8 GB de memória.

## <a name="best-practices-for-creating-assessments"></a>Boas práticas para a criação de avaliações

O aparelho Azure Migrate perfis continuamente do seu ambiente no local, e envia metadados e dados de desempenho para o Azure. Siga estas boas práticas para avaliações de servidores descobertos com um aparelho:

- **Crie avaliações as-is**: Pode criar avaliações as-is imediatamente assim que as suas máquinas aparecem no portal Azure Migrate.
- **Criar uma avaliação baseada no desempenho**: Após a criação da descoberta, recomendamos que espere pelo menos um dia antes de fazer uma avaliação baseada no desempenho:
    - Recolher dados de desempenho leva tempo. Esperar pelo menos um dia garante que existem pontos de dados de desempenho suficientes antes de fazer a avaliação.
    - Quando estiver a fazer avaliações baseadas no desempenho, certifique-se de que perfis do seu ambiente durante a duração da avaliação. Por exemplo, se criar uma avaliação com uma duração de desempenho definida para uma semana, terá de esperar pelo menos uma semana após começar a ser descoberta, para que todos os pontos de dados sejam recolhidos. Se não o fizeres, a avaliação não terá uma classificação de cinco estrelas.
- **Recalcular avaliações**: Uma vez que as avaliações são instantâneos ponto-a-tempo, não são automaticamente atualizados com os dados mais recentes. Para atualizar uma avaliação com os dados mais recentes, é necessário recalculá-la.

Siga estas boas práticas para avaliações de servidores importados para O Migrado Azure via . Ficheiro CSV:

- **Crie avaliações as-is**: Pode criar avaliações as-is imediatamente assim que as suas máquinas aparecem no portal Azure Migrate.
- **Criar uma avaliação baseada no desempenho**: Isto ajuda a obter uma melhor estimativa de custos, especialmente se tiver uma capacidade de servidor sobreprovisionada no local. No entanto, a precisão da avaliação baseada no desempenho depende dos dados de desempenho especificados por si para os servidores. 
- **Recalcular avaliações**: Uma vez que as avaliações são instantâneos ponto-a-tempo, não são automaticamente atualizados com os dados mais recentes. Para atualizar uma avaliação com os dados importados mais recentes, precisa recalculá-lo.

## <a name="best-practices-for-confidence-ratings"></a>Boas práticas para classificações de confiança

Quando executa avaliações baseadas no desempenho, uma classificação de confiança de 1 estrela (mais baixa) a 5 estrelas (mais alta) é atribuída à avaliação. Para utilizar as classificações de confiança de forma eficaz:
- A Avaliação do Servidor Migratório Azure necessita dos dados de utilização para cpu/memória VM.
- Para cada disco ligado ao VM no local, necessita dos dados de leitura/escrita IOPS/entrada.
- Para cada adaptador de rede ligado ao VM, necessita da rede dentro/fora de dados.

Dependendo da percentagem de pontos de dados disponíveis durante a duração selecionada, a classificação de confiança para uma avaliação é fornecida como resumida no quadro seguinte.

   **Disponibilidade de pontos de dados** | **Classificação de confiança**
   --- | ---
   0%-20% | 1 Estrela
   21%-40% | 2 Estrelas
   41%-60% | 3 Estrelas
   61%-80% | 4 Estrelas
   81%-100% | 5 Estrelas


## <a name="common-assessment-issues"></a>Questões comuns de avaliação

Aqui está como abordar algumas questões de ambiente comum que afetam avaliações.

###  <a name="out-of-sync-assessments"></a>Avaliações dessincronizadas

Se adicionar ou remover máquinas de um grupo depois de criar uma avaliação, a avaliação que criou será marcada **dessincronizada**. Executar a avaliação novamente **(Recalcular)** para refletir as alterações do grupo.

### <a name="outdated-assessments"></a>Avaliações desatualizadas

Se houver alterações no local para VMs que estejam num grupo que foi avaliado, a avaliação está marcada **desatualizada.** Para refletir as alterações, volte a fazer a avaliação.

### <a name="low-confidence-rating"></a>Baixa classificação de confiança

Uma avaliação pode não ter todos os pontos de dados por uma série de razões:

- Não analisou o ambiente durante o tempo para a qual está a criar a avaliação. Por exemplo, se estiver a criar uma *avaliação baseada no desempenho* com a duração do desempenho definida para uma semana, precisa de esperar pelo menos uma semana depois de iniciar a descoberta de todos os pontos de dados para serem recolhidos. Pode sempre clicar em **Recalcular** para ver a mais recente classificação de confiança aplicável. A classificação de confiança só é aplicável quando se cria uma avaliação *baseada no desempenho.*

- Poucas VMs foram encerradas durante o período para o qual a avaliação é calculada. Se houver VMs desligadas durante algum tempo, a Avaliação do Servidor não vai conseguir recolher os dados de desempenho correspondentes a esse período.

- Algumas VMs foram criadas após a deteção numa Avaliação do Servidor ter sido iniciada. Por exemplo, se estiver a criar uma avaliação para o histórico de desempenho do último mês, mas poucas VMs tiverem sido criadas no ambiente há apenas uma semana. Neste caso, os dados de desempenho das novas VMs não vão estar disponíveis durante todo este período e a classificação de confiança seria baixa.


## <a name="next-steps"></a>Passos seguintes

- [Saiba](concepts-assessment-calculation.md) como as avaliações são calculadas.
- [Aprenda](how-to-modify-assessment.md) a personalizar uma avaliação.
