---
title: Características da interação múltipla do arrendatário - Azure AD [ Microsoft Docs
description: Gerencie os seus inquilinos Azure Ative, compreendendo os seus inquilinos como recursos totalmente independentes
services: active-tenant
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 11/08/2019
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4eb09ab7fa31af5edf14b113a6a88e08df2d115
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562263"
---
# <a name="understand-how-multiple-azure-active-directory-tenants-interact"></a>Entenda como vários inquilinos do Azure Ative Directory interagem

No Azure Ative Directory (Azure AD), cada inquilino é um recurso totalmente independente: um par que é logicamente independente dos outros inquilinos que gere. Não há relação entre pais e filhos entre inquilinos. Esta independência entre inquilinos inclui independência de recursos, independência administrativa e independência sincronizada.

## <a name="resource-independence"></a>Independência de recursos
* Se criar ou apagar um recurso num inquilino, não tem qualquer impacto em qualquer recurso de outro inquilino, com exceção parcial dos utilizadores externos. 
* Se usar um dos seus nomes de domínio com um inquilino, não pode ser usado com qualquer outro inquilino.

## <a name="administrative-independence"></a>Independência administrativa
Se um utilizador não administrativo do inquilino 'Contoso' criar um teste de inquilino de teste, então:

* Por padrão, o utilizador que cria um inquilino é adicionado como um utilizador externo nesse novo inquilino, e atribuio o papel de administrador global a esse inquilino.
* Os administradores do arrendatário 'Contoso' não têm privilégios administrativos diretos para o 'Teste' do arrendatário, a menos que um administrador de 'Test' lhes conceda especificamente esses privilégios. No entanto, os administradores da 'Contoso' podem controlar o acesso ao 'Teste' do arrendatário se controlarem a conta de utilizador que criou o 'Teste'.
* Se adicionar/remover uma função de administrador para um utilizador num inquilino, a alteração não afeta as funções de administrador que o utilizador tem noutro inquilino.

## <a name="synchronization-independence"></a>Independência da sincronização
Pode configurar cada inquilino da AD Azure de forma independente para obter dados sincronizados a partir de uma única instância de qualquer um:

* A ferramenta Azure AD Connect, para sincronizar dados com uma única floresta ad.
* O Azure Ative tenant Connector for Forefront Identity Manager, para sincronizar dados com uma ou mais florestas no local e/ou fontes de dados da AD não-Azure.

## <a name="add-an-azure-ad-tenant"></a>Adicione um inquilino Azure AD
Para adicionar um inquilino Azure AD no portal Azure, inscreva-se [no portal Azure](https://portal.azure.com) com uma conta que é administrador global da Azure AD, e, à esquerda, selecione **New**.

> [!NOTE]
> Ao contrário de outros recursos Azure, os seus inquilinos não são recursos infantis de uma subscrição Azure. Se a sua subscrição Azure for cancelada ou expirada, ainda pode aceder aos dados do seu inquilino utilizando o Azure PowerShell, o Microsoft Graph API ou o centro de administração da Microsoft 365. Também pode [associar outra subscrição ao inquilino.](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
>

## <a name="next-steps"></a>Passos seguintes
Para uma visão geral alargada das questões de licenciamento da Azure AD e das melhores práticas, veja o que é o licenciamento de [inquilino sinuoso Azure Ative?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
