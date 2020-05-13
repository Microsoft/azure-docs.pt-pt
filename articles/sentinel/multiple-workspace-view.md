---
title: Trabalhar com incidentes do Azure Sentinel em muitos espaços de trabalho ao mesmo tempo Microsoft Docs
description: Como ver incidentes em vários espaços de trabalho em simultâneo em Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/20/2020
ms.author: yelevin
ms.openlocfilehash: 448998328ff15b74b0aa0b17e2435a7ff55c54a5
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124190"
---
# <a name="work-with-incidents-in-many-workspaces-at-once"></a>Trabalhar com incidentes em muitos espaços de trabalho ao mesmo tempo 

 Para tirar o máximo partido das capacidades do Azure Sentinel, a Microsoft recomenda a utilização de um ambiente de espaço de trabalho único. No entanto, existem alguns casos de utilização que exigem ter vários espaços de trabalho, em alguns casos – por exemplo, o de um Prestador de Serviços de [Segurança Gerido (MSSP)](./multiple-tenants-service-providers.md) e seus clientes – em vários inquilinos. **Multiple Workspace View** permite-lhe ver e trabalhar com incidentes de segurança em vários espaços de trabalho ao mesmo tempo, mesmo entre inquilinos, permitindo-lhe manter a visibilidade total e o controlo da capacidade de resposta à segurança da sua organização.

## <a name="entering-multiple-workspace-view"></a>Entrando na vista do espaço de trabalho múltiplo

Ao abrir o Azure Sentinel, é-lhe apresentada uma lista de todos os espaços de trabalho aos quais tem direitos de acesso, em todos os inquilinos selecionados e subscrições. À esquerda de cada nome do espaço de trabalho está uma caixa de verificação. Clicar no nome de um único espaço de trabalho irá trazê-lo para esse espaço de trabalho. Para escolher vários espaços de trabalho, clique em todas as caixas de verificação correspondentes e, em seguida, clique no botão **Multiple Workspace View** na parte superior da página.

> [!IMPORTANT]
> A Visão de Espaço de Trabalho Múltipla suporta atualmente um máximo de 10 espaços de trabalho apresentados simultaneamente. 
> 
> Se verificar mais de 10 espaços de trabalho, aparecerá uma mensagem de aviso.

Note que na lista de espaços de trabalho, pode ver o diretório, subscrição, localização e grupo de recursos associados a cada espaço de trabalho. O diretório corresponde ao inquilino.

   ![Escolha vários espaços de trabalho](./media/multiple-workspace-view/workspaces.png)

## <a name="working-with-incidents"></a>Trabalhar com incidentes

Em **Multiple Workspace View**, apenas o ecrã **Incidents** está disponível por enquanto. Parece e funciona na maioria das formas como o ecrã regular de **Incidentes.** Há algumas diferenças importantes, no entanto:

   ![Ver incidentes em vários espaços de trabalho](./media/multiple-workspace-view/incidents.png)

- Os balcões no topo da página - *Incidentes abertos,* *Novos incidentes,* *Em curso*, etc. - mostram os números de todos os espaços de trabalho selecionados coletivamente.

- Você verá incidentes de todos os espaços de trabalho selecionados e diretórios (inquilinos) em uma única lista unificada. Pode filtrar a lista por espaço de trabalho e diretório, além dos filtros do ecrã regular **incidents.**

- Terá de ler e escrever permissões em todos os espaços de trabalho dos quais selecionou incidentes. Se tiver lido apenas permissões em alguns espaços de trabalho, verá mensagens de aviso se selecionar incidentes nesses espaços de trabalho. Não poderá modificar esses incidentes ou quaisquer outros que tenha selecionado juntamente com os outros (mesmo que tenha permissões para os outros).

- Se escolher um único incidente e clicar em **ver todos os detalhes** ou **investigar,** estará a partir daí no contexto de dados do espaço de trabalho desse incidente e nenhum outro.

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ver e a trabalhar com incidentes em vários espaços de trabalho do Azure Sentinel em simultâneo. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)

