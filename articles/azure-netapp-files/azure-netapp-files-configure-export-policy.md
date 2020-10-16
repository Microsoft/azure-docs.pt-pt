---
title: Configure a política de exportação para o volume NFS - Azure NetApp Files
description: Descreve como configurar a política de exportação para controlar o acesso a um volume NFS utilizando ficheiros Azure NetApp
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 4a20a223932f82c80ad5831ef3a02bad803e26e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87533219"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Configurar a política de exportação para um volume NFS

Pode configurar a política de exportação para controlar o acesso a um volume de Ficheiros Azure NetApp. A política de exportação do Azure NetApp Files suporta volumes que utilizam o protocolo NFSv3 (NFSv3 e NFSv4.1) e o protocolo duplo (NFSv3 e SMB). 

Pode criar até cinco regras de política de exportação.

## <a name="steps"></a>Passos 

1.  Na página Volumes, selecione o volume para o qual pretende configurar a política de exportação e clique na **política de exportação**. 

    Pode também configurar a política de exportação durante a criação do volume.

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

    * **Ler e** **ler/escrever**  
        Se utilizar a encriptação Kerberos com NFSv4.1, siga as instruções na [encriptação Configure NFSv4.1 Kerberos](configure-kerberos-encryption.md).  Para o impacto de desempenho da Kerberos, consulte o [impacto de desempenho da Kerberos na NFSv4.1](configure-kerberos-encryption.md#kerberos_performance). 

        ![Opções de segurança Kerberos](../media/azure-netapp-files/kerberos-security-options.png) 

    * **Acesso à Raiz**  
        Especificar se a `root` conta pode aceder ao volume.  Por predefinição, o Root Access está definido para **On**, e a `root` conta tem acesso ao volume.

![Política de exportação](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 



## <a name="next-steps"></a>Passos seguintes 
* [Montar ou desmontar um volume para máquinas virtuais](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Gerir instantâneos](azure-netapp-files-manage-snapshots.md)
