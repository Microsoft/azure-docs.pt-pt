---
title: Criar um grupo dinâmico e verificar o status-Azure Active Directory | Microsoft Docs
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
ms.openlocfilehash: cb4f9d2f78857231d0ecd81a2538a75b4b8a2f74
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650315"
---
# <a name="create-a-dynamic-group-and-check-status"></a>Criar um grupo dinâmico e verificar o status

No Azure Active Directory (AD do Azure), você pode usar regras para determinar a associação de grupo com base nas propriedades do usuário ou do dispositivo. Este artigo informa como configurar uma regra para um grupo dinâmico no portal do Azure.
A associação dinâmica tem suporte para grupos de segurança ou grupos do Office 365. Quando uma regra de associação de grupo é aplicada, os atributos de usuário e de dispositivo são avaliados para correspondências com a regra de associação. Quando um atributo é alterado para um usuário ou dispositivo, todas as regras de grupo dinâmico na organização são processadas para alterações de associação. Os usuários e dispositivos serão adicionados ou removidos se atenderem às condições de um grupo. Os grupos de segurança podem ser usados para dispositivos ou usuários, mas os grupos do Office 365 podem ser apenas grupos de usuários.

Para obter exemplos de sintaxe, propriedades com suporte, operadores e valores para uma regra de associação, consulte [regras de associação dinâmica para grupos no Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>Para criar uma regra de associação de grupo

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com) com uma conta que esteja na função administrador global, administrador do Intune ou administrador de usuários no locatário.
2. Selecione **grupos**.
3. Selecione **todos os grupos**e selecione **novo grupo**.

   ![Selecione o comando para adicionar um novo grupo](./media/groups-create-rule/new-group-creation.png)

4. Na página **grupo** , insira um nome e uma descrição para o novo grupo. Selecione um **tipo de associação** para usuários ou dispositivos e, em seguida, selecione **Adicionar consulta dinâmica**. O construtor de regras dá suporte a até cinco expressões. Para adicionar uma sexta ou qualquer expressão subsequente, você deve usar a caixa de texto.

   ![Adicionar regra de associação a um grupo dinâmico](./media/groups-create-rule/add-dynamic-group-rule.png)

5. Para ver as propriedades de extensão personalizadas disponíveis para sua consulta de associação
   1. Selecionar **obter propriedades de extensão personalizadas**
   2. Insira a ID do aplicativo e, em seguida, selecione **Atualizar Propriedades**.
6. Depois de criar a regra, selecione **salvar**.
7. Selecione **criar** na página **novo grupo** para criar o grupo.

Se a regra que você inseriu não for válida, uma explicação do motivo pelo qual a regra não pôde ser processada será exibida na notificação do Azure no Portal. Leia com atenção para entender como corrigir a regra.

## <a name="turn-on-or-off-welcome-email"></a>Ativar ou desativar o email de boas-vindas

Quando um novo grupo do Office 365 é criado, uma notificação por email de boas-vindas é enviada aos usuários que são adicionados ao grupo. Posteriormente, se qualquer atributo de um usuário ou dispositivo for alterado, todas as regras dinâmicas do grupo na organização serão processadas para alterações de associação. Os usuários que foram adicionados também recebem a notificação de boas-vindas. Você pode desativar esse comportamento no [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps).

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

* &lt;**Data e hora**&gt;: A última vez em que a associação foi atualizada.
* **Em andamento**: As atualizações estão em andamento no momento.
* **Desconhecido**: A hora da última atualização não pode ser recuperada. O grupo pode ser novo.

Se ocorrer um erro durante o processamento da regra de associação para um grupo específico, um alerta será mostrado na parte superior da **página Visão geral** do grupo. Se nenhuma atualização de associação dinâmica pendente puder ser processada para todos os grupos dentro do locatário para mais de 24 horas, um alerta será mostrado na parte superior de **todos os grupos**.

![Processando alertas de mensagens de erro](./media/groups-create-rule/processing-error.png)

Esses artigos fornecem informações adicionais sobre grupos no Azure Active Directory.

* [Ver grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Criar um novo grupo e adicionar membros](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Gerir definições de um grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Gerir associações de um grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Gerir regras dinâmicas dos utilizadores num grupo](groups-dynamic-membership.md)
