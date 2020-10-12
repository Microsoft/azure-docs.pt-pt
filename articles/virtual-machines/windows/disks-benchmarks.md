---
title: Comparar a sua aplicação no armazenamento de disco Azure
description: Saiba mais sobre o processo de benchmarking da sua aplicação no Azure.
author: roygara
ms.author: rogarana
ms.date: 01/11/2019
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: d8bf90f4ecea992b3212200905cd9204e4079b1b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88683968"
---
# <a name="benchmarking-a-disk"></a>Benchmarking de um disco

O benchmarking é o processo de simulação de diferentes cargas de trabalho na sua aplicação e de medição do desempenho da aplicação para cada carga de trabalho. Utilizando os passos descritos na [conceção do artigo de alto desempenho,](../premium-storage-performance.md)reuniu os requisitos de desempenho da aplicação. Ao executar ferramentas de benchmarking nos VMs que hospedam a aplicação, pode determinar os níveis de desempenho que a sua aplicação pode alcançar com o Armazenamento Premium. Neste artigo, fornecemos-lhe exemplos de benchmarking de um VM Standard DS14 a provisionado com discos de armazenamento Azure Premium.

Utilizamos ferramentas comuns de benchmarking Iometer e FIO, respectivamente, para Windows e Linux. Estas ferramentas geram múltiplos fios simulando uma produção como carga de trabalho, e medem o desempenho do sistema. Utilizando as ferramentas, também pode configurar parâmetros como o tamanho do bloco e a profundidade da fila, que normalmente não pode alterar para uma aplicação. Isto dá-lhe mais flexibilidade para impulsionar o máximo desempenho em VM de alta escala a provisionado com discos premium para diferentes tipos de carga de trabalho de aplicação. Para saber mais sobre cada ferramenta de benchmarking visite [Iometer](http://www.iometer.org/) e [FIO](http://freecode.com/projects/fio).

Para seguir os exemplos abaixo, crie um Standard DS14 VM e fixe 11 discos de armazenamento Premium ao VM. Dos 11 discos, configuure 10 discos com o cache do anfitrião como "Nenhum" e encafaça-os num volume chamado NoCacheWrites. Configure o caching do anfitrião como "ReadOnly" no disco restante e crie um volume chamado CacheReads com este disco. Utilizando esta configuração, é possível ver o desempenho máximo de Leitura e Escrita a partir de um Standard DS14 VM. Para passos detalhados sobre a criação de um DS14 VM com SSDs premium, vá ao [Design para um alto desempenho.](../premium-storage-performance.md)

[!INCLUDE [virtual-machines-disks-benchmarking](../../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>Passos seguintes

Proceda ao nosso artigo sobre [design para alto desempenho.](../premium-storage-performance.md)

Nesse artigo, cria-se uma lista de verificação semelhante à sua aplicação existente para o protótipo. Utilizando ferramentas de benchmarking, pode simular as cargas de trabalho e medir o desempenho na aplicação do protótipo. Ao fazê-lo, pode determinar qual a oferta de disco que pode corresponder ou ultrapassar os requisitos de desempenho da sua aplicação. Em seguida, pode implementar as mesmas diretrizes para a sua aplicação de produção.