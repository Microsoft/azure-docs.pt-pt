---
title: Azure VMware Solutions (AVS) - Escalar privilégios AVS
description: Descreve como escalar as permissões avs para executar funções administrativas no VCenter De Nuvem Privada AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 90dd61fc9856978bab0b68de19d48493a8e0c5fd
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025338"
---
# <a name="escalate-avs-privileges-to-perform-administrative-functions-in-avs-private-cloud-vcenter"></a>Escalar privilégios AVS para executar funções administrativas em AVS Private Cloud vCenter

A abordagem de privilégios AVS foi concebida para dar aos utilizadores vCenter os privilégios de que necessitam para realizar operações normais. Em alguns casos, um usuário pode exigir privilégios adicionais para executar uma tarefa específica. Você pode escalonar privilégios de um usuário do SSO do vCenter por um período limitado.

Os motivos para escalonar privilégios podem incluir o seguinte:

* Configuração de fontes de identidade
* Gestão de utilizadores
* Exclusão do grupo de portas distribuídas
* Instalando soluções do vCenter (como aplicativos de backup)
* Criando contas de serviço

> [!WARNING]
> As ações executadas no estado de privilégio escalonado podem afetar negativamente o sistema e podem fazer com que o sistema fique indisponível. Execute apenas as ações necessárias durante o período de escalonamento.

A partir do portal AVS, aumente os [privilégios](escalate-private-cloud-privileges.md) para o utilizador local CloudOwner no vCenter SSO. Você pode escalar o privilégio de usuário remoto somente se o provedor de identidade adicional estiver configurado no vCenter. O escalonamento de privilégios envolve a adição do usuário selecionado ao grupo de administradores internos vSphere. Somente um usuário pode ter privilégios escalonados. Se você precisar escalonar os privilégios de outro usuário, primeiro desescale os privilégios dos usuários atuais.

Os usuários de fontes de identidade adicionais devem ser adicionados como membros do grupo CloudOwner.

> [!CAUTION]
> Novos usuários devem ser adicionados somente a *Cloud-Owner-Group*, *Cloud-global-cluster-admin-Group*, *Cloud-Global-Storage-admin-Group*, *Cloud-Global-Network-admin-Group* ou, *Cloud-global-VM-admin-Group*.  Os usuários adicionados ao grupo de *Administradores* serão removidos automaticamente.  Somente as contas de serviço devem ser adicionadas ao grupo de *Administradores* e as contas de serviço não devem ser usadas para entrar na interface do usuário da Web do amvSphere.
Durante o período de escalada, a AVS utiliza monitorização automatizada com notificações de alerta associadas para identificar quaisquer alterações inadvertidas no ambiente.
