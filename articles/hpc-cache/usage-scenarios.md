---
title: Cenários de cache Azure HPC
description: Descreve como saber se o seu trabalho de computação funciona bem com a Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/29/2021
ms.author: v-erkel
ms.openlocfilehash: 36e0135102fbede5505e96fb1aa255588b2f2ae2
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259976"
---
# <a name="is-your-job-a-good-fit-for-azure-hpc-cache"></a>O seu trabalho é bom para a Azure HPC Cache?

A Azure HPC Cache pode acelerar o acesso a dados para trabalhos de computação de alto desempenho em várias disciplinas. Mas não é perfeito para todos os tipos de fluxos de trabalho. Este artigo dá orientações para decidir se a Cache HPC é uma boa opção para as suas necessidades.

O artigo [de visão geral](hpc-cache-overview.md) também dá um pequeno esboço de quando usar a Cache Azure HPC e alguns exemplos de casos de uso.

Leia também [este artigo](nfs-blob-considerations.md) sobre como fazer uso eficaz do armazenamento de [bolhas montado em NFS](../storage/blobs/network-file-system-protocol-support.md), que está em pré-visualização.

## <a name="nfs-version-30-applications"></a>Aplicações NFS versão 3.0

A Azure HPC Cache suporta apenas clientes NFS 3.0.

## <a name="high-read-to-write-ratio"></a>Relação de leitura-para-escrita elevada

Cargas de trabalho onde os clientes computacional fazem mais leitura do que escrevem são geralmente bons candidatos para uma cache. Por exemplo, se a sua relação leitura-para-escrever for de 80/20 ou 70/30, a Cache Azure HPC pode ajudar ao servir ficheiros frequentemente solicitados da cache em vez de ter que buscá-los de armazenamento remoto vezes sem conta.

Buscar um ficheiro e guardá-lo na cache pela primeira vez tem uma pequena latência adicional sobre um pedido normal do cliente diretamente para o armazenamento, de modo que o aumento de eficiência vem da próxima vez que um cliente solicitar o mesmo ficheiro. Isto é especialmente verdade para ficheiros grandes. Se cada pedido de cliente for único, o impacto da HPC Cache é limitado. Mas quanto maior for o ficheiro, melhor será o desempenho ao longo do tempo após o primeiro acesso.

## <a name="file-based-analytic-workload"></a>Carga de trabalho analítico baseada em ficheiros

A Azure HPC Cache é ideal para um pipeline que utiliza dados baseados em ficheiros e atravessa um grande número de clientes computacional, especialmente se os clientes computacional forem máquinas virtuais Azure. Pode ajudar a corrigir um desempenho lento ou inconsistente que é causado por longos tempos de acesso a ficheiros.

## <a name="remote-data-access"></a>Acesso remoto a dados

O Azure HPC Cache pode ajudar a reduzir a latência se a sua carga de trabalho precisar de aceder a dados remotos que não podem ser movidos para mais perto dos recursos informáticos. Por exemplo, os seus registos podem estar na extremidade de um ambiente WAN, numa região de Azure diferente, ou num centro de dados de clientes. (Isto às vezes é chamado de "rebentamento de ficheiros".)

## <a name="heavy-request-load"></a>Carga de pedido pesado

Se um grande número de clientes solicitar dados da fonte ao mesmo tempo, a Cache Azure HPC pode acelerar o acesso ao ficheiro. Por exemplo, quando usado com um cluster de computação de alto desempenho, a Cache Azure HPC fornece escalabilidade para um elevado número de pedidos simultâneos através da cache.

## <a name="compute-resources-are-located-in-azure"></a>Os recursos computacional estão localizados em Azure

As máquinas virtuais Azure são uma resposta escalável e rentável à carga de trabalho de computação de alto desempenho. O Azure HPC Cache pode ajudar trazendo as informações de que necessitam mais perto deles, especialmente se os dados originais forem armazenados num sistema remoto.

Se um cliente quiser executar o seu pipeline atual "como está" em máquinas virtuais Azure, a Azure HPC Cache pode fornecer uma solução de armazenamento compartilhado (ou cache) baseada em POSIX para escalabilidade.

Ao utilizar a Cache Azure HPC, não é preciso re-arquitectar o oleoduto de trabalho para fazer chamadas nativas para o armazenamento de Azure Blob. Pode aceder aos seus dados no seu sistema original ou utilizar a Cache HPC para movê-lo para um novo recipiente blob.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre como planear e configurar uma cache nos artigos [de Visão Geral](hpc-cache-overview.md) e [Pré-Requisitos](hpc-cache-prerequisites.md)
* Leia considerações para a utilização [de armazenamento de blob ativado por NFS](nfs-blob-considerations.md) (PREVIEW) com cache Azure HPC
