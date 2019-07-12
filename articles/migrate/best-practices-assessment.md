---
title: Melhores práticas para a criação de avaliação com a avaliação do servidor de migrar do Azure | Documentos da Microsoft
description: Fornece sugestões para a criação de avaliações com avaliação de servidor de migrar do Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: d417efd4abf14247af171ea77b479f590e14fe76
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812968"
---
# <a name="best-practices-for-creating-assessments"></a>Melhores práticas para a criação de avaliações

[O Azure Migrate](migrate-overview.md) fornece um hub de ferramentas que ajudam a detetar, avaliar e migrar cargas de trabalho, aplicações e infraestrutura para o Microsoft Azure. O hub inclui as ferramentas do Azure Migrate e ofertas de (ISV fornecedor) de terceiros independentes de software. 

Este artigo resume as melhores práticas ao criar avaliações usando a ferramenta de avaliação do servidor de migrar do Azure. 

## <a name="about-assessments"></a>Sobre avaliações

Avaliações de que criar com a avaliação do servidor de migrar do Azure são um instantâneo de ponto no tempo dos dados. Existem dois tipos de avaliações no Azure Migrate.

**Tipo de avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Com base em desempenho** | Avaliações fazer recomendações com base nos dados de desempenho recolhidos | Recomendação de tamanho VM baseia-se nos dados de utilização de CPU e memória.<br/><br/> Recomendação de tipo de disco (standard ou premium discos geridos) baseia-se sobre o IOPS e débito dos discos no local.
**Como-está no local** | Avaliações que não utilizam dados de desempenho para fazer recomendações. | Recomendação de tamanho VM baseia-se no tamanho VM no local<br/><br> O tipo de disco recomendado baseia-se no que selecionar o tipo de armazenamento para a avaliação.

### <a name="example"></a>Exemplo
Por exemplo, se tiver uma VM no local com quatro núcleos em 20% de utilização e memória, de 8 GB, com 10% de utilização, as avaliações será da seguinte forma:

- **Avaliação com base em desempenho**:
    - Recomenda núcleos e memória com base nos núcleos (núcleos 0,8) e a utilização da memória (GB 0,8).
    - A avaliação aplica-se um fator de conforto predefinida de 30%.
    - Recomendação de VM: ~1.4 núcleos (x1.3 0,8) e ~1.4 GB de memória.
- **Como-é a avaliação de (no local)** :
    -  Recomenda uma VM com quatro núcleos; 8 GB de memória.

## <a name="best-practices-for-creating-assessments"></a>Melhores práticas para a criação de avaliações

A aplicação do Azure Migrate continuamente descreve o ambiente no local e envia dados de desempenho e de metadados para o Azure. Siga estas práticas recomendadas para a criação de avaliações:

- **Criar como-é avaliações**: Pode criar como-é avaliações imediatamente após a deteção.
- **Criar avaliação com base em desempenho**: Depois de configurar a deteção, recomendamos que aguarde, pelo menos, um dia antes de executar uma avaliação com base em desempenho:
    - Recolha de dados de desempenho demora tempo. A aguardar pelo menos um dia garante que existem suficientes pontos de dados de desempenho antes de executar a avaliação.
    - Para os dados de desempenho, a aplicação recolhe os pontos de dados em tempo real a cada 20 segundos para cada métrica de desempenho e distribui-los até um ponto único de cinco minutos de dados. A aplicação envia os dados de cinco minutos apontam para o Azure, a cada hora para o cálculo de avaliação.  
- **Obter os dados mais recentes**: Avaliações não são atualizadas automaticamente com os dados mais recentes. Para atualizar uma avaliação com os dados mais recentes, terá de executá-lo novamente. 
- **Certifique-se correspondem durações**: Quando estiver a executar avaliações baseadas em desempenho, certifique-se de seu perfil do seu ambiente durante o período de avaliação. Por exemplo, se criar uma avaliação com uma duração de desempenho definida para uma semana, terá de aguardar por, pelo menos, uma semana depois de começar a deteção, para todos os pontos de dados a serem recolhidos. Se não o fizer, a avaliação não obter uma classificação de cinco estrelas. 
- **Evitar a falta de pontos de dados**: Os seguintes problemas podem resultar na ausência de pontos de dados numa avaliação com base em desempenho:
    - As VMs estão desligadas durante a avaliação e não são recolhidos dados de desempenho. 
    - Se criar VMs durante o mês em que basear o histórico de desempenho. os dados para essas VMs serão menos de um mês. 
    - A avaliação é criada imediatamente após a deteção ou a hora de avaliação não corresponde a hora de recolha de dados de desempenho.

## <a name="best-practices-for-confidence-ratings"></a>Melhores práticas para classificações de confiança

Quando executar avaliações baseadas em desempenho, uma confiança de classificação de estrelas de 1 (menor) a 5 estrelas (mais alta) é concedida à avaliação. Para utilizar as classificações de confiança com eficiência:
- Avaliação de servidor migrar do Azure tem dos dados de utilização para CPU/memória da VM e o dados IOPS/débito de disco.
- Para cada adaptador de rede ligada a uma VM, o Azure Migrate requer a dados de entrada/saída de rede.
- Se os dados de utilização não estão disponíveis no vCenter Server, a recomendação de tamanho feita pelo Azure Migrate pode não ser fiável. 

Consoante a percentagem de pontos de dados disponíveis, as classificações de confiança para uma avaliação estão resumidas na tabela seguinte.

   **Disponibilidade de ponto de dados** | **Classificação de confiança**
   --- | ---
   0%-20% | 1 Estrela
   21%-40% | 2 Estrelas
   41%-60% | 3 Estrelas
   61%-80% | 4 Estrelas
   81%-100% | 5 Estrelas

- Se receber uma classificação de confiança para uma avaliação que é inferior a cinco estrelas, espera, pelo menos, um dia e, em seguida, recalcular a avaliação.
- Uma classificação baixa significa que as recomendações de dimensionamento pode não ser fiável. Neste caso, recomendamos que modificar as propriedades de avaliação para utilizar como-é a avaliação no local.

## <a name="common-assessment-issues"></a>Problemas comuns de avaliação

Eis como resolver alguns problemas de ambiente comuns que afetam as avaliações.

###  <a name="out-of-sync-assessments"></a>Avaliações de fora de sincronização

Se adicionar ou remover máquinas de um grupo depois de criar uma avaliação, a avaliação que criou será marcada **fora de sincronização**. Execute novamente a avaliação (**recalcular**) para refletir as alterações de grupo.

### <a name="outdated-assessments"></a>Avaliações ultrapassadas

Se houver alterações no local para VMs que estão num grupo de que é foi analisado, a avaliação está marcada **desatualizado**. Para refletir as alterações, execute novamente a avaliação.

### <a name="missing-data-points"></a>Pontos de dados em falta

Uma avaliação pode não ter todos os pontos de dados por uma série de motivos:

- VMs podem ser desligadas durante a avaliação e não são recolhidos dados de desempenho. 
- As VMs podem ser criadas durante o mês em que desempenho histórico se baseia, portanto, seus dados de desempenho são menos de um mês. 
- A avaliação foi criada imediatamente após a deteção. Para recolher os dados de desempenho para um período de tempo especificado, terá de aguardar o período de tempo antes de executar uma avaliação especificado. Por exemplo, se pretender avaliar dados de desempenho para uma semana, terá de aguardar uma semana após a deteção. Se não tiver a avaliação não obter uma classificação de cinco estrelas. 


## <a name="next-steps"></a>Passos Seguintes

- [Saiba](concepts-assessment-calculation.md) como são calculadas as avaliações.
- [Saiba](how-to-modify-assessment.md) como personalizar uma avaliação.
