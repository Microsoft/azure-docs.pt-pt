---
title: Visão geral do Microsoft Azure FXT Edge Filer
description: Descreve a cache de armazenamento híbrido Azure FXT Edge Filer, uma solução de acelerador de arquivo e acesso a ficheiros ativa para computação de alto desempenho
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: overview
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 72308bf64246a8041bd40fa557280014a13c95eb
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92218787"
---
# <a name="what-is-azure-fxt-edge-filer-hybrid-storage-cache"></a>O que é o cache de armazenamento híbrido Azure FXT Edge Filer?

O Azure FXT Edge Filer é um aparelho de armazenamento híbrido que fornece acesso rápido a ficheiros e arquivo ativo para tarefas de computação de alto desempenho (HPC).

Funciona com várias fontes de dados, seja armazenada num centro de dados local, remotamente, ou na nuvem. O Azure FXT Edge Filer pode fornecer um espaço de nome unificado para dados em diversos sistemas de armazenamento.

Três ou mais dispositivos de hardware FXT Edge Filer funcionam em conjunto como um sistema de ficheiros agrupado para fornecer a cache. Para obter mais informações sobre a compra do hardware necessário, contacte o seu representante da Microsoft.

Para saber mais, leia a informação do produto e a ficha de dados no [Azure FXT Edge Filer](https://azure.microsoft.com/services/fxt-edge-filer/).

## <a name="use-cases"></a>Casos de utilização

O Azure FXT Edge Filer melhora a produtividade para fluxos de trabalho como estes:

* Fluxo de trabalho de acesso a ficheiros pesados
* Protocolos NFSv3 ou SMB2
* Explorações de computação de 1000 a 100.000 núcleos de CPU

### <a name="nas-optimization-and-scaling"></a>Otimização e escalagem do NAS

Pode utilizar a cache do Ficheiro Azure FXT Edge para facilitar o acesso aos sistemas NetApp e Dell EMC Isilon NAS. Também pode adicionar Azure Blob ou outro armazenamento em nuvem para fornecer escalabilidade sem ter que refazer processos de acesso a dados do lado do cliente.

### <a name="wan-caching"></a>Caching WAN

O Azure FXT Edge Filer pode ser utilizado para suportar o acesso rápido de ficheiros dos utilizadores de energia quando os dados de que necessitam são armazenados noutro local. Fornecer acesso mantendo a cópia de segurança e outros sistemas de gestão de dados num centro de dados centralizado.

### <a name="active-archive-in-azure-blob"></a>Arquivo ativo em Azure Blob

Expanda o seu centro de dados para o armazenamento em nuvem com o Azure FXT Edge Filer como ponto de acesso.

## <a name="features"></a>Funcionalidades

Estão disponíveis dois modelos de hardware.

| Modelo | DRAM | NVMe SSD | Portas de rede |
|-------|------|----------|---------------|
| FXT 6600 | 1536 GB | 25.6 TB | 6 x 25Gb/10Gb + 2 x 1Gb |
| FXT 6400 | 768 GB | 12.8 TB | 6 x 25Gb/10Gb + 2 x 1Gb |

## <a name="next-steps"></a>Passos seguintes

* Continue a aprender sobre o Azure FXT Edge Filer lendo as [especificações](fxt-specs.md) ou o [tutorial de instalação](fxt-install.md).
* Saiba como comprar o Azure FXT Edge Filer na página de [produto Azure FXT Edge Filer](https://azure.microsoft.com/services/fxt-edge-filer/).
