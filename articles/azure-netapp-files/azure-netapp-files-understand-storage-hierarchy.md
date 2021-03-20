---
title: Hierarquia de armazenamento de ficheiros Azure NetApp | Microsoft Docs
description: Descreve a hierarquia de armazenamento, incluindo contas, conjuntos de capacidade e volumes do Azure NetApp Files.
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
ms.topic: overview
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 435d74e771a9d887c87c9d10e6b525ac77cf97e8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91278333"
---
# <a name="storage-hierarchy-of-azure-netapp-files"></a>Hierarquia de armazenamento do Azure NetApp Files

Antes de criar um volume no Azure NetApp Files, tem de comprar e configurar um conjunto para capacidade aprovisionada.  Para configurar um conjunto de capacidade, precisa de ter uma conta NetApp. Compreender a hierarquia de armazenamento ajuda-o a configurar e gerir os recursos do Azure NetApp Files.

> [!IMPORTANT] 
> Atualmente, o Azure NetApp Files não suporta a migração de recursos entre subscrições.

## <a name="netapp-accounts"></a><a name="azure_netapp_files_account"></a>Contas NetApp

- Uma conta NetApp serve de agrupamento administrativo dos conjuntos de capacidade constituintes.  
- Uma conta NetApp não é igual à sua conta de armazenamento do Azure geral. 
- Uma conta NetApp é regional no âmbito.   
- Pode ter várias contas NetApp numa região, mas cada uma está associada a uma única região.

## <a name="capacity-pools"></a><a name="capacity_pools"></a>Conjuntos de capacidade

Compreender como as piscinas funcionam ajuda-o a selecionar os tipos de piscina de capacidade certa para as suas necessidades de armazenamento. 

### <a name="general-rules-of-capacity-pools"></a>Regras gerais de agrupamentos de capacidade

- Um conjunto de capacidade é medido pela sua capacidade aprovisionada.   
    Consulte [os tipos QoS](#qos_types) para obter informações adicionais.  
- A capacidade é abasteceda pelos SKUs fixos que adquiriu (por exemplo, uma capacidade 4-TiB).
- Um conjunto de capacidade apenas pode ter um nível de serviço.  
- Cada pool de capacidade pode pertencer a apenas uma conta NetApp. No entanto, pode ter várias capacidades dentro de uma conta NetApp.  
- Um conjunto de capacidade não pode ser movido entre contas NetApp.   
  Por exemplo, no [Diagrama conceptual da hierarquia de armazenamento](#conceptual_diagram_of_storage_hierarchy) abaixo, não é possível mover o Conjunto de Capacidade 1 da conta NetApp E.U.A. Leste para a conta NetApp E.U.A. Oeste 2.  
- Um pool de capacidade não pode ser eliminado até que todos os volumes dentro do pool de capacidade tenham sido eliminados.

### <a name="quality-of-service-qos-types-for-capacity-pools"></a><a name="qos_types"></a>Tipos de qualidade de serviço (QoS) para piscinas de capacidade

O tipo QoS é um atributo de um pool de capacidade. O Azure NetApp Files fornece dois tipos de qoS de piscinas de capacidade. 

- *Automático (ou automático)* Tipo QoS  

    Quando cria um pool de capacidade, o tipo QoS predefinido é automático.

    Num pool de capacidade de QoS automático, a produção é atribuída automaticamente aos volumes da piscina, proporcional à quota de tamanho atribuída aos volumes. 

    A produção máxima atribuída a um volume depende do nível de serviço do pool de capacidade e da quota de dimensão do volume. Consulte [os níveis de serviço dos ficheiros Azure NetApp,](azure-netapp-files-service-levels.md) por exemplo, cálculo.

- <a name="manual_qos_type"></a>*Manual* Tipo QoS  

     > [!IMPORTANT] 
     > A utilização do tipo QoS manual para um pool de capacidade requer registo.  Consulte [Gerir uma piscina manual de capacidade QoS](manage-manual-qos-capacity-pool.md).  

    Tem a opção de utilizar o tipo QoS manual para uma piscina de capacidade.

    Num pool manual de capacidade QoS, pode atribuir a capacidade e a produção para um volume de forma independente. A produção total de todos os volumes criados com um pool manual de capacidade QoS é limitada pela produção total da piscina.  É determinado pela combinação do tamanho da piscina e da produção de nível de serviço. 

    Por exemplo, um pool de capacidade 4-TiB com o nível de serviço Ultra tem uma capacidade total de produção de 512 MiB/s (4 TiB x 128 MiB/s/TiB) disponível para os volumes.


## <a name="volumes"></a><a name="volumes"></a>Volumes

- Um volume é medido pelo consumo de capacidade lógica e é escalável. 
- O consumo de capacidade de um volume é contabilizado para a capacidade aprovisionada do seu conjunto.
- O consumo de produção de um volume conta com a produção disponível da sua piscina. Consulte [o tipo QoS Manual](#manual_qos_type).
- Cada volume pertence a apenas um conjunto, mas um conjunto pode conter vários volumes. 

## <a name="conceptual-diagram-of-storage-hierarchy"></a><a name="conceptual_diagram_of_storage_hierarchy"></a>Diagrama conceptual da hierarquia de armazenamento 
O exemplo seguinte mostra as relações da subscrição do Azure, contas NetApp, conjuntos de capacidade e volumes.   

![Diagrama conceptual da hierarquia de armazenamento](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>Passos seguintes

- [Resource limits for Azure NetApp Files](azure-netapp-files-resource-limits.md) (Limites dos recursos do Azure NetApp Files)
- [Registar-se nos Azure NetApp Files](azure-netapp-files-register.md)
- [Níveis de serviços do Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Considerações de desempenho para o Azure NetApp Files](azure-netapp-files-performance-considerations.md)
- [Configurar um conjunto de capacidade](azure-netapp-files-set-up-capacity-pool.md)
- [Gerir um conjunto de capacidade QoS manual](manage-manual-qos-capacity-pool.md)
