---
title: Qual é a hierarquia de armazenamento dos Ficheiros Azure NetApp ? Microsoft Docs
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
ms.date: 08/10/2020
ms.author: b-juche
ms.openlocfilehash: 91fecbc68efec1adcee9a2c4013dea46f6da86af
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88066479"
---
# <a name="what-is-the-storage-hierarchy-of-azure-netapp-files"></a>Qual é a hierarquia de armazenamento dos ficheiros Azure NetApp

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
- A capacidade é abasteceda pelos SKUs fixos que adquiriu (por exemplo, uma capacidade 4-TiB).
- Um conjunto de capacidade apenas pode ter um nível de serviço.  
- Cada pool de capacidade pode pertencer a apenas uma conta NetApp. No entanto, pode ter várias capacidades dentro de uma conta NetApp.  
- Um conjunto de capacidade não pode ser movido entre contas NetApp.   
  Por exemplo, no [Diagrama conceptual da hierarquia de armazenamento](#conceptual_diagram_of_storage_hierarchy) abaixo, não é possível mover o Conjunto de Capacidade 1 da conta NetApp E.U.A. Leste para a conta NetApp E.U.A. Oeste 2.  
- Um pool de capacidade não pode ser eliminado até que todos os volumes dentro do pool de capacidade tenham sido eliminados.

## <a name="volumes"></a><a name="volumes"></a>Volumes

- Um volume é medido pelo consumo de capacidade lógica e é escalável. 
- O consumo de capacidade de um volume é contabilizado para a capacidade aprovisionada do seu conjunto.
- Cada volume pertence a apenas um conjunto, mas um conjunto pode conter vários volumes. 

## <a name="conceptual-diagram-of-storage-hierarchy"></a><a name="conceptual_diagram_of_storage_hierarchy"></a>Diagrama conceptual da hierarquia de armazenamento 
O exemplo seguinte mostra as relações da subscrição do Azure, contas NetApp, conjuntos de capacidade e volumes.   

![Diagrama conceptual da hierarquia de armazenamento](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>Passos seguintes

- [Resource limits for Azure NetApp Files](azure-netapp-files-resource-limits.md) (Limites dos recursos do Azure NetApp Files)
- [Registar-se nos Azure NetApp Files](azure-netapp-files-register.md)
