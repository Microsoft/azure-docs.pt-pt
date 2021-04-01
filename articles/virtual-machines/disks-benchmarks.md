---
title: Compare a sua aplicação no Armazenamento de Disco Azure
description: Saiba mais sobre o processo de benchmarking da sua aplicação no Azure.
author: roygara
ms.author: rogarana
ms.date: 01/29/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: bfda14acc2e50005e25faafa3037805af871c1df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99094647"
---
# <a name="benchmark-a-disk"></a>Criar um disco de referência

O benchmarking é o processo de simulação de diferentes cargas de trabalho na sua aplicação e de medição do desempenho da aplicação para cada carga de trabalho. Utilizando os passos descritos na [conceção do artigo de alto desempenho,](premium-storage-performance.md)reuniu os requisitos de desempenho da aplicação. Ao executar ferramentas de benchmarking nos VMs que hospedam a aplicação, pode determinar os níveis de desempenho que a sua aplicação pode alcançar com SSDs premium. Neste artigo, fornecemos-lhe exemplos de benchmarking de um Standard_D8ds_v4 VM a provisionado com SSDs premium Azure.

Utilizamos ferramentas comuns de benchmarking DiskSpd e FIO, respectivamente, para Windows e Linux. Estas ferramentas geram múltiplos fios simulando uma produção como carga de trabalho, e medem o desempenho do sistema. Utilizando as ferramentas, também pode configurar parâmetros como o tamanho do bloco e a profundidade da fila, que normalmente não pode alterar para uma aplicação. Isto dá-lhe mais flexibilidade para impulsionar o máximo desempenho em VM de alta escala a provisionado com SSDs premium para diferentes tipos de carga de trabalho de aplicação. Para saber mais sobre cada ferramenta de benchmarking visite [DiskSpd](https://github.com/Microsoft/diskspd/wiki/) e [FIO](http://freecode.com/projects/fio).

Para seguir os exemplos abaixo, crie um Standard_D8ds_v4 e anexe quatro SSDs premium ao VM. Dos quatro discos, configura três com o hospedeiro caching como "Nenhum" e encafadá-los num volume chamado NoCacheWrites. Configure o caching do anfitrião como "ReadOnly" no disco restante e crie um volume chamado CacheReads com este disco. Utilizando esta configuração, é possível ver o desempenho máximo de Leitura e Escrita de um VM Standard_D8ds_v4. Para obter passos detalhados sobre a criação de uma Standard_D8ds_v4 com SSDs premium, consulte [Design para alto desempenho.](premium-storage-performance.md)

[!INCLUDE [virtual-machines-disks-benchmarking](../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>Passos seguintes

Proceda ao nosso artigo sobre [design para alto desempenho.](premium-storage-performance.md)

Nesse artigo, cria-se uma lista de verificação semelhante à sua aplicação existente para o protótipo. Utilizando ferramentas de benchmarking, pode simular as cargas de trabalho e medir o desempenho na aplicação do protótipo. Ao fazê-lo, pode determinar qual a oferta de disco que pode corresponder ou ultrapassar os requisitos de desempenho da sua aplicação. Em seguida, pode implementar as mesmas diretrizes para a sua aplicação de produção.
