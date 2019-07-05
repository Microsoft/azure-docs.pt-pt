---
title: Considerações de desempenho para ficheiros de NetApp do Azure | Documentos da Microsoft
description: Descreve as considerações de desempenho para os ficheiros do Azure NetApp.
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
ms.openlocfilehash: 97e3c6212edd2ade4eabb96db3543e9b3b68e2ae
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454142"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Considerações de desempenho para o Azure NetApp Files

O [limite de débito](azure-netapp-files-service-levels.md) para um volume é determinado por uma combinação de quota atribuído ao volume e o serviço de nível selecionado. Quando os planos de desempenho sobre os ficheiros do Azure NetApp, precisa entender as várias considerações. 

## <a name="quota-and-throughput"></a>Quota e débito  

O limite de taxa de transferência é apenas um fulcral do desempenho real que ocorrerá.  

Considerações de desempenho do armazenamento típico, incluindo de leitura e escrita mistura, o tamanho de transferência, padrões de aleatórios ou sequenciais e muitos outros fatores contribuem para o desempenho total entregue.  

O débito máximo empírico que tem sido observado em teste é 4,500 MiB/s.  Na camada de armazenamento Premium, uma quota de volume de 70.31 TiB irá aprovisionar um limite de taxa de transferência que seja alto o suficiente para alcançar esse nível de desempenho.  

Se estiver a considerar a atribuição de quantidades de quota para além dos 70.31 TiB de volume, a quota adicional pode ser atribuído a um volume para armazenar dados adicionais. No entanto, a quota adicional não resultará num mais aumento no débito real.  

Ver [testes de desempenho para os ficheiros do Azure NetApp](azure-netapp-files-performance-benchmarks.md) para obter informações adicionais.

## <a name="overprovisioning-the-volume-quota"></a>Aprovisionar em excesso a quota de volume

Se o desempenho de uma carga de trabalho for vinculado de limite de débito, é possível sobreaprovisionar a quota de volume para definir um nível de débito mais elevado e obter um desempenho superior.  

Por exemplo, se um volume na camada de armazenamento Premium tem apenas 500 GiB de dados, mas requer 128 MiB/s de débito, pode definir a quota para 2 TiB para que o nível de débito é definido em conformidade (64 MiB/s por TB * 2 TiB = 128 MiB/s).  

Se sobreaprovisionar consistentemente um volume para alcançar um débito mais elevado, considere a utilização em vez disso, um nível de serviço mais elevado.  No exemplo acima, pode obter o mesmo limite de taxa de transferência com metade a quota de volume utilizando a camada de armazenamento Ultra (128 MiB/s por TiB * 1 TiB = 128 MiB/s).

## <a name="dynamically-increasing-or-decreasing-volume-quota"></a>Dinamicamente aumentar ou diminuir a quota de volume

Se seus requisitos de desempenho são, por natureza, ou se aumentou necessidades de desempenho para um período de tempo fixo, dinamicamente pode aumentar ou diminuir a quota de volume instantaneamente ajustar o limite de taxa de transferência.  Tenha em atenção as seguintes considerações: 

* Quota de volume pode aumentar ou reduzir sem ser necessário para colocar em pausa e/s e acesso ao volume não é interrompido ou afetado.  

    Pode ajustar a quota durante uma transação ativa de e/s em relação a um volume.  Tenha em atenção que quota de volume nunca pode ser reduzido abaixo a quantidade de dados lógicos, que são armazenados no volume.

* Quando a quota de volume é alterada, a alteração correspondente no limite de débito é quase instantânea. 

    A alteração não interromper nem afetar o acesso ao volume ou e/s.  

* Quota de volume ajuste requer uma alteração no tamanho do conjunto de capacidade.  

    O tamanho do conjunto de capacidade pode ser ajustado dinamicamente e sem afetar a disponibilidade de volume ou e/s.

## <a name="next-steps"></a>Passos Seguintes

- [Níveis de serviço para os ficheiros do Azure NetApp](azure-netapp-files-service-levels.md)
- [Parâmetros de comparação de desempenho para os ficheiros do Azure NetApp](azure-netapp-files-performance-benchmarks.md)