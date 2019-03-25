---
title: Configurar a política de exportação para um volume NFS usando arquivos de NetApp do Azure | Documentos da Microsoft
description: Descreve como configurar a política de exportação para controlar o acesso a um volume NFS usando arquivos de NetApp do Azure
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
ms.date: 03/20/2019
ms.author: b-juche
ms.openlocfilehash: 8cda5921a1aec86d28beabbd9cea5b07a203a0e8
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401491"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Configurar a política de exportação para um volume NFS

Pode optar por configurar a política de exportação para controlo de acesso a um volume do Azure NetApp Files. Política de exportação é o suporte apenas para volumes NFS. 

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
        Atualmente, a política de exportação de ficheiros do Azure NetApp suporta apenas NFSv3.

    ![Política de exportação](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>Passos Seguintes 
* [Gerir volumes](azure-netapp-files-manage-volumes.md)
* [Montar ou desmontar um volume para máquinas virtuais](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Gerir instantâneos](azure-netapp-files-manage-snapshots.md)
