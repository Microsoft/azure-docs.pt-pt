---
title: Apagar um laboratório de Serviços de Laboratório Azure das Equipas
description: Saiba como apagar um laboratório da Azure Lab Services das Equipas.
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: 8d1e20f8f676eb9863187b550a3c0400871d670c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96433959"
---
# <a name="delete-labs-within-teams"></a>Eliminar laboratórios dentro de equipas

Este artigo mostra como eliminar um laboratório da aplicação **Azure Lab Services.**

## <a name="prerequisites"></a>Pré-requisitos

* [Crie uma conta de Serviços de Laboratório](tutorial-setup-lab-account.md#create-a-lab-account) no portal Azure.
* [Começa e cria um laboratório de Serviços de Laboratório dentro das Equipas.](how-to-get-started-create-lab-within-teams.md)

## <a name="delete-labs"></a>Eliminar laboratórios

Um laboratório criado dentro de Equipas pode ser eliminado no site lab [Services](https://labs.azure.com) eliminando o laboratório diretamente, como descrito em [Laboratórios de Gestão em Azure Lab Services](how-to-manage-classroom-labs.md). 

A eliminação do laboratório também é desencadeada quando a equipa é eliminada. Se a equipa em que o laboratório é criado for eliminada, o laboratório será automaticamente eliminado 24 horas após o sincronizamento automático da lista de utilizadores. 

> [!IMPORTANT]
> A eliminação do separador ou a desinstalação da aplicação não resultará na eliminação do laboratório. 

Se o separador for eliminado, os utilizadores da lista de membros da equipa ainda poderão aceder aos VMs no [site dos Lab Services,](https://labs.azure.com) a menos que a eliminação do laboratório seja explicitamente desencadeada pela eliminação do laboratório no site ou pela eliminação da equipa. 

## <a name="next-steps"></a>Passos seguintes

- [Use os serviços do Azure Lab dentro da visão geral das equipas](lab-services-within-teams-overview.md)
- [Gerir listas de utilizadores de laboratório dentro de Equipas](how-to-manage-user-lists-within-teams.md)
- [Gerir a piscina VM do laboratório dentro das equipas](how-to-manage-vm-pool-within-teams.md)
- [Criar e gerir horários de laboratório dentro de Equipas](how-to-create-schedules-within-teams.md)
- [Aceda a um VM dentro de equipas - Vista do aluno](how-to-access-vm-for-students-within-teams.md)

