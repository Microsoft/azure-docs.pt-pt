---
title: Concluir uma revisão de acesso das funções do Azure AD no PIM-Azure Active Directory | Microsoft Docs
description: Saiba como concluir uma revisão de acesso das funções do Azure AD no Azure AD Privileged Identity Management (PIM) e exibir os resultados
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 197abc0642d6abf1278a75c2101c195cafeb53b2
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809139"
---
# <a name="complete-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Concluir uma revisão de acesso das funções do Azure AD no Privileged Identity Management

Os administradores de função com privilégios podem examinar o acesso privilegiado após o início de uma [revisão de acesso](pim-how-to-start-security-review.md).  Privileged Identity Management (PIM) enviará automaticamente um email aos usuários em sua organização do Azure Active Directory (Azure AD) solicitando que eles revisem seu acesso. Se um usuário não recebeu um email, você pode enviar a eles as instruções em [como executar uma revisão de acesso](pim-how-to-perform-security-review.md).

Depois que o período de revisão de acesso terminar, ou todos os usuários tiverem concluído sua autorevisão, siga as etapas neste artigo para gerenciar a revisão e ver os resultados.

## <a name="manage-access-reviews"></a>Gerenciar revisões de acesso

1. Vá para a [portal do Azure](https://portal.azure.com/) e selecione o serviço de **Azure ad Privileged Identity Management** em seu painel.
1. Selecione a seção **revisões de acesso** do painel.
1. Selecione a revisão de acesso que você deseja gerenciar.

Na folha de detalhes da revisão de acesso, há várias opções para gerenciar essa revisão.

![Botões de revisão de acesso do Privileged Identity Management-captura de tela](./media/pim-how-to-complete-review/review-buttons.png)

### <a name="remind"></a>-

Se uma revisão de acesso for configurada para que os usuários se revisem, o botão **lembrar** enviará uma notificação.

### <a name="stop"></a>Parar

Todas as revisões de acesso têm uma data de término, mas você pode usar o botão **parar** para concluí-la antecipadamente. Se algum usuário não tiver sido revisado neste momento, ele não será capaz de interromper a revisão. Não é possível reiniciar uma revisão após sua interrupção.

### <a name="apply"></a>Registe-se

Após a conclusão de uma revisão de acesso, porque você atingiu a data de término ou a interrompeu manualmente, o botão **aplicar** implementa o resultado da revisão. Se o acesso de um usuário foi negado na revisão, essa é a etapa que removerá sua atribuição de função.  

### <a name="export"></a>Exportar

Se você quiser aplicar os resultados da revisão de acesso manualmente, poderá exportar a revisão. O botão **Exportar** iniciará o download de um arquivo CSV. Você pode gerenciar os resultados no Excel ou em outros programas que abrem arquivos CSV.

### <a name="delete"></a>Eliminar

Se você não estiver interessado na revisão, exclua-a. O botão **excluir** remove a revisão do serviço de Privileged Identity Management.

> [!IMPORTANT]
> Não será necessário confirmar essa alteração destrutiva, portanto, verifique se você deseja excluir essa revisão.

## <a name="next-steps"></a>Passos seguintes

- [Iniciar uma revisão de acesso para as funções do Azure AD no Privileged Identity Management](pim-how-to-start-security-review.md)
- [Executar uma revisão de acesso das minhas funções do Azure AD no Privileged Identity Management](pim-how-to-perform-security-review.md)
