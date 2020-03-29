---
title: Permissões no Consultor Azure
description: Permissões de aconselhamento e como podem bloquear a sua capacidade de configurar subscrições ou adiar ou rejeitar recomendações.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: c850d757044066d5c4a793e076436906d715833c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422324"
---
# <a name="permissions-in-azure-advisor"></a>Permissões no Consultor Azure

O Azure Advisor fornece recomendações com base na utilização e configuração dos seus recursos e subscrições Do Azure. O Advisor utiliza as [funções incorporadas](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) fornecidas pelo Controlo de [Acesso Baseado em Funções](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) para gerir o seu acesso a recomendações e funcionalidades de Aconselhamento. 

## <a name="roles-and-their-access"></a>Papéis e seu acesso

O quadro seguinte define as funções e o acesso que têm no Advisor:

| **Função** | **Ver recomendações** | **Editar regras** | **Editar configuração de subscrição** | **Editar configuração do grupo de recursos**| **Descartar e adiar recomendações**|
|---|:---:|:---:|:---:|:---:|:---:|
|Proprietário de assinatura|**X**|**X**|**X**|**X**|**X**|
|Colaborador de Assinatura|**X**|**X**|**X**|**X**|**X**|
|Leitor de assinaturas|**X**|--|--|--|--|
|Proprietário do grupo de recursos|**X**|--|--|**X**|**X**|
|Colaborador do grupo de recursos|**X**|--|--|**X**|**X**|
|Grupo de recursos Leitor|**X**|--|--|--|--|
|Proprietário de recursos|**X**|--|--|--|**X**|
|Contribuinte de Recursos|**X**|--|--|--|**X**|
|Leitor de Recursos|**X**|--|--|--|--|

> [!NOTE]
> O acesso a recomendações de visualização depende do seu acesso ao recurso impactado da recomendação.

## <a name="permissions-and-unavailable-actions"></a>Permissões e ações indisponíveis

A falta de permissões adequadas pode bloquear a sua capacidade de realizar ações no Advisor. Seguem-se alguns problemas comuns.

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>Incapaz de configurar subscrições ou grupos de recursos

Quando tentar configurar subscrições ou grupos de recursos no Advisor, poderá ver que a opção de incluir ou excluir é desativada. Este estado indica que não tem um nível de autorização suficiente para esse grupo de recursos ou subscrição. Para resolver este problema, aprenda a conceder acesso ao [utilizador.](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>Incapaz de adiar ou rejeitar uma recomendação

Se receber um erro ao tentar adiar ou rejeitar uma recomendação, poderá não ter permissões suficientes. Certifique-se de que tem pelo menos acesso contributivo ao recurso impactado da recomendação que está a adiar ou descartar. Para resolver este problema, aprenda a conceder acesso ao [utilizador.](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

## <a name="next-steps"></a>Passos seguintes

Este artigo deu uma visão geral de como o Advisor utiliza o RBAC para controlar as permissões dos utilizadores e como resolver questões comuns. Para saber mais sobre o Advisor, consulte:

- [O que é o Assistente do Azure?](https://docs.microsoft.com/azure/advisor/advisor-overview)
- [Introdução ao Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-get-started)
