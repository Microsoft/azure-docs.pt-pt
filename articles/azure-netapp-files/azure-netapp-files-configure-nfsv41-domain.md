---
title: Configure domínio padrão NFSv4.1 para Ficheiros Azure NetApp / Microsoft Docs
description: Descreve como configurar o cliente NFS para usar NFSv4.1 com Ficheiros Azure NetApp.
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
ms.openlocfilehash: 77178a23206eadae941794c92b8dd99fe2ca1e05
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73906289"
---
# <a name="configure-nfsv41-default-domain-for-azure-netapp-files"></a>Configurar o domínio predefinido do NFSv 4.1 para o Azure NetApp Files

O NFSv4 introduz o conceito de domínio de autenticação. Atualmente, o Azure NetApp Files suporta o mapeamento de utilizadores exclusivamente de raiz do serviço para o cliente NFS. Para utilizar a funcionalidade NFSv4.1 com ficheiros Azure NetApp, precisa de atualizar o cliente NFS.

## <a name="default-behavior-of-usergroup-mapping"></a>Comportamento padrão do mapeamento do utilizador/grupo

O mapeamento de `nobody` raiz não se aplica ao utilizador `localdomain`porque o domínio NFSv4 está definido para . Quando montar um volume DeNFSv4.1 do Azure NetApp Files, verá permissões de ficheiros da seguinte forma:  

![Comportamento padrão do mapeamento do utilizador/grupo para NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-default-behavior-user-group-mapping.png)

Como mostra o exemplo acima, o utilizador `file1` deve ser, `root`mas mapeia por `nobody` padrão.  Este artigo mostra-lhe `file1` como `root`definir o utilizador para .  

## <a name="steps"></a>Passos 

1. Editar `/etc/idmapd.conf` o ficheiro no cliente da NFS.   
    Descodere a `#Domain` linha `#` (isto é, retire `localdomain` a `defaultv4iddomain.com`linha) e mude o valor para . 

    Configuração inicial: 
    
    ![Configuração inicial para NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-initial-config.png)

    Configuração atualizada:
    
    ![Configuração atualizada para NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-updated-config.png)

2. Desmonte quaisquer volumes NFS atualmente montados.
3. Atualize `/etc/idmapd.conf` o ficheiro.
4. Reinicie `rpcbind` o serviço`service rpcbind restart`no seu anfitrião ( ou simplesmente reinicie o hospedeiro).
5. Monte os volumes NFS conforme necessário.   

    Consulte [o Monte ou desmonte um volume para máquinas virtuais Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md). 

O exemplo seguinte mostra a alteração resultante do utilizador/grupo: 

![Configuração resultante para NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-resulting-config.png)

Como o exemplo mostra, o utilizador/grupo passou agora de `nobody` . `root`

## <a name="behavior-of-other-non-root-users-and-groups"></a>Comportamento de outros utilizadores e grupos (não-raiz)

O Azure NetApp Files suporta utilizadores locais (utilizadores criados localmente num hospedeiro) que tenham permissões associadas a ficheiros ou pastas em volumes NFSv4.1. No entanto, o serviço não suporta atualmente mapear os utilizadores/grupos em vários nós. Por isso, os utilizadores criados num dos anfitriões não mapeiam por padrão os utilizadores criados noutro anfitrião. 

No exemplo `Host1` seguinte, tem três contas de`testuser01` `testuser02`utilizador `testuser03`de teste existentes ( , , ): 

![Configuração resultante para NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-host1-users.png)

Em `Host2`, note que as contas de utilizador de teste não foram criadas, mas o mesmo volume é montado em ambos os anfitriões:

![Configuração resultante para NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-host2-users.png)

## <a name="next-step"></a>Passo seguinte 

[Montar ou desmontar um volume para máquinas virtuais Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)

