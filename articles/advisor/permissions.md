---
title: Permissões no Assistente do Azure
description: Permissões do Advisor e como eles podem bloquear a sua capacidade de configurar as subscrições ou adiar ou ignorar recomendações.
services: advisor
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 04/03/2019
ms.author: kasparks
ms.openlocfilehash: cbd2e456c96dbf8ca01387f0c7c17a1541dbfe55
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60467617"
---
# <a name="permissions-in-azure-advisor"></a>Permissões no Assistente do Azure

O Assistente do Azure fornece recomendações com base na utilização e configuração dos seus recursos do Azure e subscrições. Assistente utiliza a [funções incorporadas](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) fornecido pelo [controlo de acesso baseado em funções](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) para gerir o acesso ao recomendações e as funcionalidades do Advisor. 

## <a name="roles-and-their-access"></a>Funções e o respetivo acesso

A tabela seguinte define as funções e o acesso que têm no assistente:

| **Função** | **Ver recomendações** | **Editar regras** | **Editar configuração de subscrição** | **Editar a configuração do grupo de recursos**| **Dispensar e adiar recomendações**|
|---|:---:|:---:|:---:|:---:|:---:|
|Proprietário da subscrição|**X**|**X**|**X**|**X**|**X**|
|Contribuinte da subscrição|**X**|**X**|**X**|**X**|**X**|
|Leitor de subscrição|**X**|--|--|--|--|
|Proprietário do grupo de recursos|**X**|--|--|**X**|**X**|
|Contribuinte do grupo de recursos|**X**|--|--|**X**|**X**|
|Leitor do grupo de recursos|**X**|--|--|--|--|
|Proprietário do Recurso|**X**|--|--|--|**X**|
|Contribuinte de recursos|**X**|--|--|--|**X**|
|Leitor de recursos|**X**|--|--|--|--|

> [!NOTE]
> Acesso para ver recomendações é dependente de seu acesso ao recurso afetado da recomendação.

## <a name="permissions-and-unavailable-actions"></a>Permissões e as ações disponíveis

Falta de permissões adequadas pode bloquear a capacidade de executar ações no Advisor. Seguem-se alguns problemas comuns.

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>Não é possível configurar subscrições ou grupos de recursos

Quando está tentando configurar subscrições ou grupos de recursos no assistente, pode ver que a opção para incluir ou excluir está desativada. Este status indica que não tem um nível suficiente de permissão para esse grupo de recursos ou subscrição. Para resolver este problema, saiba como [conceder um acesso de utilizador](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>Não é possível adiar ou ignorar uma recomendação

Se receber um erro ao tentar adiar ou ignorar uma recomendação, pode não ter permissões suficientes. Certifique-se de que tem, pelo menos, acesso de Contribuidor para o recurso afetado da recomendação está a adiar a ou dispensar. Para resolver este problema, saiba como [conceder um acesso de utilizador](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

## <a name="next-steps"></a>Passos Seguintes

Este artigo lhe forneceu uma visão geral de como o assistente utiliza o RBAC para permissões de controlo de utilizador e como resolver problemas comuns. Para saber mais sobre o Advisor, consulte:

- [O que é o Azure Advisor?](https://docs.microsoft.com/azure/advisor/advisor-overview)
- [Introdução ao Assistente do Azure](https://docs.microsoft.com/azure/advisor/advisor-get-started)
