---
title: Visão geral do Microsoft Azure FXT Edge Filer
description: Descreve o cache de armazenamento híbrido do Azure FXT Edge Filer, uma solução de arquivo ativo e acelerador de acesso a arquivos para computação de alto desempenho
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: overview
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: acf35015ff7851f7ea2a2527852be2573512e35d
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254835"
---
# <a name="what-is-azure-fxt-edge-filer-hybrid-storage-cache"></a>O que é o cache de armazenamento híbrido do Filer do Azure FXT Edge?

O Azure FXT Edge Filer é um dispositivo de cache de armazenamento híbrido que fornece acesso rápido a arquivos e arquivos ativos para tarefas de HPC (computação de alto desempenho).

Ele funciona com várias fontes de dados, sejam armazenadas em um local data center, remotamente ou na nuvem. O arquivo de borda do Azure FXT Edge pode fornecer um namespace unificado para dados em diversos sistemas de armazenamento.

Três ou mais dispositivos de hardware do FXT Edge Filer funcionam juntos como um sistema de arquivos clusterizado para fornecer o cache. Para obter detalhes sobre como adquirir o hardware necessário, entre em contato com seu representante da Microsoft. 

Para saber mais, leia as informações e o folha de dados do produto no [arquivo de borda do Azure FXT Edge](https://azure.microsoft.com/services/fxt-edge-filer/).

## <a name="use-cases"></a>Casos de utilização

O arquivo de borda do Azure FXT Edge aprimora a produtividade melhor para fluxos de trabalho como estes:

* Fluxo de trabalho de acesso a arquivo intenso de leitura 
* Protocolos NFSv3 ou SMB2
* Farms de computação de 1000 a 100.000 núcleos de CPU

### <a name="nas-optimization-and-scaling"></a>Otimização e dimensionamento do NAS

Você pode usar o cache do Filer do Azure FXT Edge para suavizar o acesso aos sistemas existentes do NetApp e do Dell EMC Isilon NAS. Você também pode adicionar blob do Azure ou outro armazenamento em nuvem para fornecer escalabilidade sem precisar retrabalhar com processos de acesso a dados no lado do cliente. 

### <a name="wan-caching"></a>Caching de WAN

O arquivo de borda do Azure FXT Edge pode ser usado para dar suporte ao acesso rápido a arquivos de usuários avançados quando os dados necessários são armazenados em outro lugar. Forneça acesso enquanto mantém o backup e outros sistemas de gerenciamento de dados em um data center centralizado. 

### <a name="active-archive-in-azure-blob"></a>Arquivo ativo no blob do Azure

Expanda sua data center para o armazenamento em nuvem com o Azure FXT Edge Filer como o ponto de acesso. 

## <a name="features"></a>Funcionalidades 

Dois modelos de hardware estão disponíveis. 

| Modelo | DRAM | SSD NVMe | Portas de rede | 
|-------|------|----------|---------------|
| FXT 6600 | 1536 GB | 25,6 TB | 6 x 25 GB/10 GB + 2 x 1 GB |
| FXT 6400 | 768 GB | 12,8 TB | 6 x 25 GB/10 GB + 2 x 1 GB |


## <a name="next-steps"></a>Passos seguintes

* Continue aprendendo sobre o arquivo de borda do Azure FXT Edge lendo o tutorial de [especificações](fxt-specs.md) ou [instalação](fxt-install.md).
* Saiba como comprar o Azure FXT Edge Filer na [página do produto Filer do Azure FXT Edge](https://azure.microsoft.com/services/fxt-edge-filer/).