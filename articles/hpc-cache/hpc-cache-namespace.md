---
title: Compreenda o espaço agregado de nomes Azure HPC Cache
description: Como planear o espaço de nome virtual para o seu Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 1c28f549cf93d77f6aef6bcde6a2225345a79cc9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91612953"
---
# <a name="plan-the-aggregated-namespace"></a>Planear o espaço de nomes agregado

O Azure HPC Cache permite que os clientes acedam a uma variedade de sistemas de armazenamento através de um espaço de nome virtual que esconde os detalhes do sistema de armazenamento back-end.

Depois de adicionar um alvo de armazenamento, você configura um ou mais caminhos de espaço de nome virado para o cliente para o alvo de armazenamento. As máquinas de clientes montam este caminho de ficheiro e podem fazer pedidos de leitura de ficheiros para a cache em vez de montarem o sistema de armazenamento diretamente.

Como o Azure HPC Cache gere este sistema de ficheiros virtuais, pode alterar o alvo de armazenamento sem alterar o caminho virado para o cliente. Por exemplo, pode substituir um sistema de armazenamento de hardware por armazenamento em nuvem sem precisar de reescrever os procedimentos do lado do cliente.

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

Um alvo de armazenamento NFS pode ter vários caminhos virtuais do espaço de nome, desde que cada um refira um caminho de exportação único. (Leia [os caminhos do espaço de nome NFS](add-namespace-paths.md#nfs-namespace-paths) para aprender o número máximo recomendado de caminhos de espaço de nome por alvo de armazenamento NFS.)

Como os caminhos de origem NFS são subdiretas da mesma exportação, você precisará definir vários caminhos de espaço de nome a partir do mesmo alvo de armazenamento.

| Nome anfitrião do alvo de armazenamento  | Caminho de exportação NFS     | Caminho subdirecional | Caminho do espaço de nome    |
|--------------------------|---------------------|-------------------|-------------------|
| *Endereço IP ou nome de anfitrião* | /goldline/modelos | acme2017/sku798   | /modelos/sku798 |
| *Endereço IP ou nome de anfitrião* | /goldline/modelos | acme2017/sku980   | /modelos/sku980 |

Uma aplicação ao cliente pode montar a cache e aceder facilmente aos caminhos agregados de ficheiros do espaço de nome ``/source`` ``/templates/sku798`` , e ``/templates/sku980`` .

Uma abordagem alternativa pode ser criar um caminho virtual como `/templates` esse ligações ao diretório dos `acme2017` pais, e, em seguida, fazer com que os clientes naveguem para o indivíduo `sku798` e `sku980` diretórios depois de montar a cache. No entanto, não é possível criar um caminho de espaço de nome que seja um subdiretório de outro caminho de espaço de nome. Por isso, se criar um caminho para o `acme2017` diretório, também não pode criar nenhum caminho de espaço para aceder diretamente às suas subdiretas.

A página de definições **do Espaço nome Azure** HPC Cache mostra o sistema de ficheiros virado para o cliente e permite-lhe adicionar ou editar caminhos. Leia [Configurar o espaço de nome agregado](add-namespace-paths.md) para mais detalhes.

## <a name="next-steps"></a>Passos seguintes

Depois de ter decidido como configurar o seu sistema de ficheiros virtuais, tome estas medidas para o criar:

* [Crie metas](hpc-cache-add-storage.md) de armazenamento para adicionar os seus sistemas de armazenamento back-end à sua Cache Azure HPC
* [Adicione caminhos de espaço](add-namespace-paths.md) de nome para criar o espaço de nome agregado que as máquinas do cliente usam para aceder a ficheiros
