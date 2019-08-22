---
title: Atualizar e gerenciar uma regra de grupo dinâmico e solucionar problemas de associação-Azure Active Directory | Microsoft Docs
description: Como criar uma regra de associação de grupo no portal do Azure, verifique o status.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/12/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce3bce5e2656efea0a4fc3d7aa6be46f1e6926ec
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657349"
---
# <a name="update-a-dynamic-group-to-manage-membership-in-azure-active-directory"></a>Atualizar um grupo dinâmico para gerenciar a associação no Azure Active Directory

No Azure Active Directory (AD do Azure), você pode usar regras para determinar a associação de grupo com base nas propriedades do usuário ou do dispositivo. Este artigo informa como configurar uma regra para um grupo dinâmico no portal do Azure.
A associação dinâmica tem suporte para grupos de segurança ou grupos do Office 365. Quando uma regra de associação de grupo é aplicada, os atributos de usuário e de dispositivo são avaliados para correspondências com a regra de associação. Quando um atributo é alterado para um usuário ou dispositivo, todas as regras de grupo dinâmico na organização são processadas para alterações de associação. Os usuários e dispositivos serão adicionados ou removidos se atenderem às condições de um grupo.

Para obter exemplos de sintaxe, propriedades com suporte, operadores e valores para uma regra de associação, consulte [regras de associação dinâmica para grupos no Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-update-a-group-membership-rule"></a>Para atualizar uma regra de associação de grupo

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com) com uma conta que esteja na função administrador global, administrador do Intune ou administrador de usuários no locatário.
1. Selecione **grupos** > **todos os grupos**.
1. Selecione um grupo para abrir seu perfil.
1. Na página perfil do grupo, selecione regras de **associação dinâmica**. O construtor de regras dá suporte a até cinco expressões. Para adicionar uma sexta ou qualquer expressão subsequente, você deve usar a caixa de texto.

   ![Adicionar regra de associação a um grupo dinâmico](./media/groups-update-rule/update-dynamic-group-rule.png)

1. Para ver as propriedades de extensão personalizadas disponíveis para sua regra de associação:
   1. Selecionar **obter propriedades de extensão personalizadas**
   2. Insira a ID do aplicativo e, em seguida, selecione **Atualizar Propriedades**.
1. Depois de atualizar a regra, selecione **salvar**.

Se a regra que você inseriu não for válida, uma explicação do motivo pelo qual a regra não pôde ser processada será exibida na notificação do Azure no Portal. Leia com atenção para entender como corrigir a regra.

## <a name="check-processing-status-for-a-rule"></a>Verificar o status de processamento de uma regra

Você pode ver o status de processamento de associação e a data da última atualização na página **visão geral** do grupo.
  
  ![exibição do status do grupo dinâmico](./media/groups-create-rule/group-status.png)

As seguintes mensagens de status podem ser mostradas para o status de **processamento da Associação** :

* **Avaliando**:  A alteração do grupo foi recebida e as atualizações estão sendo avaliadas.
* **Processamento**: As atualizações estão sendo processadas.
* **Atualização concluída**: O processamento foi concluído e todas as atualizações aplicáveis foram feitas.
* **Erro de processamento**:  O processamento não pôde ser concluído devido a um erro ao avaliar a regra de associação.
* **Atualização pausada**: As atualizações da regra de associação dinâmica foram pausadas pelo administrador. MembershipRuleProcessingState é definido como "Paused".

As seguintes mensagens de status podem ser mostradas para o status da **última atualização de associação** :

* **Data e hora**: A última vez em que a associação foi atualizada.
* **Em andamento**: As atualizações estão em andamento no momento.
* **Desconhecido**: A hora da última atualização não pode ser recuperada. O grupo pode ser novo.

Se ocorrer um erro durante o processamento da regra de associação para um grupo específico, um alerta será mostrado na parte superior da **página Visão geral** do grupo. Se nenhuma atualização de associação dinâmica pendente puder ser processada para todos os grupos dentro do locatário para mais de 24 horas, um alerta será mostrado na parte superior de **todos os grupos**.

![Processando alertas de mensagens de erro](./media/groups-create-rule/processing-error.png)

## <a name="next-steps"></a>Passos seguintes

Esses artigos fornecem informações adicionais sobre como trabalhar com grupos dinâmicos no Azure AD.

* Para obter uma referência completa à estrutura de regras dinâmicas, consulte [sintaxe de regra de associação dinâmica](groups-dynamic-membership.md).
* [Crie um grupo de associação estático e adicione membros](../fundamentals/active-directory-groups-create-azure-portal.md).
