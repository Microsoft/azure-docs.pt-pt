---
title: Padrão de referência de seu aplicativo no armazenamento de disco do Azure - discos geridos
description: Saiba mais sobre o processo de benchmark a sua aplicação no Azure.
services: virtual-machines-windows,storage
author: roygara
ms.author: rogarana
ms.date: 01/11/2019
ms.topic: article
ms.service: virtual-machines-windows
ms.tgt_pltfrm: windows
ms.subservice: disks
ms.openlocfilehash: 8db1fb3c9b3ed551cd668cf14105eb8bfb486251
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60679823"
---
# <a name="benchmarking-a-disk"></a>Comparações de benchmark entre um disco

Padrão de referência é o processo de simulação de diferentes cargas de trabalho na sua aplicação e medir o desempenho de aplicações para cada carga de trabalho. Utilizando os passos descritos no [conceber para o artigo de elevado desempenho](premium-storage-performance.md), que reuniu os requisitos de desempenho do aplicativo. Ao executar o benchmark ferramentas nas VMs que aloja a aplicação, é possível determinar os níveis de desempenho que seu aplicativo pode alcançar com o armazenamento Premium. Neste artigo, fornecemos exemplos de comparações de benchmark entre uma VM DS14 padrão aprovisionado com discos de armazenamento Premium do Azure.

Usamos ferramentas comuns de benchmark Iometer e FIO, para Windows e Linux, respetivamente. Essas ferramentas gerar vários threads, simulando uma produção, como a carga de trabalho e medem o desempenho do sistema. Usando as ferramentas também pode configurar parâmetros como bloquear a profundidade tamanho e a fila, que normalmente não é possível alterar para uma aplicação. Isso lhe dá mais flexibilidade para orientar o máximo desempenho numa escala elevada VM aprovisionado com os discos premium para diferentes tipos de cargas de trabalho de aplicação. Para saber mais sobre cada ferramenta de benchmark visite [Iometer](http://www.iometer.org/) e [FIO](http://freecode.com/projects/fio).

Para seguir os exemplos abaixo, criar uma VM DS14 padrão e anexar discos de armazenamento Premium 11 a VM. Os discos de 11, configure 10 discos com o anfitrião de colocação em cache como "None" e do stripe-los num volume chamado NoCacheWrites. Configurar o anfitrião como "Só de leitura" colocação em cache no disco restante e criar um volume chamado CacheReads com este disco. Com esta configuração, é capaz de ver o máximo desempenho de leitura e escrita de uma VM DS14 padrão. Para obter passos detalhados sobre como criar uma VM de DS14 com premium SSDs, aceda a [conceber o elevado desempenho](premium-storage-performance.md).

[!INCLUDE [virtual-machines-disks-benchmarking](../../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>Passos Seguintes

Percorra o nosso design para o artigo de elevado desempenho. Aqui, cria uma lista de verificação semelhante à sua aplicação existente para o protótipo. Utilizar ferramentas de Benchmarking pode simular as cargas de trabalho e medir o desempenho na aplicação de protótipo. Ao fazer isso, pode determinar o disco oferta pode corresponder ou superar as suas necessidades de desempenho de aplicativo. Em seguida, pode implementar as mesmas diretrizes para a sua aplicação de produção.

> [!div class="nextstepaction"]
> Consulte o artigo sobre [conceber o elevado desempenho](premium-storage-performance.md) começar.