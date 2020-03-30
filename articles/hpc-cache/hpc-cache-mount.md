---
title: Monte uma cache Azure HPC
description: Como ligar clientes a um serviço Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: d906ed9a1a55e936c6374806a9037085c47e3b01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73582220"
---
# <a name="mount-the-azure-hpc-cache"></a>Monte a Cache Azure HPC

Após a criação da cache, os clientes nFS podem acessá-lo com um simples comando de montagem.

O comando do suporte é composto por dois elementos:

* Um dos endereços de montagem do cache (listado na página de visão geral da cache)
* O caminho virtual do espaço de nome que definiu quando criou o alvo de armazenamento

![screenshot da página de visão geral da instância de Cache Azure HPC, com uma caixa de destaque em torno da lista de endereços de montagem na parte inferior direita](media/hpc-cache-mount-addresses.png)

> [!NOTE] 
> Os endereços de montagem de cache correspondem às interfaces de rede dentro da sub-rede da cache. Num grupo de recursos, estes NICs `-cluster-nic-` estão listados com nomes que terminam e um número. Não altere ou elimine estas interfaces, ou a cache ficará indisponível.

Os caminhos de espaço de nome virtual são mostrados na página de alvos de **Armazenamento.** Clique num nome de alvo de armazenamento individual para ver os seus detalhes, incluindo os caminhos agregados do espaço de nome associados.

![screenshot do painel alvo de armazenamento da cache, com uma caixa de destaque em torno de uma entrada na coluna Caminho da tabela](media/hpc-cache-view-namespace-paths.png)

## <a name="mount-command-syntax"></a>Sintaxe de comando do monte

Utilize um comando de montagem como o seguinte:

> sudo montagem *cache_mount_address*:/*namespace_path* *local_path* {*opções*}

Exemplo:

```
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -orw,tcp,mountproto=tcp,vers3,hard
root@test-client:/tmp# 
```

Após o sucesso deste comando, o conteúdo da ``hpccache`` exportação de armazenamento deve ser visível no diretório sobre o cliente.

> [!NOTE] 
> Os seus clientes devem poder aceder à rede virtual e à sub-rede que alberga a sua cache. Por exemplo, criar VMs de clientes dentro da mesma rede virtual, ou usar um ponto final, gateway ou outra solução na rede virtual para acesso a partir do exterior. Lembre-se que nada mais pode ser hospedado dentro da subnet do cache.

### <a name="mount-command-options"></a>Opções de comando de montagem

Para uma montagem robusta do cliente, passe estas configurações e argumentos no seu comando de montagem: 

``mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Definições recomendadas de comando de montagem | |
--- | --- 
``hard`` | Os suportes macios para a Cache Azure HPC estão associados a falhas de aplicação e possível perda de dados. 
``proto=netid`` | Esta opção suporta o tratamento adequado dos erros de rede NFS.
``mountproto=netid`` | Esta opção suporta o tratamento adequado dos erros de rede para as operações de montagem.
``retry=n`` | Preparar ``retry=30`` para evitar falhas transitórias de montagem. (Recomenda-se um valor diferente em suportes de primeiro plano.)

## <a name="next-steps"></a>Passos seguintes

* Para mover dados para os alvos de armazenamento da cache, leia [O novo armazenamento da Blob Azure](hpc-cache-ingest.md).
