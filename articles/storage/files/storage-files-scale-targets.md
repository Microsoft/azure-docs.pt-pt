---
title: Metas de desempenho e escalabilidade de arquivos do Azure | Microsoft Docs
description: Saiba mais sobre as metas de escalabilidade e desempenho para arquivos do Azure, incluindo capacidade, taxa de solicitação e limites de largura de banda de entrada e saída.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 766dacb69a3f1857197684f552d05a1376e94509
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514858"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Metas de desempenho e escalabilidade de arquivos do Azure

Os [arquivos do Azure](storage-files-introduction.md) oferecem compartilhamentos de arquivos totalmente gerenciados na nuvem que são acessíveis por meio do protocolo SMB padrão do setor. Este artigo discute as metas de desempenho e escalabilidade para arquivos e Sincronização de Arquivos do Azure do Azure.

Os destinos de escalabilidade e desempenho listados aqui são destinos de alto nível, mas podem ser afetados por outras variáveis em sua implantação. Por exemplo, a taxa de transferência de um arquivo também pode ser limitada por sua largura de banda de rede disponível, não apenas pelos servidores que hospedam o serviço arquivos do Azure. É altamente recomendável testar seu padrão de uso para determinar se a escalabilidade e o desempenho dos arquivos do Azure atendem às suas necessidades. Também estamos comprometidos em aumentar esses limites ao longo do tempo. Não hesite em nos enviar comentários, seja nos comentários abaixo ou no [UserVoice do Azure files](https://feedback.azure.com/forums/217298-storage/category/180670-files), sobre quais limites você gostaria de ver aumentamos.

## <a name="azure-storage-account-scale-targets"></a>Destinos de escala da conta de armazenamento do Azure

O recurso pai de um compartilhamento de arquivos do Azure é uma conta de armazenamento do Azure. Uma conta de armazenamento representa um pool de armazenamento no Azure que pode ser usado por vários serviços de armazenamento, incluindo arquivos do Azure, para armazenar dados. Outros serviços que armazenam dados em contas de armazenamento são o armazenamento de BLOBs do Azure, o armazenamento de filas do Azure e o armazenamento de tabelas do Azure. Os destinos a seguir aplicam todos os serviços de armazenamento que armazenam dados em uma conta de armazenamento:

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> A utilização da conta de armazenamento de uso geral de outros serviços de armazenamento afeta os compartilhamentos de arquivos do Azure em sua conta de armazenamento. Por exemplo, se você atingir a capacidade máxima da conta de armazenamento com o armazenamento de BLOBs do Azure, não poderá criar novos arquivos no compartilhamento de arquivos do Azure, mesmo que o compartilhamento de arquivos do Azure esteja abaixo do tamanho máximo de compartilhamento.

## <a name="azure-files-scale-targets"></a>Destinos de escala de arquivos do Azure

Há três categorias de limitações a serem consideradas para os arquivos do Azure: contas de armazenamento, compartilhamentos e arquivos.

Por exemplo: com compartilhamentos de arquivos premium, um único compartilhamento pode atingir 100.000 IOPS e um único arquivo pode ser dimensionado para até 5.000 IOPS. Portanto, se você tiver três arquivos em um único compartilhamento, o máximo de IOPS que você pode obter desse compartilhamento é 15.000.

### <a name="standard-storage-account-limits"></a>Limites de conta de armazenamento Standard

Consulte a seção [destinos de escala da conta de armazenamento do Azure](#azure-storage-account-scale-targets) para esses limites.

### <a name="premium-filestorage-account-limits"></a>Limites da conta de armazenamento Premium

[!INCLUDE [azure-storage-limits-filestorage](../../../includes/azure-storage-limits-filestorage.md)]

> [!IMPORTANT]
> Os limites da conta de armazenamento são aplicados a todos os compartilhamentos. O dimensionamento até o máximo para contas de armazenamento de filebackup só será atingível se houver apenas um compartilhamento por conta de armazenamento de File.

### <a name="file-share-and-file-scale-targets"></a>Destinos de escala de arquivo e compartilhamento de arquivos

> [!NOTE]
> Compartilhamentos de arquivos padrão maiores que 5 TiB têm certas limitações e restrições regionais.
> Para obter uma lista de limitações, informações regionais e instruções para habilitar esses tamanhos maiores de compartilhamento de arquivos, consulte a seção [integrado a compartilhamentos de arquivos maiores](storage-files-planning.md#onboard-to-larger-file-shares-standard-tier) do guia de planejamento.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Destinos de escala Sincronização de Arquivos do Azure

A Sincronização de Arquivos do Azure foi projetada com o objetivo de uso ilimitado, mas o uso ilimitado nem sempre é possível. A tabela a seguir indica os limites do teste da Microsoft e também indica quais destinos são os limites rígidos:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Sincronização de Arquivos do Azure métricas de desempenho

Como o agente de Sincronização de Arquivos do Azure é executado em um computador Windows Server que se conecta aos compartilhamentos de arquivos do Azure, o desempenho de sincronização efetivo depende de vários fatores em sua infraestrutura: Windows Server e a configuração de disco subjacente, largura de banda de rede entre o servidor e o armazenamento do Azure, o tamanho do arquivo, o tamanho total do conjunto de arquivos e a atividade no conjunto de um. Como Sincronização de Arquivos do Azure funciona no nível do arquivo, as características de desempenho de uma solução baseada em Sincronização de Arquivos do Azure são mais bem medidas no número de objetos (arquivos e diretórios) processados por segundo.

Por Sincronização de Arquivos do Azure, o desempenho é crítico em dois estágios:

1. **Provisionamento de uso único inicial**: para otimizar o desempenho no provisionamento inicial, consulte [integração com sincronização de arquivos do Azure](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) para obter os detalhes de implantação ideais.
2. **Sincronização em andamento**: depois que os dados são inicialmente propagados nos compartilhamentos de arquivos do Azure, sincronização de arquivos do Azure mantém vários pontos de extremidade em sincronia.

Para ajudá-lo a planejar sua implantação para cada um dos estágios, abaixo estão os resultados observados durante o teste interno em um sistema com uma configuração

| Configuração do sistema |  |
|-|-|
| CPU | 64 núcleos virtuais com 64 MiB L3 cache |
| Memória | 128 GiB |
| Discos | Discos SAS com RAID 10 com cache com bateria reserva |
| Rede | Rede de 1 Gbps |
| Carga de trabalho | Uso Geral servidor de arquivos|

| Provisionamento de uso único inicial  |  |
|-|-|
| Número de objetos | 25 milhões objetos |
| Tamanho do conjunto de um| ~ 4,7 TiB |
| Tamanho médio do arquivo | ~ 200 KiB (maior arquivo: 100 GiB) |
| Carregar taxa de transferência | 20 objetos por segundo |
| Taxa de transferência de download do namespace * | 400 objetos por segundo |

\* Quando um novo ponto de extremidade do servidor for criado, o agente de Sincronização de Arquivos do Azure não baixará nenhum conteúdo do arquivo. Primeiro, ele sincroniza o namespace completo e, em seguida, dispara a recall em segundo plano para baixar os arquivos, em sua totalidade ou, se a disposição em camadas da nuvem estiver habilitada, para a política de camadas de nuvem definida no ponto de extremidade do servidor.

| Sincronização em andamento  |   |
|-|--|
| Número de objetos sincronizados| 125.000 objetos (cerca de 1% de variação) |
| Tamanho do conjunto de um| 50 GiB |
| Tamanho médio do arquivo | ~ 500 KiB |
| Carregar taxa de transferência | 30 objetos por segundo |
| Taxa de transferência de download completo * | 60 objetos por segundo |

\* Se a disposição em camadas da nuvem estiver habilitada, você provavelmente observará melhor desempenho, já que apenas alguns dos dados do arquivo são baixados. Sincronização de Arquivos do Azure apenas baixa os dados de arquivos armazenados em cache quando eles são alterados em qualquer um dos pontos de extremidade. Para qualquer arquivo em camadas ou recém-criados, o agente não baixa os dados do arquivo e sincroniza apenas o namespace para todos os pontos de extremidade do servidor. O agente também dá suporte a downloads parciais de arquivos em camadas conforme eles são acessados pelo usuário. 

> [!Note]  
> Os números acima não são uma indicação do desempenho que você vai experimentar. O desempenho real dependerá de vários fatores, conforme descrito no início desta seção.

Como um guia geral para sua implantação, você deve ter algumas coisas em mente:

- A taxa de transferência do objeto aproximadamente é dimensionada em proporção ao número de grupos de sincronização no servidor. A divisão de dados em vários grupos de sincronização em um servidor produz uma melhor taxa de transferência, que também é limitada pelo servidor e pela rede.
- A taxa de transferência do objeto é inversamente proporcional à taxa de transferência MiB por segundo. Para arquivos menores, você passará por uma taxa de transferência mais alta em termos do número de objetos processados por segundo, mas com uma taxa de transferência de MiB por segundo mais baixa. Por outro lado, para arquivos maiores, você obterá menos objetos processados por segundo, mas taxa de transferência de MiB por segundo superior. A taxa de transferência de MiB por segundo é limitada pelos destinos de escala de arquivos do Azure.

## <a name="see-also"></a>Ver também

- [Planear uma implementação dos Ficheiros do Azure](storage-files-planning.md)
- [Planear uma implementação do Azure File Sync](storage-sync-files-planning.md)
- [Escalabilidade e metas de desempenho para outros serviços de armazenamento](../common/storage-scalability-targets.md)
