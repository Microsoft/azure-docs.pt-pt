---
title: Azure destinos de escalabilidade e desempenho de ficheiros
description: Saiba mais sobre os destinos de escalabilidade e desempenho para ficheiros do Azure, incluindo a capacidade, taxa de pedidos e limites de largura de banda de entrada e saída.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2e05f0cb46e1e54ced5911c0a78dd026dbb7f4fa
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905597"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Azure destinos de escalabilidade e desempenho de ficheiros

[Os ficheiros do Azure](storage-files-introduction.md) oferece totalmente geridos partilhas de ficheiros na cloud que estão acessíveis através do protocolo SMB padrão do setor. Este artigo aborda os destinos de escalabilidade e desempenho para ficheiros do Azure e Azure File Sync.

Os destinos de escalabilidade e desempenho aqui listados são os destinos de alta tecnologia, mas podem ser afetados pelas outras variáveis na sua implementação. Por exemplo, a taxa de transferência para um ficheiro pode também ser limitada pelo sua largura de banda de rede disponível, não apenas os servidores que aloja o serviço de ficheiros do Azure. Recomendamos vivamente que teste o seu padrão de utilização para determinar se a escalabilidade e desempenho dos ficheiros do Azure satisfazer os seus requisitos. Estamos também empenhados em aumentar estes limites ao longo do tempo. Não hesite em enviar-nos comentários, tanto nos comentários abaixo ou no [UserVoice de ficheiros do Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files), sobre os limites gostariam de ver-na aumentar.

## <a name="azure-storage-account-scale-targets"></a>Metas de dimensionamento de conta de armazenamento do Azure

O recurso principal para uma partilha de ficheiros do Azure é uma conta de armazenamento do Azure. Uma conta de armazenamento representa um agrupamento de armazenamento do Azure que pode ser utilizado por vários serviços de armazenamento, incluindo ficheiros do Azure, para armazenar dados. Outros serviços que armazenam dados em contas de armazenamento são o armazenamento de Blobs do Azure, o armazenamento de filas do Azure e o armazenamento de tabelas do Azure. Os destinos seguintes aplicam-se todos os serviços de armazenamento armazenar dados numa conta de armazenamento:

[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> A utilização da conta de armazenamento de uso geral de outros serviços de armazenamento afeta os compartilhamentos de arquivos do Azure em sua conta de armazenamento. Por exemplo, se atingir a capacidade de conta de armazenamento máximo com o armazenamento de Blobs do Azure, não poderá criar novos ficheiros na partilha de ficheiros do Azure, mesmo se a partilha de ficheiros estiver abaixo do tamanho máximo de partilha.

## <a name="azure-files-scale-targets"></a>Alvos de dimensionamento de ficheiros do Azure

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
> As ações de ficheiros padrão superiores a 5 TiB têm certas limitações.
> Para obter uma lista de limitações, informações regionais e instruções para habilitar esses tamanhos maiores de compartilhamento de arquivos, consulte a seção [integrado a compartilhamentos de arquivos maiores](storage-files-planning.md#onboard-to-larger-file-shares-standard-tier) do guia de planejamento.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Alvos de dimensionamento do Azure File Sync

A Sincronização de Arquivos do Azure foi projetada com o objetivo de uso ilimitado, mas o uso ilimitado nem sempre é possível. A tabela a seguir indica os limites do teste da Microsoft e também indica quais destinos são os limites rígidos:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Métricas de desempenho de sincronização de ficheiros do Azure

Uma vez que o agente de sincronização de ficheiros do Azure é executado num computador Windows Server que liga-se às partilhas de ficheiros do Azure, o desempenho da sincronização em vigor depende de vários fatores na sua infraestrutura: Windows Server e a configuração do disco subjacente, largura de banda de rede entre o servidor e o armazenamento do Azure, o tamanho de ficheiro, tamanho do conjunto de dados total e a atividade no conjunto de dados. Uma vez que o Azure File Sync funciona no nível de arquivo, as características de desempenho de uma solução baseada no Azure File Sync melhor é medido no número de objetos (ficheiros e diretórios) processado por segundo.

Para o Azure File Sync, o desempenho é essencial em duas fases:

1. **Aprovisionamento de uma única vez inicial**: para otimizar o desempenho após o aprovisionamento inicial, consulte [integração com o Azure File Sync](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) para obter os detalhes de implementação ideal.
2. **Sincronização em curso**: depois dos dados inicialmente são implantados em partilhas de ficheiros do Azure, Azure File Sync mantém vários pontos de extremidade em sincronia.

Para ajudar a planear a implementação para cada uma das etapas, abaixo estão os resultados observados durante os testes internos num sistema com uma configuração

| Configuração do sistema |  |
|-|-|
| CPU | 64 núcleos virtuais com a cache de MiB L3 64 |
| Memória | 128 GiB |
| Discos | Cache de segurança de discos SAS com o RAID 10 com bateria |
| Rede | Rede de 1 Gbps |
| Carga de trabalho | Servidor de ficheiros para fins gerais|

| Aprovisionamento de uma única vez inicial  |  |
|-|-|
| Número de objetos | 25 milhões objetos |
| Tamanho do conjunto de dados| ~ 4,7 TiB |
| Tamanho do arquivo média | ~ 200 KiB (maior arquivo: 100 GiB) |
| Carregar o débito | 20 objetos por segundo |
| Espaço de nomes Download débito * | 400 objetos por segundo |

\* Quando é criado um novo ponto de final de servidor, o agente do Azure File Sync não transferir os conteúdos do ficheiro. -Lo primeiro sincroniza o espaço de nomes completo e, em seguida, os acionadores em segundo plano Lembre-se para transferir os ficheiros, qualquer um em sua totalidade ou, se em camada de cloud está ativada para a política de disposição em camadas na cloud, defina o ponto final de servidor.

| Sincronização em curso  |   |
|-|--|
| Número de objetos sincronizados| 125,000 objetos (alterações a % de ~ 1) |
| Tamanho do conjunto de dados| 50 giB |
| Tamanho do arquivo média | ~ 500 KiB |
| Carregar o débito | 20 objetos por segundo |
| Débito de transferência completa * | 60 objetos por segundo |

\* Se cloud em camadas estiver ativada, o que é provável que observar um melhor desempenho, como apenas alguns do ficheiro de dados são transferidos. O Azure File Sync transfere apenas os dados de ficheiros em cache quando eles são alterados em qualquer um dos pontos finais. Para ficheiros em camadas ou criados recentemente, o agente não transfere os dados de ficheiro e, em vez disso, só sincroniza o espaço de nomes para todos os pontos finais do servidor. O agente também suporta parciais transferências de ficheiros em camadas como estes são acedidos pelo utilizador. 

> [!Note]  
> Os números acima não são uma indicação do desempenho que irá ocorrer. O desempenho real dependerá vários fatores, conforme descrito no início desta secção.

Como um guia Geral para a sua implementação, deve manter algumas coisas em mente:

- Aproximadamente dimensiona o débito de objeto forma proporcional, segundo o número de grupos de sincronização no servidor. Dividir dados em vários grupos de sincronização num servidor produz o melhor débito, também é limitado pela rede e do servidor.
- O débito de objeto é inversamente proporcional ao MiB por segundo de débito. Para ficheiros mais pequenos, terá um débito mais elevado em termos de número de objetos processados por segundo, mas inferior MiB por segundo de débito. Por outro lado, para arquivos grandes, irá obter menos objetos processados por segundo, mas superior MiB por segundo de débito. O MiB por segundo de débito é limitado pelos alvos de dimensionamento de ficheiros do Azure.

## <a name="see-also"></a>Ver também

- [Planear uma implementação dos Ficheiros do Azure](storage-files-planning.md)
- [Planear uma implementação do Azure File Sync](storage-sync-files-planning.md)
