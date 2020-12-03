---
title: Características da interação múltiplo de inquilino - Azure AD / Microsoft Docs
description: Compreender a independência de dados das suas organizações do Azure Ative Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: overview
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ce3e4c6a7708fba15560564577c9b01722c8aec
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548057"
---
# <a name="understand-how-multiple-azure-active-directory-organizations-interact"></a>Entenda como várias organizações de Diretório Ativo Azure interagem

No Azure Ative Directory (Azure AD), cada organização Azure AD é totalmente independente: um par que é logicamente independente das outras organizações AD Azure que gere. Esta independência entre organizações inclui independência de recursos, independência administrativa e independência de sincronização. Não existe uma relação pai-filho entre organizações.

## <a name="resource-independence"></a>Independência de recursos

* Se criar ou eliminar um recurso AD Azure numa organização, não tem qualquer impacto em nenhum recurso de outra organização, com a exceção parcial dos utilizadores externos.
* Se registar um dos seus nomes de domínio com uma organização, não pode ser usado por nenhuma outra organização.

## <a name="administrative-independence"></a>Independência administrativa

Se um utilizador não administrativo da organização 'Contoso' criar uma organização de teste 'Test', então:

* Por padrão, o utilizador que cria uma organização é adicionado como um utilizador externo nessa nova organização, e atribuiu o papel de administrador global nessa organização.
* Os administradores da organização 'Contoso' não têm privilégios administrativos diretos para a organização 'Test', a menos que um administrador de 'Test' lhes conceda especificamente esses privilégios. No entanto, os administradores de 'Contoso' podem controlar o acesso à organização 'Test' se controlarem a conta de utilizador que criou o 'Teste'.
* Se adicionar ou remover uma função AD Azure para um utilizador numa organização, a alteração não afeta as funções que o utilizador é atribuído em qualquer outra organização Azure AD.

## <a name="synchronization-independence"></a>Independência da sincronização

Pode configurar cada organização AD Azure de forma independente para obter a sincronização de dados a partir de uma única instância de ambos:

* A ferramenta Azure AD Connect, para sincronizar dados com uma única floresta AD.
* O Azure Ative Directory Connector for Forefront Identity Manager, para sincronizar dados com uma ou mais florestas no local e/ou fontes de dados AD não-Azure.

## <a name="add-an-azure-ad-organization"></a>Adicione uma organização AD AZure

Para adicionar uma organização AZure AD no portal Azure, inscreva-se no [portal Azure](https://portal.azure.com) com uma conta que é um administrador global da AD AZure, e selecione **New**.

> [!NOTE]
> Ao contrário de outros recursos Azure, as suas organizações Azure AD não são recursos infantis de uma subscrição do Azure. Se a subscrição do Azure for cancelada ou expirada, ainda pode aceder aos dados da sua organização Azure AD utilizando o Azure PowerShell, o Microsoft Graph API ou o centro de administração microsoft 365. Também pode [associar outra subscrição à organização.](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
>

## <a name="next-steps"></a>Passos seguintes

Para considerações de licenciamento da AZure AD e boas práticas, veja [o que é o licenciamento do Azure Ative Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
