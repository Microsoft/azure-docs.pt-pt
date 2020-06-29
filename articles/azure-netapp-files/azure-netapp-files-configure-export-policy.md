---
title: Configure a política de exportação para o volume NFS - Azure NetApp Files
description: Descreve como configurar a política de exportação para controlar o acesso a um volume NFS utilizando ficheiros Azure NetApp
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 10/18/2019
ms.openlocfilehash: e59648ee76b6715029c690329cbf8f4f1eee7243
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/27/2020
ms.locfileid: "85483657"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Configurar a política de exportação para um volume NFS

Pode optar por configurar a política de exportação para controlo de acesso a um volume do Azure NetApp Files. A política de exportação do Azure NetApp Files suporta apenas volumes NFS.  Tanto a NFSv3 como a NFSv4 são apoiadas. 

## <a name="steps"></a>Passos 

1.  Clique na política de **exportação** a partir do painel de navegação dos Ficheiros Azure NetApp. 

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
