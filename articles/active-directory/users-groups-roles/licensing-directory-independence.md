---
title: Características de uma interação de locatário múltiplo – Azure AD | Microsoft Docs
description: Gerencie seus locatários de locatários ativos do Azure compreendendo seus locatários como recursos totalmente independentes
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
ms.openlocfilehash: 0ce791ee3536b9ab07605787209e59b7e5d60126
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74026300"
---
# <a name="understand-how-multiple-azure-active-directory-tenants-interact"></a>Entender como vários locatários Azure Active Directory interagem

No Azure Active Directory (AD do Azure), cada locatário é um recurso totalmente independente: um ponto que é logicamente independente dos outros locatários que você gerencia. Não há nenhuma relação pai-filho entre locatários. Essa independência entre locatários inclui independência de recursos, independência administrativa e independência de sincronização.

## <a name="resource-independence"></a>Independência de recursos
* Se você criar ou excluir um recurso em um locatário, ele não afetará nenhum recurso em outro locatário, com a exceção parcial de usuários externos. 
* Se você usar um de seus nomes de domínio com um locatário, ele não poderá ser usado com nenhum outro locatário.

## <a name="administrative-independence"></a>Independência administrativa
Se um usuário não administrativo do locatário "contoso" criar um locatário de teste "Test", então:

* Por padrão, o usuário que cria um locatário é adicionado como um usuário externo nesse novo locatário e atribuiu a função de administrador global nesse locatário.
* Os administradores do locatário ' contoso ' não têm privilégios administrativos diretos para o locatário ' teste ', a menos que um administrador de ' teste ' especificamente conceda a eles esses privilégios. No entanto, os administradores de ' contoso ' podem controlar o acesso ao locatário ' test ' se controlam a conta de usuário que criou ' test '.
* Se você adicionar/remover uma função de administrador para um usuário em um locatário, a alteração não afetará as funções de administrador que o usuário tem em outro locatário.

## <a name="synchronization-independence"></a>Independência de sincronização
Você pode configurar cada locatário do Azure AD independentemente para obter dados sincronizados de uma única instância de:

* A ferramenta de Azure AD Connect, para sincronizar dados com uma única floresta do AD.
* O conector de locatário ativo do Azure para o Forefront Identity Manager, para sincronizar dados com uma ou mais florestas locais e/ou fontes de dados não Azure AD.

## <a name="add-an-azure-ad-tenant"></a>Adicionar um locatário do Azure AD
Para adicionar um locatário do Azure AD no portal do Azure, entre no [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do Azure AD e, à esquerda, selecione **novo**.

> [!NOTE]
> Ao contrário de outros recursos do Azure, seus locatários não são recursos filho de uma assinatura do Azure. Se sua assinatura do Azure for cancelada ou expirada, você ainda poderá acessar os dados do locatário usando Azure PowerShell, o API do Graph do Azure ou o centro de administração do Microsoft 365. Você também pode [associar outra assinatura ao locatário](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>Passos seguintes
Para obter uma ampla visão geral dos problemas de licenciamento do Azure AD e das práticas recomendadas, consulte [o que é o licenciamento de locatário ativo do Azure?](../fundamentals/active-directory-licensing-whatis-azure-portal.md).
