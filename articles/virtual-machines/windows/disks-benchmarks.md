---
title: Benchmarking da sua aplicação no Armazenamento de Discos Azure
description: Conheça o processo de benchmarking da sua aplicação no Azure.
author: roygara
ms.author: rogarana
ms.date: 01/11/2019
ms.topic: conceptual
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: 77e542e9bff399e58b433286385864478bcb5076
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75719550"
---
# <a name="benchmarking-a-disk"></a>Benchmarking um disco

O benchmarking é o processo de simulação de diferentes cargas de trabalho na sua aplicação e de medição do desempenho da aplicação para cada carga de trabalho. Utilizando os passos descritos no desenho para artigo de [alto desempenho,](premium-storage-performance.md)reuniu os requisitos de desempenho da aplicação. Ao executar ferramentas de benchmarking nos VMs que hospedam a aplicação, pode determinar os níveis de desempenho que a sua aplicação pode alcançar com o Armazenamento Premium. Neste artigo, fornecemos-lhe exemplos de benchmarking de um VM Standard DS14 aprovisionado com discos de armazenamento Premium Azure.

Usamos ferramentas comuns de benchmarking Iometer e FIO, para Windows e Linux, respectivamente. Estas ferramentas geram vários fios simulando uma produção como a carga de trabalho, e medem o desempenho do sistema. Utilizando as ferramentas, também pode configurar parâmetros como o tamanho do bloco e a profundidade da fila, que normalmente não pode alterar para uma aplicação. Isto dá-lhe mais flexibilidade para impulsionar o desempenho máximo num VM de alta escala, aprovisionado com discos premium para diferentes tipos de cargas de trabalho de aplicação. Para saber mais sobre cada ferramenta de benchmarking visite [Iometer](http://www.iometer.org/) e [FIO](http://freecode.com/projects/fio).

Para seguir os exemplos abaixo, crie um VM Standard DS14 e fixe 11 discos de armazenamento Premium ao VM. Dos 11 discos, configure 10 discos com o cacheching do hospedeiro como "Nenhum" e os stripe num volume chamado NoCacheWrites. Configure o cacheching do anfitrião como "ReadOnly" no disco restante e crie um volume chamado CacheReads com este disco. Utilizando esta configuração, é possível ver o desempenho máximo de Leitura e Escrita de um VM Standard DS14. Para passos detalhados sobre a criação de um DS14 VM com SSDs premium, vá ao [Design para um alto desempenho.](premium-storage-performance.md)

[!INCLUDE [virtual-machines-disks-benchmarking](../../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>Passos seguintes

Prossiga o nosso artigo sobre [design para alto desempenho.](premium-storage-performance.md)

Nesse artigo, cria-se uma lista de verificação semelhante à sua aplicação existente para o protótipo. Utilizando ferramentas de benchmarking, pode simular as cargas de trabalho e medir o desempenho na aplicação do protótipo. Ao fazê-lo, pode determinar qual a oferta de disco que pode corresponder ou ultrapassar os requisitos de desempenho da sua aplicação. Depois pode implementar as mesmas orientações para a sua aplicação de produção.