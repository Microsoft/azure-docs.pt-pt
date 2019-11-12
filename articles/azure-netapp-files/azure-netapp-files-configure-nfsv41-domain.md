---
title: Configurar o domínio padrão do NFSv 4.1 para Azure NetApp Files | Microsoft Docs
description: Descreve como configurar o cliente NFS para usar o NFSv 4.1 com o Azure NetApp Files.
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
ms.sourcegitcommit: f226cdd6406372b5693d46b6d04900f2f0cda4e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2019
ms.locfileid: "73906289"
---
# <a name="configure-nfsv41-default-domain-for-azure-netapp-files"></a>Configurar o domínio padrão do NFSv 4.1 para Azure NetApp Files

NFSv4 introduz o conceito de domínio de autenticação. O Azure NetApp Files atualmente dá suporte ao mapeamento de usuário somente raiz do serviço para o cliente NFS. Para usar a funcionalidade NFSv 4.1 com Azure NetApp Files, você precisa atualizar o cliente NFS.

## <a name="default-behavior-of-usergroup-mapping"></a>Comportamento padrão do mapeamento de usuário/grupo

O mapeamento de raiz usa como padrão o usuário `nobody` porque o domínio NFSv4 está definido como `localdomain`. Ao montar um volume Azure NetApp Files NFSv 4.1 como raiz, você verá permissões de arquivo da seguinte maneira:  

![Comportamento padrão do mapeamento de usuário/grupo para NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-default-behavior-user-group-mapping.png)

Como mostra o exemplo acima, o usuário para `file1` deve ser `root`, mas ele é mapeado para `nobody` por padrão.  Este artigo mostra como definir o `file1` usuário para `root`.  

## <a name="steps"></a>Passos 

1. Edite o arquivo de `/etc/idmapd.conf` no cliente NFS.   
    Remova a marca de comentário da linha `#Domain` (ou seja, remove o `#` da linha) e altere o valor `localdomain` para `defaultv4iddomain.com`. 

    Configuração inicial: 
    
    ![Configuração inicial do NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-initial-config.png)

    Configuração atualizada:
    
    ![Configuração atualizada para o NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-updated-config.png)

2. Desmonte todos os volumes NFS montados no momento.
3. Atualize o arquivo de `/etc/idmapd.conf`.
4. Reinicie o serviço de `rpcbind` em seu host (`service rpcbind restart`) ou simplesmente reinicialize o host.
5. Monte os volumes de NFS conforme necessário.   

    Confira [montar ou desmontar um volume para máquinas virtuais Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md). 

O exemplo a seguir mostra a alteração de usuário/grupo resultante: 

![Configuração resultante para NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-resulting-config.png)

Como mostra o exemplo, o usuário/grupo foi alterado de `nobody` para `root`.

## <a name="behavior-of-other-non-root-users-and-groups"></a>Comportamento de outros usuários e grupos (não raiz)

O Azure NetApp Files dá suporte a usuários locais (usuários criados localmente em um host) que têm permissões associadas a arquivos ou pastas nos volumes NFSv 4.1. No entanto, no momento, o serviço não dá suporte ao mapeamento de usuários/grupos em vários nós. Portanto, os usuários criados em um host não são mapeados por padrão para os usuários criados em outro host. 

No exemplo a seguir, `Host1` tem três contas de usuário de teste existentes (`testuser01`, `testuser02``testuser03`): 

![Configuração resultante para NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-host1-users.png)

No `Host2`, observe que as contas de usuário de teste não foram criadas, mas o mesmo volume é montado em ambos os hosts:

![Configuração resultante para NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-host2-users.png)

## <a name="next-step"></a>Passo seguinte 

[Montar ou desmontar um volume para máquinas virtuais Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)

