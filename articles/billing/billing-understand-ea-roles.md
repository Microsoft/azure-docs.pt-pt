---
title: Compreender as funções de administrador para a empresa no Azure | Documentos da Microsoft
description: Saiba mais sobre funções de administrador de empresa no Azure.
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2018
ms.author: banders
ms.openlocfilehash: 98ed28af8df246549fb521a81f1968e1f5c28cc4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60370718"
---
# <a name="understand-azure-enterprise-agreement-administrative-roles-in-azure"></a>Compreender as funções administrativas do contrato Enterprise do Azure no Azure

Para ajudar a gerir a utilização da sua organização e gastos do Azure os clientes com um contrato Enterprise (EA) podem atribuir cinco funções distintas de administrativas:

- Administrador de empresa
- Administrador de empresa (só de leitura)
- Administrador do departamento
- Administrador do departamento (só de leitura)
- Proprietário da Conta
 
Estas funções são específicas ao gerenciamento de contratos Enterprise do Azure e são, além das funções incorporadas, que o Azure tem para controlar o acesso aos recursos. Para obter mais informações, consulte [funções incorporadas para recursos do Azure](../role-based-access-control/built-in-roles.md).

As secções seguintes descrevem as limitações e as capacidades de cada função.

## <a name="user-limit-for-admin-roles"></a>Limite de utilizador para funções de administrador

|Função| Limite de utilizadores|
|---|---|
|Administrador de empresa|Ilimitado|
|Administrador de empresa (só de leitura)|Ilimitado|
|Administrador do departamento|Ilimitado|
|Administrador do departamento (só de leitura)|Ilimitado|
|Proprietário da Conta|1 por conta<sup>1</sup>|

<sup>1</sup> cada conta necessita de uma conta exclusiva da Microsoft ou conta escolar ou profissional.

## <a name="organization-structure-and-permissions-by-role"></a>Estrutura da organização e as permissões por função

|Tarefas| Administrador de empresa|Administrador de empresa (só de leitura)|Administrador do departamento|Administrador do departamento (só de leitura)|Proprietário da Conta|
|---|---|---|---|---|---|
|Ver administradores da empresa|✔|✔|✘|✘|✘|
|Adicionar ou remover administradores da empresa|✔|✘|✘|✘|✘|
|Ver contactos de notificação<sup>2</sup> |✔|✔|✘|✘|✘|
|Adicionar ou remover contactos de notificação<sup>2</sup> |✔|✘|✘|✘|✘|
|Criar e gerir os departamentos |✔|✘|✘|✘|✘|
|Administradores do departamento de vista|✔|✔|✔|✔|✘|
|Adicionar ou remover administradores do departamento|✔|✔|✔|✘|✘|
|Contas de vista na inscrição |✔|✔|✔<sup>3</sup>|✔<sup>3</sup>|✘|
|Adicionar contas para a inscrição e alterar o proprietário da conta|✔|✘|✔<sup>3</sup>|✘|✘|
|Criar e gerir subscrições e permissões de subscrição|✘|✘|✘|✘|✔|

- <sup>2</sup> contactos de notificação são enviados comunicações por e-mail sobre o Azure Enterprise Agreement.
- <sup>3</sup> tarefa está limitada a contas do seu departamento.


## <a name="usage-and-costs-access-by-role"></a>Acesso de utilização e os custos por função

|Tarefas| Administrador de empresa|Administrador de empresa (só de leitura)|Administrador do departamento|Administrador do departamento (só de leitura) |Proprietário da Conta|
|---|---|---|---|---|---|
|Saldo de crédito de vista incluindo alocação monetária|✔|✔|✘|✘|✘|
|As quotas de gastos do departamento de vista|✔|✔|✘|✘|✘|
|Definir quotas de gastos do departamento|✔|✘|✘|✘|✘|
|Ver a folha de preços EA da organização|✔|✔|✘|✘|✘|
|Ver detalhes de utilização e de custos|✔|✔|✔<sup>4</sup>|✔<sup>4</sup>|✔<sup>5</sup>|
|Gerir recursos no portal do Azure|✘|✘|✘|✘|✔|

- <sup>4</sup> requer que o administrador Corporativo ativar **cobranças de vista DA** política no portal da empresa. O administrador do departamento, em seguida, pode ver os detalhes de custos para o departamento.
- <sup>5</sup> requer que o administrador Corporativo ativar **cobranças de exibição AO** política no portal da empresa. O proprietário da conta, em seguida, pode ver os detalhes de custos para a conta.


## <a name="pricing-in-azure-portal"></a>Preço no portal do Azure

Poderá ver preços diferentes no portal do Azure, dependendo da sua função administrativa e como as políticas de encargos do modo de exibição são definidas pelo administrador de empresa. As duas políticas no portal da empresa, que afetam os preços que vir no portal do Azure são:

- Custos de vista DA
- Custos de exibição de pedidos

Para saber como definir estas políticas, veja [gerir o acesso a informações de faturação para o Azure](billing-manage-access.md).

A tabela seguinte mostra a relação entre as funções de administrador do Enterprise Agreement, a política de encargos do Vista, a função de controlo (RBAC) de acesso baseado em funções no portal do Azure e os preços que vê no portal do Azure. O administrador de empresa vê sempre os detalhes de utilização com base nos preços de EA da organização. No entanto, o administrador do departamento e o proprietário da conta veem vistas de preços diferentes com base na política de cobrança do modo de exibição e a respetiva função RBAC. A função de administrador do departamento listada na tabela a seguir se refere ao administrador do departamento e funções de administrador do departamento (só de leitura).

|Função de administrador do contrato Enterprise|Ver a política de custos para a função|Função RBAC|Ver preços|
|---|---|---|---|
|Proprietário da conta ou administrador do departamento|✔ Ativada|Owner|Preços de EA da organização|
|Proprietário da conta ou administrador do departamento|✘ Desativado|Owner|Preço de revenda|
|Proprietário da conta ou administrador do departamento|✔ Ativada |Nenhum|Sem preço|
|Proprietário da conta ou administrador do departamento|✘ Desativado |Nenhum|Sem preço|
|Nenhuma|Não aplicável |Owner|Preço de revenda|

Define a função de administrador de empresa e ver os custos de políticas no portal da empresa. A função RBAC pode ser atualizada no portal do Azure. Para obter mais informações, consulte [gerir o acesso com RBAC e o portal do Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passos Seguintes

- [Gerir o acesso a informações de faturação para o Azure](billing-manage-access.md)
- [Gerir o acesso através do RBAC e do portal do Azure](../role-based-access-control/role-assignments-portal.md)
- [Built-in roles for Azure resources](../role-based-access-control/built-in-roles.md) (Funções incorporadas para recursos do Azure)
