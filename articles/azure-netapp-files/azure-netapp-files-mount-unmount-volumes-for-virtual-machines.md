---
title: Monte Azure NetApp Ficheiros volumes para máquinas virtuais
description: Aprenda a montar ou desmonte um volume para máquinas virtuais Windows ou máquinas virtuais Linux em Azure.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 04/30/2020
ms.openlocfilehash: b7586fcd0a262fa325e171480ec0becc297f65fe
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/27/2020
ms.locfileid: "85483589"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Montar ou desmontar um volume para máquinas virtuais Windows ou Linux 

Pode montar ou desmontar um volume para máquinas virtuais Windows ou Linux, se necessário.  As instruções de montagem das máquinas virtuais Linux estão disponíveis nos ficheiros Azure NetApp.  

> [!IMPORTANT] 
> Deve ter pelo menos uma política de exportação para poder aceder a um volume NFS.

1. Clique na lâmina **Volumes** e, em seguida, selecione o volume para o qual pretende montar. 
2. Clique **em montar as instruções** a partir do volume selecionado e, em seguida, siga as instruções para montar o volume. 

    ![Monte instruções NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Monte instruções SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
    Se estiver a utilizar o NFSv4.1, utilize o seguinte comando para montar o seu sistema de ficheiros:`sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

3. Se pretender ter um volume NFS montado automaticamente quando um VM Azure for iniciado ou reiniciado, adicione uma entrada `/etc/fstab` no ficheiro no anfitrião. 

    Por exemplo:`$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP`é o endereço IP do volume Azure NetApp Files encontrado na lâmina de propriedades de volume.
    * `$FILEPATH`é a via de exportação do volume Azure NetApp Files.
    * `$MOUNTPOINT`é o diretório criado no anfitrião Linux usado para montar a exportação NFS.

4. Se pretender montar o volume para o Windows utilizando NFS:

    a. Monte primeiro o volume num Unix ou Linux VM.  
    b. Executar um `chmod 777` ou comandar contra o `chmod 775` volume.  
    c. Monte o volume através do cliente NFS no Windows.

## <a name="next-steps"></a>Passos seguintes

* [Configurar o domínio predefinido do NFSv 4.1 para o Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [FAQ sobre NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
* [Visão geral do sistema de ficheiros de rede](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview)
