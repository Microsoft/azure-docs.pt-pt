---
title: Como rever atribuições de funções do SYNAPSE NO Synapse Studio
description: Este artigo descreve como rever atribuições de funções do SYNAPSE RBAC usando o Synapse Studio
author: billgib
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/1/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: ee3b2c69d7782dca10b16a3b1726b75bfd99326e
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98218130"
---
# <a name="how-to-review-synapse-rbac-role-assignments"></a>Como rever atribuições de funções do SYNAPSE RBAC

As funções de RBAC sinapse são usadas para atribuir permissões a utilizadores, grupos e outros princípios de segurança para permitir o acesso e utilização dos recursos da Sinapse.  [Saiba mais](./synapse-workspace-synapse-rbac.md)

Este artigo explica como rever as atribuições de funções atuais para um espaço de trabalho.

Com qualquer papel de RBAC da Synapse, pode listar atribuições de funções de Sinapse RBAC para todos os âmbitos, incluindo atribuições para objetos a que não tem acesso. Apenas um administrador da Sinapse pode conceder acesso ao RBAC da Sinapse.  

>[!Note]
>Os utilizadores convidados (utilizadores de um inquilino de AD diferente) não podem ver ou gerir atribuições de funções, mesmo que atribuíssem a função de Administrador de Sinapse.    

## <a name="open-synapse-studio"></a>Open Synapse Studio  

Para rever as atribuições de funções, abra primeiro [o Synapse Studio](https://web.azuresynapse.net/) e selecione o seu espaço de trabalho. 

![Faça login no espaço de trabalho](./media/common/login-workspace.png) 
 
 Depois de abrir o seu espaço de trabalho, selecione o hub **Manage** à esquerda, em seguida, expanda a secção **de Segurança** e selecione o controlo **de acesso**. 

 ![Selecione Controlo de Acesso na secção de Segurança à esquerda](./media/how-to-manage-synapse-rbac-role-assignments/left-nav-security-access-control.png)

## <a name="review-workspace-role-assignments"></a>Rever atribuições de funções de espaço de trabalho

O ecrã de controlo do Access lista todas as atribuições de funções atuais para o espaço de trabalho, agrupadas por função. Cada atribuição inclui o nome principal, tipo principal, função e seu âmbito.

![Ecrã de Controlo de Acesso](./media/how-to-review-synapse-rbac-role-assignments/access-control-assignments.png)

Se um diretor tiver o mesmo papel em diferentes âmbitos, verá várias atribuições para o principal, uma para cada âmbito.  

Se um papel for atribuído a um grupo de segurança, verá as funções explicitamente atribuídas ao grupo, mas não as funções herdadas de grupos-mãe.  

Pode filtrar a lista pelo nome principal ou e-mail e filtrar seletivamente os tipos, funções e âmbitos de objetos. insira o seu nome ou pseudónimo de e-mail no filtro Nome para ver as funções que lhe são atribuídas. Apenas um administrador da Sinapse pode alterar as suas funções.

>[!Important] 
>Se for direta ou indiretamente membro de um grupo que lhe seja atribuído funções, poderá ter permissões que não são mostradas.

>[!tip]
>Pode encontrar os seus membros do grupo utilizando o Azure Ative Directory no portal Azure.  

Se criar um novo espaço de trabalho, você e o chefe de serviço MSI do espaço de trabalho recebem automaticamente o papel de Administrador de Sinapse no âmbito do espaço de trabalho.

## <a name="next-steps"></a>Passos seguintes

Saiba [como gerir as atribuições de funções do SYNAPC.](./how-to-manage-synapse-rbac-role-assignments.md)

Saiba [qual o papel que precisa para fazer tarefas específicas](./synapse-workspace-understand-what-role-you-need.md)