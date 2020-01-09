---
title: Montar Azure NetApp Files volumes para máquinas virtuais
description: Saiba como montar ou desmontar um volume para máquinas virtuais do Windows ou máquinas virtuais do Linux no Azure.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: 3457adf67fa067a154eef008b08d8cfcc1d9eaa0
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551544"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Montar ou desmontar um volume para máquinas virtuais Windows ou Linux 

Você pode montar ou desmontar um volume para máquinas virtuais Windows ou Linux, conforme necessário.  As instruções de montagem para máquinas virtuais do Linux estão disponíveis no Azure NetApp Files.  

1. Clique na folha **volumes** e selecione o volume para o qual você deseja montar. 
2. Clique em **instruções de montagem** do volume selecionado e siga as instruções para montar o volume. 

    ![Instruções de montagem NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Instruções de montagem SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
    Se você estiver usando o NFSv 4.1, use o seguinte comando para montar o sistema de arquivos: `sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

3. Se você quiser que um volume NFS seja montado automaticamente quando uma VM do Azure for iniciada ou reiniciada, adicione uma entrada ao arquivo de `/etc/fstab` no host. 

    Por exemplo: `$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP` é o endereço IP do volume de Azure NetApp Files encontrado na folha de propriedades do volume.
    * `$FILEPATH` é o caminho de exportação do volume de Azure NetApp Files.
    * `$MOUNTPOINT` é o diretório criado no host do Linux usado para montar a exportação do NFS.

4. Se você quiser montar o volume para o Windows usando NFS:

    a. Monte o volume em uma VM UNIX ou Linux primeiro.  
    b. Execute um comando `chmod 777` ou `chmod 775` no volume.  
    c. Monte o volume por meio do cliente NFS no Windows.

## <a name="next-steps"></a>Passos seguintes

* [Configurar o domínio padrão do NFSv 4.1 para Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Perguntas frequentes sobre NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
