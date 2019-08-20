---
title: Escalonar privilégios de nuvem privada-solução VMware do Azure por CloudSimple
description: Descreve como escalonar privilégios em sua nuvem privada para funções administrativas no vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b54f1d8c0705488c53925d097433e77aed3eac72
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617618"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>Escalonar privilégios do vCenter da nuvem privada no portal do CloudSimple

Para acesso administrativo ao seu vCenter de nuvem privada, você pode escalonar temporariamente seus privilégios de CloudSimple.  Usando privilégios elevados, você pode instalar soluções VMware, adicionar fontes de identidade e gerenciar usuários.

Novos usuários podem ser criados no domínio de SSO do vCenter e recebem acesso ao vCenter.  Ao criar novos usuários, adicione-os aos grupos internos do CloudSimple para acessar o vCenter.  Para obter mais informações, consulte [modelo de permissão de nuvem privada CloudSimple do VMware vCenter](https://docs.azure.cloudsimple.com/learn-private-cloud-permissions/).

> [!CAUTION]
> Não faça nenhuma alteração de configuração para os componentes de gerenciamento. As ações executadas durante o estado de privilégio escalonado podem afetar negativamente o sistema ou podem fazer com que o sistema fique indisponível.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="escalate-privileges"></a>Escalar privilégios

1. Acesse o [portal do CloudSimple](access-cloudsimple-portal.md).

2. Abra a página **recursos** , selecione a nuvem privada para a qual você deseja escalonar privilégios.

3. Próximo à parte inferior da página Resumo em **alterar privilégios de vSphere**, clique em **escalar**.

    ![Alterar o privilégio de vSphere](media/escalate-private-cloud-privilege.png)

4. Selecione o tipo de usuário vSphere.  Somente `CloudOwner@cloudsimple.local` o usuário local pode ser escalonado.

5. Selecione o intervalo de tempo de escalonamento na lista suspensa. Escolha o período mais curto que permitirá que você conclua a tarefa.

6. Marque a caixa de seleção para confirmar que você entendeu os riscos.

    ![Caixa de diálogo escalar privilégio](media/escalate-private-cloud-privilege-dialog.png)

7. Clique em **OK**.

8. O processo de escalonamento pode levar alguns minutos. Quando terminar, clique em **OK**.

O escalonamento de privilégios começa e dura até o final do intervalo selecionado.  Você pode entrar em sua nuvem privada vCenter para realizar tarefas administrativas.

> [!IMPORTANT]
> Somente um usuário pode ter privilégios escalonados.  Você deve desescalonar os privilégios do usuário antes de poder escalonar os privilégios de outro usuário.

## <a name="extend-privilege-escalation"></a>Estender elevação de privilégio

Se precisar de mais tempo para concluir suas tarefas, você poderá estender o período de escalonamento de privilégios.  Escolha o intervalo de tempo de escalonamento adicional que permite que você conclua as tarefas administrativas.

1. Nas**nuvens privadas** de **recursos** > no portal do CloudSimple, selecione a nuvem privada para a qual você deseja estender o escalonamento de privilégios.

2. Próximo à parte inferior da guia Resumo, clique em **estender elevação de privilégio**.

    ![Estender elevação de privilégio](media/de-escalate-private-cloud-privilege.png)

3. Selecione um intervalo de tempo de escalonamento na lista suspensa. Examine a nova hora de término do escalonamento.

4. Clique em **salvar** para estender o intervalo.

## <a name="de-escalate-privileges"></a>Desescalonamento de privilégios

Depois que suas tarefas administrativas forem concluídas, você deverá desescalonar seus privilégios.  

1. Nas**nuvens privadas** de **recursos** > no portal do CloudSimple, selecione a nuvem privada para a qual você deseja desescalonar privilégios.

2. Cliqueem desescalonamento.

3. Clique em **OK**.

> [!IMPORTANT]
> Para evitar erros, saia do vCenter e entre novamente depois de desescalonamento de privilégios.

## <a name="next-steps"></a>Passos Seguintes

* [Configurar fontes de identidade do vCenter para usar Active Directory](https://docs.azure.cloudsimple.com/set-vcenter-identity/)
* Instalar solução de backup para [fazer backup de máquinas virtuais de carga de trabalho](https://docs.azure.cloudsimple.com/backup-workloads-veeam/)