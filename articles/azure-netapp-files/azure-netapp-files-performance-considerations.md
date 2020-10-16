---
title: Considerações de desempenho para ficheiros Azure NetApp Microsoft Docs
description: Saiba mais sobre o desempenho dos Ficheiros Azure NetApp, incluindo a relação entre quota e limite de produção e como aumentar/diminuir dinamicamente a quota de volume.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: edfebe3d9470defbe70b3694d5574e58ca3b5938
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91325526"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Considerações de desempenho para o Azure NetApp Files

O [limite de produção](azure-netapp-files-service-levels.md) para um volume com QoS automático é determinado por uma combinação da quota atribuída ao volume e ao nível de serviço selecionado. Para volumes com QoS manual, o limite de produção pode ser definido individualmente. Quando faz planos de desempenho sobre ficheiros Azure NetApp, precisa de compreender várias considerações. 

## <a name="quota-and-throughput"></a>Quota e produção  

O limite de produção é apenas um determinante do desempenho real que será realizado.  

Considerações típicas de desempenho de armazenamento, incluindo mistura de leitura e escrita, o tamanho da transferência, padrões aleatórios ou sequenciais, e muitos outros fatores contribuirão para o desempenho total entregue.  

O rendimento empírico máximo observado nos testes é de 4.500 MiB/s.  No nível de armazenamento Premium, uma quota automática de volume QoS de 70.31 TiB irá prever um limite de produção suficientemente elevado para atingir este nível de desempenho.  

No caso de volumes automáticos de QoS, se estiver a considerar atribuir valores de quota de volume para além de 70,31 TiB, a quota adicional pode ser atribuída a um volume para armazenar dados adicionais. No entanto, a quota acrescida não resultará num novo aumento da produção efetiva.  

O mesmo teto de produção empírica aplica-se aos volumes com QoS manual. A potência máxima pode atribuir a um volume de 4.500 MiB/s.

## <a name="automatic-qos-volume-quota-and-throughput"></a>Quota e produção automática de volume QoS

Esta secção descreve a gestão de quotas e a produção de volumes com o tipo QoS automático.

### <a name="overprovisioning-the-volume-quota"></a>Sobreprovisionamento da quota de volume

Se o desempenho de uma carga de trabalho for limitado, é possível sobreprovisionar a quota automática de volume QoS para definir um nível de produção mais elevado e alcançar um desempenho mais elevado.  

Por exemplo, se um volume QoS automático no nível de armazenamento Premium tiver apenas 500 GiB de dados, mas requer 128 MiB/s de produção, pode definir a quota para 2 TiB de modo a que o nível de produção seja definido em conformidade (64 MiB/s por TB * 2 TiB = 128 MiB/s).  

Se tiver uma sobreprovisionação de um volume para obter uma produção mais elevada, considere utilizar os volumes QoS manuais ou utilizar um nível de serviço mais elevado.  No exemplo acima, pode atingir o mesmo limite de produção com metade da quota automática de volume QoS utilizando o nível de armazenamento Ultra (128 MiB/s por TiB * 1 TiB = 128 MiB/s).

### <a name="dynamically-increasing-or-decreasing-volume-quota"></a>Aumento dinâmico ou diminuição da quota de volume

Se os seus requisitos de desempenho forem de natureza temporária, ou se tiver necessidades de desempenho aumentadas por um período de tempo fixo, pode aumentar dinamicamente ou diminuir a quota de volume para ajustar instantaneamente o limite de produção.  Note as seguintes considerações: 

* A quota de volume pode ser aumentada ou diminuída sem necessidade de pausa de OI, e o acesso ao volume não é interrompido ou impactado.  

    Pode ajustar a quota durante uma transação de E/S ativa em volume.  Note que a quota de volume nunca pode ser diminuída abaixo da quantidade de dados lógicos armazenados no volume.

* Quando a quota de volume é alterada, a alteração correspondente no limite de produção é quase instantânea. 

    A alteração não interrompe nem impacta o acesso ao volume ou i/O.  

* A regulação da quota de volume pode exigir uma alteração no tamanho da piscina.  

    O tamanho da piscina pode ser ajustado de forma dinâmica e sem impacto na disponibilidade de volume ou I/O.

## <a name="manual-qos-volume-quota-and-throughput"></a>Quota de volume e produção de volume QoS manual 

Se utilizar volumes QoS manuais, não precisa de aumentar a quota de volume para obter uma produção mais elevada, porque a produção pode ser atribuída a cada volume de forma independente. No entanto, ainda precisa de garantir que o pool de capacidade é pré-a provisionado com rendimento suficiente para as suas necessidades de desempenho. A produção de um pool de capacidade é a provisionada de acordo com o seu tamanho e nível de serviço. Consulte [os níveis de serviço dos ficheiros Azure NetApp](azure-netapp-files-service-levels.md) para obter mais detalhes.


## <a name="next-steps"></a>Passos seguintes

- [Níveis de serviços do Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Testes de referência de desempenho para Linux](performance-benchmarks-linux.md)