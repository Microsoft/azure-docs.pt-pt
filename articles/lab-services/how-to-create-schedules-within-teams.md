---
title: Criar horários de serviços de laboratório da Azure dentro de equipas
description: Saiba como criar horários de Serviços de Laboratório dentro de Equipas.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: 5578a2f63a4d6f58998a54560299a4356ed65997
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946748"
---
# <a name="create-and-manage-lab-services-schedules-within-teams"></a>Criar e gerir horários de Serviços de Laboratório dentro de Equipas

Os horários permitem-lhe configurar um laboratório de sala de aula de tal forma que os VMs no laboratório comecem e desliguem automaticamente numa hora especificada. Pode definir um horário único ou um horário recorrente. Os seguintes procedimentos dão-lhe passos para criar e gerir horários para um laboratório de sala de aula: 

Eis como os horários afetam as máquinas virtuais do laboratório: 

- A máquina virtual do modelo não está incluída nos horários. 
- Apenas as máquinas virtuais atribuídas são iniciadas. Isto significa que, se uma máquina não for reclamada por um utilizador final (estudante), a máquina não arrancará nas horas programadas. 
- Todas as máquinas virtuais (reclamadas por um utilizador ou não) são paradas com base no horário do laboratório. 

> [!IMPORTANT]
> O tempo de funcionamento programado dos VM não conta com a quota atribuída a um utilizador. A quota é para o tempo fora do horário horário que um aluno gasta em VMs. 

## <a name="createeditdelete-a-schedule-for-the-lab"></a>Criar/editar/excluir um horário para o laboratório

Crie um evento programado para o laboratório para que os VMs no laboratório sejam automaticamente iniciados/parados em momentos específicos. A quota de utilizador especificada anteriormente é o tempo adicional atribuído a cada utilizador fora desta hora programada. 

1. Mude para a página **Agendas** e **selecione Adicionar evento agendado** na barra de ferramentas (superior/esquerda da janela). 
1. Definir os seguintes parâmetros para o horário:
    1. Confirme que **a Standard** está selecionada do **tipo Evento**. Selecione **Iniciar apenas** para especificar apenas a hora de início para os VMs. Selecione **Parar apenas** para especificar apenas o tempo de paragem para os VMs. 
    1. Especificar a **data de início.**
    1. Especifique a **hora de início** em que deseja que os VMs sejam iniciados.
    1. Especificar o **tempo de paragem** em que os VM devem ser desligados. 
    1. Especifique o **fuso horário** para os tempos de início e de paragem especificados. 
    1. Na secção **Repetir,** selecione **Todas as semanas** ou **Nunca**. 
    1. Para **notas (opcionais)**, introduza qualquer descrição ou notas para o horário. 
1. Clique em **Guardar**. 

### <a name="view-schedules-in-calendar"></a>Ver horários no calendário

Pode ver as datas e horários programados no calendário. Selecione o botão **Today** no canto superior direito para mudar para a data atual no calendário. Selecione **a seta esquerda** para mudar para a semana anterior e **seta direita** para mudar para a semana seguinte no calendário. 

### <a name="edit-a-schedule"></a>Editar uma programação

Quando seleciona uma programação realçada no calendário, vê botões para **editar** ou **eliminar** o horário. 

Na página de **eventos agendada para Editar,** pode atualizar o horário e selecionar **Guardar**. 

### <a name="delete-a-schedule"></a>Eliminar uma agenda

1. Para eliminar um horário, selecione uma programação realçada no calendário e selecione o botão ícone do lixo (apagar):
1. Na caixa de diálogo **de evento agendada para eliminar,** selecione **Sim** para confirmar a eliminação. 

## <a name="automatic-shutdown-and-disconnect-settings"></a>Definições automáticas de paragem e desconexão

No topo da página, irá notar um link para as definições **de autoshutdown.**

Pode ativar várias funcionalidades de controlo de custos de autorredução para evitar custos adicionais quando as máquinas virtuais não estão a ser utilizadas ativamente. A combinação das três funcionalidades de paragem e desconexão automáticas que se seguem captura a maioria dos casos em que os utilizadores deixam acidentalmente as suas máquinas virtuais em funcionamento:
 
- Desconectar automaticamente os utilizadores de máquinas virtuais que o SISTEMA considera inativas.
- Desligue automaticamente as máquinas virtuais quando os utilizadores se desligam.
- Desligue automaticamente as máquinas virtuais que são iniciadas mas os utilizadores não se conectam.

Para mais detalhes, clique no ícone de *informação* ao lado das opções nas definições.

## <a name="next-steps"></a>Próximos passos

Consulte os seguintes artigos:

- [Use os serviços do Azure Lab dentro da visão geral das equipas](lab-services-within-teams-overview.md)
- [Começar e criar um laboratório de serviços de laboratório de equipas](how-to-get-started-create-lab-within-teams.md)
- [Gerir listas de utilizadores de Serviços de Laboratório de Equipas](how-to-manage-user-lists-within-teams.md)
- [Gerir uma piscina VM em Serviços de Laboratório de Equipas](how-to-manage-vm-pool-within-teams.md)
- [Aceda a um VM (visão do aluno) nos Serviços de Laboratório das Equipas](how-to-access-vm-for-students-within-teams.md)