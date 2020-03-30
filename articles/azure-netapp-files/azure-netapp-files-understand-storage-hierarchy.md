---
title: Qual é a hierarquia de armazenamento dos Ficheiros Azure NetApp [ Microsoft Docs
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
ms.date: 02/27/2020
ms.author: b-juche
ms.openlocfilehash: 70d3a2a501952a5e20b1ff8e99f48f4d7aefce8d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "78163969"
---
# <a name="what-is-the-storage-hierarchy-of-azure-netapp-files"></a>Qual é a hierarquia de armazenamento dos Ficheiros Azure NetApp

Antes de criar um volume no Azure NetApp Files, tem de comprar e configurar um conjunto para capacidade aprovisionada.  Para configurar um conjunto de capacidade, precisa de ter uma conta NetApp. Compreender a hierarquia de armazenamento ajuda-o a configurar e gerir os recursos do Azure NetApp Files.

> [!IMPORTANT] 
> Atualmente, o Azure NetApp Files não suporta a migração de recursos entre subscrições.

## <a name="netapp-accounts"></a><a name="azure_netapp_files_account"></a>Contas NetApp

- Uma conta NetApp serve de agrupamento administrativo dos conjuntos de capacidade constituintes.  
- Uma conta NetApp não é igual à sua conta de armazenamento do Azure geral. 
- Uma conta NetApp é regional no âmbito.   
- Pode ter várias contas NetApp numa região, mas cada uma está associada a uma única região.

## <a name="capacity-pools"></a><a name="capacity_pools"></a>Conjuntos de capacidade

- Um conjunto de capacidade é medido pela sua capacidade aprovisionada.  
- A capacidade é aprovisionada pelas SKUs fixas que adquiriu (por exemplo, uma capacidade de 4 TiB).
- Um conjunto de capacidade apenas pode ter um nível de serviço.  
- Cada piscina de capacidade pode pertencer a apenas a uma conta NetApp. No entanto, pode ter vários pools de capacidade dentro de uma conta NetApp.  
- Um conjunto de capacidade não pode ser movido entre contas NetApp.   
  Por exemplo, no [Diagrama conceptual da hierarquia de armazenamento](#conceptual_diagram_of_storage_hierarchy) abaixo, não é possível mover o Conjunto de Capacidade 1 da conta NetApp E.U.A. Leste para a conta NetApp E.U.A. Oeste 2.  
- Um conjunto de capacidades não pode ser eliminado até que todos os volumes dentro do conjunto de capacidades tenham sido eliminados.

## <a name="volumes"></a><a name="volumes"></a>Volumes

- Um volume é medido pelo consumo lógico de capacidade e é escalável. 
- O consumo de capacidade de um volume é contabilizado para a capacidade aprovisionada do seu conjunto.
- Cada volume pertence a apenas um conjunto, mas um conjunto pode conter vários volumes. 
- Um volume não pode ser movido através de piscinas de capacidade. <!--Within the same NetApp account, you can move a volume across pools.  -->   
  Por exemplo, no [diagrama conceptual da hierarquia de armazenamento](#conceptual_diagram_of_storage_hierarchy) abaixo, não é possível mover os volumes do Pool 1 de Capacidade para o Pool de Capacidade 2.
- Um volume não pode ser eliminado até que todos os seus instantâneos tenham sido eliminados.

## <a name="conceptual-diagram-of-storage-hierarchy"></a><a name="conceptual_diagram_of_storage_hierarchy"></a>Diagrama conceptual da hierarquia de armazenamento 
O exemplo seguinte mostra as relações da subscrição do Azure, contas NetApp, conjuntos de capacidade e volumes.   

![Diagrama conceptual da hierarquia de armazenamento](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>Passos seguintes

- [Limites de recurso para os Azure NetApp Files](azure-netapp-files-resource-limits.md)
- [Registar nos Azure NetApp Files](azure-netapp-files-register.md)
