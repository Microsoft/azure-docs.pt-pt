---
title: Criar ou editar um grupo dinâmico e obter o estatuto - Azure AD / Microsoft Docs
description: Como criar ou atualizar uma regra de membro do grupo no portal Azure e verificar o seu estado de processamento.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a25caa3b59d242b8dcc8a52e1a5d684815cf7bf
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96547785"
---
# <a name="create-or-update-a-dynamic-group-in-azure-active-directory"></a>Criar ou atualizar um grupo dinâmico no Azure Ative Directory

No Azure Ative Directory (Azure AD), pode utilizar regras para determinar a adesão ao grupo com base nas propriedades do utilizador ou do dispositivo. Este artigo diz como configurar uma regra para um grupo dinâmico no portal Azure.
A adesão dinâmica é suportada por grupos de segurança ou Grupos Microsoft 365. Quando uma regra de membro do grupo é aplicada, os atributos do utilizador e do dispositivo são avaliados para partidas com a regra de adesão. Quando um atributo muda para um utilizador ou dispositivo, todas as regras dinâmicas do grupo na organização são processadas para alterações de membros. Os utilizadores e dispositivos são adicionados ou removidos se cumprirem as condições de um grupo. Os grupos de segurança podem ser utilizados para dispositivos ou utilizadores, mas os Grupos Microsoft 365 podem ser apenas grupos de utilizadores. A utilização de grupos Dinâmicos requer licença P1 premium Azure AD. Consulte [as regras de adesão dynamic para grupos](./groups-dynamic-membership.md) para obter mais detalhes. 

## <a name="rule-builder-in-the-azure-portal"></a>Construtor de regras no portal Azure

A Azure AD fornece um construtor de regras para criar e atualizar as suas regras importantes mais rapidamente. O construtor de regras apoia a construção até cinco expressões. O construtor de regras torna mais fácil formar uma regra com algumas expressões simples, no entanto, não pode ser usado para reproduzir todas as regras. Se o construtor de regras não apoiar a regra que pretende criar, pode utilizar a caixa de texto.

Aqui estão alguns exemplos de regras avançadas ou sintaxe para as quais recomendamos que construa usando a caixa de texto:

- Governar com mais de cinco expressões
- A regra dos relatórios diretos
- Definição [da precedência do operador](groups-dynamic-membership.md#operator-precedence)
- [Regras com expressões complexas;](groups-dynamic-membership.md#rules-with-complex-expressions) Por exemplo `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> O construtor de regras pode não ser capaz de exibir algumas regras construídas na caixa de texto. Pode ver uma mensagem quando o construtor de regras não é capaz de mostrar a regra. O construtor de regras não altera a sintaxe, validação ou processamento de regras dinâmicas do grupo de qualquer forma.

![Screenshot que mostra a página "Regras de adesão dinâmicas" com a ação "Adicionar expressão" no separador "Regras de Configuração" selecionadas.](./media/groups-create-rule/update-dynamic-group-rule.png)

Por exemplo, sintaxe, propriedades suportadas, operadores e valores para uma regra de adesão, consulte [regras de adesão dinâmica para grupos no Azure Ative Directory](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>Para criar uma regra de adesão ao grupo

1. Inscreva-se no [centro de administração Azure AD](https://aad.portal.azure.com) com uma conta que está no administrador global, administrador intune ou administrador do utilizador na organização Azure AD.
1. Procure e selecione **Grupos.**
1. Selecione **Todos os grupos** e selecione **Novo grupo**.

   ![Selecione o comando para adicionar novo grupo](./media/groups-create-rule/create-new-group-azure-active-directory.png)

1. Na página **do Grupo,** insira um nome e descrição para o novo grupo. Selecione um **tipo de adesão** para utilizadores ou dispositivos e, em seguida, selecione **Adicionar consulta dinâmica**. O construtor de regras suporta até cinco expressões. Para adicionar mais de cinco expressões, deve utilizar a caixa de texto.

   ![Screenshot que mostra a página "Todos os grupos" com a ação "Novo grupo" selecionada.](./media/groups-create-rule/add-dynamic-group-rule.png)

1. Para ver as propriedades de extensão personalizadas disponíveis para a sua consulta de adesão:
   1. Selecione **Obtenha propriedades de extensão personalizadas**
   1. Introduza o ID da aplicação e, em seguida, **selecione Propriedades Refresh**.
1. Depois de criar a regra, **selecione Save**.
1. Selecione **Criar** na página **de grupo Nova** para criar o grupo.

Se a regra que inseriu não for válida, uma explicação do porquê da regra não poder ser processada é exibida numa notificação do Azure no portal. Leia-o atentamente para entender como corrigir a regra.

## <a name="to-update-an-existing-rule"></a>Para atualizar uma regra existente

1. Inscreva-se no [centro de administração Azure AD](https://aad.portal.azure.com) com uma conta que está no administrador global, administrador do grupo, administrador intune ou administrador do utilizador na organização Azure AD.
1. Selecione **Grupos**  >  **Todos os grupos**.
1. Selecione um grupo para abrir o seu perfil.
1. Na página de perfil do grupo, selecione **Regras de adesão Dinâmica**. O construtor de regras suporta até cinco expressões. Para adicionar mais de cinco expressões, deve utilizar a caixa de texto.

   ![Adicionar regra de adesão para um grupo dinâmico](./media/groups-create-rule/update-dynamic-group-rule.png)

1. Para ver as propriedades de extensão personalizadas disponíveis para a sua regra de adesão:
   1. Selecione **Obtenha propriedades de extensão personalizadas**
   1. Introduza o ID da aplicação e, em seguida, **selecione Propriedades Refresh**.
1. Depois de atualizar a regra, **selecione Guardar**.

## <a name="turn-on-or-off-welcome-email"></a>Ligue ou desligue o e-mail de boas-vindas

Quando um novo grupo Microsoft 365 é criado, uma notificação de e-mail bem-vinda é enviada para os utilizadores que são adicionados ao grupo. Mais tarde, se quaisquer atributos de um utilizador ou dispositivo mudarem, todas as regras dinâmicas do grupo na organização são processadas para alterações de membros. Os utilizadores que são adicionados também recebem a notificação de boas-vindas. Pode desligar este comportamento em [Exchange PowerShell](/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps&preserve-view=true).

## <a name="check-processing-status-for-a-rule"></a>Verifique o estado de processamento de uma regra

Pode ver o estado de processamento da adesão e a última data atualizada na página **'Visão Geral'** do grupo.
  
  ![exibição do estado de grupo dinâmico](./media/groups-create-rule/group-status.png)

As seguintes mensagens de estado podem ser mostradas para o estado **de processamento da adesão:**

- **Avaliação:** A alteração de grupo foi recebida e as atualizações estão a ser avaliadas.
- **Processamento:** As atualizações estão a ser processadas.
- **Atualização completa:** O processamento foi concluído e todas as atualizações aplicáveis foram es feitas.
- **Erro de processamento**: O processamento não pôde ser concluído devido a um erro de avaliação da regra de adesão.
- **Atualização interrompida**: As atualizações dinâmicas da regra de adesão foram interrompidas pelo administrador. MembershipRuleProcessingState está definido para "Pausa".

As seguintes mensagens de estado podem ser mostradas para **o estado atualizado da última atualização da adesão:**

- &lt;**Data e hora** &gt; : A última vez que a adesão foi atualizada.
- **Em curso**: As atualizações estão atualmente em curso.
- **Desconhecido**: A última atualização não pode ser recuperada. O grupo pode ser novo.

Se ocorrer um erro durante o processamento da regra de adesão a um grupo específico, é apresentado um alerta no topo da **página 'Visão Geral'** do grupo. Se não houver atualizações dinâmicas pendentes de adesão para todos os grupos dentro da organização por mais de 24 horas, é apresentado um alerta no topo de **todos os grupos**.

![alertas de mensagens de erro de processamento](./media/groups-create-rule/processing-error.png)

Estes artigos fornecem informações adicionais sobre grupos no Diretório Ativo Azure.

- [Ver grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Criar um novo grupo e adicionar membros](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Gerir definições de um grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Gerir associações de um grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Gerir regras dinâmicas dos utilizadores num grupo](groups-dynamic-membership.md)