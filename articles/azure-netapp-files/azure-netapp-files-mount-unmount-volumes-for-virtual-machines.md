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
ms.openlocfilehash: 49c5fc4a9d4fed2176baf220e3fcc8f887757a36
ms.sourcegitcommit: f226cdd6406372b5693d46b6d04900f2f0cda4e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2019
ms.locfileid: "73906222"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Montar ou desmontar um volume para máquinas virtuais Windows ou Linux 

Você pode montar ou desmontar um volume para máquinas virtuais Windows ou Linux, conforme necessário.  As instruções de montagem para máquinas virtuais do Linux estão disponíveis no Azure NetApp Files.  

1. Clique na folha **volumes** e selecione o volume para o qual você deseja montar. 
2. Clique em **instruções de montagem** do volume selecionado e siga as instruções para montar o volume. 

    ![Instruções de montagem NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Instruções de montagem SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
Se você estiver usando o NFSv 4.1, use o seguinte comando para montar o sistema de arquivos:  

`sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

Consulte [Configurar o domínio padrão do nfsv 4.1 para Azure NetApp files](azure-netapp-files-configure-nfsv41-domain.md).