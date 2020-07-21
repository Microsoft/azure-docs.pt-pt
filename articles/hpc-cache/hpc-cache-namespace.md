---
title: Utilize o espaço de nome agregado Azure HPC Cache
description: Como planear o espaço de nome virtual para o seu Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: c16d2f9e9c94603361d9a096f33d559105f2d28d
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86497034"
---
# <a name="plan-the-aggregated-namespace"></a>Planear o espaço de nomes agregado

O Azure HPC Cache permite que os clientes acedam a uma variedade de sistemas de armazenamento através de um espaço de nome virtual que esconde os detalhes do sistema de armazenamento back-end.

Quando adiciona um alvo de armazenamento, define o caminho do ficheiro virado para o cliente. As máquinas de clientes montam este caminho de ficheiro e podem fazer pedidos de leitura de ficheiros para a cache em vez de montarem o sistema de armazenamento diretamente.

Como o Azure HPC Cache gere este sistema de ficheiros virtuais, pode alterar o alvo de armazenamento sem alterar o caminho virado para o cliente. Por exemplo, pode substituir um sistema de armazenamento de hardware por armazenamento em nuvem sem precisar de reescrever procedimentos voltados para o cliente.

## <a name="aggregated-namespace-example"></a>Exemplo agregado do espaço de nome

Planeie o seu espaço de nome agregado para que as máquinas clientes possam alcançar convenientemente a informação de que necessitam, e para que os administradores e engenheiros de fluxo de trabalho possam facilmente distinguir os caminhos.

Por exemplo, considere um sistema onde um exemplo de Cache Azure HPC está a ser usado para processar dados armazenados em Azure Blob. A análise requer ficheiros de modelo que são armazenados num datacenter no local.

Os dados do modelo são armazenados num datacenter, e as informações necessárias para este trabalho são armazenadas nestas subdiretórios:

* */goldline/templates/acme2017/sku798*
* */goldline/templates/acme2017/sku980*

O sistema de armazenamento de datacenter expõe estas exportações:

* */*
* */linha dourada*
* */goldline/modelos*

Os dados a analisar foram copiados para um recipiente de armazenamento Azure Blob denominado "recolha de fontes" utilizando o [utilitário CLFSLoad](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload).

Para permitir o fácil acesso através da cache, considere criar alvos de armazenamento com estes caminhos de espaço de nome virtual:

| Sistema de armazenamento back-end <br/> (Caminho de arquivo NFS ou recipiente Blob) | Caminho virtual do espaço de nome |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /modelos/sku798      |
| /goldline/templates/acme2017/sku980     | /modelos/sku980      |
| recolher fonte                        | /fonte/               |

Um alvo de armazenamento NFS pode ter vários caminhos virtuais do espaço de nome, desde que cada um refira um caminho de exportação único.

Como os caminhos de origem NFS são subdiretas da mesma exportação, você precisará definir vários caminhos de espaço de nome a partir do mesmo alvo de armazenamento.

| Nome anfitrião do alvo de armazenamento  | Caminho de exportação NFS     | Caminho subdirecional | Caminho do espaço de nome    |
|--------------------------|---------------------|-------------------|-------------------|
| *Endereço IP ou nome de anfitrião* | /goldline/modelos | acme2017/sku798   | /modelos/sku798 |
| *Endereço IP ou nome de anfitrião* | /goldline/modelos | acme2017/sku980   | /modelos/sku980 |

Uma aplicação ao cliente pode montar a cache e aceder facilmente aos caminhos agregados de ficheiros do espaço de nome ``/source`` ``/templates/sku798`` , e ``/templates/sku980`` .

## <a name="next-steps"></a>Próximos passos

Depois de ter decidido como configurar o seu sistema de ficheiros virtuais, [crie alvos](hpc-cache-add-storage.md) de armazenamento para mapear o seu armazenamento traseiro para os caminhos de ficheiros virtuais virados para o cliente.
