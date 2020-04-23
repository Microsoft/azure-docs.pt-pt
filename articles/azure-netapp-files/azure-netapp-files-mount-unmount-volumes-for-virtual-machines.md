---
title: Volumes de ficheiros Mount Azure NetApp para máquinas virtuais
description: Aprenda a montar ou desmontar um volume para máquinas virtuais windows ou máquinas virtuais Linux em Azure.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: c439ff8df95d759e96d2fc82356bda8551507e8d
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084945"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Montar ou desmontar um volume para máquinas virtuais Windows ou Linux 

Pode montar ou desmontar um volume para máquinas virtuais Windows ou Linux, se necessário.  As instruções de montagem para máquinas virtuais Linux estão disponíveis nos Ficheiros Azure NetApp.  

1. Clique na lâmina **Volumes** e, em seguida, selecione o volume para o qual pretende montar. 
2. Clique nas **instruções** do Monte a partir do volume selecionado e, em seguida, siga as instruções para montar o volume. 

    ![Instruções de montagem NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Instruções de montagem SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
    Se estiver a utilizar o NFSv4.1, utilize o seguinte comando para montar o seu sistema de ficheiros:`sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

3. Se pretender ter um volume NFS montado automaticamente quando um VM Azure é iniciado `/etc/fstab` ou reiniciado, adicione uma entrada no ficheiro no hospedeiro. 

    Por exemplo:`$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP`é o endereço IP do volume de Ficheiros Azure NetApp encontrado na lâmina de propriedades de volume.
    * `$FILEPATH`é a via de exportação do volume de Ficheiros Azure NetApp.
    * `$MOUNTPOINT`é o diretório criado no hospedeiro Linux usado para montar a exportação nFS.

4. Se pretender montar o volume para Windows utilizando NFS:

    a. Monte primeiro o volume num VM Unix ou Linux.  
    b. Executar `chmod 777` um `chmod 775` ou comandar contra o volume.  
    c. Monte o volume através do cliente NFS no Windows.

## <a name="next-steps"></a>Passos seguintes

* [Configurar o domínio predefinido do NFSv 4.1 para o Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Perguntas frequentes nfs](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
* [Visão geral do sistema de ficheiros de rede](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview)
