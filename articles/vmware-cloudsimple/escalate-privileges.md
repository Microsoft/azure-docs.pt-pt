---
title: Solução Azure VMware by CloudSimple - Escalar privilégios CloudSimple
description: Descreve como escalar as permissões CloudSimple para executar funções administrativas no vCenter de Nuvem Privada
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 36c6969ed89d0bb9222f52aa81de0d4128b9e533
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77025338"
---
# <a name="escalate-cloudsimple-privileges-to-perform-administrative-functions-in-private-cloud-vcenter"></a>Escalar privilégios CloudSimple para executar funções administrativas em Private Cloud vCenter

A abordagem de privilégios CloudSimple foi concebida para dar aos utilizadores vCenter os privilégios de que necessitam para realizar operações normais. Em alguns casos, um utilizador pode exigir privilégios adicionais para executar uma determinada tarefa.  Pode escalar privilégios de um utilizador vCenter SSO por um período limitado.

As razões para a escalada dos privilégios podem incluir o seguinte:

* Configuração de fontes de identidade
* Gestão de utilizadores
* Eliminação do grupo portuário distribuído
* Instalação de soluções vCenter (como aplicações de backup)
* Criação de contas de serviço

> [!WARNING]
> As ações tomadas no estado privilegiado escalado podem afetar negativamente o seu sistema e podem fazer com que o seu sistema fique indisponível. Execute apenas as ações necessárias durante o período de escalada.

A partir do portal CloudSimple, aumente os [privilégios](escalate-private-cloud-privileges.md) para o utilizador local CloudOwner no vCenter SSO.  Só é possível escalar o privilégio do utilizador remoto se o fornecedor de identidade adicional estiver configurado no vCenter.  A escalada de privilégios envolve a adição do utilizador selecionado ao grupo de administradores integrados vSphere.  Só um utilizador pode ter privilégios escalonados.  Se precisar de escalar os privilégios de outro utilizador, primeiro desagrave os privilégios dos atuais utilizadores.

Os utilizadores de fontes de identidade adicionais devem ser adicionados como membros do grupo CloudOwner.

> [!CAUTION]
> Os novos utilizadores devem ser adicionados apenas ao *Cloud-Owner-Group*, *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group,* *Cloud-Global-Network-Admin-Group* ou, *Cloud-Global-VM-Admin-Group*.  Os utilizadores adicionados ao grupo *Administradores* serão removidos automaticamente.  Apenas as contas de serviço devem ser adicionadas ao grupo *de administradores* e as contas de serviço não devem ser utilizadas para iniciar sessão na VSphere web UI.

Durante o período de escalada, a CloudSimple utiliza monitorização automatizada com notificações de alerta associadas para identificar quaisquer alterações inadvertidas no ambiente.
