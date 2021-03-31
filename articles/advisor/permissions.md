---
title: Permissões em Azure Advisor
description: Permissões de aconselhamento e como podem bloquear a sua capacidade de configurar subscrições ou adiar ou rejeitar recomendações.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 402a21c47c4cba8f747d5d4601f9c95034c99262
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91712932"
---
# <a name="permissions-in-azure-advisor"></a>Permissões em Azure Advisor

O Azure Advisor fornece recomendações baseadas na utilização e configuração dos seus recursos e subscrições Azure. O Advisor utiliza as [funções incorporadas fornecidas](../role-based-access-control/built-in-roles.md) pelo [Azure role-based access control (Azure RBAC)](../role-based-access-control/overview.md) para gerir o seu acesso a recomendações e funcionalidades do Advisor. 

## <a name="roles-and-their-access"></a>Funções e o seu acesso

A tabela que se segue define as funções e o acesso que têm no Conselho consultivo:

| **Role** | **Ver recomendações** | **Editar regras** | **Editar configuração de subscrição** | **Editar configuração do grupo de recursos**| **Desprezem e adiem recomendações**|
|---|:---:|:---:|:---:|:---:|:---:|
|Proprietário de Assinatura|**X**|**X**|**X**|**X**|**X**|
|Colaborador de Assinatura|**X**|**X**|**X**|**X**|**X**|
|Leitor de Assinaturas|**X**|--|--|--|--|
|Proprietário do grupo de recursos|**X**|--|--|**X**|**X**|
|Contribuinte do grupo de recursos|**X**|--|--|**X**|**X**|
|Leitor de grupo de recursos|**X**|--|--|--|--|
|Proprietário de Recursos|**X**|--|--|--|**X**|
|Contribuinte de Recursos|**X**|--|--|--|**X**|
|Leitor de Recursos|**X**|--|--|--|--|

> [!NOTE]
> O acesso às recomendações de visualização depende do seu acesso ao recurso impactado da recomendação.

## <a name="permissions-and-unavailable-actions"></a>Permissões e ações indisponíveis

A falta de permissões adequadas pode bloquear a sua capacidade de realizar ações no Advisor. Seguem-se alguns problemas comuns.

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>Incapaz de configurar subscrições ou grupos de recursos

Quando tentar configurar subscrições ou grupos de recursos no Advisor, poderá ver que a opção de incluir ou excluir é desativada. Este estado indica que não tem um nível suficiente de permissão para esse grupo de recursos ou subscrição. Para resolver este problema, aprenda a [conceder acesso](../role-based-access-control/quickstart-assign-role-user-portal.md)ao utilizador .

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>Incapaz de adiar ou rejeitar uma recomendação

Se receber um erro ao tentar adiar ou rejeitar uma recomendação, poderá não ter permissões suficientes. Certifique-se de que tem pelo menos acesso ao recurso impactado da recomendação que está a adiar ou a despedir. Para resolver este problema, aprenda a [conceder acesso](../role-based-access-control/quickstart-assign-role-user-portal.md)ao utilizador .

## <a name="next-steps"></a>Passos seguintes

Este artigo deu uma visão geral de como o Advisor usa o Azure RBAC para controlar as permissões dos utilizadores e como resolver questões comuns. Para saber mais sobre o Conselheiro, consulte:

- [O que é o Assistente do Azure?](./advisor-overview.md)
- [Introdução ao Azure Advisor](./advisor-get-started.md)
