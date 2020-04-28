---
title: Configure a política de exportação para volume NFS - Ficheiros Azure NetApp
description: Descreve como configurar a política de exportação para controlar o acesso a um volume NFS utilizando ficheiros Azure NetApp
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: b96fca3a5627a1c6c96c8db5c1c209a51c5e102a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75551563"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Configurar a política de exportação para um volume NFS

Pode optar por configurar a política de exportação para controlo de acesso a um volume do Azure NetApp Files. A política de exportação do Azure NetApp Files suporta apenas volumes NFS.  Tanto a NFSv3 como a NFSv4 são apoiadas. 

## <a name="steps"></a>Passos 

1.  Clique na política de **exportação** a partir do painel de navegação azure NetApp Files. 

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

    ![Política de exportação](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>Passos seguintes 
* [Gerir volumes](azure-netapp-files-manage-volumes.md)
* [Montar ou desmontar um volume para máquinas virtuais](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Gerir instantâneos](azure-netapp-files-manage-snapshots.md)
