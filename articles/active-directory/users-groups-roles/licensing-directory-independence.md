---
title: Características da interação múltipla do arrendatário - Azure AD [ Microsoft Docs
description: Compreender os seus inquilinos do Azure Ative Directory como organizações totalmente independentes
services: active-tenant
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 04/07/2020
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: 175d9ce7db1657e0e654f46adaf8a8d8ef28c25e
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878124"
---
# <a name="understand-how-multiple-azure-active-directory-organizations-interact"></a>Entenda como várias organizações de Diretório Saem do Azure Ative

No Azure Ative Directory (Azure AD), cada inquilino é uma organização totalmente independente: um par que é logicamente independente das outras organizações azure aD que gere. Esta independência entre organizações inclui independência de recursos, independência administrativa e independência sincronizada. Não há relação entre pais e filhos entre organizações.

## <a name="resource-independence"></a>Independência de recursos

* Se criar ou apagar um recurso Azure AD numa organização, não tem qualquer impacto em nenhum recurso de outra organização, com exceção parcial dos utilizadores externos.
* Se registar um dos seus nomes de domínio com uma organização, não pode ser usado por nenhuma outra organização.

## <a name="administrative-independence"></a>Independência administrativa

Se um utilizador não administrativo da organização 'Contoso' criar uma organização de teste 'Test', então:

* Por padrão, o utilizador que cria uma organização é adicionado como um utilizador externo nessa nova organização, e atribuiu o papel de administrador global nessa organização.
* Os administradores da organização 'Contoso' não têm privilégios administrativos diretos para a organização 'Test', a menos que um administrador de 'Test' lhes conceda especificamente esses privilégios. No entanto, os administradores da 'Contoso' podem controlar o acesso à organização 'Test' se controlarem a conta de utilizador que criou o 'Teste'.
* Se adicionar ou remover uma função de AD Azure para um utilizador numa organização, a alteração não afeta as funções que o utilizador está atribuído em qualquer outra organização da AD Azure.

## <a name="synchronization-independence"></a>Independência da sincronização

Pode configurar cada organização da AD Azure de forma independente para obter dados sincronizados a partir de uma única instância de qualquer um:

* A ferramenta Azure AD Connect, para sincronizar dados com uma única floresta ad.
* O Conector de Diretório Ativo Azure para Gestor de Identidade de Vanguarda, para sincronizar dados com uma ou mais florestas no local e/ou fontes de dados da AD não-Azure.

## <a name="add-an-azure-ad-organization"></a>Adicione uma organização azure AD

Para adicionar uma organização Azure AD no portal Azure, inscreva-se [no portal Azure](https://portal.azure.com) com uma conta que é administradorglobal da Azure AD, e selecione **New**.

> [!NOTE]
> Ao contrário de outros recursos Azure, as suas organizações de AD Azure não são recursos infantis de uma subscrição Azure. Se a sua subscrição Azure for cancelada ou expirada, ainda pode aceder aos dados da sua organização Azure AD utilizando o Azure PowerShell, o Microsoft Graph API ou o centro de administração da Microsoft 365. Também pode [associar outra subscrição à organização.](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
>

## <a name="next-steps"></a>Passos seguintes

Para considerações de licenciamento da Azure AD e boas práticas, consulte o licenciamento de [Diretório Ativo Azure?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
