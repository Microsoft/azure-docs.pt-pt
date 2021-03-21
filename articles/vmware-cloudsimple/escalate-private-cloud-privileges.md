---
title: Escalar privilégios de nuvem privada
titleSuffix: Azure VMware Solution by CloudSimple
description: Descreve como escalar privilégios na sua nuvem privada para funções administrativas no vCenter
author: Ajayan1008
ms.author: v-hborys
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c2215bd9a60fdcf76077c3a1f2a91631dc0dbe88
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97895827"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>Escalar privilégios private Cloud vCenter do portal CloudSimple

Para acesso administrativo ao seu VCenter Private Cloud, pode aumentar temporariamente os seus privilégios CloudSimple.  Utilizando privilégios elevados, pode instalar soluções VMware, adicionar fontes de identidade e gerir os utilizadores.

Novos utilizadores podem ser criados no domínio sSO vCenter e ter acesso ao vCenter.  Quando criar novos utilizadores, adicione-os aos grupos cloudSimple construídos para aceder ao vCenter.  Para obter mais informações, consulte [cloudSimple Private Cloud modelo de permissão VMware vCenter](./learn-private-cloud-permissions.md).

> [!CAUTION]
> Não faça alterações de configuração para componentes de gestão. As ações tomadas durante o estado privilegiado escalonado podem afetar negativamente o seu sistema ou podem fazer com que o seu sistema fique indisponível.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="escalate-privileges"></a>Escalar privilégios

1. Aceda ao [portal CloudSimple](access-cloudsimple-portal.md).

2. Abra a página **Recursos,** selecione a Nuvem Privada para a qual pretende aumentar os privilégios.

3. Perto da parte inferior da página Resumo sob os **privilégios Change vSphere,** clique em **Escalate**.

    ![Alterar privilégio vSphere](media/escalate-private-cloud-privilege.png)

4. Selecione o tipo de utilizador vSphere.  Só `CloudOwner@cloudsimple.local` o utilizador local pode ser escalado.

5. Selecione o intervalo de tempo de escalada a partir da queda. Escolha o período mais curto que lhe permitirá completar a tarefa.

6. Selecione a caixa de verificação para confirmar que compreende os riscos.

    ![Intensificar o diálogo de privilégios](media/escalate-private-cloud-privilege-dialog.png)

7. Clique em **OK**.

8. O processo de escalada pode levar alguns minutos. Quando terminar, clique em **OK**.

A escalada de privilégios começa e dura até ao final do intervalo selecionado.  Pode iniciar sôms no seu vCenter de nuvem privada para fazer tarefas administrativas.

> [!IMPORTANT]
> Só um utilizador pode ter privilégios escalonados.  Tem de desacelerar os privilégios do utilizador antes de poder aumentar os privilégios de outro utilizador.

> [!CAUTION]
> Os novos utilizadores devem ser adicionados apenas ao *Cloud-Owner-Group*, *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group,* *Cloud-Global-Network-Admin-Group* ou, *Cloud-Global-VM-Admin-Group*.  Os utilizadores *adicionados* ao grupo de Administradores serão removidos automaticamente.  Apenas as contas de serviço devem ser *adicionadas* ao grupo de administradores e as contas de serviço não devem ser usadas para iniciar súm na uI web vSphere.

## <a name="extend-privilege-escalation"></a>Alargar a escalada de privilégios

Se necessitar de tempo adicional para completar as suas tarefas, pode prolongar o período de escalada de privilégio.  Escolha o intervalo de tempo de escala adicional que lhe permite completar as tarefas administrativas.

1. Nas   >  **Nuvens Privadas** de Recursos no portal CloudSimple, selecione a Nuvem Privada para a qual pretende estender a escalada de privilégios.

2. Perto da parte inferior do separador Resumo, clique **em Prolongar a escalada do privilégio.**

    ![Alargar a escalada de privilégios](media/de-escalate-private-cloud-privilege.png)

3. Selecione um intervalo de tempo de escalada a partir da queda. Reveja o fim da nova escalada.

4. Clique **em Guardar** para prolongar o intervalo.

## <a name="de-escalate-privileges"></a>Privilégios de desescalantes

Uma vez concluídas as suas tarefas administrativas, deverá desescalar os seus privilégios.  

1. Nas   >  **Nuvens Privadas** de Recursos no portal CloudSimple, selecione a Nuvem Privada para a qual deseja desescalar os privilégios.

2. Clique **em De-escalate**.

3. Clique em **OK**.

> [!IMPORTANT]
> Para evitar erros, assine o vCenter e inscreva-se novamente após a desescalar os privilégios.

## <a name="next-steps"></a>Passos seguintes

* [Criar fontes de identidade vCenter para utilizar o Ative Directory](./set-vcenter-identity.md)
* Instale solução de backup para [backup de máquinas virtuais de carga de trabalho](./backup-workloads-veeam.md)
