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
ms.date: 08/30/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c387e2d78adcaebc430073a2a45818c4a0928b9f
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422365"
---
# <a name="update-a-dynamic-group-to-manage-membership-in-azure-active-directory"></a>Atualizar um grupo dinâmico para gerenciar a associação no Azure Active Directory

No Azure Active Directory (AD do Azure), você pode usar regras para determinar a associação de grupo com base nas propriedades do usuário ou do dispositivo. Este artigo informa como configurar uma regra para um grupo dinâmico no portal do Azure.
A associação dinâmica tem suporte para grupos de segurança ou grupos do Office 365. Quando uma regra de associação de grupo é aplicada, os atributos de usuário e de dispositivo são avaliados para correspondências com a regra de associação. Quando um atributo é alterado para um usuário ou dispositivo, todas as regras de grupo dinâmico na organização são processadas para alterações de associação. Os usuários e dispositivos serão adicionados ou removidos se atenderem às condições de um grupo.

## <a name="rule-builder-in-the-azure-portal"></a>Construtor de regras no portal do Azure

O Azure AD fornece um construtor de regras para criar e atualizar suas regras importantes mais rapidamente. O construtor de regras dá suporte à construção de até cinco expressões. O construtor de regras torna mais fácil formar uma regra com algumas expressões simples, no entanto, ela não pode ser usada para reproduzir todas as regras. Se o construtor de regras não oferecer suporte à regra que você deseja criar, você poderá usar a caixa de texto.

Aqui estão alguns exemplos de regras avançadas ou sintaxe para as quais recomendamos que você construa usando a caixa de texto:

- Regra com mais de cinco expressões
- A regra de relatórios diretos
- Definindo a [precedência de operador](groups-dynamic-membership.md#operator-precedence)
- [Regras com expressões complexas](groups-dynamic-membership.md#rules-with-complex-expressions); por exemplo `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> O construtor de regras pode não ser capaz de exibir algumas regras construídas na caixa de texto. Você poderá ver uma mensagem quando o construtor de regras não puder exibir a regra. O construtor de regras não altera a sintaxe com suporte, a validação nem o processamento de regras de grupo dinâmicas de forma alguma.

![Adicionar regra de associação a um grupo dinâmico](./media/groups-update-rule/update-dynamic-group-rule.png)

Para obter exemplos de sintaxe, propriedades com suporte, operadores e valores para uma regra de associação, consulte [regras de associação dinâmica para grupos no Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-update-a-group-membership-rule"></a>Para atualizar uma regra de associação de grupo

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com) com uma conta que esteja na função administrador global, administrador de grupo, administrador do Intune ou administrador de usuário no locatário.
1. Selecione **grupos** > **todos os grupos**.
1. Selecione um grupo para abrir seu perfil.
1. Na página perfil do grupo, selecione regras de **associação dinâmica**. O construtor de regras dá suporte a até cinco expressões. Para adicionar mais de cinco expressões, você deve usar a caixa de texto.

   ![Adicionar regra de associação a um grupo dinâmico](./media/groups-update-rule/update-dynamic-group-rule.png)

1. Para ver as propriedades de extensão personalizadas disponíveis para sua regra de associação:
   1. Selecionar **obter propriedades de extensão personalizadas**
   1. Insira a ID do aplicativo e, em seguida, selecione **Atualizar Propriedades**.
1. Depois de atualizar a regra, selecione **salvar**.

Se a regra que você inseriu não for válida, uma explicação do motivo pelo qual a regra não pôde ser processada será exibida em uma notificação do Azure no Portal. Leia com atenção para entender como corrigir a regra.

## <a name="check-processing-status-for-a-rule"></a>Verificar o status de processamento de uma regra

Você pode ver o status de processamento de associação e a data da última atualização na página **visão geral** do grupo.
  
  ![exibição do status do grupo dinâmico](./media/groups-create-rule/group-status.png)

As seguintes mensagens de status podem ser mostradas para o status de **processamento da Associação** :

- **Avaliando**: a alteração do grupo foi recebida e as atualizações estão sendo avaliadas.
- **Processamento**: as atualizações estão sendo processadas.
- **Atualização concluída**: o processamento foi concluído e todas as atualizações aplicáveis foram feitas.
- **Erro de processamento**: não foi possível concluir o processamento devido a um erro ao avaliar a regra de associação.
- **Atualização pausada**: atualizações de regra de associação dinâmica foram pausadas pelo administrador. MembershipRuleProcessingState é definido como "Paused".

As seguintes mensagens de status podem ser mostradas para o status da **última atualização de associação** :

- **Data e hora**: a última vez em que a associação foi atualizada.
- **Em andamento**: as atualizações estão em andamento no momento.
- **Desconhecido**: a hora da última atualização não pode ser recuperada. O grupo pode ser novo.

Se ocorrer um erro durante o processamento da regra de associação para um grupo específico, um alerta será mostrado na parte superior da **página Visão geral** do grupo. Se nenhuma atualização de associação dinâmica pendente puder ser processada para todos os grupos dentro do locatário para mais de 24 horas, um alerta será mostrado na parte superior de **todos os grupos**.

![Processando alertas de mensagens de erro](./media/groups-create-rule/processing-error.png)

## <a name="next-steps"></a>Passos Seguintes

Esses artigos fornecem informações adicionais sobre como trabalhar com grupos dinâmicos no Azure AD.

- Para obter uma referência completa à estrutura de regras dinâmicas, consulte [sintaxe de regra de associação dinâmica](groups-dynamic-membership.md).
- [Crie um grupo de associação estático e adicione membros](../fundamentals/active-directory-groups-create-azure-portal.md).
