---
title: Gerir uma piscina VM em Azure Lab Services from Teams
description: Saiba como gerir uma piscina VM em Azure Lab Services from Teams.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: b838e0561bb48b20450e49aaef37baf3c9ecb4d0
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946758"
---
# <a name="manage-a-vm-pool-in-lab-services-from-teams"></a>Gerir uma piscina VM em Serviços de Laboratório de Equipas

A criação da Máquina Virtual (VM) começa assim que o modelo VM é publicado pela primeira vez. Serão criados VMs que igualem o número de utilizadores na lista de utilizadores do laboratório. Os VMs são automaticamente atribuídos aos alunos no seu primeiro login nos Serviços Azure Lab. 

## <a name="publish-a-template-and-manage-a-vm-pool"></a>Publique um modelo e gere uma piscina VM

Para publicar o modelo, vá à janela teams Lab Services, selecione **o separador Modelo** -> **...**  ->  **Publicar**.

Uma vez configurado o modelo VM e quando o educador selecionar para publicar o modelo, será criado o número de VMs equivalente ao número de utilizadores na lista de utilizadores do laboratório. Assim que o laboratório for publicado e os VMs forem criados, os Utilizadores serão automaticamente registados no laboratório e os VMs serão-lhes atribuídos no seu primeiro login nos Serviços Azure Lab, ou seja, quando acederem pela primeira vez à app de serviços do **Azure Lab.** 

Quando uma sincronização da lista de utilizadores é ativada, a Capacidade de Laboratório (número de VMs no laboratório) será automaticamente atualizada com base nas alterações à adesão à equipa. Serão criados novos VM à medida que forem adicionados novos utilizadores e os VM atribuídos aos utilizadores removidos da equipa também serão eliminados. Para obter mais informações, consulte [como gerir os utilizadores dentro das Equipas.](how-to-manage-user-lists-within-teams.md) 

Os educadores podem continuar a aceder aos VM dos alunos diretamente a partir do separador VM Pool. E os educadores podem aceder aos VM atribuídos a si mesmos a partir do separador **de piscina de máquinas Virtuais** ou clicando no botão My Virtual **Machines** (canto superior/direito do ecrã). 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-vm-pool-with-teams/vm-pool.png" alt-text="Piscina VM":::

## <a name="next-steps"></a>Próximos passos

Consulte os seguintes artigos:

- [Use os serviços do Azure Lab dentro da visão geral das equipas](lab-services-within-teams-overview.md)
- [Começar e criar um laboratório de serviços de laboratório de equipas](how-to-get-started-create-lab-within-teams.md)
- [Gerir listas de utilizadores de Serviços de Laboratório de Equipas](how-to-manage-user-lists-within-teams.md)
- [Criar horários de Serviços de Laboratório de Equipas](how-to-create-schedules-within-teams.md)
- [Aceda a um VM (visão do aluno) nos Serviços de Laboratório das Equipas](how-to-access-vm-for-students-within-teams.md)


