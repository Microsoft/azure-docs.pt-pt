---
title: Use o dashboard para um laboratório de sala de aula nos Serviços de Laboratório Azure Microsoft Docs
description: Aprenda a usar o dashboard para um laboratório de sala de aula nos Serviços de Laboratório Azure.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 3834e6910faea38e87489d1ad0075d4bd9b8c6c6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018312"
---
# <a name="dashboard-for-classroom-labs"></a>Painel para laboratórios de sala de aula
Este artigo descreve a visão do painel de um laboratório de sala de aula nos Serviços do Laboratório Azure. 

![A captura de ecrã mostra a visão do painel de um laboratório de sala de aula nos Serviços do Laboratório Azure](./media/use-dashboard/dashboard.png)

## <a name="costs-and-billing-tile"></a>Custos e azulejos de faturação
Este azulejo fornece os seguintes detalhes da estimativa de custos:

| Definição | Valor | 
| ------- | ----- | 
| Horário de quota | O número máximo de horas que um utilizador pode utilizar o VM fora das horas programadas. |
| Horário horário | Horas que serão incorridas com base no horário definido no laboratório. Este valor só está disponível se houver uma data definida em todos os eventos de agenda. |
| Horas/utilizador | A soma das horas de quota e horário programado. |
| Utilizadores máximos | Número máximo de utilizadores no laboratório com base em todas as máquinas virtuais a reclamar. |
| Horas x utilizadores | Horas/utilizador multiplicado pelo número de utilizadores. |
| Quota ajustada | A soma das horas de quota adicionadas aos utilizadores específicos. |
| Total de horas * $/hora | O custo por hora com base no tamanho VM selecionado. Isto baseia-se no pagamento regular à medida que vais ao preço. |
| Custo total estimado | Este é o preço máximo deste laboratório com base nas configurações atuais. |

## <a name="template-tile"></a>Azulejo de modelo
Veja as seguintes informações sobre este azulejo:

- A data em que o modelo foi criado 
- A data em que o modelo foi publicado pela última vez 

Também tem um link para navegar para a página **modelo** onde você pode [gerir o modelo VM](how-to-create-manage-template.md) para a classe. 

## <a name="virtual-machine-pool-tile"></a>Azulejo de piscina de máquina virtual

Veja as seguintes informações sobre este azulejo:

- Número de máquinas virtuais atribuídas a estudantes (utilizadores)
- Número de máquinas virtuais que ainda não foram atribuídas aos alunos

Também tem um link para navegar para a página **de piscina de máquinas virtuais** onde você pode gerir a piscina de [máquinas virtuais](how-to-set-virtual-machine-passwords.md) no laboratório. 

## <a name="users-tile"></a>Azulejo dos utilizadores

Veja as seguintes informações sobre este azulejo:

- Número de utilizadores registados na classe
- Número de utilizadores que são adicionados ao laboratório mas não registados na classe 

Também tem um link para navegar para a página **dos Utilizadores** onde pode [gerir os utilizadores](how-to-configure-student-usage.md) para o laboratório. 

## <a name="schedules-tile"></a>Azulejos horários
Vê os eventos atuais para o laboratório no azulejo. Também tem um link para navegar para a página **Agenda onde** pode criar e [gerir horários.](how-to-create-schedules.md) O azulejo mostra detalhes para apenas dois eventos agendados e o número de eventos agendados restantes para o laboratório. 

![Eventos agendados](./media/use-dashboard/scheduled-events.png)

