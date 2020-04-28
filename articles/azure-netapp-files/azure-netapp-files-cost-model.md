---
title: Modelo de custo para Ficheiros Azure NetApp [ Microsoft Docs
description: Descreve o modelo de custo para os Ficheiros Azure NetApp para gerir as despesas do serviço.
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
ms.openlocfilehash: aea783b818550b8219e1a0498256280f61f678e1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "70995102"
---
# <a name="cost-model-for-azure-netapp-files"></a>Modelo de custo para os Azure NetApp Files 

Compreender o modelo de custos dos Ficheiros Azure NetApp ajuda-o a gerir as suas despesas a partir do serviço.

## <a name="calculation-of-capacity-consumption"></a>Cálculo do consumo de capacidade

Os Ficheiros Azure NetApp são faturados na capacidade de armazenamento aprovisionado.  A capacidade aprovisionada é atribuída através da criação de piscinas de capacidade.  Os pools de capacidade são faturados com base em $/provisioned-GiB/mês em incrementos de hora a hora. O tamanho mínimo para uma única piscina de capacidade é de 4 TiB, e as piscinas de capacidade podem ser subsequentemente expandidas em incrementos de 1-TiB. Os volumes são criados dentro de piscinas de capacidade.  A cada volume é atribuída uma quota que se decrea da capacidade aprovisionada pelas piscinas. A quota que pode ser atribuída a volumes varia entre um mínimo de 100 GiB e um máximo de 100 TiB.  

Para um volume ativo, o consumo de capacidade contra a quota baseia-se na capacidade lógica (eficaz).

Se o consumo real de capacidade de um volume exceder a sua quota de armazenamento, o volume pode continuar a crescer. As escritas continuarão a ser permitidas enquanto o tamanho real do volume for inferior ao limite do sistema (100 TiB).  

A capacidade total utilizada num conjunto de capacidades contra o seu montante provisionado é a soma do maior da quota atribuída ou do consumo efetivo de todos os volumes dentro da piscina: 

   ![Cálculo total da capacidade utilizada](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

O diagrama abaixo ilustra estes conceitos.  
* Dispomos de um pool de capacidade com 4 TiB de capacidade aprovisionada.  A piscina contém três volumes.  
    * O volume 1 é atribuído a uma quota de 2 TiB e tem 800 GiB de consumo.  
    * O volume 2 é atribuído a uma quota de 1 TiB e tem 100 GiB de consumo.  
    * O volume 3 é atribuído a uma quota de 500 GiB, mas tem 800 GiB de consumo (overage).  
* A piscina de capacidade é medição para 4 TiB de capacidade (o montante provisionado).  
    3.8 TiB de capacidade é consumido (2 TiB e 1 TiB de quota dos volumes 1 e 2, e 800 GiB de consumo efetivo para o volume 3). E restam 200 GiB de capacidade.

   ![Piscina de capacidade com três volumes](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>Excesso de consumo de capacidade  

Quando a capacidade total utilizada de um pool excede a sua capacidade provisionada, os dados ainda são permitidos.  Após o período de carência (uma hora), se a capacidade utilizada da piscina ainda exceder a sua capacidade provisionada, o tamanho da piscina será automaticamente aumentado em incrementos de 1 TiB até que a capacidade provisionada seja superior à capacidade total utilizada.  Por exemplo, na ilustração acima, se o Volume 3 continuar a crescer e o consumo real atingir 1,2 TiB, então após o período de graça, a piscina será automaticamente redimensionada para 5 TiB.  O resultado é que a capacidade de piscina aprovisionada (5 TiB) excede a capacidade utilizada (4.2 TiB).  

## <a name="manual-changes-of-the-pool-size"></a>Alterações manuais do tamanho da piscina  

Pode aumentar manualmente ou diminuir manualmente o tamanho da piscina. No entanto, aplicam-se os seguintes constrangimentos:
* Limites mínimos e máximos de serviço  
    Consulte o artigo sobre limites de [recursos.](azure-netapp-files-resource-limits.md)
* Um incremento de 1-TiB após a compra mínima inicial de 4 TiB
* Um incremento mínimo de faturação de uma hora
* O tamanho da piscina provisionado não pode ser diminuído para menos do que a capacidade total utilizada na piscina.

## <a name="behavior-of-maximum-size-pool-overage"></a>Comportamento de excesso de piscina de tamanho máximo   

O tamanho máximo de uma piscina de capacidade que você pode criar ou redimensionar é 500 TiB.  Quando a capacidade total utilizada numa piscina de capacidade exceder 500 TiB, ocorrerão as seguintes situações:
* Os dados escritos continuarão a ser permitidos (se o volume estiver abaixo do máximo de 100 TiB do sistema).
* Após o período de carência de uma hora, a piscina será automaticamente redimensionada em incrementos de 1-TiB, até que a capacidade aprovisionada na piscina exceda a capacidade total utilizada.
* A capacidade adicional de piscina aprovisionada e faturada superior a 500 TiB não pode ser utilizada para atribuir quota de volume. Também não pode ser usado para expandir os limites de QoS de desempenho.  
    Consulte [os níveis de serviço](azure-netapp-files-service-levels.md) sobre limites de desempenho e tamanho sinuoso de QoS.

O diagrama abaixo ilustra estes conceitos:
* Dispomos de uma piscina de capacidade com um nível de armazenamento Premium e uma capacidade de 500 TiB. A piscina contém nove volumes.
    * Os volumes 1 a 8 são atribuídos a uma quota de 60 TiB cada.  A capacidade total usada é de 480 TiB.  
        Cada volume tem um limite QoS de 3,75 GiB/s de entrada (60 TiB * 64 MiB/s).  
    * O volume 9 é atribuído a uma quota de 20 TiB.  
        O volume 9 tem um limite QoS de 1.25 GiB/s de entrada (20 TiB * 64 MiB/s).
* O volume 9 é um cenário de sobre-idade. Tem 25 TiB de consumo real.  
    * Após o período de carência de uma hora, a piscina de capacidade será redimensionada para 505 TiB.  
        Ou seja, capacidade total usada = quota 8 * 60-TiB para volumes 1 a 8 e 25 TiB de consumo real para o Volume 9.
    * A capacidade cobrada é 505 TiB.
    * O contingente de volume para o volume 9 não pode ser aumentado (uma vez que o contingente total atribuído para o pool não pode exceder 500 TiB).
    * A produção adicional de QoS não pode ser atribuída (porque o QoS total para a piscina ainda é baseado em 500 TiB).

   ![Piscina de capacidade com nove volumes](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>Consumo de capacidade de instantâneos 

O consumo de capacidade de instantâneos em Ficheiros Azure NetApp é cobrado contra a quota do volume principal.  Como resultado, partilha a mesma taxa de faturação que o conjunto de capacidades a que o volume pertence.  No entanto, ao contrário do volume ativo, o consumo instantâneo é medido com base na capacidade incremental consumida.  Os instantâneos dos Ficheiros Azure NetApp são de natureza diferencial. Dependendo da taxa de variação dos dados, os instantâneos consomem muitas vezes muito menos capacidade do que a capacidade lógica do volume ativo. Por exemplo, assuma que tem uma imagem instantânea de um volume de 500 GiB que contém apenas 10 GiB de dados diferenciais. A capacidade cobrada contra a quota de volume desse instantâneo seria de 10 GiB, e não de 500 GiB. 

## <a name="next-steps"></a>Passos seguintes

* [Página de preços dos Ficheiros Azure NetApp](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Níveis de serviços do Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Limites de recurso para os Azure NetApp Files](azure-netapp-files-resource-limits.md)
