---
title: Descrição geral do filtro de borda do Microsoft Azure FXT
description: Descreve o cache de armazenamento híbrido de ficheiros do Azure FXT Edge, um arquivo de Active Directory e a solução de acelerador de acesso de arquivo para computação de alto desempenho
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: overview
ms.date: 07/01/2019
ms.author: v-erkell
ms.openlocfilehash: 58d4a2a52757b6ace1059fcccf379df3de5fd75c
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542907"
---
# <a name="what-is-azure-fxt-edge-filer-hybrid-storage-cache"></a>O que é o cache de armazenamento do Azure FXT Edge filtro híbrida?

Filtro de borda FXT do Azure é um armazenamento de híbrida colocação em cache da aplicação que fornece acesso de rápida de ficheiros e de arquivo Active Directory para tarefas de computação de alto desempenho (HPC).

Ele funciona com várias origens de dados, quer armazenados num Datacenter local, remotamente, ou na cloud. O filtro de borda do Azure FXT pode fornecer um espaço de nomes unificado para os dados em sistemas de armazenamento diversos.

Três ou mais dispositivos de hardware de filtro de borda FXT funcionam em conjunto como um sistema de ficheiros em cluster para fornecer a cache. Para obter detalhes sobre a aquisição de hardware necessários, contacte o seu representante da Microsoft. 

Para saber mais, continue a ler a folha de dados e informações de produto [ficheiros do Azure FXT Edge](https://azure.microsoft.com/services/fxt-edge-filer/).

## <a name="use-cases"></a>Casos de utilização

O filtro de borda do Azure FXT aprimora a produtividade melhor para fluxos de trabalho como esta:

* Fluxo de trabalho de acesso de leitura intensiva ficheiro 
* Protocolos NFSv3 ou SMB2
* Farms de 1000 para 100 000 núcleos de CPU de computação

### <a name="nas-optimization-and-scaling"></a>Otimização de NAS e dimensionamento

Pode utilizar a cache de ficheiros do Azure FXT Edge para suavizar o acesso a sistemas da Dell EMC Isilon NAS e da NetApp existentes. Também pode adicionar BLOBs do Azure ou outro armazenamento na cloud para fornecer escalabilidade sem ter de Refazer processos de acesso de dados no lado do cliente. 

### <a name="wan-caching"></a>A colocação em cache WAN

Filtro de borda FXT do Azure pode ser utilizado para suportar o acesso de rápida de ficheiros de usuários avançados quando os dados que precisam são armazenados noutro local. Fornece acesso, mantendo a cópia de segurança e outros sistemas de gestão de dados num Datacenter centralizado. 

### <a name="active-archive-in-azure-blob"></a>Arquivo Active Directory no Blob do Azure

Expanda o seu centro de dados para armazenamento na cloud com o filtro de borda de FXT do Azure como o ponto de acesso. 

## <a name="features"></a>Funcionalidades 

Dois modelos de hardware estão disponíveis. 

| Modelo | DRAM | NVMe SSD | Portas de rede | 
|-------|------|----------|---------------|
| FXT 6600 | 1536 GB | 25.6 TB | 6 x 25Gb/10Gb + 2 x 1Gb |
| FXT 6400 | 768 GB | 12.8 TB | 6 x 25Gb/10Gb + 2 x 1Gb |


## <a name="next-steps"></a>Passos Seguintes

* Continue a aprender sobre os ficheiros do Azure FXT Edge lendo o [especificações](fxt-specs.md) ou [tutorial de instalação](fxt-install.md).
* Saiba como comprar o Azure FXT Edge ficheiros nos [página de produto de ficheiros do Azure FXT Edge](https://azure.microsoft.com/services/fxt-edge-filer/).