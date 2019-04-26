---
title: Iniciar uma revisão de acesso para funções de recursos do Azure no PIM - Azure Active Directory | Documentos da Microsoft
description: Saiba como iniciar uma revisão de acesso para funções de recursos do Azure no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46903967b375d882dc3c7a62cd0b7f8b6059f8b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60287072"
---
# <a name="start-an-access-review-for-azure-resource-roles-in-pim"></a>Iniciar uma revisão de acesso para funções de recursos do Azure no PIM
Atribuições de função tornam-se "obsoletas" quando os utilizadores tem acesso que não precisam mais privilegiado. Para reduzir o risco associado a estas atribuições de função obsoleta, os administradores de função com privilégios regularmente devem rever funções. Este documento aborda os passos para iniciar uma revisão de acesso no Azure Active Directory (Azure AD) Privileged Identity Management (PIM).

A partir do PIM página da aplicação principal, aceda a:

* **As revisões de acesso** > **adicionar**

![Adicionar as revisões de acesso](media/azure-pim-resource-rbac/rbac-access-review-home.png)

Quando seleciona a **Add** botão, o **criar uma revisão de acesso** é apresentado o painel. Neste painel, configure a revisão com um nome e o limite de tempo, escolher uma função para rever e, em seguida, decida quem faz a revisão.

![Criar uma revisão de acesso](media/azure-pim-resource-rbac/rbac-create-access-review.png)

### <a name="configure-the-review"></a>Configurar a revisão
Para criar uma revisão de acesso, nomeie-o primeiro e, em seguida, defina uma data de início e de fim.

![Configurar examine - captura de ecrã](media/azure-pim-resource-rbac/rbac-access-review-setting-1.png)

Verifique o comprimento da revisão tempo suficiente para os utilizadores para concluí-la. Se a eles concluída antes da data de fim, eles podem sempre pare a revisão desde o início.

### <a name="choose-a-role-to-review"></a>Escolher uma função para rever
Cada revisão se concentra numa única função. A menos que iniciou a revisão de acesso de um painel de função específica, terá de escolher uma função agora.

1. Aceda a **rever a associação de função**
   
    ![Rever a associação de função - captura de ecrã](media/azure-pim-resource-rbac/rbac-access-review-setting-2.png)
2. Escolha uma função a partir da lista.

### <a name="decide-who-will-perform-the-review"></a>Decida quem as executará a revisão
Existem três opções para a execução de uma revisão. Pode atribuir a revisão a alguém para concluir, pode fazê-lo por conta própria ou, cada utilizador pode rever o seu próprio acesso.

1. Escolha uma das opções:
   
   * **Utilizadores selecionados**: Utilize esta opção quando não sabe quem precisa de acesso. Com esta opção, pode atribuir a revisão a um proprietário do recurso ou o Gestor de grupo para concluir.
   * **Atribuído (autónomo)**: Utilize esta opção para que os utilizadores, reveja as suas próprias atribuições de funções.
   
2. Aceda a **selecionar revisores**.
   
    ![Selecionar revisores - captura de ecrã](media/azure-pim-resource-rbac/rbac-access-review-setting-3.png)

### <a name="start-the-review"></a>Inicie a revisão
Por fim, pode exigir que os utilizadores forneçam um motivo para aprovação de acesso. Adicione uma descrição da revisão se assim o desejar. Em seguida, selecione **iniciar**.

Certifique-se de que permitir que os utilizadores saber que existe uma revisão de acesso, esperando que eles e mostrar-lhes [como realizar uma revisão de acesso](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Gerir a revisão de acesso
No dashboard de recursos do Azure do PIM, pode monitorizar o progresso conforme os revisores de concluir as revisões. Nenhum direito de acesso forem alterado no diretório até [concluiu a revisão](pim-resource-roles-complete-access-review.md).

Até que o período de avaliação está acima, pode relembrar aos utilizadores para concluir a revisão ou pare a revisão desde o início da seção de revisões de acesso.

## <a name="next-steps"></a>Passos Seguintes

- [Concluir uma revisão de acesso para funções de recursos do Azure no PIM](pim-resource-roles-complete-access-review.md)
- [Efetuar uma revisão de acesso das minhas funções de recurso no PIM](pim-resource-roles-perform-access-review.md)
- [Iniciar uma revisão de acesso para funções do Azure AD no PIM](pim-how-to-start-security-review.md)
