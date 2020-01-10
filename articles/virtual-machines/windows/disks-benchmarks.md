---
title: Benchmarking de seu aplicativo no Armazenamento em Disco do Azure
description: Saiba mais sobre o processo de benchmark de seu aplicativo no Azure.
author: roygara
ms.author: rogarana
ms.date: 01/11/2019
ms.topic: conceptual
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: 77e542e9bff399e58b433286385864478bcb5076
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75719550"
---
# <a name="benchmarking-a-disk"></a>Benchmarking de um disco

O benchmark é o processo de simular cargas de trabalho diferentes em seu aplicativo e medir o desempenho do aplicativo para cada carga de trabalho. Usando as etapas descritas no [artigo projetando para alto desempenho](premium-storage-performance.md), você reuniu os requisitos de desempenho do aplicativo. Executando ferramentas de benchmark nas VMs que hospedam o aplicativo, você pode determinar os níveis de desempenho que seu aplicativo pode atingir com o armazenamento Premium. Neste artigo, fornecemos exemplos de benchmarking de uma VM DS14 padrão provisionada com discos de armazenamento Premium do Azure.

Usamos ferramentas comuns de benchmark, Iometer e FIO, para Windows e Linux, respectivamente. Essas ferramentas geram vários threads simulando uma produção, como carga de trabalho, e medem o desempenho do sistema. Usando as ferramentas, você também pode configurar parâmetros como tamanho do bloco e profundidade da fila, que normalmente não é possível alterar para um aplicativo. Isso proporciona mais flexibilidade para impulsionar o desempenho máximo em uma VM de alta escala provisionada com discos Premium para diferentes tipos de cargas de trabalho de aplicativo. Para saber mais sobre cada ferramenta de benchmark, visite [Iometer](http://www.iometer.org/) e [fio](http://freecode.com/projects/fio).

Para seguir os exemplos abaixo, crie uma VM DS14 padrão e anexe 11 discos de armazenamento Premium à VM. Dos 11 discos, configure 10 discos com o cache de host como "nenhum" e distribua-os em um volume chamado NoCacheWrites. Configure o cache de host como "ReadOnly" no disco restante e crie um volume chamado CacheReads com esse disco. Usando essa configuração, você pode ver o desempenho máximo de leitura e gravação de uma VM DS14 padrão. Para obter etapas detalhadas sobre como criar uma VM DS14 com o SSDs Premium, vá para [design para alto desempenho](premium-storage-performance.md).

[!INCLUDE [virtual-machines-disks-benchmarking](../../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>Passos seguintes

Vá para nosso artigo sobre como [projetar para alto desempenho](premium-storage-performance.md).

Neste artigo, você cria uma lista de verificação semelhante ao aplicativo existente para o protótipo. Usando ferramentas de benchmark, você pode simular as cargas de trabalho e medir o desempenho no aplicativo prototype. Ao fazer isso, você pode determinar qual oferta de disco pode corresponder ou ultrapassar os requisitos de desempenho do aplicativo. Em seguida, você pode implementar as mesmas diretrizes para seu aplicativo de produção.