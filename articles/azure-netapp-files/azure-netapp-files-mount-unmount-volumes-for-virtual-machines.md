---
title: Montar ou desmontar um volume de Azure NetApp Files para máquinas virtuais Windows ou Linux | Microsoft Docs
description: Descreve como montar ou desmontar um volume para máquinas virtuais ou máquinas virtuais Linux.
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
ms.date: 11/08/2019
ms.author: b-juche
ms.openlocfilehash: 84496fbc8a415171172d0a138f647ecb0310b6c7
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173588"
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

## <a name="next-steps"></a>Passos seguintes

* [Configurar o domínio padrão do NFSv 4.1 para Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Perguntas frequentes sobre NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
