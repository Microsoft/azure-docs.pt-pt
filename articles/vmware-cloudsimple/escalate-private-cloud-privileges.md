---
title: Aumentar os privilégios de nuvem privada - solução de VMware do Azure por CloudSimple
description: Descreve como aumentar os privilégios na sua nuvem privada para funções administrativas no vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d11c88b91b13cca13120a9203e376fdc2c3d6d8d
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332536"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>Aumentar os privilégios de vCenter de nuvem privada no CloudSimple portal 

Para obter acesso administrativo ao vCenter sua nuvem privada, pode aumentar temporariamente os seus privilégios de CloudSimple.  Utilizar privilégios elevados, pode instalar soluções de VMware, adicionar origens de identidade e gerir utilizadores.

Novos utilizadores podem ser criados no domínio SSO vCenter e dadas acesso ao vCenter.  Quando cria novos utilizadores, adicioná-los para os grupos de builtin CloudSimple para aceder ao vCenter.  Para obter mais informações, consulte [modelo de permissão de nuvem privada CloudSimple do VMware vCenter](https://docs.azure.cloudsimple.com/learn-private-cloud-permissions/).

> [!CAUTION]
> Não faça quaisquer alterações de configuração para componentes de gestão. Ações executadas durante o estado com privilégios escalado podem afetar negativamente o seu sistema ou podem fazer com que o seu sistema fique indisponível.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="escalate-privileges"></a>Escalar privilégios

1. Acesso a [CloudSimple portal](access-cloudsimple-portal.md).

2. Abra o **recursos** , selecione a nuvem privada para o qual pretende aumentar os privilégios.

3. Perto da parte inferior da página de resumo sob **alterar os privilégios de vSphere**, clique em **Escalate**.

    ![Alterar o privilégio de vSphere](media/escalate-private-cloud-privilege.png)

4. Selecione o tipo de utilizador do vSphere.  Apenas **CloudOwner@cloudsimple.local** usuário local pode ser escalado.

5. Selecione o intervalo de tempo escalate na lista pendente. Escolha o período mais curto que irá permitir-lhe concluir a tarefa.

6. Selecione a caixa de verificação para confirmar que entenda os riscos.

    ![Escalar a caixa de diálogo de privilégio](media/escalate-private-cloud-privilege-dialog.png)

7. Clique em **OK**.

8. O processo de escalonamento pode demorar alguns minutos. Quando terminar, clique em **OK**.

O Escalamento de privilégios começa e dura até ao fim do intervalo selecionado.  Pode iniciar sessão seu vCenter de nuvem privada para fazer tarefas administrativas.

> [!IMPORTANT]
> Apenas um utilizador pode ter escalou privilégios.  Anular deve aumentar os privilégios do utilizador antes de pode aumentar os privilégios de outro utilizador.

## <a name="extend-privilege-escalation"></a>Expandir o Escalamento de privilégios

Se necessitar de mais tempo a concluir suas tarefas, pode estender o período de escalonamento de privilégio.  Escolha o que intervalo de tempo permite-lhe concluir as tarefas administrativas de escalar adicional.

1. Sobre o **recursos** > **nuvens privadas** no portal do CloudSimple, selecione a nuvem privada para o qual deseja estender o Escalamento de privilégios.

2. Perto da parte inferior do separador Resumo, clique em **estender o Escalamento de privilégios**.

    ![Expandir o Escalamento de privilégios](media/de-escalate-private-cloud-privilege.png)

3. Selecione um intervalo de tempo escalate na lista pendente. Reveja a nova hora de fim de escalonamento.

4. Clique em **guardar** para expandir o intervalo.

## <a name="de-escalate-privileges"></a>Anular escalonar privilégios

Depois de suas tarefas administrativas estiverem concluídas, anular deve aumentar os seus privilégios.  

1. Sobre o **recursos** > **nuvens privadas** no portal do CloudSimple, selecione a nuvem privada para o qual pretende anular escalonar privilégios.

2. Clique em **anular escalar**.

3. Clique em **OK**.

> [!IMPORTANT]
> Para evitar eventuais erros, termine a sessão vCenter e inicie sessão novamente depois de anular escalonar privilégios.

## <a name="next-steps"></a>Passos Seguintes

* [Configurar origens de identidade do vCenter para utilizar o Active Directory](https://docs.azure.cloudsimple.com/set-vcenter-identity/)
* Instalar a solução de backup para [máquinas de virtuais de carga de trabalho de cópia de segurança](https://docs.azure.cloudsimple.com/backup-workloads-veeam/)