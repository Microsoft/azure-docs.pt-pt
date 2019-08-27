---
title: Entender as funções de administrador para empresas no Azure | Microsoft Docs
description: Saiba mais sobre as funções de administrador corporativo no Azure.
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
ms.openlocfilehash: 39526defb8f41ddacb0a26d7ad852f820ca6ea77
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034534"
---
# <a name="understand-azure-enterprise-agreement-administrative-roles-in-azure"></a>Entender as funções administrativas do Azure Enterprise Agreement no Azure

Para ajudar a gerenciar o uso e o gasto de sua organização, os clientes do Azure com um EA (Enterprise Agreement) podem atribuir cinco funções administrativas distintas:

- Administrador de Empresa
- Administrador corporativo (somente leitura)
- Administrador de Departamento
- Administrador do departamento (somente leitura)
- Proprietário da Conta
 
Essas funções são específicas para gerenciar os acordos do Azure Enterprise e são além das funções internas que o Azure tem para controlar o acesso aos recursos. Para obter mais informações, consulte [funções internas para recursos do Azure](../role-based-access-control/built-in-roles.md).

As seções a seguir descrevem as limitações e os recursos de cada função.

## <a name="user-limit-for-admin-roles"></a>Limite de usuários para funções de administrador

|Role| Limite de usuários|
|---|---|
|Administrador de Empresa|Ilimitado|
|Administrador corporativo (somente leitura)|Ilimitado|
|Administrador de Departamento|Ilimitado|
|Administrador do departamento (somente leitura)|Ilimitado|
|Proprietário da Conta|1 por conta<sup>1</sup>|

<sup>1</sup> cada conta requer um conta Microsoft exclusivo ou uma conta corporativa ou de estudante.

## <a name="organization-structure-and-permissions-by-role"></a>Estrutura da organização e permissões por função

|Tarefas| Administrador de Empresa|Administrador corporativo (somente leitura)|Administrador de Departamento|Administrador do departamento (somente leitura)|Proprietário da Conta|
|---|---|---|---|---|---|
|Exibir administradores corporativos|✔|✔|✘|✘|✘|
|Adicionar ou remover administradores corporativos|✔|✘|✘|✘|✘|
|Exibir contatos de notificação<sup>2</sup> |✔|✔|✘|✘|✘|
|Adicionar ou remover contatos de notificação<sup>2</sup> |✔|✘|✘|✘|✘|
|Criar e gerenciar departamentos |✔|✘|✘|✘|✘|
|Exibir administradores do departamento|✔|✔|✔|✔|✘|
|Adicionar ou remover administradores do departamento|✔|✘|✔|✘|✘|
|Exibir contas no registro |✔|✔|✔<sup>3</sup>|✔<sup>3</sup>|✘|
|Adicionar contas ao registro e alterar o proprietário da conta|✔|✘|✔<sup>3</sup>|✘|✘|
|Criar e gerenciar assinaturas e permissões de assinatura|✘|✘|✘|✘|✔|

- <sup>2</sup> os contatos de notificação são enviados para comunicações por email sobre o Enterprise Agreement do Azure.
- <sup>3</sup> a tarefa está limitada a contas em seu departamento.


## <a name="usage-and-costs-access-by-role"></a>Uso e custos de acesso por função

|Tarefas| Administrador de Empresa|Administrador corporativo (somente leitura)|Administrador de Departamento|Administrador do departamento (somente leitura) |Proprietário da Conta|
|---|---|---|---|---|---|
|Exibir saldo de crédito, incluindo compromisso monetário|✔|✔|✘|✘|✘|
|Exibir cotas de gastos do departamento|✔|✔|✘|✘|✘|
|Definir cotas de gastos do departamento|✔|✘|✘|✘|✘|
|Exibir a folha de preços EA da organização|✔|✔|✘|✘|✘|
|Exibir detalhes de uso e custo|✔|✔|✔<sup>4</sup>|✔<sup>4</sup>|✔<sup>5</sup>|
|Gerenciar recursos no portal do Azure|✘|✘|✘|✘|✔|

- <sup>4</sup> requer que o administrador corporativo habilite a política de encargos **da exibição** do dos no Enterprise Portal. O administrador do departamento pode ver os detalhes de custo do departamento.
- <sup>5</sup> requer que o administrador corporativo habilite a política de encargos de **exibição** do ao no Enterprise Portal. O proprietário da conta pode ver os detalhes de custo da conta.


## <a name="pricing-in-azure-portal"></a>Preços no portal do Azure

Você pode ver preços diferentes na portal do Azure dependendo da sua função administrativa e como as políticas de exibição de cobranças são definidas pelo administrador corporativo. As duas políticas no Enterprise Portal que afetam os preços que você vê na portal do Azure são:

- Cobranças DA exibição do DA
- Encargos da exibição do ao

Para saber como definir essas políticas, consulte [gerenciar o acesso às informações de cobrança do Azure](billing-manage-access.md).

A tabela a seguir mostra a relação entre as Enterprise Agreement funções de administrador, a política de encargos de exibição, a função de RBAC (controle de acesso baseado em função) no portal do Azure e os preços que você vê no portal do Azure. O administrador corporativo sempre vê os detalhes de uso com base no preço EA da organização. No entanto, o administrador do departamento e o proprietário da conta veem diferentes exibições de preço com base na política de encargo de exibição e sua função de RBAC. A função Administrador do departamento listada na tabela a seguir refere-se às funções administrador do departamento e administrador do departamento (somente leitura).

|Enterprise Agreement função de administrador|Exibir política de encargos para função|Função RBAC|Exibição de preços|
|---|---|---|---|
|Proprietário da conta ou administrador do departamento|✔ Habilitado|Owner|Preço EA da organização|
|Proprietário da conta ou administrador do departamento|✘ Desabilitado|Owner|Preços do varejo|
|Proprietário da conta ou administrador do departamento|✔ Habilitado |nenhum|Sem preços|
|Proprietário da conta ou administrador do departamento|✘ Desabilitado |nenhum|Sem preços|
|Nenhum|Não aplicável |Owner|Preços do varejo|

Você define a função de administrador corporativo e exibe as políticas de cobranças no Enterprise Portal. A função RBAC pode ser atualizada no portal do Azure. Para obter mais informações, consulte [gerenciar o acesso usando RBAC e o portal do Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passos seguintes

- [Gerir o acesso a informações de faturação para o Azure](billing-manage-access.md)
- [Gerir o acesso através do RBAC e do portal do Azure](../role-based-access-control/role-assignments-portal.md)
- [Built-in roles for Azure resources](../role-based-access-control/built-in-roles.md) (Funções incorporadas para recursos do Azure)
