---
title: Criar uma Cache Azure HPC
description: Como criar uma instância de Cache Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: aaa939051a1aeafdb0650119772fc7214506aa8d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73582184"
---
# <a name="plan-the-aggregated-namespace"></a>Planear o espaço de nomes agregado

O Azure HPC Cache permite aos clientes aceder em vários sistemas de armazenamento através de um espaço de nome virtual que esconde os detalhes do sistema de armazenamento back-end.

Ao adicionar um alvo de armazenamento, define o caminho de ficheiro virado para o cliente. As máquinas de clientes montam este caminho de ficheiros e podem fazer pedidos de leitura de ficheiros para a cache em vez de montar em diretamente o sistema de armazenamento.

Uma vez que o Azure HPC Cache gere este sistema de ficheiros virtuais, pode alterar o alvo de armazenamento sem alterar o caminho virado para o cliente. Por exemplo, pode substituir um sistema de armazenamento de hardware por armazenamento em nuvem sem precisar de reescrever procedimentos voltados para o cliente.

## <a name="aggregated-namespace-example"></a>Exemplo de espaço de nome agregado

Planeie o seu espaço de nome agregado para que as máquinas de clientes possam alcançar convenientemente a informação de que necessitam, e para que administradores e engenheiros de fluxo de trabalho possam facilmente distinguir os caminhos.

Por exemplo, considere um sistema onde uma instância de Cache Azure HPC está sendo usada para processar dados armazenados em Azure Blob. A análise requer ficheiros de modelo que são armazenados num centro de dados no local.

Os dados do modelo são armazenados num datacenter, e a informação necessária para este trabalho é armazenada nestes subdiretórios:

    /goldline/templates/acme2017/sku798
    /goldline/templates/acme2017/sku980 

O sistema de armazenamento de datacenter expõe estas exportações:

    /
    /goldline
    /goldline/templates

Os dados a analisar foram copiados para um contentor de armazenamento Azure Blob chamado "sourcecollection" utilizando o [utilitário CLFSLoad](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload).

Para permitir um fácil acesso através da cache, considere criar alvos de armazenamento com estes caminhos de espaço de nome virtual:

| Sistema de armazenamento de back-end <br/> (Caminho de ficheiro SNFS ou recipiente blob) | Caminho de espaço de nome virtual |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /modelos/sku798      |
| /goldline/templates/acme2017/sku980     | /modelos/sku980      |
| recolha de fontes                        | /fonte/               |

Um alvo de armazenamento NFS pode ter múltiplos caminhos de espaço de nome virtual, desde que cada um refira um caminho de exportação único.

Como os caminhos-de-origem da NFS são subdirectivos da mesma exportação, terá de definir múltiplos caminhos espaciais a partir do mesmo alvo de armazenamento.

| Nome de anfitrião alvo de armazenamento  | Rota de exportação nFS      | Caminho do subdiretório | Caminho espaço de nome    |
|--------------------------|----------------------|-------------------|-------------------|
| *Endereço IP ou nome de anfitrião* | /goldline/modelos  | acme2017/sku798   | /modelos/sku798 |
| *Endereço IP ou nome de anfitrião* | /goldline/modelos  | acme2017/sku980   | /modelos/sku980 |

Uma aplicação do cliente pode montar a cache e ``/source`` ``/templates/sku798``aceder ``/templates/sku980``facilmente aos caminhos de ficheiros de espaço de nome agregados, e .

## <a name="next-steps"></a>Passos seguintes

Depois de ter decidido como configurar o seu sistema de ficheiros virtuais, [crie alvos](hpc-cache-add-storage.md) de armazenamento para mapear o seu armazenamento back-end para os caminhos de ficheiros virtuais virados para o cliente.
