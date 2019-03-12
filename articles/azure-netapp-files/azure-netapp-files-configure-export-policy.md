---
title: Configurar a política de exportação para um volume do Azure NetApp Files | Microsoft Docs
description: Descreve como configurar a política de exportação para controlo de acesso a um volume do Azure NetApp Files
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
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 1098c5c2f42b242b3ba2f68ec7480de90dd7c22e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57766653"
---
# <a name="configure-export-policy-for-a-volume"></a>Configurar a política de exportação para um volume

Pode optar por configurar a política de exportação para controlo de acesso a um volume do Azure NetApp Files. 

## <a name="steps"></a>Passos 

1.  Clique no painel **Criar Política de Exportação** no painel Gerir Volume. 

2.  Especifique informações para os seguintes campos para criar uma regra de política de exportação:   
    *  **Índice**   
        Especifique o número de índice para a regra.  
        Uma política de exportação pode consistir em até cinco regras. As regras são avaliadas de acordo com a respetiva ordem na lista de números de índice. As regras com números de índice mais baixos são avaliadas primeiro. Por exemplo, a regra com o número de índice 1 é avaliada antes da regra com o número de índice 2. 

    * **Clientes Permitidos**   
        Especifique o valor num dos seguintes formatos:  
        * Endereço IPv4, por exemplo, `10.1.12.24` 
        * Endereço IPv4 com uma máscara de sub-rede expressa como número de bits, por exemplo, `10.1.12.10/4`

    * **Acesso**  
        Selecione um dos seguintes tipos de acesso:  
        * Sem Acesso 
        * Leitura e Escrita
        * Só de Leitura

    * **Protocolos**   
        Especifique o protocolo a usar para a política de exportação.   
        Atualmente, o Azure NetApp Files suporta apenas NFSv3.

    ![Política de exportação](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>Passos Seguintes 
* [Gerir volumes](azure-netapp-files-manage-volumes.md)
* [Montar ou desmontar um volume para máquinas virtuais](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Gerir instantâneos](azure-netapp-files-manage-snapshots.md)
