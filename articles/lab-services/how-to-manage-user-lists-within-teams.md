---
title: Gerir listas de utilizadores do Azure Lab Services de Equipas
description: Saiba como gerir as listas de utilizadores do Azure Lab Services a partir de Equipas.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: fa8f96a84be5c4c91e0153216e15963ec0e3d6f8
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946714"
---
# <a name="manage-lab-services-user-lists-from-teams"></a>Gerir listas de utilizadores de Serviços de Laboratório de Equipas

Quando um laboratório é criado dentro de Equipas (ver [Iniciar e criar um laboratório de Lab Services a partir de Equipas),](how-to-get-started-create-lab-within-teams.md)a lista de utilizadores do laboratório é automaticamente povoada e sincronizada com a associação da equipa. Todos na equipa, incluindo proprietários, membros e convidados serão automaticamente adicionados à lista de utilizadores do laboratório. Os serviços de laboratório da Azure mantêm uma sincronização com a equipa e uma sincronização automática é ativada a cada 24 horas. 

## <a name="sync-users"></a>Utilizadores sincronizados

Os educadores podem usar o botão **Sync** para ativar uma sincronização manual assim que a adesão à equipa for atualizada. 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-users-with-teams/sync-users.png" alt-text="Utilizadores sincronizados":::

Uma vez que a sincronização automática ou manual esteja completa, o seguinte é verdade dependendo se o laboratório foi publicado.

* Se o laboratório não tiver sido publicado pelo menos uma vez:
    * Os utilizadores serão adicionados ou eliminados da lista de utilizadores do laboratório de acordo com as alterações à adesão à equipa. 
* Se o laboratório tiver sido publicado pelo menos uma vez, além de adicionar ou eliminar utilizadores, a capacidade do laboratório será automaticamente atualizada.
    * Se houver novidades na equipa, serão criados novos VMs.
    * Se algum utilizador tiver sido eliminado da equipa, o VM associado também será eliminado.

## <a name="next-steps"></a>Próximos passos

Uma vez configurado o modelo VM e quando o educador selecionar para publicar o modelo, será criado o número de VMs equivalente ao número de utilizadores na lista de utilizadores do laboratório. Assim que o laboratório for publicado e os VMs forem criados, os Utilizadores serão automaticamente registados no laboratório e os VMs serão-lhes atribuídos no seu primeiro login nos Serviços Azure Lab, ou seja, quando acederem pela primeira vez à app de serviços do **Azure Lab.** 

Para publicar o modelo VM, vá à janela teams Lab Services, selecione **o separador Modelo** -> **...**  ->  **Publicar**.

Para gerir piscinas VM, consulte [gerir uma piscina VM em Lab Services from Teams](how-to-manage-vm-pool-within-teams.md).

### <a name="also-review"></a>Também revisão

Consulte os seguintes artigos:

- [Use os serviços do Azure Lab dentro da visão geral das equipas](lab-services-within-teams-overview.md)
- [Começar e criar um laboratório de serviços de laboratório de equipas](how-to-get-started-create-lab-within-teams.md)
- [Criar horários de Serviços de Laboratório de Equipas](how-to-create-schedules-within-teams.md)
- [Aceda a um VM (visão do aluno) nos Serviços de Laboratório das Equipas](how-to-access-vm-for-students-within-teams.md)

