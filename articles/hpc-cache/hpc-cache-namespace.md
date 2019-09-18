---
title: Criar um cache HPC do Azure
description: Como criar uma instância de cache do HPC do Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: c3d14eaefaa1f317cb061273866ffee83747f12b
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71036851"
---
# <a name="configure-aggregated-namespace"></a>Configurar namespace agregado
<!-- change link in GUI -->

O cache HPC do Azure permite que os clientes acessem uma variedade de sistemas de armazenamento por meio de um namespace virtual que oculta os detalhes do sistema de armazenamento de back-end.

Ao adicionar um destino de armazenamento, você define o filePath voltado para o cliente. Computadores cliente montam este FilePath. Você pode alterar o destino de armazenamento associado a esse caminho. Por exemplo, você pode substituir um sistema de armazenamento de hardware pelo armazenamento em nuvem sem a necessidade de reescrever procedimentos voltados para o cliente.

## <a name="aggregated-namespace-example"></a>Exemplo de namespace agregado

Planeje o namespace agregado para que os computadores cliente possam acessar convenientemente as informações de que precisam e os administradores e engenheiros de fluxo de trabalho possam distinguir facilmente os caminhos.

Por exemplo, considere um sistema em que uma instância de cache do Azure HPC está sendo usada para processar dados armazenados no blob do Azure. A análise requer arquivos de modelo que são armazenados em um datacenter local.

Os dados do modelo são armazenados em um datacenter e as informações necessárias para esse trabalho são armazenadas nesses subdiretórios:

    /goldline/templates/acme2017/sku798
    /goldline/templates/acme2017/sku980 

O sistema de armazenamento do datacenter expõe essas exportações: 

    /
    /goldline
    /goldline/templates

Os dados a serem analisados foram copiados para um contêiner de armazenamento de BLOBs do Azure denominado "SourceCollection" usando o [utilitário CLFSLoad](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload).

Para permitir acesso fácil por meio do cache, considere a criação de destinos de armazenamento com estes caminhos de namespace virtual:

| Back-end de NFS FilePath ou contêiner de BLOB | Caminho do namespace virtual |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| SourceCollection                        | /Source               |

Como os caminhos de origem do NFS são subdiretórios da mesma exportação, você precisará definir vários caminhos de namespace do mesmo destino de armazenamento. 

| Nome de host do destino de armazenamento  | Caminho de exportação do NFS      | Caminho do subdiretório | Caminho do namespace    |
|--------------------------|----------------------|-------------------|-------------------|
| *Endereço IP ou nome do host* | /goldline/templates  | acme2017/sku798   | /templates/sku798 |
| *Endereço IP ou nome do host* | /goldline/templates  | acme2017/sku980   | /templates/sku980 |

Um aplicativo cliente pode montar o cache e acessar facilmente o namespace agregado, o/Source, o/templates/sku798 e o/templates/sku980.

## <a name="next-steps"></a>Passos seguintes

Depois de decidir como configurar seu sistema de arquivos virtual, [crie destinos de armazenamento](hpc-cache-add-storage.md) para mapear o armazenamento de back-end para seus caminhos de filecaminhos virtuais voltados para o cliente.
