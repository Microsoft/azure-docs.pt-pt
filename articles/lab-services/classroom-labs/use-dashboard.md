---
title: Use o dashboard para um laboratório de sala de aula em Serviços de Laboratório Azure [ Microsoft Docs
description: Aprenda a usar o dashboard para um laboratório de sala de aula em Serviços de Laboratório Azure.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 363e5705e9ccf4aa207ff7e5cafb615bc01bc7d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538945"
---
# <a name="dashboard-for-classroom-labs"></a>Painel de instrumentos para laboratórios de sala de aula
Este artigo descreve a visão do painel de um laboratório de sala de aula em Azure Lab Services. 

![Dashboard](../media/use-dashboard/dashboard.png)

## <a name="costs-and-billing-tile"></a>Custos e azulejos de faturação
Este azulejo fornece os seguintes detalhes da estimativa de custos:

| Definição | Valor | 
| ------- | ----- | 
| Horário de quota | O número máximo de horas que um utilizador pode utilizar o VM fora do horário programado. |
| Horário programado | Horas que serão incorridas com base no horário definido no laboratório. Este valor só está disponível se houver um conjunto de/para data em todos os eventos de programação. |
| Horas/utilizador | A soma das horas de quotas e horários programados. |
| Utilizadores máximos | Número máximo de utilizadores no laboratório com base em todas as máquinas virtuais a serem reclamadas. |
| Horas x utilizadores | Horas/utilizador multiplicados pelo número de utilizadores. |
| Quota ajustada | A soma das horas de quota adicionadas a utilizadores específicos. |
| Horas totais * $/hora | O custo por hora com base no tamanho vm selecionado. Isto baseia-se no pagamento regular à medida que vai ao preço. |
| Custo total estimado | Este é o preço máximo para este laboratório com base nas definições atuais. |

## <a name="template-tile"></a>Azulejo de modelo
Você vê as seguintes informações sobre este azulejo:

- A data em que o modelo foi criado 
- A data em que o modelo foi publicado pela última vez 

Também tem um link para navegar para a página **Modelo** onde você pode [gerir o modelo VM](how-to-create-manage-template.md) para a classe. 

## <a name="virtual-machine-pool-tile"></a>Azulejo de piscina de máquina virtual

Você vê as seguintes informações sobre este azulejo:

- Número de máquinas virtuais que são atribuídas a estudantes (utilizadores)
- Número de máquinas virtuais que ainda não foram atribuídas aos alunos

Também tem um link para navegar para a página de piscina de **máquinas virtuais** onde você pode [gerir a piscina de máquinas virtuais](how-to-set-virtual-machine-passwords.md) no laboratório. 

## <a name="users-tile"></a>Azulejo sinuoso

Você vê as seguintes informações sobre este azulejo:

- Número de utilizadores registados na classe
- Número de utilizadores que são adicionados ao laboratório mas não registados na classe 

Também tem um link para navegar na página **utilizadores** onde pode [gerir os utilizadores](how-to-configure-student-usage.md) para o laboratório. 

## <a name="schedules-tile"></a>Azulejos de horários
Vê os eventos agendados para o laboratório no azulejo. Também tem um link para navegar para a página **Agendar** onde pode [criar e gerir horários.](how-to-create-schedules.md) O azulejo mostra-lhe detalhes para apenas dois eventos agendados e o número de eventos agendados restantes para o laboratório. 

![Eventos agendados](../media/use-dashboard/scheduled-events.png)

