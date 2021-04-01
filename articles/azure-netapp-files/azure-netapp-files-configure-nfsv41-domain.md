---
title: Configure o domínio NFSv4.1 padrão para ficheiros Azure NetApp | Microsoft Docs
description: Descreve como configurar o cliente NFS para a utilização de NFSv4.1 com ficheiros Azure NetApp.
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/14/2020
ms.author: b-juche
ms.openlocfilehash: c3c853190d5f63bbe9012727d8b7b7ac91da135f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92072157"
---
# <a name="configure-nfsv41-default-domain-for-azure-netapp-files"></a>Configurar o domínio predefinido do NFSv 4.1 para o Azure NetApp Files

O NFSv4 introduz o conceito de domínio de autenticação. A Azure NetApp Files suporta atualmente o mapeamento de utilizadores apenas com raiz do serviço para o cliente NFS. Para utilizar a funcionalidade NFSv4.1 com ficheiros Azure NetApp, é necessário atualizar o cliente NFS.

## <a name="default-behavior-of-usergroup-mapping"></a>Comportamento predefinido do mapeamento utilizador/grupo

O mapeamento de raiz é padrão para o `nobody` utilizador porque o domínio NFSv4 é definido por `localdomain` padrão. Quando montar um volume NFSv4.1 de Ficheiros Azure NetApp como raiz, verá as permissões de ficheiros da seguinte forma:  

![Comportamento predefinido do mapeamento do utilizador/grupo para NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-default-behavior-user-group-mapping.png)

Como mostra o exemplo acima, o utilizador `file1` deve ser , mas `root` mapeia `nobody` por padrão.  Este artigo mostra-lhe como definir o `file1` utilizador `root` alterando a `idmap Domain` definição para `defaultv4iddomain.com` .  

## <a name="steps"></a>Passos 

1. Edite o `/etc/idmapd.conf` ficheiro no cliente da NFS.   
    Descomprimir a linha `#Domain` (isto é, retirar `#` a linha) e alterar o valor `localdomain` para `defaultv4iddomain.com` . 

    Configuração inicial: 
    
    ![Configuração inicial para NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-initial-config.png)

    Configuração atualizada:
    
    ![Configuração atualizada para NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-updated-config.png)

2. Desmonte os volumes NFS atualmente montados.
3. Atualize o `/etc/idmapd.conf` ficheiro.
4. Reinicie o `rpcbind` serviço no seu anfitrião ou simplesmente `service rpcbind restart` reinicie o anfitrião.
5. Monte os volumes NFS conforme necessário.   

    Consulte [o Mount ou desmonte um volume para máquinas virtuais Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md). 

O exemplo a seguir mostra a alteração utilizador/grupo resultante: 

![Screenshot que mostra um exemplo da alteração utilizador/grupo resultante.](../media/azure-netapp-files/azure-netapp-files-nfsv41-resulting-config.png)

Como mostra o exemplo, o utilizador/grupo passou agora a ser alterado `nobody` de `root` .

## <a name="behavior-of-other-non-root-users-and-groups"></a>Comportamento de outros utilizadores e grupos (não-raiz)

O Azure NetApp Files suporta utilizadores locais (utilizadores criados localmente num anfitrião) que têm permissões associadas a ficheiros ou pastas em volumes NFSv4.1. No entanto, o serviço não suporta atualmente o mapeamento dos utilizadores/grupos em vários nós. Portanto, os utilizadores criados num hospedeiro não mapeiam por padrão para os utilizadores criados noutro hospedeiro. 

No exemplo seguinte, `Host1` tem três contas de utilizador de teste existentes ( , `testuser01` , `testuser02` `testuser03` , 

![Screenshot que mostra que o Host1 tem três contas de utilizador de teste existentes.](../media/azure-netapp-files/azure-netapp-files-nfsv41-host1-users.png)

Em `Host2` , note que as contas de utilizador do teste não foram criadas, mas o mesmo volume é montado em ambos os anfitriões:

![Configuração resultante para NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-host2-users.png)

## <a name="next-step"></a>Passo seguinte 

[Montar ou desmontar um volume para máquinas virtuais Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)

