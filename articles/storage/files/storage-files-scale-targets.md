---
title: Metas de escalabilidade e desempenho de Ficheiros do Azure
description: Conheça os alvos de escalabilidade e desempenho dos Ficheiros Azure, incluindo a capacidade, a taxa de pedido e os limites de largura de banda de entrada e saída.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 46c46faf8f7ee52978ae5542ab7ebd72a41b8357
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81536451"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Metas de escalabilidade e desempenho de Ficheiros do Azure

[O Azure Files](storage-files-introduction.md) oferece partilhas de ficheiros totalmente geridas na nuvem que são acessíveis através do protocolo Padrão SMB da indústria. Este artigo discute os alvos de escalabilidade e desempenho para os Ficheiros Azure e O Sincronizado de Ficheiros Azure.

Os alvos de escalabilidade e desempenho listados aqui são alvos de alta qualidade, mas podem ser afetados por outras variáveis na sua implementação. Por exemplo, a entrada de um ficheiro também pode ser limitada pela largura de banda da rede disponível, e não apenas pelos servidores que hospedam o serviço Azure Files. Recomendamos vivamente testar o seu padrão de utilização para determinar se a escalabilidade e desempenho dos Ficheiros Azure satisfazem os seus requisitos. Estamos também empenhados em aumentar estes limites ao longo do tempo. Por favor, não hesite em dar-nos feedback, seja nos comentários abaixo ou no [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files), sobre quais os limites que gostaria de nos ver aumentar.

## <a name="azure-storage-account-scale-targets"></a>Metas de escala de conta de armazenamento azure

O recurso-mãe para uma partilha de ficheiros Azure é uma conta de armazenamento Azure. Uma conta de armazenamento representa um conjunto de armazenamento em Azure que pode ser usado por vários serviços de armazenamento, incluindo Ficheiros Azure, para armazenar dados. Outros serviços que armazenam dados em contas de armazenamento são o armazenamento azure Blob, armazenamento de fila Azure e armazenamento de mesa Azure. Os seguintes alvos aplicam todos os serviços de armazenamento armazenando dados numa conta de armazenamento:

[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> A utilização da conta de armazenamento de outros serviços de armazenamento afeta as suas ações de ficheiroS Azure na sua conta de armazenamento. Por exemplo, se atingir a capacidade máxima da conta de armazenamento com armazenamento Azure Blob, não poderá criar novos ficheiros na sua partilha de ficheiros Azure, mesmo que a sua quota de ficheiroS Azure esteja abaixo do tamanho máximo das ações.

## <a name="azure-files-scale-targets"></a>Alvos de escala de Ficheiros Azure

Existem três categorias de limitações a ter em conta para os Ficheiros Azure: contas de armazenamento, ações e ficheiros.

Por exemplo: Com ações de ficheiropremium, uma única ação pode atingir 100.000 IOPS e um único ficheiro pode escalar até 5.000 IOPS. Então, se tiver três ficheiros numa só ação, o iOPS máximo que pode obter dessa parte é de 15.000.

### <a name="standard-storage-account-limits"></a>Limites padrão da conta de armazenamento

Consulte a secção de metas de escala de conta de [armazenamento Azure](#azure-storage-account-scale-targets) para estes limites.

### <a name="premium-filestorage-account-limits"></a>Limites de conta premium FileStorage

[!INCLUDE [azure-storage-limits-filestorage](../../../includes/azure-storage-limits-filestorage.md)]

> [!IMPORTANT]
> Os limites da conta de armazenamento aplicam-se a todas as ações. A escala até ao máximo para as contas de FileStorage só é alcançável se houver apenas uma ação por conta fileStorage.

### <a name="file-share-and-file-scale-targets"></a>Alvos de partilha de ficheiros e escala de ficheiros

> [!NOTE]
> As ações de ficheiros padrão superiores a 5 TiB têm certas limitações. Para obter uma lista de limitações e instruções para permitir tamanhos maiores de partilha de ficheiros, consulte as [maiores partilhas de ficheiros ativadas na](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) secção de partilhas de ficheiros padrão do guia de planeamento.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Alvos de dimensionamento do Azure File Sync

O Azure File Sync foi concebido com o objetivo de utilização ilimitada, mas nem sempre é possível uma utilização ilimitada. O quadro seguinte indica os limites dos testes da Microsoft e indica também quais os alvos que são limites rígidos:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Métricas de desempenho do Azure File Sync

Uma vez que o agente Dessincronização de ficheiros Azure funciona numa máquina do Windows Server que se conecta às partilhas de ficheiros Azure, o desempenho de sincronização eficaz depende de uma série de fatores na sua infraestrutura: Windows Server e configuração de disco subjacente, largura de banda da rede entre o servidor e o armazenamento Azure, tamanho de ficheiro, tamanho total do conjunto de dados e atividade no conjunto de dados. Uma vez que o Azure File Sync funciona a nível de ficheiros, as características de desempenho de uma solução baseada em Sincronização de Ficheiros Azure são mais bem medidas no número de objetos (ficheiros e diretórios) processados por segundo.

Para o Azure File Sync, o desempenho é crítico em duas fases:

1. **Disposição inicial única**: Para otimizar o desempenho no fornecimento inicial, consulte o [Onboarding com o Azure File Sync](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) para obter os detalhes ideais de implementação.
2. **Sincronização em curso**: Após a primeira semeada dos dados nas ações de ficheiro Sedinulado, o Azure File Sync mantém vários pontos finais sincronizados.

Para ajudá-lo a planear a sua implantação para cada uma das etapas, abaixo estão os resultados observados durante os testes internos num sistema com um config

| Configuração do sistema |  |
|-|-|
| CPU | 64 Núcleos Virtuais com 64 mib l3 cache |
| Memória | 128 GiB |
| Disco | Discos SAS com RAID 10 com cache apoiado por bateria |
| Rede | Rede de 1 Gbps |
| Carga de trabalho | Servidor de ficheiros de propósito geral|

| Provisionamento único inicial  |  |
|-|-|
| Número de objetos | 25 milhões de objetos |
| Tamanho do conjunto de dados| ~4.7 Tib |
| Tamanho médio do arquivo | ~200 KiB (Maior Arquivo: 100 GiB) |
| Upload De entrada | 20 objetos por segundo por grupo de sincronização |
| Passe de download de espaço de nome* | 400 objetos por segundo |

*Quando um novo ponto final do servidor é criado, o agente Dossincronização de ficheiros Azure não descarrega nenhum dos conteúdos de ficheiro. Primeiro sincroniza o espaço completo de nomes e, em seguida, dispara a recolha de fundo para descarregar os ficheiros, quer na sua totalidade, quer, se o tiering em nuvem estiver ativado, para a política de tiering de nuvem definida no ponto final do servidor.

| Sincronização contínua  |   |
|-|--|
| Número de objetos sincronizados| 125.000 objetos (~1% churn) |
| Tamanho do conjunto de dados| 50 GiB |
| Tamanho médio do arquivo | ~500 KiB |
| Upload De entrada | 20 objetos por segundo por grupo de sincronização |
| Download Completo Através de Putput* | 60 objetos por segundo |

*Se o tiering de nuvem estiver ativado, é provável que observe um melhor desempenho, uma vez que apenas alguns dos dados do ficheiro são descarregados. O Azure File Sync apenas descarrega os dados dos ficheiros em cache quando são alterados em qualquer um dos pontos finais. Para quaisquer ficheiros hierárquicos ou recém-criados, o agente não descarrega os dados do ficheiro e, em vez disso, sincroniza apenas o espaço de nome para todos os pontos finais do servidor. O agente também suporta transferências parciais de ficheiros hierárquicos à medida que são acedidos pelo utilizador. 

> [!Note]  
> Os números acima não indicam o desempenho que irá experimentar. O desempenho real dependerá de múltiplos fatores, tal como delineado no início desta secção.

Como guia geral para a sua implantação, deve ter algumas coisas em mente:

- A entrada do objeto escala aproximadamente proporcionalmente ao número de grupos de sincronização no servidor. Dividir dados em vários grupos de sincronização num servidor produz uma melhor produção, que também é limitada pelo servidor e pela rede.
- A entrada do objeto é inversamente proporcional ao MiB por segundo de entrada. Para ficheiros mais pequenos, irá experimentar uma maior entrada em termos do número de objetos processados por segundo, mas menor MiB por segundo de entrada. Inversamente, para ficheiros maiores, obterá menos objetos processados por segundo, mas mib mais alto por segundo de entrada. A entrada mib por segundo é limitada pelos alvos de escala de Ficheiros Azure.

## <a name="see-also"></a>Consulte também

- [Planear uma implementação dos Ficheiros do Azure](storage-files-planning.md)
- [Planear uma implementação da Sincronização de Ficheiros do Azure](storage-sync-files-planning.md)
