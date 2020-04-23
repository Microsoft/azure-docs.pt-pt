---
title: Escalar privilégios privados de nuvem
titleSuffix: Azure VMware Solution by CloudSimple
description: Descreve como escalar privilégios na sua nuvem privada para funções administrativas no vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 63dc18c522a1e2e3b03bdf806945e0be67774b18
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870464"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>Escalar privilégios private Cloud vCenter do portal CloudSimple

Para acesso administrativo ao seu vCenter Cloud Privado, pode aumentar temporariamente os seus privilégios CloudSimple.  Utilizando privilégios elevados, pode instalar soluções VMware, adicionar fontes de identidade e gerir os utilizadores.

Novos utilizadores podem ser criados no domínio VCenter SSO e ter acesso ao vCenter.  Quando criar novos utilizadores, adicione-os aos grupos de construção CloudSimple para aceder ao vCenter.  Para mais informações, consulte o [modelo de permissão CloudSimple Private Cloud do VMware vCenter](https://docs.microsoft.com/azure/vmware-cloudsimple/learn-private-cloud-permissions/).

> [!CAUTION]
> Não faça alterações de configuração para componentes de gestão. As ações tomadas durante o estado privilegiado escalado podem afetar negativamente o seu sistema ou podem fazer com que o seu sistema fique indisponível.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="escalate-privileges"></a>Escalar privilégios

1. Aceda ao [portal CloudSimple](access-cloudsimple-portal.md).

2. Abra a página **Recursos,** selecione a Nuvem Privada para a qual pretende escalar privilégios.

3. Perto da parte inferior da página resumo sob **os privilégios Change vSphere,** clique em **Escalate**.

    ![Alterar privilégio vSphere](media/escalate-private-cloud-privilege.png)

4. Selecione o tipo de utilizador vSphere.  Só `CloudOwner@cloudsimple.local` o utilizador local pode ser escalado.

5. Selecione o intervalo de tempo de escalada a partir da queda. Escolha o período mais curto que lhe permitirá completar a tarefa.

6. Selecione a caixa de verificação para confirmar que compreende os riscos.

    ![Escalar o diálogo de privilégio](media/escalate-private-cloud-privilege-dialog.png)

7. Clique em **OK**.

8. O processo de escalada pode demorar alguns minutos. Quando terminar, clique em **OK**.

A escalada de privilégios começa e dura até ao final do intervalo selecionado.  Pode iniciar sessão no seu vCenter de nuvem privada para fazer tarefas administrativas.

> [!IMPORTANT]
> Só um utilizador pode ter privilégios escalonados.  Tem de desescalar os privilégios do utilizador antes de poder aumentar os privilégios de outro utilizador.

> [!CAUTION]
> Os novos utilizadores devem ser adicionados apenas ao *Cloud-Owner-Group*, *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group,* *Cloud-Global-Network-Admin-Group* ou, *Cloud-Global-VM-Admin-Group*.  Os utilizadores adicionados ao grupo *Administradores* serão removidos automaticamente.  Apenas as contas de serviço devem ser adicionadas ao grupo *de administradores* e as contas de serviço não devem ser utilizadas para iniciar sessão na VSphere web UI.

## <a name="extend-privilege-escalation"></a>Alargar a escalada de privilégios

Se necessitar de mais tempo para completar as suas tarefas, pode prolongar o período de escalada de privilégios.  Escolha o intervalo de tempo adicional de escalada que lhe permite completar as tarefas administrativas.

1. Sobre os **Recursos** > **Nuvens Privadas** no portal CloudSimple, selecione a Nuvem Privada para a qual pretende alargar a escalada de privilégios.

2. Perto da parte inferior do separador Resumo, clique em Prolongar a escalada de **privilégios**.

    ![Alargar a escalada de privilégios](media/de-escalate-private-cloud-privilege.png)

3. Selecione um intervalo de tempo de escalada a partir da queda. Reveja o novo tempo final da escalada.

4. Clique em **Guardar** para prolongar o intervalo.

## <a name="de-escalate-privileges"></a>Desescalar privilégios

Uma vez que as suas tarefas administrativas estejam completas, deve desescalar os seus privilégios.  

1. Sobre os **Recursos** > **Nuvens Privadas** no portal CloudSimple, selecione a Nuvem Privada para a qual pretende desescalar privilégios.

2. Clique em **De-escalar**.

3. Clique em **OK**.

> [!IMPORTANT]
> Para evitar erros, assine o vCenter e volte a inscrever-se após a desescalada de privilégios.

## <a name="next-steps"></a>Passos seguintes

* [Configurar fontes de identidade vCenter para usar o Diretório Ativo](https://docs.microsoft.com/azure/vmware-cloudsimple/set-vcenter-identity/)
* Instale solução de backup para [máquinas virtuais](https://docs.microsoft.com/azure/vmware-cloudsimple/backup-workloads-veeam/) de carga de trabalho de backup