---
title: Criar horários de serviços de laboratório da Azure dentro de equipas
description: Saiba como criar horários de Serviços de Laboratório dentro de Equipas.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: 202b202f99868875a51d13e95fbcac677246cc05
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042342"
---
# <a name="create-and-manage-lab-services-schedules-within-teams"></a>Criar e gerir horários de Serviços de Laboratório dentro de Equipas

Os horários permitem-lhe configurar um laboratório de sala de aula de tal forma que os VMs no laboratório comecem e desliguem automaticamente numa hora especificada. Pode definir um horário único ou um horário recorrente. Os seguintes procedimentos dão-lhe passos para criar e gerir horários para um laboratório de sala de aula: 

Eis como os horários afetam as máquinas virtuais do laboratório: 

- A máquina virtual do modelo não está incluída nos horários. 
- Apenas as máquinas virtuais atribuídas são iniciadas. Isto significa que, se uma máquina não for reclamada por um utilizador final (estudante), a máquina não arrancará nas horas programadas. 
- Todas as máquinas virtuais (reclamadas por um utilizador ou não) são paradas com base no horário do laboratório. 

> [!IMPORTANT]
> O tempo de funcionamento programado dos VM não conta com a quota atribuída a um utilizador. A quota é para o tempo fora do horário horário que um aluno gasta em VMs. 

Os utilizadores podem criar, editar e apagar horários de laboratório dentro de Equipas, tal como no site dos [laboratórios.](https://labs.azure.com) Consulte o artigo sobre [a criação e gestão de horários.](how-to-create-schedules-within-teams.md)

## <a name="automatic-shutdown-and-disconnect-settings"></a>Definições automáticas de paragem e desconexão

Pode ativar várias funcionalidades de controlo de custos de autorredução para evitar custos adicionais quando as máquinas virtuais não estão a ser utilizadas ativamente. A combinação das três funcionalidades de paragem e desconexão automáticas que se seguem captura a maioria dos casos em que os utilizadores deixam acidentalmente as suas máquinas virtuais em funcionamento:
 
- Desconectar automaticamente os utilizadores de máquinas virtuais que o SISTEMA considera inativas.
- Desligue automaticamente as máquinas virtuais quando os utilizadores se desligam.
- Desligue automaticamente as máquinas virtuais que são iniciadas mas os utilizadores não se conectam.

Para obter mais detalhes, consulte o artigo sobre [a configuração das definições de paragem automática para um laboratório](how-to-enable-shutdown-disconnect.md).

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos:

- [Use os serviços do Azure Lab dentro da visão geral das equipas](lab-services-within-teams-overview.md)
- [Começar e criar um laboratório dentro das equipas](how-to-get-started-create-lab-within-teams.md)
- [Gerir listas de utilizadores de laboratório dentro de Equipas](how-to-manage-user-lists-within-teams.md)
- [Gerir a piscina VM do laboratório dentro das equipas](how-to-manage-vm-pool-within-teams.md)
- [Aceda a um VM dentro de equipas - Vista do aluno](how-to-access-vm-for-students-within-teams.md)
