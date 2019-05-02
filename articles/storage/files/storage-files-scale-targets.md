---
title: Azure destinos de escalabilidade e desempenho de ficheiros | Documentos da Microsoft
description: Saiba mais sobre os destinos de escalabilidade e desempenho para ficheiros do Azure, incluindo a capacidade, taxa de pedidos e limites de largura de banda de entrada e saída.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 7/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bd60d6453b71387578b880ad580fb1741e6e512b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64697913"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Azure destinos de escalabilidade e desempenho de ficheiros

[Os ficheiros do Azure](storage-files-introduction.md) oferece totalmente geridos partilhas de ficheiros na cloud que estão acessíveis através do protocolo SMB padrão do setor. Este artigo aborda os destinos de escalabilidade e desempenho para ficheiros do Azure e Azure File Sync.

Os destinos de escalabilidade e desempenho aqui listados são os destinos de alta tecnologia, mas podem ser afetados pelas outras variáveis na sua implementação. Por exemplo, a taxa de transferência para um ficheiro pode também ser limitada pelo sua largura de banda de rede disponível, não apenas os servidores que aloja o serviço de ficheiros do Azure. Recomendamos vivamente que teste o seu padrão de utilização para determinar se a escalabilidade e desempenho dos ficheiros do Azure satisfazer os seus requisitos. Estamos também empenhados em aumentar estes limites ao longo do tempo. Não hesite em enviar-nos comentários, tanto nos comentários abaixo ou no [UserVoice de ficheiros do Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files), sobre os limites gostariam de ver-na aumentar.

## <a name="azure-storage-account-scale-targets"></a>Metas de dimensionamento de conta de armazenamento do Azure

O recurso principal para uma partilha de ficheiros do Azure é uma conta de armazenamento do Azure. Uma conta de armazenamento representa um agrupamento de armazenamento do Azure que pode ser utilizado por vários serviços de armazenamento, incluindo ficheiros do Azure, para armazenar dados. Outros serviços que armazenam dados em contas de armazenamento são o armazenamento de Blobs do Azure, o armazenamento de filas do Azure e o armazenamento de tabelas do Azure. Os destinos seguintes aplicam-se todos os serviços de armazenamento armazenar dados numa conta de armazenamento:

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> Utilização da conta de armazenamento para fins gerais de outros serviços de armazenamento afeta as partilhas de ficheiros do Azure na sua conta de armazenamento. Por exemplo, se atingir a capacidade de conta de armazenamento máximo com o armazenamento de Blobs do Azure, não poderá criar novos ficheiros na partilha de ficheiros do Azure, mesmo se a partilha de ficheiros estiver abaixo do tamanho máximo de partilha.

## <a name="azure-files-scale-targets"></a>Alvos de dimensionamento de ficheiros do Azure

### <a name="premium-files-scale-targets"></a>Ficheiros Premium Dimensionar destinos

Existem três categorias de limitações a considerar para os ficheiros de premium: ficheiros, partilhas e contas de armazenamento.

Por exemplo: Uma única partilha pode atingir 100 000 IOPS e um único ficheiro pode ser dimensionado até 5000 IOPS. Assim, por exemplo, se tiver três arquivos no compartilhamento de um, o IOPS máximo que pode obter a partir de que a partilha é 15 000.

### <a name="premium-filestorage-account-limits"></a>Limites de conta do Premium filestorage

Ficheiros Premium utilizam uma conta de armazenamento exclusivo denominada **filestorage (pré-visualização)**, esta conta tem destinos de escala ligeiramente diferente do que a conta de armazenamento utilizado por ficheiros padrão. Para os alvos de dimensionamento de conta de armazenamento, consulte a tabela a [metas de dimensionamento de conta de armazenamento do Azure](#azure-storage-account-scale-targets) secção.

> [!IMPORTANT]
> Limites de conta de armazenamento se aplicam a todas as partilhas. Dimensionar até o máximo para as contas de armazenamento só está alcançável se existir apenas uma partilha por conta de armazenamento.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Alvos de dimensionamento do Azure File Sync

Com o Azure File Sync, tentamos tanto quanto possível para conceber para utilização ilimitada, no entanto, isso nem sempre é possível. A tabela abaixo indica os limites de nosso teste, e os destinos são limites, na verdade, disco rígidos:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Métricas de desempenho de sincronização de ficheiros do Azure

Uma vez que o agente de sincronização de ficheiros do Azure é executado num computador Windows Server que liga-se às partilhas de ficheiros do Azure, o desempenho da sincronização em vigor depende de vários fatores na sua infraestrutura: Windows Server e a configuração do disco subjacente, largura de banda de rede entre o servidor e o armazenamento do Azure, ficheiros tamanho, tamanho do conjunto de dados total e a atividade no conjunto de dados. Uma vez que o Azure File Sync funciona no nível de arquivo, as características de desempenho de uma solução baseada no Azure File Sync melhor é medido no número de objetos (ficheiros e diretórios) processado por segundo.

Para o Azure File Sync, o desempenho é essencial em duas fases:

1. **Aprovisionamento de uma única vez inicial**: Para otimizar o desempenho após o aprovisionamento inicial, consulte [integração com o Azure File Sync](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) para obter os detalhes de implementação ideal.
2. **Sincronização em curso**: Depois dos dados inicialmente são implantados em partilhas de ficheiros do Azure, o Azure File Sync mantém vários pontos de extremidade em sincronia.

Para ajudar a planear a implementação para cada uma das etapas, abaixo estão os resultados observados durante os testes internos num sistema com uma configuração

| Configuração do sistema |  |
|-|-|
| CPU | 64 núcleos virtuais com a cache de MiB L3 64 |
| Memória | 128 GiB |
| Disco | Cache de segurança de discos SAS com o RAID 10 com bateria |
| Rede | Rede de 1 Gbps |
| Carga de trabalho | Servidor de ficheiros para fins gerais|

| Aprovisionamento de uma única vez inicial  |  |
|-|-|
| Número de objetos | objetos de milhões de 25 |
| Tamanho do conjunto de dados| ~4.7 TiB |
| Tamanho do arquivo média | ~ 200 KiB (maior arquivo: 100 GiB) |
| Carregar o débito | 20 objetos por segundo |
| Espaço de nomes Download débito * | 400 objetos por segundo |

* Quando é criado um novo ponto de final de servidor, o agente do Azure File Sync não transferir os conteúdos do ficheiro. -Lo primeiro sincroniza o espaço de nomes completo e, em seguida, os acionadores em segundo plano Lembre-se para transferir os ficheiros, qualquer um em sua totalidade ou, se em camada de cloud está ativada para a política de disposição em camadas na cloud, defina o ponto final de servidor.

| Sincronização em curso  |   |
|-|--|
| Número de objetos sincronizados| 125,000 objetos (alterações a % de ~ 1) |
| Tamanho do conjunto de dados| 50 giB |
| Tamanho do arquivo média | ~ 500 KiB |
| Carregar o débito | objetos de 30 por segundo |
| Débito de transferência completa * | 60 objetos por segundo |

* Se cloud em camadas estiver ativada, o que é provável que observar um melhor desempenho, como apenas alguns do ficheiro de dados são transferidos. O Azure File Sync transfere apenas os dados de ficheiros em cache quando eles são alterados em qualquer um dos pontos finais. Para ficheiros em camadas ou criados recentemente, o agente não transfere os dados de ficheiro e, em vez disso, só sincroniza o espaço de nomes para todos os pontos finais do servidor. O agente também suporta parciais transferências de ficheiros em camadas como estes são acedidos pelo utilizador. 

> [!Note]  
> Os números acima não são uma indicação do desempenho que irá ocorrer. O desempenho real dependerá vários fatores, conforme descrito no início desta secção.

Como um guia Geral para a sua implementação, deve manter algumas coisas em mente:

- Aproximadamente dimensiona o débito de objeto forma proporcional, segundo o número de grupos de sincronização no servidor. Dividir dados em vários grupos de sincronização num servidor produz o melhor débito, também é limitado pela rede e do servidor.
- O débito de objeto é inversamente proporcional ao MiB por segundo de débito. Para ficheiros mais pequenos, terá um débito mais elevado em termos de número de objetos processados por segundo, mas inferior MiB por segundo de débito. Por outro lado, para arquivos grandes, irá obter menos objetos processados por segundo, mas superior MiB por segundo de débito. O MiB por segundo de débito é limitado pelos alvos de dimensionamento de ficheiros do Azure.

## <a name="see-also"></a>Consulte também

- [Planear uma implementação dos Ficheiros do Azure](storage-files-planning.md)
- [Planear uma implementação do Azure File Sync](storage-sync-files-planning.md)
- [Metas de escalabilidade e desempenho para outros serviços de armazenamento](../common/storage-scalability-targets.md)
