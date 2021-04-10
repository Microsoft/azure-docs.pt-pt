---
title: Considerações de desempenho NFS 3.0 no armazenamento Azure Blob (pré-visualização) | Microsoft Docs
description: Otimize o desempenho dos seus pedidos de armazenamento do Sistema de Ficheiros de Rede (NFS) 3.0 utilizando as recomendações deste artigo.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 28fb5ffc0ce9c554294eaca9e460694856cb95a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589876"
---
# <a name="network-file-system-nfs-30-performance-considerations-in-azure-blob-storage-preview"></a>Análises de desempenho do Sistema de Ficheiros de Rede (NFS) 3.0 no armazenamento do Blob Azure (pré-visualização)

O armazenamento blob suporta agora o protocolo Sistema de Ficheiros de Rede (NFS) 3.0. Este artigo contém recomendações que o ajudam a otimizar o desempenho dos seus pedidos de armazenamento. Para saber mais sobre o suporte NFS 3.0 no Armazenamento Azure Blob, consulte [o suporte ao sistema de ficheiros de rede (NFS) 3.0 no armazenamento do Blob Azure Blob (pré-visualização)](network-file-system-protocol-support.md).

> [!NOTE]
> O suporte do protocolo NFS 3.0 no armazenamento Azure Blob está em pré-visualização pública. Suporta contas de armazenamento GPV2 com desempenho de nível padrão nas seguintes regiões: Austrália Leste, Coreia Central e South Central EUA. A pré-visualização também suporta blob de bloco com nível de desempenho premium em todas as regiões públicas.

## <a name="add-clients-to-increase-throughput"></a>Adicionar clientes para aumentar a produção 

O Azure Blob Storage escala linearmente até atingir o limite máximo de saída e entrada da conta de armazenamento. Assim, as suas aplicações podem obter uma maior produção utilizando mais clientes.  Para visualizar os limites de saída e entrada de conta de armazenamento, consulte os [objetivos de Escalabilidade e desempenho para contas de armazenamento padrão.](../common/scalability-targets-standard-account.md)

O gráfico seguinte mostra como a largura de banda aumenta à medida que adiciona mais clientes. Neste gráfico, um cliente é uma Máquina Virtual (VM) e a conta utiliza o nível de desempenho padrão. 

> [!div class="mx-imgBorder"]
> ![Desempenho padrão](./media/network-file-system-protocol-support-performance/standard-performance-tier.png)

O gráfico seguinte mostra este mesmo efeito quando aplicado a uma conta que utiliza o nível de desempenho premium.

> [!div class="mx-imgBorder"]
> ![Desempenho premium](./media/network-file-system-protocol-support-performance/premium-performance-tier.png)

## <a name="use-premium-performance-tier-for-small-scale-applications"></a>Utilize o nível de desempenho premium para aplicações de pequena escala

Nem todas as aplicações podem aumentar adicionando mais clientes. Para essas aplicações, a conta de [armazenamento de blocos Azure premium blob](storage-blob-create-account-block-blob.md) oferece consistente baixa latência e altas taxas de transação. A conta de armazenamento de blob de bloco premium pode atingir a largura de banda máxima com menos fios e clientes. Por exemplo, com um único cliente, uma conta de armazenamento de blocos premium pode atingir **2,3x** largura de banda em comparação com a mesma configuração usada com uma conta de armazenamento v2 de desempenho geral padrão. 

Cada barra no gráfico seguinte mostra a diferença na largura de banda alcançada entre contas de armazenamento de desempenho premium e padrão. À medida que o número de clientes aumenta, essa diferença diminui.  

> [!div class="mx-imgBorder"]
> ![Desempenho relativo](./media/network-file-system-protocol-support-performance/relative-performance.png)

## <a name="avoid-frequent-overwrites-on-data"></a>Evite sobreposições frequentes em dados

Demora mais tempo a concluir uma operação de substituição do que uma nova operação de escrita. Isto porque uma operação de substituição de NFS, especialmente uma edição parcial de ficheiros no local, é uma combinação de várias operações de bolhas subjacentes: uma leitura, uma modificação e uma operação de escrita. Portanto, uma aplicação que requer edições frequentes no local não é adequada para contas de armazenamento de blob ativadas por NFS. 

## <a name="other-best-practice-recommendations"></a>Outras recomendações de boas práticas 

- Utilize VMs com largura de banda de rede suficiente.

- Utilize vários pontos de montagem quando as suas cargas de trabalho o permitirem.

- Use o máximo de fios possível.

- Use grandes tamanhos de blocos.

- Faça pedidos de armazenamento de um cliente que se encontra na mesma região que a conta de armazenamento. Isto pode melhorar a latência da rede.

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre o suporte NFS 3.0 no Armazenamento Azure Blob, consulte [o suporte ao sistema de ficheiros de rede (NFS) 3.0 no armazenamento do Blob Azure Blob (pré-visualização)](network-file-system-protocol-support.md).

- Para começar, consulte [o armazenamento do Mount Blob utilizando o protocolo Network File System (NFS) 3.0 (pré-visualização)](network-file-system-protocol-support-how-to.md).
