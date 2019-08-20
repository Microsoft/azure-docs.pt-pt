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
ms.openlocfilehash: 524772578ad724e969bbeab0be0a3edcf32a845f
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69619615"
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

Durante o período de escalonamento, o CloudSimple usa o monitoramento automatizado com notificações de alerta associadas para identificar quaisquer alterações involuntárias no ambiente.
