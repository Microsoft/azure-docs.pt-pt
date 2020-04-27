---
title: Considerações de desempenho para Ficheiros Azure NetApp / Microsoft Docs
description: Descreve considerações de desempenho para Ficheiros Azure NetApp.
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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "67454142"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Considerações de desempenho para o Azure NetApp Files

O limite de [produção](azure-netapp-files-service-levels.md) para um volume é determinado por uma combinação da quota atribuída ao volume e ao nível de serviço selecionado. Quando faz planos de desempenho sobre ficheiros Azure NetApp, precisa de compreender várias considerações. 

## <a name="quota-and-throughput"></a>Quota e produção  

O limite de produção é apenas um determinante do desempenho real que será realizado.  

Considerações típicas de desempenho de armazenamento, incluindo ler e escrever mix, o tamanho da transferência, padrões aleatórios ou sequenciais, e muitos outros fatores contribuirão para o desempenho total entregue.  

A potência empírica máxima observada nos testes é de 4.500 MiB/s.  No nível de armazenamento Premium, uma quota de volume de 70.31 TiB fornecerá um limite de produção suficientemente elevado para atingir este nível de desempenho.  

Se estiver a considerar atribuir valores de quota de volume para além de 70.31 TiB, pode ser atribuída uma quota adicional a um volume para armazenar dados adicionais. No entanto, a quota adicional não resultará num novo aumento do produto efetivo.  

Consulte os critérios de referência de [Desempenho para ficheiros Azure NetApp](azure-netapp-files-performance-benchmarks.md) para obter informações adicionais.

## <a name="overprovisioning-the-volume-quota"></a>Sobreprovisionamento da quota de volume

Se o desempenho de uma carga de trabalho for limitado ao limite de produção, é possível sobressaltos a quota de volume para definir um nível de produção mais elevado e obter um desempenho mais elevado.  

Por exemplo, se um volume no nível de armazenamento Premium tiver apenas 500 GiB de dados mas necessitar de 128 MiB/s de produção, pode definir a quota para 2 TiB de modo a que o nível de produção seja definido em conformidade (64 MiB/s por TB * 2 TiB = 128 MiB/s).  

Se fornecer um volume consistentemente superior a um volume para obter uma maior entrada, considere utilizar um nível de serviço mais elevado.  No exemplo acima, pode atingir o mesmo limite de produção com metade da quota de volume utilizando o nível de armazenamento Ultra (128 MiB/s por TiB * 1 TiB = 128 MiB/s).

## <a name="dynamically-increasing-or-decreasing-volume-quota"></a>Aumento dinâmico ou diminuição da quota de volume

Se os seus requisitos de desempenho forem de natureza temporária, ou se tiver necessidades de desempenho aumentadas durante um período de tempo fixo, pode aumentar ou diminuir dinamicamente a quota de volume para ajustar instantaneamente o limite de produção.  Note as seguintes considerações: 

* A quota de volume pode ser aumentada ou diminuída sem necessidade de pausa sem a IO, e o acesso ao volume não é interrompido ou impactado.  

    Pode ajustar a quota durante uma transação ativa de I/S em relação a um volume.  Note que a quota de volume nunca pode ser diminuída abaixo da quantidade de dados lógicos armazenados no volume.

* Quando a quota de volume é alterada, a alteração correspondente no limite de produção é quase instantânea. 

    A alteração não interrompe nem afeta o acesso ao volume ou o I/O.  

* O ajuste da quota de volume requer uma alteração do tamanho da capacidade do pool.  

    O tamanho da capacidade do pool pode ser ajustado dinamicamente e sem afetar a disponibilidade de volume ou o I/S.

## <a name="next-steps"></a>Passos seguintes

- [Níveis de serviços do Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Referências de desempenho para o Azure NetApp Files](azure-netapp-files-performance-benchmarks.md)