---
title: Criar ou editar um grupo dinâmico e obter estatuto - Azure AD / Microsoft Docs
description: Como criar ou atualizar uma regra de adesão ao grupo no portal Azure, e verificar o seu estado de processamento.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/07/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2ed7f27e2145f666f38eec5ddc6c985a4d32138
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79266380"
---
# <a name="create-or-update-a-dynamic-group-in-azure-active-directory"></a>Criar ou atualizar um grupo dinâmico no Diretório Ativo do Azure

No Azure Ative Directory (Azure AD), pode utilizar regras para determinar a adesão ao grupo com base nas propriedades do utilizador ou do dispositivo. Este artigo diz como criar uma regra para um grupo dinâmico no portal Azure.
A adesão dinâmica é apoiada para grupos de segurança ou grupos do Office 365. Quando uma regra de membro do grupo é aplicada, os atributos do utilizador e do dispositivo são avaliados para os jogos com a regra de adesão. Quando um atributo muda para um utilizador ou dispositivo, todas as regras dinâmicas do grupo na organização são processadas para alterações de adesão. Os utilizadores e dispositivos são adicionados ou removidos se cumprirem as condições para um grupo. Os grupos de segurança podem ser utilizados para dispositivos ou utilizadores, mas os grupos do Office 365 podem ser apenas grupos de utilizadores.

## <a name="rule-builder-in-the-azure-portal"></a>Construtor de regras no portal Azure

A Azure AD fornece um construtor de regras para criar e atualizar as suas regras importantes mais rapidamente. O construtor de regras apoia a construção até cinco expressões. O construtor de regras torna mais fácil formar uma regra com algumas expressões simples, no entanto, não pode ser usado para reproduzir todas as regras. Se o construtor de regras não apoiar a regra que pretende criar, pode utilizar a caixa de texto.

Aqui estão alguns exemplos de regras avançadas ou sintaxe para as quais recomendamos que construa usando a caixa de texto:

- Governar com mais de cinco expressões
- A regra dos relatórios diretos
- Definição da [precedência do operador](groups-dynamic-membership.md#operator-precedence)
- [Regras com expressões complexas;](groups-dynamic-membership.md#rules-with-complex-expressions) por exemplo, `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> O construtor de regras pode não ser capaz de exibir algumas regras construídas na caixa de texto. Pode ver uma mensagem quando o construtor de regras não é capaz de exibir a regra. O construtor de regras não altera de forma alguma a sintaxe suportada, validação ou processamento de regras dinâmicas do grupo.

![Adicionar regra de adesão para um grupo dinâmico](./media/groups-create-rule/update-dynamic-group-rule.png)

Por exemplo, sintaxe, propriedades suportadas, operadores e valores para uma regra de adesão, ver regras de [adesão dinâmicas para grupos no Diretório Ativo Azure](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>Para criar uma regra de associação de grupo

1. Inscreva-se no [centro de administração da Azure AD](https://aad.portal.azure.com) com uma conta que se encontra no administrador global, administrador intune ou papel de administrador de utilizador no inquilino.
1. Procure e selecione **Grupos**.
1. **Selecione Todos os grupos**e selecione **Novo grupo**.

   ![Selecione o comando para adicionar novo grupo](./media/groups-create-rule/create-new-group-azure-active-directory.png)

1. Na página do **Grupo,** insira um nome e descrição para o novo grupo. Selecione um **tipo de Membro** para utilizadores ou dispositivos e, em seguida, selecione Adicionar consulta **dinâmica**. O construtor de regras suporta até cinco expressões. Para adicionar mais de cinco expressões, deve utilizar a caixa de texto.

   ![Adicionar regra de adesão para um grupo dinâmico](./media/groups-create-rule/add-dynamic-group-rule.png)

1. Para ver as propriedades de extensão personalizadas disponíveis para a sua consulta de adesão:
   1. Selecione **Obter propriedades de extensão personalizadas**
   1. Introduza o ID da aplicação e, em seguida, selecione **propriedades Refresh**.
1. Depois de criar a regra, selecione **Guardar**.
1. Selecione **Criar** na página do **novo grupo** para criar o grupo.

Se a regra que inseriu não for válida, uma explicação do porquê da regra não poder ser processada é exibida numa notificação do Azure no portal. Leia-o atentamente para entender como corrigir a regra.

## <a name="to-update-an-existing-rule"></a>Para atualizar uma regra existente

1. Inscreva-se no [centro de administração da Azure AD](https://aad.portal.azure.com) com uma conta que está no administrador global, administrador do Grupo, administrador intune ou papel de administrador de utilizador no inquilino.
1. Selecione **Grupos** > **Todos os grupos**.
1. Selecione um grupo para abrir o seu perfil.
1. Na página de perfil do grupo, selecione regras de **adesão dinâmicas.** O construtor de regras suporta até cinco expressões. Para adicionar mais de cinco expressões, deve utilizar a caixa de texto.

   ![Adicionar regra de adesão para um grupo dinâmico](./media/groups-create-rule/update-dynamic-group-rule.png)

1. Para ver as propriedades de extensão personalizadas disponíveis para a sua regra de adesão:
   1. Selecione **Obter propriedades de extensão personalizadas**
   1. Introduza o ID da aplicação e, em seguida, selecione **propriedades Refresh**.
1. Depois de atualizar a regra, selecione **Guardar**.

## <a name="turn-on-or-off-welcome-email"></a>Ligue ou desligue e-mail de boas-vindas

Quando um novo grupo do Office 365 é criado, é enviada uma notificação de e-mail de boas-vindas aos utilizadores que são adicionados ao grupo. Mais tarde, se algum atributo de um utilizador ou dispositivo mudar, todas as regras dinâmicas do grupo na organização são processadas para alterações de adesão. Os utilizadores que são adicionados também recebem a notificação de boas-vindas. Pode desligar este comportamento na [Exchange PowerShell.](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps)

## <a name="check-processing-status-for-a-rule"></a>Verifique o estado de processamento por uma regra

Pode ver o estado de processamento de membros e a última data atualizada na página **de visão geral** do grupo.
  
  ![exibição de estado dinâmico do grupo](./media/groups-create-rule/group-status.png)

As seguintes mensagens de estado podem ser mostradas para o estado de **processamento da Adesão:**

- **Avaliação**: A alteração do grupo foi recebida e as atualizações estão a ser avaliadas.
- **Processamento**: As atualizações estão a ser processadas.
- **Atualização completa**: O processamento foi concluído e todas as atualizações aplicáveis foram feitas.
- **Erro de processamento**: O processamento não pôde ser concluído devido a um erro de avaliação da regra de adesão.
- **Atualização pausada**: As atualizações dinâmicas da regra de adesão foram interrompidas pelo administrador. MembershipRuleProcessingState está definido para "Paused".

As seguintes mensagens de estado podem ser mostradas para **a Adesão última** situação atualizada:

- &lt;**Data e hora**&gt;: A última vez que a adesão foi atualizada.
- **Em curso**: As atualizações estão atualmente em curso.
- **Desconhecido**: O último tempo de atualização não pode ser recuperado. O grupo pode ser novo.

Se ocorrer um erro durante o processamento da regra de adesão para um grupo específico, é apresentado um alerta no topo da **página de visão geral** do grupo. Se não forem processadas atualizações dinâmicas de adesão para todos os grupos dentro do arrendatário por mais de 24 horas, é apresentado um alerta no topo de **Todos os grupos**.

![processamento alertas de mensagem de erro](./media/groups-create-rule/processing-error.png)

Estes artigos fornecem informações adicionais sobre grupos no Diretório Ativo Azure.

- [Ver grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Criar um novo grupo e adicionar membros](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Gerir definições de um grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Gerir associações de um grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Gerir regras dinâmicas dos utilizadores num grupo](groups-dynamic-membership.md)
