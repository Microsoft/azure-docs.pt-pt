---
title: Modelo de custo para ficheiros de NetApp do Azure | Documentos da Microsoft
description: Descreve o modelo de custos para ficheiros de NetApp do Azure para a gestão de despesas do serviço.
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
ms.date: 05/01/2019
ms.author: b-juche
ms.openlocfilehash: b06e3366224b90899dd3f9f9439edf897de82794
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524224"
---
# <a name="cost-model-for-azure-netapp-files"></a>Modelo de custo para os Azure NetApp Files 

Compreender o modelo de custo para ficheiros de NetApp do Azure ajuda-o a gerir as suas despesas do serviço.

## <a name="calculation-of-capacity-consumption"></a>Cálculo de consumo de capacidade

Os ficheiros NetApp do Azure é cobrado numa capacidade de armazenamento aprovisionado.  Capacidade aprovisionada é alocada ao criar conjuntos de capacidade.  Agrupamentos de capacidade são faturados com base em $/ aprovisionado-GiB/mês em incrementos de hora a hora. O tamanho mínimo para um conjunto único de capacidade é 4 TiB e conjuntos de capacidade podem ser expandidos, em seguida, em incrementos de 1 TiB. Volumes são criados dentro de agrupamentos de capacidade.  Cada volume é atribuída uma quota que decrementa partir da capacidade aprovisionada de conjuntos. A quota que pode ser atribuída a volumes intervalos de um mínimo de 100 GiB até um máximo de 92 TiB.  

Para um volume de Active Directory, o consumo de capacidade para a quota baseia-se na lógica capacidade (em vigor).

Se o consumo de capacidade real de um volume exceder a quota de armazenamento, o volume pode continuar a crescer. Escritas ainda terão permissão, desde que o tamanho do volume real é menor que o limite do sistema (100 TiB).  

A capacidade total utilizada num conjunto capacidade em relação a quantidade aprovisionado é a soma de maior da quota atribuída ou real consumo de todos os volumes dentro do conjunto: 

   ![Cálculo da capacidade total utilizada](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

O diagrama abaixo ilustra esses conceitos.  
* Temos um conjunto de capacidade com 4 TiB de capacidade aprovisionada.  O conjunto contém três volumes.  
    * Volume 1 é atribuído uma quota de 2 TiB e tem 800 GiB de consumo.  
    * Volume 2 está atribuído uma quota de 1 TiB e tem 100 GiB de consumo.  
    * Volume 3 é atribuído uma quota de 500 GiB, mas tem 800 GiB de consumo (excesso).  
* O conjunto de capacidade limitado para 4 TiB de capacidade (a quantidade de aprovisionamento).  
    3,8 TiB de capacidade é consumido (2 TiB e 1 TiB de quota de Volumes 1 e 2 e 800 GiB de consumo real para o Volume 3). E 200 GiB de capacidade restante.

   ![Conjunto de capacidade com três volumes](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>Utilização excedida no consumo de capacidade  

Quando o total utilizado a capacidade de um conjunto excede o limite da sua capacidade aprovisionada, escritas de dados continuam a ser permitidas.  Após o período de tolerância (uma hora), se a capacidade utilizada do conjunto ainda excede a sua capacidade aprovisionada, em seguida, o tamanho do conjunto automaticamente aumenta em incrementos de 1 TiB até que a capacidade aprovisionada é maior do que a capacidade total utilizada.  Por exemplo, na ilustração acima, se Volume 3 continua a crescer e consumo real atinge TiB de 1,2, em seguida, após o período de tolerância, o agrupamento será automaticamente redimensionado para 5 TiB.  O resultado é que a capacidade de agrupamento aprovisionado (5 TiB) excede a capacidade utilizada (4.2 TiB).  

## <a name="manual-changes-of-the-pool-size"></a>Alterações manuais do tamanho do conjunto  

Pode aumentar ou diminuir manualmente o tamanho do conjunto. No entanto, as seguintes restrições aplicam-se:
* Limites mínimos e máximo do serviço  
    Consulte o artigo [limites de recursos](azure-netapp-files-resource-limits.md).
* Um incremento de 1 TiB após a compra mínimo de 4-TiB inicial
* Um incremento de faturação mínimo de uma hora
* O tamanho do conjunto de aprovisionamento não pode ser diminuído para menor do que o total utilizado capacidade do conjunto.

## <a name="behavior-of-maximum-size-pool-overage"></a>Comportamento de utilização excedida do conjunto de tamanho máximo   

O tamanho máximo de um conjunto de capacidade que pode criar ou redimensionar para é 500 TiB.  Quando o total utilizado a capacidade de um conjunto de capacidade excede 500 TiB, ocorrerão as seguintes situações:
* Escritas de dados ainda serão permitidas (se o volume é inferior ao máximo de sistema de 100 TiB).
* Após o período de cortesia de uma hora, o agrupamento será automaticamente redimensionado em incrementos de 1 TiB, até que a capacidade de agrupamento aprovisionado excede a capacidade total utilizada.
* Adicional aprovisionado e são faturadas a capacidade de agrupamento que tib de 500 exceder não pode ser utilizado para atribuir a quota de volume. Ele também não pode ser usado para expandir os limites de QoS de desempenho.  
    Ver [níveis de serviço](azure-netapp-files-service-levels.md) sobre limites de desempenho e dimensionamento de QoS.

O diagrama abaixo ilustra esses conceitos:
* Temos um conjunto de capacidade com uma camada de armazenamento Premium e uma capacidade de TiB de 500. O conjunto contém nove volumes.
    * Volumes de 1 a 8 são atribuídos uma quota de 60 TiB.  A capacidade total utilizada é 480 TiB.  
        Cada volume tem um limite de QoS de 3,75 GiB/s de débito (60 TiB * 64 MiB/s).  
    * Volume 9 é atribuído uma quota de TiB de 20.  
        Volume 9 tem um limite de QoS de 1.25 GiB/s de débito (60 TiB * 64 MiB/s).
* O volume 9 é um cenário de utilização excedido. Ela tem 25 TiB de consumo real.  
    * Após o período de cortesia de uma hora, o conjunto de capacidade será redimensionado para 505 TiB.  
        Ou seja, o total utilizado capacidade = 8 * 60-TiB quota para Volumes de 1 a 8 e 25 TiB de consumo real para o Volume 9.
    * A capacidade de faturação é 505 TiB.
    * Não é possível aumentar a quota de volume para o Volume 9 (porque a quota atribuída total para o conjunto não pode exceder os 500 TiB).
    * Não pode ser atribuída a taxa de transferência de QoS adicional (porque o QoS total para o conjunto ainda é baseado em 500 TiB).

   ![Conjunto de capacidade com nove volumes](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>Consumo de capacidade de instantâneos 

O consumo de capacidade de instantâneos nos ficheiros de NetApp do Azure é cobrado para a quota do volume principal.  Como resultado, ele compartilha a mesma tarifa de faturação que o conjunto de capacidade à qual pertence o volume.  No entanto, ao contrário do volume do Active Directory, o consumo de instantâneo é medido com base na capacidade incremental consumida.  Os instantâneos de ficheiros NetApp do Azure são diferenciais por natureza. Consoante a taxa de alteração dos dados, os instantâneos costumam consumam muito menos capacidade do que a capacidade de lógica do volume ativo. Por exemplo, suponha que tenha um instantâneo de um volume de 500 GiB que contém apenas 10 GiB de dados diferenciais. A capacidade de que é cobrada para a quota de volume para esse instantâneo seria GiB 10, não 500 GiB. 

## <a name="next-steps"></a>Passos Seguintes

* [Ficheiros NetApp do Azure, página de preços](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Níveis de serviço para os ficheiros do Azure NetApp](azure-netapp-files-service-levels.md)
* [Resource limits for Azure NetApp Files](azure-netapp-files-resource-limits.md) (Limites dos recursos do Azure NetApp Files)
