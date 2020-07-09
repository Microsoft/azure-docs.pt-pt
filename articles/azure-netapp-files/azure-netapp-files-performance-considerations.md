---
title: Considerações de desempenho para ficheiros Azure NetApp Microsoft Docs
description: Descreve considerações de desempenho para ficheiros Azure NetApp.
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
ms.date: 06/25/2019
ms.author: b-juche
ms.openlocfilehash: 4badf550809df6cc63711c47e6e6f5b3690fdfca
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82691323"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Considerações de desempenho para o Azure NetApp Files

O [limite de produção](azure-netapp-files-service-levels.md) de um volume é determinado por uma combinação do contingente atribuído ao volume e ao nível de serviço selecionado. Quando faz planos de desempenho sobre ficheiros Azure NetApp, precisa de compreender várias considerações. 

## <a name="quota-and-throughput"></a>Quota e produção  

O limite de produção é apenas um determinante do desempenho real que será realizado.  

Considerações típicas de desempenho de armazenamento, incluindo mistura de leitura e escrita, o tamanho da transferência, padrões aleatórios ou sequenciais, e muitos outros fatores contribuirão para o desempenho total entregue.  

O rendimento empírico máximo observado nos testes é de 4.500 MiB/s.  No nível de armazenamento Premium, uma quota de volume de 70.31 TiB irá prever um limite de produção suficientemente elevado para atingir este nível de desempenho.  

Se estiver a considerar atribuir valores de quota de volume para além de 70,31 TiB, a quota adicional pode ser atribuída a um volume para armazenar dados adicionais. No entanto, a quota acrescida não resultará num novo aumento da produção efetiva.  

## <a name="overprovisioning-the-volume-quota"></a>Sobreprovisionamento da quota de volume

Se o desempenho de uma carga de trabalho for limitado, é possível sobreprovisionar a quota de volume para definir um nível de produção mais elevado e alcançar um desempenho mais elevado.  

Por exemplo, se um volume no nível de armazenamento Premium tiver apenas 500 GiB de dados mas necessitar de 128 MiB/s de produção, pode definir a quota para 2 TiB de modo a que o nível de produção seja definido em conformidade (64 MiB/s por TB * 2 TiB = 128 MiB/s).  

Se tiver uma sobreprovisionação de um volume para obter uma produção mais elevada, considere usar um nível de serviço mais elevado.  No exemplo acima, pode atingir o mesmo limite de produção com metade da quota de volume utilizando o nível de armazenamento Ultra (128 MiB/s por TiB * 1 TiB = 128 MiB/s).

## <a name="dynamically-increasing-or-decreasing-volume-quota"></a>Aumento dinâmico ou diminuição da quota de volume

Se os seus requisitos de desempenho forem de natureza temporária, ou se tiver necessidades de desempenho aumentadas por um período de tempo fixo, pode aumentar dinamicamente ou diminuir a quota de volume para ajustar instantaneamente o limite de produção.  Note as seguintes considerações: 

* A quota de volume pode ser aumentada ou diminuída sem necessidade de pausa de OI, e o acesso ao volume não é interrompido ou impactado.  

    Pode ajustar a quota durante uma transação de E/S ativa em volume.  Note que a quota de volume nunca pode ser diminuída abaixo da quantidade de dados lógicos armazenados no volume.

* Quando a quota de volume é alterada, a alteração correspondente no limite de produção é quase instantânea. 

    A alteração não interrompe nem impacta o acesso ao volume ou i/O.  

* Ajustar a quota de volume requer uma alteração no tamanho da piscina.  

    O tamanho da piscina pode ser ajustado de forma dinâmica e sem impacto na disponibilidade de volume ou I/O.

## <a name="next-steps"></a>Passos seguintes

- [Níveis de serviços do Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Testes de referência de desempenho para Linux](performance-benchmarks-linux.md)