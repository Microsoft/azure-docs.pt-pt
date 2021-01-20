---
title: Metas de escalabilidade e desempenho de Ficheiros do Azure
description: Conheça os objetivos de escalabilidade e desempenho para os Ficheiros Azure, incluindo a capacidade, taxa de pedido e limites de largura de banda de entrada e saída.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e10f45af89e19f6fe62ff729f96d870e008c96ec
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611105"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Metas de escalabilidade e desempenho de Ficheiros do Azure

[O Azure Files](storage-files-introduction.md) oferece ações de ficheiros totalmente geridas na nuvem que são acessíveis através do protocolo SMB padrão da indústria. Este artigo discute os objetivos de escalabilidade e desempenho para Azure Files e Azure File Sync.

Os alvos de escalabilidade e desempenho listados aqui são alvos de alta qualidade, mas podem ser afetados por outras variáveis na sua implantação. Por exemplo, a produção de um ficheiro também pode ser limitada pela largura de banda de rede disponível, e não apenas pelos servidores que hospedam o serviço Azure Files. Recomendamos vivamente testar o seu padrão de utilização para determinar se a escalabilidade e desempenho dos Ficheiros Azure satisfazem os seus requisitos. Estamos também empenhados em aumentar estes limites ao longo do tempo. Por favor, não hesite em nos dar feedback, quer nos comentários abaixo, quer no [Azure Files UserVoice,](https://feedback.azure.com/forums/217298-storage/category/180670-files)sobre quais os limites que gostaria de nos ver aumentar.

## <a name="azure-storage-account-scale-targets"></a>Metas de escala de conta de armazenamento Azure

O recurso principal para uma partilha de ficheiros Azure é uma conta de armazenamento Azure. Uma conta de armazenamento representa um conjunto de armazenamento em Azure que pode ser usado por vários serviços de armazenamento, incluindo Azure Files, para armazenar dados. Outros serviços que armazenam dados em contas de armazenamento são o armazenamento Azure Blob, o armazenamento da Fila Azure e o armazenamento da Mesa Azure. Os seguintes alvos aplicam todos os serviços de armazenamento que armazenam dados numa conta de armazenamento:

[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> A utilização da conta de armazenamento de propósito geral de outros serviços de armazenamento afeta as suas ações de ficheiroS Azure na sua conta de armazenamento. Por exemplo, se atingir a capacidade máxima de conta de armazenamento com o armazenamento Azure Blob, não será capaz de criar novos ficheiros na sua parte de ficheiroS Azure, mesmo que a sua parte de ficheiro Azure esteja abaixo do tamanho máximo da partilha.

## <a name="azure-files-scale-targets"></a>Metas de dimensionamento dos Ficheiros do Azure

Existem três categorias de limitações a considerar para os Ficheiros Azure: contas de armazenamento, ações e ficheiros.

Por exemplo: Com ações de ficheiros premium, uma única ação pode atingir 100.000 IOPS e um único ficheiro pode escalar até 5.000 IOPS. Então, se tiver três ficheiros numa só ação, o IOPS máximo que pode obter dessa parte é de 15.000.

### <a name="standard-storage-account-limits"></a>Limites padrão da conta de armazenamento

Consulte a secção de metas da [balança de armazenamento Azure](#azure-storage-account-scale-targets) para estes limites.

### <a name="premium-filestorage-account-limits"></a>Limites da conta Premium FileStorage

[!INCLUDE [azure-storage-limits-filestorage](../../../includes/azure-storage-limits-filestorage.md)]

> [!IMPORTANT]
> Os limites da conta de armazenamento aplicam-se a todas as ações. A escala até ao máximo para contas de arquitetões só é possível se houver apenas uma ação por conta Detorage.

### <a name="file-share-and-file-scale-targets"></a>Alvos de partilha de ficheiros e escala de ficheiros

> [!NOTE]
> As ações de ficheiros standard maiores do que 5 TiB têm determinadas limitações. Para obter uma lista de limitações e instruções para permitir tamanhos de ações de ficheiros maiores, consulte o [permitir ações de ficheiros maiores na](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) secção de partilhas de ficheiros padrão do guia de planeamento.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Alvos de dimensionamento do Azure File Sync

O Azure File Sync foi concebido com o objetivo de uma utilização ilimitada, mas nem sempre é possível uma utilização ilimitada. O quadro seguinte indica os limites dos testes da Microsoft e indica também quais os alvos que são limites rígidos:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Métricas de desempenho do Azure File Sync

Uma vez que o agente Azure File Sync funciona numa máquina do Windows Server que se conecta às partilhas de ficheiros Azure, o desempenho sincronizado eficaz depende de uma série de fatores na sua infraestrutura: Windows Server e a configuração subjacente do disco, largura de banda de rede entre o servidor e o armazenamento do Azure, tamanho do ficheiro, tamanho total do conjunto de dados e a atividade no conjunto de dados. Uma vez que o Azure File Sync funciona ao nível do ficheiro, as características de desempenho de uma solução baseada em Azure File Sync são melhor medidas no número de objetos (ficheiros e diretórios) processados por segundo.

Para Azure File Sync, o desempenho é fundamental em duas fases:

1. **Provisão inicial:** Para otimizar o desempenho no provisionamento inicial, consulte o [Onboarding com Azure File Sync](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) para obter os detalhes de implementação ideais.
2. **Sincronização em curso**: Depois de os dados terem sido inicialmente semeados nas ações de ficheiros Azure, o Azure File Sync mantém vários pontos finais sincronizados.

Para ajudá-lo a planear a sua implantação para cada uma das fases, abaixo estão os resultados observados durante os testes internos num sistema com uma config

| Configuração do sistema | Detalhes |
|-|-|
| CPU | 64 Núcleos Virtuais com cache MiB L3 64 |
| Memória | 128 GiB |
| Disco | Discos SAS com RAID 10 com cache apoiado por bateria |
| Rede | Rede 1 Gbps |
| Carga de trabalho | Servidor de ficheiros de finalidade geral|

| Provisão única inicial  | Detalhes |
|-|-|
| Número de objetos | 25 milhões de objetos |
| Tamanho do conjunto de dados| ~4.7 TiB |
| Tamanho médio do ficheiro | ~200 KiB (Maior Arquivo: 100 GiB) |
| Enumeração inicial da alteração da nuvem | 20 objetos por segundo  |
| Envio de produção | 20 objetos por segundo por grupo de sincronização |
| Transfer para o Espaço Nome | 400 objetos por segundo |

### <a name="initial-one-time-provisioning"></a>Provisão única inicial

**Enumeração inicial da mudança de nuvem**: Quando um novo grupo de sincronização é criado, a enumeração inicial da mudança de nuvem é o primeiro passo que irá executar. Neste processo, o sistema irá enumerar todos os itens na Azure File Share. Durante este processo, não haverá atividade sincronizada, ou seja, nenhum itens será descarregado do ponto final da nuvem para o ponto final do servidor e nenhum itens será carregado do ponto final do servidor para o ponto final da nuvem. A atividade de sincronização será retomada assim que a enumeração inicial da alteração da nuvem estiver concluída.
A taxa de desempenho é de 20 objetos por segundo. Os clientes podem estimar o tempo que levará para completar a enumeração inicial da alteração da nuvem, determinando o número de itens na partilha de nuvem e usando as seguintes fórmulas para obter o tempo em dias. 

   **Tempo (em dias) para a enumeração inicial da nuvem = (Número de objetos no ponto final da nuvem)/((20 * 60 * 60 * 24)**

**Produção de download de namespace** Quando um novo ponto final do servidor é adicionado a um grupo de sincronização existente, o agente Azure File Sync não descarrega nenhum dos conteúdos do ficheiro a partir do ponto final da nuvem. Primeiro sincroniza o espaço de nome completo e, em seguida, desencadeia a chamada de fundo para descarregar os ficheiros, quer na sua totalidade, quer, se o tiering da nuvem estiver ativado, para a política de tiering de nuvem definida no ponto final do servidor.


| Sincronização em curso  | Detalhes  |
|-|--|
| Número de objetos sincronizados| 125.000 objetos (~1% churn) |
| Tamanho do conjunto de dados| 50 GiB |
| Tamanho médio do ficheiro | ~500 KiB |
| Envio de produção | 20 objetos por segundo por grupo de sincronização |
| Download Completo Do Rendimento* | 60 objetos por segundo |

*Se o tiering da nuvem estiver ativado, é provável que observe um melhor desempenho, uma vez que apenas alguns dos dados do ficheiro são descarregados. O Azure File Sync apenas descarrega os dados dos ficheiros em cache quando são alterados em qualquer um dos pontos finais. Para quaisquer ficheiros hierárquicos ou recém-criados, o agente não descarrega os dados do ficheiro e, em vez disso, apenas sincroniza o espaço de nomes em todos os pontos finais do servidor. O agente também suporta downloads parciais de ficheiros hierárquicos à medida que são acedidos pelo utilizador. 

> [!Note]  
> Os números acima não são uma indicação do desempenho que irá experimentar. O desempenho real dependerá de múltiplos fatores, tal como descritos no início desta secção.

Como guia geral para a sua implantação, deve ter em mente algumas coisas:

- O produto do objeto escala aproximadamente proporcionalmente ao número de grupos de sincronização no servidor. Dividir dados em vários grupos de sincronização num servidor produz melhor produção, que também é limitada pelo servidor e pela rede.
- A produção do objeto é inversamente proporcional à produção mib por segundo. Para ficheiros mais pequenos, experimentará uma produção mais elevada em termos do número de objetos processados por segundo, mas menor MiB por segundo. Inversamente, para ficheiros maiores, obterá menos objetos processados por segundo, mas mais alto MiB por segundo. A produção miB por segundo é limitada pelos alvos de escala de Ficheiros Azure.

## <a name="see-also"></a>Ver também

- [Planear uma implementação de Ficheiros do Azure](storage-files-planning.md)
- [Planear uma implementação do Azure File Sync](storage-sync-files-planning.md)
