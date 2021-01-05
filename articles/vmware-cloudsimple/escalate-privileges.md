---
title: Azure VMware Solution by CloudSimple - Escalate CloudSimple privileges
description: Descreve como escalar as permissões CloudSimple para executar funções administrativas no VCenter Private Cloud
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d481717a79856583d23f61107678d2ecd1af68d6
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895732"
---
# <a name="escalate-cloudsimple-privileges-to-perform-administrative-functions-in-private-cloud-vcenter"></a>Escalate CloudSimple privilégios para desempenhar funções administrativas em Private Cloud vCenter

A abordagem de privilégios CloudSimple destina-se a dar aos utilizadores do vCenter os privilégios de que necessitam para executarem operações normais. Em alguns casos, um utilizador pode exigir privilégios adicionais para executar uma determinada tarefa.  Pode aumentar os privilégios de um utilizador SSO vCenter por um período limitado.

As razões para a escalada dos privilégios podem incluir os seguintes:

* Configuração de fontes de identidade
* Gestão de utilizadores
* Supressão do grupo portuário distribuído
* Instalação de soluções vCenter (como aplicações de backup)
* Criação de contas de serviço

> [!WARNING]
> As ações tomadas no estado privilegiado escalonado podem afetar negativamente o seu sistema e podem fazer com que o seu sistema fique indisponível. Execute apenas as ações necessárias durante o período de escalada.

A partir do portal CloudSimple, aumente os [privilégios](escalate-private-cloud-privileges.md) para o utilizador local CloudOwner no vCenter SSO.  Só pode aumentar o privilégio do utilizador remoto se o fornecedor de identidade adicional estiver configurado no vCenter.  A escalada de privilégios envolve a adição do utilizador selecionado ao grupo de Administradores incorporados vSphere.  Só um utilizador pode ter privilégios escalonados.  Se precisar de aumentar os privilégios de outro utilizador, primeiro desescala os privilégios dos utilizadores atuais.

Os utilizadores de fontes de identidade adicionais devem ser adicionados como membros do grupo CloudOwner.

> [!CAUTION]
> Os novos utilizadores devem ser adicionados apenas ao *Cloud-Owner-Group*, *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group,* *Cloud-Global-Network-Admin-Group* ou, *Cloud-Global-VM-Admin-Group*.  Os utilizadores *adicionados* ao grupo de Administradores serão removidos automaticamente.  Apenas as contas de serviço devem ser *adicionadas* ao grupo de administradores e as contas de serviço não devem ser usadas para iniciar súm na uI web vSphere.

Durante o período de escalada, a CloudSimple utiliza monitorização automatizada com notificações de alerta associadas para identificar quaisquer alterações inadvertidas ao ambiente.
