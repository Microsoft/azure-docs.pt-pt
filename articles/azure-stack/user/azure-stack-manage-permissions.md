---
title: Gerir permissões para recursos por utilizador no Azure Stack | Documentos da Microsoft
description: Como um administrador de serviços ou inquilino, saiba como gerir as permissões RBAC.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: 3565bf4c4a19bcf1b136b4cbb781006658865a1c
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57767214"
---
# <a name="manage-access-to-resources-with-azure-stack-role-based-access-control"></a>Gerir o acesso a recursos com o controlo de acesso de Azure Stack Role-Based

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

O Azure Stack suporta o controlo de acesso baseado em funções (RBAC), o mesmo [modelo de segurança para gestão de acesso](https://docs.microsoft.com/azure/role-based-access-control/overview) que utiliza o Microsoft Azure. Pode utilizar o RBAC para gerir o utilizador, grupo ou aplicação acesso aos serviços, recursos e subscrições.

## <a name="basics-of-access-management"></a>Noções básicas da gestão de acesso

Controlo de acesso baseado em funções fornece controlo de acesso detalhado que pode utilizar para proteger o seu ambiente. Concede aos utilizadores as permissões exatas que precisam ao atribuir uma função RBAC num determinado âmbito. O âmbito da atribuição de função pode ser uma subscrição, um grupo de recursos ou um único recurso. Leitura a [controlo de acesso baseado em funções no portal do Azure](https://docs.microsoft.com/azure/role-based-access-control/overview) artigo para obter informações mais detalhadas sobre a gestão de acesso.

### <a name="built-in-roles"></a>Funções incorporadas

O Azure Stack tem três funções básicas que pode aplicar a todos os tipos de recursos:

* **Proprietário** podem gerir tudo, incluindo o acesso aos recursos.
* **Contribuinte** podem gerir tudo, exceto o acesso aos recursos.
* **Leitor** podem ver tudo, mas não é possível fazer alterações.

### <a name="resource-hierarchy-and-inheritance"></a>Hierarquia de recursos e herança

O Azure Stack tem a seguinte hierarquia de recursos:

* Cada subscrição pertence a um diretório.
* Cada grupo de recursos pertence a uma subscrição.
* Cada recurso pertence a um grupo de recursos.

Acesso que concede a um âmbito principal é herdado em âmbitos subordinados. Por exemplo:

* Atribuir a função de leitor a um grupo do Azure AD no âmbito da subscrição. Os membros desse grupo podem ver a cada grupo de recursos e recursos na subscrição.
* Atribua a função de contribuinte a um aplicativo no âmbito do grupo de recursos. O aplicativo pode gerir os recursos de todos os tipos no grupo de recursos, mas não outros grupos de recursos na subscrição.

### <a name="assigning-roles"></a>Atribuir funções

Pode atribuir mais de uma função a um utilizador e cada função pode ser associada a outro âmbito. Por exemplo:

* Atribuir a função de leitor do TestUser-A à subscrição 1.
* Atribuição de função TestUser-A o proprietário a TestVM-1.

O Azure [atribuições de funções](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) artigo fornece informações detalhadas sobre a ver, atribuir e exclusão de funções.

### <a name="resource-hierarchy-and-inheritance"></a>Hierarquia de recursos e herança

O Azure Stack tem a seguinte hierarquia de recursos:

* Cada subscrição pertence a um diretório.
* Cada grupo de recursos pertence a uma subscrição.
* Cada recurso pertence a um grupo de recursos.

Acesso que concede a um âmbito principal é herdado em âmbitos subordinados. Por exemplo:

* Atribuir a **leitor** função a um grupo do Azure AD no âmbito da subscrição. Os membros desse grupo podem ver a cada grupo de recursos e recursos na subscrição.
* Atribuir a **contribuinte** função a uma aplicação no âmbito do grupo de recursos. O aplicativo pode gerir os recursos de todos os tipos no grupo de recursos, mas não outros grupos de recursos na subscrição.

### <a name="assigning-roles"></a>Atribuir funções

Pode atribuir mais de uma função a um utilizador e cada função pode ser associada a outro âmbito. Por exemplo:

* Atribuir a função de leitor do TestUser-A à subscrição 1.
* Atribuição de função TestUser-A o proprietário a TestVM-1.

O Azure [atribuições de funções](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) artigo fornece informações detalhadas sobre a ver, atribuir e exclusão de funções.

## <a name="set-access-permissions-for-a-user"></a>Definir permissões de acesso para um utilizador

Os passos seguintes descrevem como configurar permissões para um utilizador.

1. Inicie sessão com uma conta que tenha permissões de proprietário do recurso que pretende gerir.
2. Na navegação à esquerda, selecione **Grupos de recursos**.
3. Escolha o nome do grupo de recursos que pretende definir permissões em.
4. No painel de navegação do grupo de recursos, escolha **controlo de acesso (IAM)**. O **atribuições de funções** vista lista os itens que têm acesso ao grupo de recursos. Pode filtrar e agrupar os resultados.
5. Sobre o **controlo de acesso** menu barra, escolha **Add**.
6. No **adicionar permissões** painel:

   * Escolha a função que pretende atribuir a partir da **função** na lista pendente.
   * Escolha o recurso que pretende atribuir a partir da **atribuir acesso a** na lista pendente.
   * Selecione o utilizador, grupo ou aplicação no diretório para o qual pretende dar acesso. Pode procurar o diretório com os nomes a apresentar, endereços de correio eletrónico e identificadores de objetos.

7. Selecione **Guardar**.

## <a name="next-steps"></a>Passos Seguintes

[Criar principais de serviço](azure-stack-create-service-principals.md)