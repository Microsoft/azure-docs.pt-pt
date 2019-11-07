---
title: Use o painel para um laboratório de sala de aula no Azure Lab Services | Microsoft Docs
description: Saiba como usar o painel para um laboratório de sala de aula no Azure Lab Services.
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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: d1e34a493b747383ce479bcad638098b41e59d2b
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73588190"
---
# <a name="dashboard-for-classroom-labs"></a>Painel para laboratórios de sala de aula
Este artigo descreve a exibição de painel de um laboratório de sala de aula no Azure Lab Services. 

![Dashboard](../media/use-dashboard/dashboard.png)

## <a name="costs-and-billing-tile"></a>Bloco de custos e cobrança
Este bloco fornece os seguintes detalhes de estimativa de custo:

| Definição | Valor | 
| ------- | ----- | 
| Horas de cota | O número máximo de horas que um usuário pode usar a VM fora das horas agendadas. |
| Horas agendadas | Horas que serão cobradas com base no conjunto de agendamento do laboratório. Esse valor só estará disponível se houver uma data de início definida em todos os eventos de agendamento. |
| Horas/usuário | A soma das horas de cota e horas agendadas. |
| Máximo de usuários | Número máximo de usuários no laboratório com base em todas as máquinas virtuais a serem reivindicadas. |
| Horas x usuários | Horas/usuários multiplicado pelo número de usuários. |
| Cota ajustada | A soma das horas de cota adicionadas a usuários específicos. |
| Total de horas * $/hora | O custo por hora com base no tamanho da VM selecionado. Isso se baseia no preço pago conforme o uso normal. |
| Custo estimado total | Este é o preço máximo para este laboratório com base nas configurações atuais. |

## <a name="template-tile"></a>Bloco de modelo
Você verá as seguintes informações neste bloco:

- A data em que o modelo foi criado 
- A data em que o modelo foi publicado pela última vez 

Ele também tem um link para navegar até a página de **modelo** , na qual você pode [gerenciar a VM de modelo](how-to-create-manage-template.md) para a classe. 

## <a name="virtual-machine-pool-tile"></a>Bloco do pool da máquina virtual

Você verá as seguintes informações neste bloco:

- Número de máquinas virtuais atribuídas a alunos (usuários)
- Número de máquinas virtuais que ainda não foram atribuídas aos alunos

Ele também tem um link para navegar até a página do **pool de máquinas virtuais** , onde você pode [gerenciar o pool de máquinas virtuais](how-to-set-virtual-machine-passwords.md) no laboratório. 

## <a name="users-tile"></a>Bloco usuários

Você verá as seguintes informações neste bloco:

- Número de usuários registrados na classe
- Número de usuários que são adicionados ao laboratório, mas não registrados na classe 

Ele também tem um link para navegar até a página **usuários** , onde você pode [gerenciar os usuários](how-to-configure-student-usage.md) do laboratório. 

## <a name="schedules-tile"></a>Bloco de agendas
Você verá os eventos agendados atuais para o laboratório no bloco. Ele também tem um link para navegar até a página **agenda** , na qual você pode [criar e gerenciar agendas](how-to-create-schedules.md). O bloco mostra detalhes de apenas dois eventos agendados e o número de eventos agendados restantes para o laboratório. 

![Eventos agendados](../media/use-dashboard/scheduled-events.png)

