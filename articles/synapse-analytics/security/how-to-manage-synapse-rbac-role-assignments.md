---
title: Como gerir atribuições do SYNAPSE RBAC no Synapse Studio
description: Este artigo descreve como atribuir e revogar funções de Sinaapse RBAC aos principais de segurança da AAD
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: bed0c00b8cb5718456302dff06e98ff2f7f2b4e8
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102194"
---
# <a name="how-to-manage-synapse-rbac-role-assignments-in-synapse-studio"></a>Como gerir atribuições de funções do SYNAPC no Synapse Studio

O SYNAPSE RBAC utiliza funções para atribuir permissões a utilizadores, grupos e outros princípios de segurança para permitir o acesso e utilização de recursos e artefactos de código da Sinapse.  [Saiba mais](./synapse-workspace-synapse-rbac.md)

Este artigo mostra como adicionar e eliminar atribuições de funções de RBAC da Synapse.

>[!Note]
>- Para gerir as atribuições de funções do SYNAPSE, é necessário ter a função de Administrador de Sinapse no espaço de trabalho ou num âmbito de nível inferior que inclua os objetos que pretende gerir. Se for administrador da Sinapse no espaço de trabalho, pode conceder acesso a todos os objetos do espaço de trabalho. 
>- **Os utilizadores convidados** de um inquilino de AD diferente não podem ver ou gerir atribuições de funções, mesmo que atribuíssem a função de Administrador de Sinapse.
>- Para ajudá-lo a recuperar o acesso a um espaço de trabalho no caso de não serem atribuídos ou disponíveis administradores da Synapse, os utilizadores com permissões para gerir as atribuições de funções **do Azure RBAC** no espaço de trabalho também podem gerir atribuições de funções **de SYNAPSe RBAC,** permitindo a adição de administrador de sinapse ou outras atribuições de funções de Sinapse.
>- O acesso às piscinas SQL é gerido com permissões SQL.  Com exceção das funções de Administrador da Sinapse e administrador do Synapse SQL, as funções de RBAC da Synapse não concedem acesso a piscinas SQL.

>[!important]
>- As alterações introduzidas nas atribuições de funções do SYNAPSE RBAC podem demorar 2 a 5 minutos a produzir efeitos. 
>- Se estiver a gerir permissões do SYNAPSE RBAC modificando a adesão a grupos de segurança, então as alterações à adesão são geridas usando o Azure Ative Directory.  As alterações aos membros do grupo podem demorar entre 10 a 15 minutos ou mais a produzir efeito.

## <a name="open-synapse-studio"></a>Open Synapse Studio  

Para atribuir uma função a um utilizador, grupo, principal de serviço ou identidade gerida, abra primeiro [o Synapse Studio](https://web.azuresynapse.net/) e selecione o seu espaço de trabalho. 

![Faça login no espaço de trabalho](./media/common/login-workspace.png) 
 
 Depois de abrir o seu espaço de trabalho, expanda a secção **de Segurança** à esquerda e selecione o controlo **de acesso**. 

 ![Selecione Controlo de Acesso na secção de Segurança à esquerda](./media/how-to-manage-synapse-rbac-role-assignments/left-nav-security-access-control.png)

O ecrã de controlo de acesso lista as atribuições de funções atuais.  Pode filtrar a lista pelo nome principal ou e-mail e filtrar seletivamente os tipos, funções ou âmbitos de objeto incluídos. A partir deste ecrã, pode adicionar ou remover atribuições de funções.  

## <a name="add-a-synapse-role-assignment"></a>Adicione uma atribuição de função synapse

No ecrã de controlo de acesso, selecione **+Adicionar** para criar uma nova atribuição de funções

![Clique + Adicionar para criar uma nova atribuição de função](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add.png)

No separador de atribuição de funções Add, pode criar atribuições de funções no âmbito do espaço de trabalho ou no âmbito do item do espaço de trabalho. 

## <a name="add-workspace-scoped-role-assignment"></a>Adicionar atribuição de função de espaço de trabalho

Primeiro, selecione **Workspace** como o âmbito e, em seguida, selecione a **função Sinapse RBAC**.  Selecione **o(s) principal(s)** para ser atribuído o papel e, em seguida, crie a(s) atribuição de funções. 

![Adicionar atribuição de função de espaço de trabalho - selecione função](./media/how-to-manage-synapse-rbac-role-assignments/access-control-workspace-role-assignment.png) 

A função atribuída será aplicada a todos os objetos aplicáveis no espaço de trabalho.

## <a name="add-workspace-item-scoped-role-assignment"></a>Adicionar atribuição de função de item de espaço de trabalho

Para atribuir uma função num âmbito mais fino, selecione o **item workspace** como âmbito e, em seguida, selecione o **tipo de artigo** de digitalização .       

![Adicionar atribuição de função de item de espaço de trabalho - selecione o tipo de item](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add-workspace-item-assignment-select-item-type.png) 

Selecione o **item** específico a ser utilizado como o âmbito e, em seguida, selecione a **função** a atribuir a partir do drop-down.  As listas de espera listam apenas as funções que são válidas para o tipo de produto selecionado. [Saiba mais](./synapse-workspace-synapse-rbac.md).  

![Adicionar atribuição de função de item de ponto de espaço de trabalho - selecione função](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add-workspace-item-assignment-select-role.png) 
 
Em seguida, **selecione o(s) principal(s)** a que o papel deve ser atribuído.  Pode selecionar iterativamente vários principais.  **Selecione Aplicar** para criar a(s) atribuição de funções.

## <a name="remove-a-synapse-rbac-role-assignment"></a>Remover uma atribuição de função DE Sinapse RBAC

Para revogar o acesso do SYNAPSE RBAC, você remove as atribuições de funções apropriadas.  No ecrã de controlo access, utilize os filtros para localizar a ou as atribuições de funções a remover.  Verifique as atribuições de funções e, em seguida, **selecione Remover o acesso**.   

![Excluir uma atribuição de função para remover o acesso](./media/how-to-manage-synapse-rbac-role-assignments/access-control-remove-access.png)

Lembre-se que as alterações nas atribuições de funções levarão 2-5 minutos a produzir efeito.   

## <a name="next-steps"></a>Passos seguintes

[Compreender as funções de RBAC da Sinapse necessárias para executar tarefas comuns](./synapse-workspace-understand-what-role-you-need.md)