---
title: Compreender a hierarquia de armazenamento do Azure NetApp Files | Microsoft Docs
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
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: b-juche
ms.openlocfilehash: 3df092377a29492facd972745933cd5f4a1ae3bf
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59275526"
---
# <a name="understand-the-storage-hierarchy-of-azure-netapp-files"></a>Compreender a hierarquia de armazenamento do Azure NetApp Files

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

## <a name="volumes"></a>Volumes

- Um volume é medido ao consumo de capacidade de lógica e escalonável. 
- O consumo de capacidade de um volume é contabilizado para a capacidade aprovisionada do seu conjunto.
- Cada volume pertence a apenas um conjunto, mas um conjunto pode conter vários volumes. 
- Na mesma conta NetApp, é possível mover um volume entre vários conjuntos.    
  Por exemplo, no [Diagrama Conceptual da hierarquia de armazenamento](#conceptual_diagram_of_storage_hierarchy) abaixo, é possível mover os volumes do Conjunto de Capacidade 1 para o Conjunto de Capacidade 2.

## <a name="conceptual_diagram_of_storage_hierarchy"></a>Diagrama conceptual da hierarquia de armazenamento 
O exemplo seguinte mostra as relações da subscrição do Azure, contas NetApp, conjuntos de capacidade e volumes.   

![Diagrama conceptual da hierarquia de armazenamento](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>Passos Seguintes

- [Limites de recurso para os Azure NetApp Files](azure-netapp-files-resource-limits.md)
- [Registar nos Azure NetApp Files](azure-netapp-files-register.md)
