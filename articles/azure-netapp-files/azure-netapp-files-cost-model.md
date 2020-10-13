---
title: Modelo de custos para ficheiros Azure NetApp / Microsoft Docs
description: Descreve o modelo de custos para a Azure NetApp Files para gerir as despesas do serviço.
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
ms.openlocfilehash: 9c4eebae6909c9ef0969bc85bcb9a985db2a7c02
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91325611"
---
# <a name="cost-model-for-azure-netapp-files"></a>Modelo de custo para os Azure NetApp Files 

Compreender o modelo de custos para a Azure NetApp Files ajuda-o a gerir as suas despesas a partir do serviço. 

Para modelo de custos específico para a replicação transversal, consulte [o modelo custo para a replicação entre regiões.](cross-region-replication-introduction.md#cost-model-for-cross-region-replication)

## <a name="calculation-of-capacity-consumption"></a>Cálculo do consumo de capacidade

O Azure NetApp Files é faturado sobre a capacidade de armazenamento a provisionada.  A capacidade a provisionada é atribuída através da criação de agrupamentos de capacidade.  Os pools de capacidade são faturados com base em $/Provisioned-GiB/mês em incrementos horárias. O tamanho mínimo para uma piscina de capacidade única é 4 TiB, e as piscinas de capacidade podem ser posteriormente expandidas em incrementos de 1-TiB. Os volumes são criados dentro de piscinas de capacidade.  A cada volume é atribuída uma quota que decrementa a capacidade alocada das piscinas. A quota que pode ser atribuída a volumes varia de um mínimo de 100 GiB a um máximo de 100 TiB.  

Para um volume ativo, o consumo de capacidade contra quota baseia-se numa capacidade lógica (eficaz).

Se o consumo real de capacidade de um volume exceder a sua quota de armazenamento, o volume pode continuar a crescer. As gravações continuarão a ser permitidas desde que o tamanho real do volume seja inferior ao limite do sistema (100 TiB).  

A capacidade total utilizada num agrupamento de capacidades face ao montante previsto é a soma do maior do contingente atribuído ou do consumo efetivo de todos os volumes dentro da piscina: 

   ![Cálculo total da capacidade utilizada](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

O diagrama abaixo ilustra estes conceitos.  
* Dispomos de um pool de capacidade com 4 TiB de capacidade abastado.  A piscina contém três volumes.  
    * O volume 1 é atribuído a uma quota de 2 TiB e tem 800 GiB de consumo.  
    * O Volume 2 é atribuído a uma quota de 1 TiB e tem 100 GiB de consumo.  
    * O volume 3 é atribuído a uma quota de 500 GiB, mas tem 800 GiB de consumo (excesso).  
* O pool de capacidade é medido para 4 TiB de capacidade (o montante provisionado).  
    3.8 O TIB da capacidade é consumido (2 TiB e 1 TiB da quota dos volumes 1 e 2 e 800 GiB do consumo efetivo para o volume 3). E 200 GiB de capacidade restante.

   ![Piscina de capacidade com três volumes](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>Excesso de consumo de capacidade  

Quando a capacidade total utilizada de um pool exceder a sua capacidade a provisionada, os dados ainda são permitidos.  Após o período de carência (uma hora), se a capacidade utilizada da piscina ainda exceder a sua capacidade a provisionada, então o tamanho da piscina será automaticamente aumentado em incrementos de 1 TiB até que a capacidade a provisionada seja superior à capacidade total utilizada.  Por exemplo, na ilustração acima, se o Volume 3 continuar a crescer e o consumo real atingir 1.2 TiB, então após o período de carência, a piscina será automaticamente redimensionada para 5 TiB.  O resultado é que a capacidade de piscina a provisionada (5 TiB) excede a capacidade utilizada (4.2 TiB).  

Embora o tamanho da piscina de capacidade cresça automaticamente para satisfazer a procura do volume, não é automaticamente reduzido quando o tamanho do volume diminui. Se pretender reduzir o tamanho do pool após uma diminuição do tamanho do volume (por exemplo, após a limpeza de dados de um volume), tem de reduzir _manualmente_ o tamanho da piscina.

## <a name="manual-changes-of-the-pool-size"></a>Alterações manuais do tamanho da piscina  

Pode aumentar manualmente ou diminuir o tamanho da piscina. No entanto, aplicam-se os seguintes constrangimentos:
* Limites mínimos e máximos de serviço  
    Consulte o artigo sobre [os limites de recursos.](azure-netapp-files-resource-limits.md)
* Um incremento de 1-TiB após a compra mínima inicial de 4-TiB
* Um incremento mínimo de faturação de uma hora
* O tamanho da piscina a provisionada não pode ser diminuído para menos do que a capacidade total utilizada na piscina.
* Para piscinas de capacidade com QoS manual, o tamanho da piscina só pode ser diminuído se o tamanho e o nível de serviço fornecerem mais produção do que a produção real atribuída de todos os volumes.

## <a name="behavior-of-maximum-size-pool-overage"></a>Comportamento de excesso de piscina de tamanho máximo   

O tamanho máximo de uma piscina de capacidade que pode criar ou redimensionar é de 500 TiB.  Quando a capacidade total utilizada num pool de capacidade exceder 500 TiB, ocorrerão as seguintes situações:
* As gravações de dados continuarão a ser permitidas (se o volume estiver abaixo do máximo do sistema de 100 TiB).
* Após o período de carência de uma hora, a piscina será automaticamente redimensionada em incrementos 1-TiB, até que a capacidade total de fornecimento da piscina exceda a capacidade total utilizada.
* A capacidade adicional de piscina a provisionada e faturada superior a 500 TiB não pode ser utilizada para atribuir quota de volume. Também não pode ser usado para expandir os limites de desempenho QoS.  
    Consulte [os níveis de serviço](azure-netapp-files-service-levels.md) sobre os limites de desempenho e o tamanho do QoS.

O diagrama abaixo ilustra estes conceitos:
* Dispomos de uma piscina de capacidade com um nível de armazenamento Premium e uma capacidade de 500-TiB. A piscina contém nove volumes.
    * Os volumes 1 a 8 são atribuídos a uma quota de 60 TiB cada.  A capacidade total utilizada é de 480 TiB.  
        Cada volume tem um limite de QoS de 3,75 GiB/s de produção (60 TiB * 64 MiB/s).  
    * O volume 9 é atribuído a uma quota de 20 TiB.  
        O volume 9 tem um limite de QoS de 1,25 GiB/s de produção (20 TiB * 64 MiB/s).
* O volume 9 é um cenário exagerado. Tem 25 TiB de consumo real.  
    * Após o período de carência de uma hora, a capacidade será redimensionada para 505 TiB.  
        Ou seja, capacidade total utilizada = quota de 8 * 60-TiB para os volumes 1 a 8, e 25 TiB do consumo efetivo para o Volume 9.
    * A capacidade de faturação é de 505 TiB.
    * A quota de volume do Volume 9 não pode ser aumentada (uma vez que o contingente total atribuído para o pool não pode exceder 500 TiB).
    * A produção adicional de QoS não pode ser atribuída (porque o QoS total para a piscina ainda é baseado em 500 TiB).

   ![Piscina de capacidade com nove volumes](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>Consumo de capacidade de instantâneos 

O consumo de capacidade de instantâneos nos Ficheiros Azure NetApp é cobrado contra a quota do volume-mãe.  Como resultado, partilha a mesma taxa de faturação que o pool de capacidade a que pertence o volume.  No entanto, ao contrário do volume ativo, o consumo instantâneo é medido com base na capacidade incremental consumida.  As fotos dos Ficheiros Azure NetApp são de natureza diferencial. Dependendo da taxa de alteração dos dados, os instantâneos consomem frequentemente muito menos capacidade do que a capacidade lógica do volume ativo. Por exemplo, assuma que tem uma imagem instantânea de um volume de 500 GiB que contém apenas 10 GiB de dados diferenciais. A capacidade que é cobrada contra a quota de volume para esse instantâneo seria 10 GiB, não 500 GiB. 

## <a name="next-steps"></a>Passos seguintes

* [Página de preços do Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Níveis de serviços do Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Resource limits for Azure NetApp Files](azure-netapp-files-resource-limits.md) (Limites dos recursos do Azure NetApp Files)
* [Modelo de custos para a replicação entre regiões](cross-region-replication-introduction.md#cost-model-for-cross-region-replication)
