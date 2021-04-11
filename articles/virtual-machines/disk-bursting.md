---
title: Segurança do disco gerido
description: Saiba mais sobre o disco a rebentar para discos Azure e máquinas virtuais Azure.
author: albecker1
ms.author: albecker
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 9758b026ef205e6608f7fc4110219dc5f267369e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568720"
---
# <a name="managed-disk-bursting"></a>Segurança do disco gerido
[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting.md)]

[Os SSDs premium Azure](disks-types.md#premium-ssd) oferecem dois modelos de estourar:

- Um modelo de rebentamento a pedido (pré-visualização), onde o disco rebenta sempre que as suas necessidades excedem a sua capacidade atual. Este modelo incorre em cargas adicionais sempre que o disco rebenta. A explosão de não crédito só está disponível em discos superiores a 512 GiB em tamanho.
- Um modelo baseado no crédito, onde o disco só rebenta se tiver créditos rebentados acumulados no seu balde de crédito. Este modelo não incorre em encargos adicionais quando o disco rebenta. A explosão baseada em crédito só está disponível nos discos 512 GiB e menores.

Além disso, o [nível de desempenho dos discos geridos pode ser alterado](disks-change-performance.md), o que poderia ser ideal se a sua carga de trabalho estivesse a funcionar em rajada.

|  |Rebentamento baseado em crédito  |Rebentamento a pedido  |Alteração do nível de desempenho  |
|---------|---------|---------|---------|
| **Cenários**|Ideal para dimensionamento de curto prazo (30 minutos ou menos).|Ideal para dimensionamento de curto prazo (Não restrito ao tempo).|Ideal se a sua carga de trabalho estivesse continuamente a funcionar em rajada.|
|**Custo**     |Gratuito         |O custo é variável, consulte a secção [de Faturação](#billing) para mais detalhes.        |O custo de cada nível de desempenho é fixo, consulte [os preços dos Discos Geridos](https://azure.microsoft.com/pricing/details/managed-disks/) para mais detalhes.         |
|**Disponibilidade**     |Disponível apenas para SSDs premium 512 GiB e menor.         |Disponível apenas para SSDs premium superior a 512 GiB.         |Disponível para todos os tamanhos SSD premium.         |
|**Ativação**     |Ativado por padrão em discos elegíveis.         |Deve ser ativado pelo utilizador.         |O utilizador tem de alterar manualmente o seu nível.         |

## <a name="common-scenarios"></a>Cenários comuns
Os seguintes cenários podem beneficiar muito da explosão:
- **Melhore os tempos de arranque**  – Com o rebentamento, o seu caso irá arrancar a uma taxa significativamente mais rápida. Por exemplo, o disco de OS padrão para VMs habilitados a prémios é o disco P4, que é um desempenho previsto de até 120 IOPS e 25 MB/s. Com o rebentamento, o P4 pode ir até 3500 IOPS e 170 MB/s permitindo que o arranque acelere até 6X.
- **Lidar com trabalhos de lote** – Algumas cargas de trabalho de aplicação são de natureza cíclica. Exigem um desempenho de base na maior parte do tempo, e um desempenho mais elevado por curtos períodos de tempo. Um exemplo disso é um programa de contabilidade que processa transações diárias que requerem uma pequena quantidade de tráfego de discos. No final do mês, este programa completaria relatórios de reconciliação que precisam de uma quantidade muito maior de tráfego de discos.
- **Picos de tráfego** – Os servidores web e as suas aplicações podem experimentar picos de tráfego a qualquer momento. Se o seu servidor web for apoiado por VMs ou discos que usam estouragens, os servidores estariam melhor equipados para lidar com picos de tráfego. 

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]

## <a name="next-steps"></a>Passos seguintes

Para ativar o rebentamento a pedido, consulte [Ativar o rebentamento a pedido](disks-enable-bursting.md).
Para aprender a obter informações sobre os seus recursos de explosão, consulte [as métricas de explosão do disco.](disks-metrics.md)
