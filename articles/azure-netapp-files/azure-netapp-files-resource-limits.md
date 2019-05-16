---
title: Limites de recursos para ficheiros de NetApp do Azure | Documentos da Microsoft
description: Descreve os limites de recursos de ficheiros de NetApp do Azure, incluindo limites de contas, conjuntos de capacidade, volumes, instantâneos e a sub-rede do delegado NetApp.
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
ms.date: 05/02/2019
ms.author: b-juche
ms.openlocfilehash: d55e11ace4ca306c3d3ec8c0094a751966289db6
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523055"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Limites de recurso para os Azure NetApp Files

Compreender limites de recursos para ficheiros de NetApp do Azure ajuda-o a gerir os seus volumes.

## <a name="resource-limits"></a>Limites de recursos

A tabela seguinte descreve os limites de recursos para ficheiros de NetApp do Azure:

|  Resource  |  Limite predefinido  |  Ajustável através de pedido de suporte  |
|----------------|---------------------|--------------------------------------|
|  Número de contas de NetApp por subscrição do Azure   |  10    |  Sim   |
|  Número de conjuntos de capacidade por conta do NetApp   |    25     |   Sim   |
|  Número de volumes por conjunto de capacidade     |    500   |    Sim     |
|  Número de instantâneos por volume       |    255     |    Não        |
|  Número de sub-redes delegada para ficheiros de NetApp (Microsoft.NetApp/volumes) do Azure por rede Virtual do Azure    |   1   |    Não    |
|  Tamanho mínimo de um conjunto único de capacidade   |  4 TiB     |    Não  |
|  Tamanho máximo de um conjunto único de capacidade    |  500 TiB   |   Não   |
|  Tamanho mínimo de um único volume    |    100 GiB    |    Não    |
|  Máximo atribuído a quota de um único volume *   |   92 TiB   |    Não   |
|  Tamanho máximo de um único volume *     |    100 TiB    |    Não       |

* Um volume pode ser criado manualmente ou redimensionado para TiB maximally 92. No entanto, um volume pode crescer até 100 TiB num cenário de utilização excedido. Ver [modelo de custos para os ficheiros do Azure NetApp](azure-netapp-files-cost-model.md) para obter detalhes sobre o excesso de capacidade. 

## <a name="request-limit-increase"></a>Pedir aumento de limite 

Pode criar um pedido de suporte do Azure para aumentar os limites ajustável da tabela acima. 

De plano de navegação do portal do Azure: 

1. Clique em **ajuda + suporte**.
2. Clique em **+ novo pedido de suporte**.
3. No separador Noções básicas, forneça as seguintes informações: 
    1. Tipo de problema: Selecione **limites de serviço e subscrição (cotas)**.
    2. Subscrições: Selecione a subscrição para o recurso que precisa de aumentar a quota.
    3. Tipo de quota: Selecione **armazenamento: Os ficheiros do Azure NetApp limita**.
    4. Clique em **seguinte: Soluções**.
4. Na guia detalhes:
    1. Na caixa de descrição, forneça as seguintes informações para o tipo de recurso correspondente:

        |  Resource  |    Recursos de principal      |    Novos limites de pedido     |    Motivo para aumento de quota       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Conta |  *ID de subscrição*   |  *Pedido novo máximo **conta** número*    |  *O cenário ou utilize maiúsculas e minúsculas lhe for pedido o pedido?*  |
        |  Conjunto    |  *ID de subscrição, o URI de conta*  |  *Pedido novo máximo **agrupamento** número*   |  *O cenário ou utilize maiúsculas e minúsculas lhe for pedido o pedido?*  |
        |  Volume  |  *ID de subscrição, conta de URI, agrupamento de URI*   |  *Pedido novo máximo **volume** número*     |  *O cenário ou utilize maiúsculas e minúsculas lhe for pedido o pedido?*  |

    2. Especifica método de suporte e fornecem as suas informações de contrato o adequados.

    3. Clique em **seguinte: Rever + criar** para criar o pedido. 


## <a name="next-steps"></a>Passos Seguintes  

- [Compreender a hierarquia de armazenamento de ficheiros do Azure NetApp](azure-netapp-files-understand-storage-hierarchy.md)
- [Modelo de custo para os ficheiros do Azure NetApp](azure-netapp-files-cost-model.md)
