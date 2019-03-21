---
title: Criar um grupo dinâmico e verificar o estado - Azure Active Directory | Documentos da Microsoft
description: Como criar uma regra de associação de grupo no portal do Azure, verifique o estado.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f828ff83e6b9c60eb08edef7f47e88185fb5aef8
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58199686"
---
# <a name="create-a-dynamic-group-and-check-status"></a>Criar um grupo dinâmico e verificar o Estado

No Azure Active Directory (Azure AD), pode utilizar regras para determinar a associação de grupo com base nas propriedades de utilizador ou dispositivo. Este artigo mostra como configurar uma regra para um grupo dinâmico no portal do Azure.
Associação de grupo dinâmica é suportada para grupos de segurança ou grupos do Office 365. Quando uma regra de associação de grupo é aplicada, os atributos de utilizador e dispositivo são avaliados para correspondências com a regra de associação. Quando um atributo é alterado para um utilizador ou dispositivo, todas as regras de grupo dinâmico na organização são processadas para alterações de associação. Utilizadores e dispositivos são adicionados ou removidos se cumprem as condições para um grupo.

Para obter exemplos de sintaxe, propriedades suportadas, operadores e valores para uma regra de associação, consulte [regras de associação dinâmica para grupos no Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>Para criar uma regra de associação de grupo

1. Inicie sessão para o [Centro de administração do Azure AD](https://aad.portal.azure.com) com uma conta que seja o Administrador Global, o administrador do Intune ou a função de administrador do utilizador no inquilino.
2. Selecione **grupos**.
3. Selecione **todos os grupos**e selecione **novo grupo**.

   ![Selecione o comando para adicionar o novo grupo](./media/groups-create-rule/new-group-creation.png)

4. Sobre o **grupo** página, introduza um nome e descrição para o novo grupo. Selecione um **tipo de associação** para utilizadores ou dispositivos e em seguida, selecione **adicionar consulta dinâmica**. Pode usar o construtor de regra para criar uma regra simples, ou [escreve uma regra de associação](groups-dynamic-membership.md).

   ![Adicionar regra de associação para um grupo dinâmico](./media/groups-create-rule/add-dynamic-group-rule.png)

5. Para ver as propriedades de extensão personalizado disponíveis para a consulta de associação
   1. Selecione **obter propriedades de extensão personalizado**
   2. Introduza o ID da aplicação e, em seguida, selecione **atualizar propriedades**. 
6. Depois de criar a regra, selecione **adicionar consulta** na parte inferior do painel.
7. Selecione **Create** sobre o **grupo** painel para criar o grupo.

Se a regra que introduziu não é válida, obter uma explicação de por que não foi possível processar a regra é apresentada no canto superior direito do portal. Lê-lo cuidadosamente para compreender como corrigir a regra.

## <a name="turn-on-or-off-welcome-email"></a>Ativar ou desativar o e-mail de boas-vindas

Quando é criado um novo grupo do Office 365, uma notificação de boas-vindas é enviada os utilizadores que são adicionados ao grupo. Mais tarde, se alterar os atributos de um utilizador ou dispositivo, todas as regras de grupo dinâmico na organização são processadas para alterações de associação. Em seguida, a utilizadores que são adicionados também recebem a notificação de boas-vindas. Pode desativar esse comportamento no [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps). 

## <a name="check-processing-status-for-a-rule"></a>Verificar o estado de processamento para uma regra

Pode ver a associação ao processar o estado e a data da última atualização no **descrição geral** página para o grupo.
  
  ![exibição de estado de grupo dinâmico](./media/groups-create-rule/group-status.png)

As seguintes mensagens de estado podem ser exibidas para **processamento de associação** Estado:

* **Avaliar**:  Foi recebida a alteração do grupo e as atualizações estão a ser avaliadas.
* **Processamento**: As atualizações estão a ser processadas.
* **Atualização concluída**: Processamento foi concluído e todas as atualizações aplicáveis foram feitas.
* **Erro de processamento**:  Não foi possível concluir o processamento devido a um erro ao avaliar a regra de associação.
* **Atualizações em pausa**: Atualizações de regra de associação de grupo dinâmica foram interrompidas pelo administrador. MembershipRuleProcessingState está definido como "Em pausa".

As seguintes mensagens de estado podem ser exibidas para **associação atualizado pela última vez** Estado:

* &lt;**Data e hora**&gt;: A última vez em que a associação foi atualizada.
* **Em curso**: As atualizações estão atualmente em curso.
* **Desconhecido**: Não é possível obter a hora da última atualização. O grupo poderá ser novo.

Se ocorrer um erro ao processar a regra de associação para um grupo específico, um alerta é mostrado na parte superior dos **página de descrição geral** para o grupo. Se não pendente de associação de grupo dinâmica atualizações podem ser processadas para todos os grupos no inquilino para obter mais, em seguida, 24 horas, um alerta é mostrado na parte superior do **todos os grupos**.

![alertas de mensagem de erro de processamento](./media/groups-create-rule/processing-error.png)

Estes artigos fornecem informações adicionais sobre os grupos no Azure Active Directory.

* [Ver grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Criar um novo grupo e adicionar membros](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Gerir definições de um grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Gerir associações de um grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Gerir regras dinâmicas dos utilizadores num grupo](groups-dynamic-membership.md)
