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
ms.openlocfilehash: d3f268a4ce2660350055367770e987a06828e2d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101740125"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Configurar a política de exportação para um volume NFS

Pode configurar a política de exportação para controlar o acesso a um volume de Ficheiros Azure NetApp. A política de exportação do Azure NetApp Files suporta volumes que utilizam o protocolo NFSv3 (NFSv3 e NFSv4.1) e o protocolo duplo (NFSv3 e SMB). 

Pode criar até cinco regras de política de exportação.

## <a name="configure-the-policy"></a>Configurar a política 

1.  Na página **Volumes,** selecione o volume para o qual pretende configurar a política de exportação e, em seguida, selecione **a política de exportação**. Pode também configurar a política de exportação durante a criação do volume.

2.  Para criar uma regra de política de exportação, especifique as seguintes informações:   
    * **Índice**: Especificar o número de índice para a regra.  
      
      Uma política de exportação pode consistir em até cinco regras. As regras são avaliadas de acordo com a respetiva ordem na lista de números de índice. As regras com números de índice mais baixos são avaliadas primeiro. Por exemplo, a regra com o número de índice 1 é avaliada antes da regra com o número de índice 2. 

    * **Clientes Permitidos**: Especifique o valor num dos seguintes formatos:  
      * Endereço IPv4. Exemplo: `10.1.12.24`
      * Endereço IPv4 com uma máscara de sub-rede expressa em vários pedaços. Exemplo: `10.1.12.10/4`
      * Endereços IP separados por vírgula. Pode introduzir vários IPs de hospedeiro numa única regra separando-os com vírgulas. Exemplo: `10.1.12.25,10.1.12.28,10.1.12.29`

    * **Acesso**: Selecione um dos seguintes tipos de acesso:  
      * Sem Acesso 
      * Leitura e Escrita
      * Só de Leitura

    * **Leia apenas** e **Leia/Escreva**: Se utilizar a encriptação Kerberos com NFSv4.1, siga as instruções na [encriptação Configure NFSv4.1 Kerberos](configure-kerberos-encryption.md).  Para o impacto de desempenho da Kerberos, consulte [o impacto de desempenho da Kerberos nos volumes NFSv4.1](performance-impact-kerberos.md). 

      ![Opções de segurança Kerberos](../media/azure-netapp-files/kerberos-security-options.png) 

    * **Acesso à Raiz**: Especifique se a `root` conta pode aceder ao volume.  Por predefinição, o Root Access está definido para **On**, e a `root` conta tem acesso ao volume.

      ![Política de exportação](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 

## <a name="next-steps"></a>Passos seguintes 
* [Montar ou desmontar um volume para máquinas virtuais](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Gerir instantâneos](azure-netapp-files-manage-snapshots.md)
