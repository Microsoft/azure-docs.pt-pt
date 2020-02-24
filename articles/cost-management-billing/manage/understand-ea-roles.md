---
title: Compreender as funções de administrador do Enterprise no Azure | Microsoft Docs
description: Saiba mais sobre as funções de administrador do Enterprise no Azure.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: banders
ms.openlocfilehash: 9fab4d8ba0cf2e6f684a1b9de177084f8ce31604
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462195"
---
# <a name="understand-azure-enterprise-agreement-administrative-roles-in-azure"></a>Compreender as funções administrativas do Contrato Enterprise do Azure

Para ajudar a gerir a utilização e o gasto da sua organização, os clientes do Azure com um Contrato Enterprise (EA) podem atribuir cinco funções administrativas distintas:

- Administrador do Enterprise (EA)
- Administrador do Enterprise (só de leitura)<sup>1</sup>
- Administrador de Departamento
- Administrador de Departamento (só de leitura)
- Proprietário da Conta

<sup>1</sup> O contacto de faturação do contrato EA terá esta função.

Estas funções são específicas para gerir Contratos Enterprise do Azure e são uma adição às funções incorporadas que o Azure possui para controlar o acesso aos recursos. Para obter mais informações, veja as [Funções incorporadas dos recursos do Azure](../../role-based-access-control/built-in-roles.md).

As seções seguintes descrevem as limitações e as capacidades de cada função.

## <a name="user-limit-for-admin-roles"></a>Limite de utilizador para funções de administrador

|Função| Limite de utilizador|
|---|---|
|Administrador do Enterprise (EA)|Ilimitado|
|Administrador do Enterprise (só de leitura)|Ilimitado|
|Administrador de Departamento|Ilimitado|
|Administrador de Departamento (só de leitura)|Ilimitado|
|Proprietário da Conta|1 por conta<sup>2</sup>|

<sup>2</sup> Cada conta requer uma conta Microsoft ou uma conta escolar ou profissional exclusiva.

## <a name="organization-structure-and-permissions-by-role"></a>Estrutura e permissões da organização por função

|Tarefas| Administrador do Enterprise (EA)|Administrador do Enterprise (só de leitura)|Administrador de Departamento|Administrador de Departamento (só de leitura)|Proprietário da Conta|
|---|---|---|---|---|---|
|Ver Administradores do Enterprise|✔|✔|✘|✘|✘|
|Adicionar ou remover Administradores do Enterprise|✔|✘|✘|✘|✘|
|Ver Contactos de Notificações<sup>3</sup> |✔|✔|✘|✘|✘|
|Adicionar ou remover Contatos de Notificações<sup>3</sup> |✔|✘|✘|✘|✘|
|Criar e gerir Departamentos |✔|✘|✘|✘|✘|
|Ver Administradores de Departamento|✔|✔|✔|✔|✘|
|Adicionar ou remover Administradores de Departamento|✔|✘|✔|✘|✘|
|Ver Contas durante a inscrição |✔|✔|✔<sup>4</sup>|✔<sup>4</sup>|✘|
|Adicionar Contas à inscrição e alterar o Proprietário da Conta|✔|✘|✔<sup>4</sup>|✘|✘|
|Criar e gerir subscrições e permissões de subscrições|✘|✘|✘|✘|✔|

- <sup>3</sup> Os contactos de notificações recebem comunicações por e-mail sobre o Contrato Enterprise do Azure.
- <sup>4</sup> A tarefa está limitada às contas no seu departamento.


## <a name="usage-and-costs-access-by-role"></a>Acesso de utilização e custos por função

|Tarefas| Administrador do Enterprise (EA)|Administrador do Enterprise (só de leitura)|Administrador de Departamento|Administrador de Departamento (só de leitura) |Proprietário da Conta|
|---|---|---|---|---|---|
|Ver saldo do crédito, incluindo alocação monetária|✔|✔|✘|✘|✘|
|Ver quotas de despesas do departamento|✔|✔|✘|✘|✘|
|Definir quotas de despesas do departamento|✔|✘|✘|✘|✘|
|Ver folha de preços do EA da organização|✔|✔|✘|✘|✘|
|Ver detalhes de utilização e custo|✔|✔|✔<sup>5</sup>|✔<sup>5</sup>|✔<sup>6</sup>|
|Gerir recursos no portal do Azure|✘|✘|✘|✘|✔|

- <sup>5</sup> Requer que o Administrador do Enterprise ative a política **Os Administradores de Departamento podem ver os custos** no Enterprise Portal. O Administrador de Departamento pode assim ver os detalhes dos custos do departamento.
- <sup>6</sup> Requer que o Administrador do Enterprise ative a política **Os Proprietários de Contas podem ver os custos** no Enterprise Portal. O Proprietário da Conta pode ver os detalhes dos custos da conta.


## <a name="pricing-in-azure-portal"></a>Preços no portal do Azure

Pode ver preços diferentes no portal do Azure, consoante a sua função administrativa e como as políticas de visualização de custos são definidas pelo Administrador do Enterprise. As duas políticas no Enterprise Portal que afetam os preços que vê no portal do Azure são:

- Os Administradores de Departamento podem ver os custos
- Os Proprietários de Contas podem ver os custos

Para saber como definir essas políticas, veja [Gerir o acesso às informações de faturação do Azure](manage-billing-access.md).

A tabela seguinte mostra a relação entre as funções de administrador do Contrato Enterprise, a política de visualização dos custos, a função de controlo de acesso baseado em funções (RBAC) no portal do Azure e os preços que vê no portal do Azure. O Administrador do Enterprise vê sempre os detalhes de utilização com base no preço do EA da organização. No entanto, o Administrador de Departamento e o Proprietário da Conta têm visualizações de preços diferentes com base na política de visualização de custos e na função RBAC. A função Administrador de Departamento listada na tabela seguinte refere-se às funções Administrador de Departamento e Administrador de Departamento (só de leitura).

|Função de administrador do Contrato Enterprise|Política de visualização dos custos por função|Função RBAC|Visualização dos preços|
|---|---|---|---|
|Proprietário da Conta OU Administrador de Departamento|✔ Ativada|Proprietário|Preço do EA da Organização|
|Proprietário da Conta OU Administrador de Departamento|✘ Desativada|Proprietário|Preços de retalho|
|Proprietário da Conta OU Administrador de Departamento|✔ Ativada |nenhuma|Sem preços|
|Proprietário da Conta OU Administrador de Departamento|✘ Desativada |nenhuma|Sem preços|
|Nenhuma|Não aplicável |Proprietário|Preços de retalho|

Deve definir a função de administrador do Enterprise e as políticas de visualização de custos no Enterprise Portal. A função RBAC pode ser atualizada no portal do Azure. Para obter mais informações, veja [Gerir o acesso através do RBAC e do portal do Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passos seguintes

- [Gerir o acesso às informações de faturação do Azure](manage-billing-access.md)
- [Gerir o acesso através do RBAC e do portal do Azure](../../role-based-access-control/role-assignments-portal.md)
- [Built-in roles for Azure resources](../../role-based-access-control/built-in-roles.md) (Funções incorporadas para recursos do Azure)
