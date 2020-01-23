---
title: Solução do Azure VMware por CloudSimple-escalonar privilégios de CloudSimple
description: Descreve como escalonar permissões de CloudSimple para executar funções administrativas no vCenter de nuvem privada
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 36c6969ed89d0bb9222f52aa81de0d4128b9e533
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76542853"
---
# <a name="escalate-cloudsimple-privileges-to-perform-administrative-functions-in-private-cloud-vcenter"></a>Escalonar privilégios de CloudSimple para executar funções administrativas no vCenter de nuvem privada

A abordagem de privilégios CloudSimple foi projetada para dar aos usuários do vCenter os privilégios necessários para executar operações normais. Em alguns casos, um usuário pode exigir privilégios adicionais para executar uma tarefa específica.  Você pode escalonar privilégios de um usuário do SSO do vCenter por um período limitado.

Os motivos para escalonar privilégios podem incluir o seguinte:

* Configuração de fontes de identidade
* Gestão de utilizadores
* Exclusão do grupo de portas distribuídas
* Instalando soluções do vCenter (como aplicativos de backup)
* Criando contas de serviço

> [!WARNING]
> As ações executadas no estado de privilégio escalonado podem afetar negativamente o sistema e podem fazer com que o sistema fique indisponível. Execute apenas as ações necessárias durante o período de escalonamento.

No portal do CloudSimple, [escalonar privilégios](escalate-private-cloud-privileges.md) para o usuário local CLOUDOWNER no SSO do vCenter.  Você pode escalar o privilégio de usuário remoto somente se o provedor de identidade adicional estiver configurado no vCenter.  O escalonamento de privilégios envolve a adição do usuário selecionado ao grupo de administradores internos vSphere.  Somente um usuário pode ter privilégios escalonados.  Se você precisar escalonar os privilégios de outro usuário, primeiro desescale os privilégios dos usuários atuais.

Os usuários de fontes de identidade adicionais devem ser adicionados como membros do grupo CloudOwner.

> [!CAUTION]
> Novos usuários devem ser adicionados somente a *Cloud-Owner-Group*, *Cloud-global-cluster-admin-Group*, *Cloud-Global-Storage-admin-Group*, *Cloud-Global-Network-admin-Group* ou, *Cloud-global-VM-admin-Group*.  Os usuários adicionados ao grupo de *Administradores* serão removidos automaticamente.  Somente as contas de serviço devem ser adicionadas ao grupo de *Administradores* e as contas de serviço não devem ser usadas para entrar na interface do usuário da Web do amvSphere.

Durante o período de escalonamento, o CloudSimple usa o monitoramento automatizado com notificações de alerta associadas para identificar quaisquer alterações involuntárias no ambiente.
