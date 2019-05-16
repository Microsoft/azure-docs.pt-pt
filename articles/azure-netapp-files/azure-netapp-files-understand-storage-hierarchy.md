---
title: O que é a hierarquia de armazenamento de ficheiros do Azure NetApp | Documentos da Microsoft
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
ms.date: 04/16/2019
ms.author: b-juche
ms.openlocfilehash: c2984e012ae83a8bc17d72ed4eac0c5c469c2694
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65522875"
---
# <a name="what-is-the-storage-hierarchy-of-azure-netapp-files"></a>O que é a hierarquia de armazenamento de ficheiros do Azure NetApp

Antes de criar um volume no Azure NetApp Files, tem de comprar e configurar um conjunto para capacidade aprovisionada.  Para configurar um conjunto de capacidade, precisa de ter uma conta NetApp. Compreender a hierarquia de armazenamento ajuda-o a configurar e gerir os recursos do Azure NetApp Files.

## <a name="azure_netapp_files_account"></a>Contas NetApp

- Uma conta NetApp serve de agrupamento administrativo dos conjuntos de capacidade constituintes.  
- Uma conta NetApp não é igual à sua conta de armazenamento do Azure geral. 
- Uma conta NetApp é regional no âmbito.   
- Pode ter várias contas NetApp numa região, mas cada uma está associada a uma única região.

## <a name="capacity_pools"></a>Conjuntos de capacidade

- Um conjunto de capacidade é medido pela sua capacidade aprovisionada.  
- A capacidade é aprovisionada pelos SKUs fixos que comprou (por exemplo, uma capacidade de 4 TiB).
- Um conjunto de capacidade apenas pode ter um nível de serviço.  
- Cada conjunto de capacidade pode pertencer a apenas uma conta de NetApp. No entanto, pode ter múltiplos conjuntos de capacidade numa conta do NetApp.  
- Um conjunto de capacidade não pode ser movido entre contas NetApp.   
  Por exemplo, no [Diagrama conceptual da hierarquia de armazenamento](#conceptual_diagram_of_storage_hierarchy) abaixo, não é possível mover o Conjunto de Capacidade 1 da conta NetApp E.U.A. Leste para a conta NetApp E.U.A. Oeste 2.  
- Não é possível eliminar um conjunto de capacidade até que todos os volumes dentro do conjunto de capacidade tem sido eliminados.

## <a name="volumes"></a>Volumes

- Um volume é medido ao consumo de capacidade de lógica e escalonável. 
- O consumo de capacidade de um volume é contabilizado para a capacidade aprovisionada do seu conjunto.
- Cada volume pertence a apenas um conjunto, mas um conjunto pode conter vários volumes. 
- Um volume não pode ser movido entre conjuntos de capacidade. <!--Within the same NetApp account, you can move a volume across pools.  -->   
  Por exemplo, no [diagrama Conceptual da hierarquia de armazenamento de](#conceptual_diagram_of_storage_hierarchy) abaixo, não é possível mover os volumes de capacidade do agrupamento de 1 a 2 de conjunto de capacidade.
- Não é possível eliminar um volume, até que todos os respetivos instantâneos tem sido eliminados.

## <a name="conceptual_diagram_of_storage_hierarchy"></a>Diagrama conceptual da hierarquia de armazenamento 
O exemplo seguinte mostra as relações da subscrição do Azure, contas NetApp, conjuntos de capacidade e volumes.   

![Diagrama conceptual da hierarquia de armazenamento](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>Passos Seguintes

- [Resource limits for Azure NetApp Files](azure-netapp-files-resource-limits.md) (Limites dos recursos do Azure NetApp Files)
- [Registre-se para os ficheiros do Azure NetApp](azure-netapp-files-register.md)
