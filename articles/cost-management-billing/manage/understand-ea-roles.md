---
title: Compreender as funções de administrador do Enterprise no Azure
description: Saiba mais sobre as funções de administrador do Enterprise no Azure.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: banders
ms.openlocfilehash: 2cd786304180369fee39c7b362f27a44728f3ad8
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87124345"
---
# <a name="managing-azure-enterprise-roles"></a>Gerir as funções Enterprise do Azure

Para ajudar a gerir a utilização e o gasto da sua organização, os clientes do Azure com um Contrato Enterprise (EA) podem atribuir cinco funções administrativas distintas:

- Administrador do Enterprise (EA)
- Administrador do Enterprise (só de leitura)<sup>1</sup>
- Administrador de Departamento
- Administrador de Departamento (só de leitura)
- Proprietário da Conta <sup>2</sup>

<sup>1</sup> O contacto de faturação do contrato EA terá esta função.

<sup>2</sup> O contacto de faturação não pode ser adicionado nem alterado no portal EA do Azure e será adicionado à inscrição do EA com base no utilizador configurado como o contacto de faturação no nível do acordo. Para alterar o contacto de faturação, tem de ser feito um pedido através de um consultor parceiro/software ao Regional Operations Center (ROC).

O primeiro administrador da inscrição que é configurado durante o aprovisionamento da inscrição determina o tipo de autenticação da conta de faturação. Quando o contacto de faturação é adicionado ao Portal EA como administrador só de leitura, é-lhe dada autenticação da conta Microsoft. 

Por exemplo, se o tipo de autenticação inicial estiver configurado como Mista, o EA será adicionado como uma conta Microsoft e o contacto de faturação terá privilégios de administração de EA só de leitura. Se o administrador do EA não aprovar a autorização da conta Microsoft relativamente a um contacto de faturação já existente, esse administrador pode eliminar o utilizador em causa e pedir ao cliente que o volte a adicionar como administrador só de leitura com uma conta escolar ou profissional definida apenas ao nível da inscrição no Portal EA.

Estas funções são específicas para gerir Contratos Enterprise do Azure e são uma adição às funções incorporadas que o Azure possui para controlar o acesso aos recursos. Para obter mais informações, veja [Funções incorporadas do Azure](../../role-based-access-control/built-in-roles.md).

As seções seguintes descrevem as limitações e as capacidades de cada função.

## <a name="user-limit-for-admin-roles"></a>Limite de utilizador para funções de administrador

|Função| Limite de utilizador|
|---|---|
|Administrador do Enterprise (EA)|Ilimitado|
|Administrador do Enterprise (só de leitura)|Ilimitado|
|Administrador de Departamento|Ilimitado|
|Administrador de Departamento (só de leitura)|Ilimitado|
|Proprietário da Conta|1 por conta<sup>3</sup>|

<sup>3</sup> Cada conta requer uma conta Microsoft ou uma conta escolar ou profissional exclusiva.

## <a name="organization-structure-and-permissions-by-role"></a>Estrutura e permissões da organização por função

|Tarefas| Administrador do Enterprise (EA)|Administrador do Enterprise (só de leitura)|Administrador de Departamento|Administrador de Departamento (só de leitura)|Proprietário da Conta| Parceiro|
|---|---|---|---|---|---|---|
|Ver Administradores do Enterprise|✔|✔|✘|✘|✘|✔|
|Adicionar ou remover Administradores do Enterprise|✔|✘|✘|✘|✘|✘|
|Ver Contactos de Notificações<sup>4</sup> |✔|✔|✘|✘|✘|✔|
|Adicionar ou remover Contactos de Notificações<sup>4</sup> |✔|✘|✘|✘|✘|✘|
|Criar e gerir Departamentos |✔|✘|✘|✘|✘|✘|
|Ver Administradores de Departamento|✔|✔|✔|✔|✘|✔|
|Adicionar ou remover Administradores de Departamento|✔|✘|✔|✘|✘|✘|
|Ver Contas durante a inscrição |✔|✔|✔<sup>5</sup>|✔<sup>5</sup>|✘|✔|
|Adicionar Contas à inscrição e alterar o Proprietário da Conta|✔|✘|✔<sup>5</sup>|✘|✘|✘|
|Criar e gerir subscrições e permissões de subscrições|✘|✘|✘|✘|✔|✘|

- <sup>4</sup> Os contactos de notificações recebem comunicações por e-mail sobre o Contrato Enterprise do Azure.
- <sup>5</sup> A tarefa está limitada às contas no seu departamento.

## <a name="add-a-new-enterprise-administrator"></a>Adicionar um novo administrador corporativo

Os administradores corporativos têm muitos privilégios ao gerir uma inscrição de EA do Azure. O administrador do EA do Azure inicial foi criado quando o contrato de EA foi configurado. No entanto, pode adicionar ou remover novos administradores a qualquer momento. Apenas são adicionados novos administradores por administradores existentes. Para obter mais informações sobre como adicionar administradores corporativos adicionais, veja [Criar outro administrador corporativo](ea-portal-get-started.md#create-another-enterprise-administrator). Para obter mais informações sobre as funções e tarefas do perfil de faturação, veja [Funções e tarefas do perfil de faturação](understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="update-account-owner-state-from-pending-to-active"></a>Atualizar o estado do proprietário da conta de pendente para ativo

Quando são adicionados novos Proprietários de Conta (AO) a uma inscrição do EA do Azure pela primeira vez, o estado aparece como _Pendente_. Quando um novo proprietário de conta recebe o e-mail de boas-vindas de ativação, ele pode iniciar sessão para ativar a respetiva conta. Após ativar a conta, o estado da conta é atualizado de _Pendente_ para _Ativo_. O proprietário da conta tem de ler a mensagem “Aviso” e selecionar **Continuar**. Pode ser solicitado a novos utilizadores que introduzam o nome próprio e o apelido para criar uma Conta de Comércio. Nesse caso, têm de adicionar as informações necessárias para continuar e, em seguida, a conta é ativada.

## <a name="add-a-department-admin"></a>Adicionar um Administrador de Departamento

Após a criação de um departamento por um administrador do EA do Azure, o administrador corporativo do Azure pode adicionar administradores de departamento e associar cada um deles a um departamento. Um administrador de departamento pode criar novas contas. São necessárias novas contas para que as subscrições do EA do Azure sejam criadas.

Para obter mais informações sobre como adicionar um administrador de departamento, veja [Criar um administrador de departamento do EA do Azure](ea-portal-get-started.md#add-a-department-administrator).

## <a name="usage-and-costs-access-by-role"></a>Acesso de utilização e custos por função

|Tarefas| Administrador do Enterprise (EA)|Administrador do Enterprise (só de leitura)|Administrador de Departamento|Administrador de Departamento (só de leitura) |Proprietário da Conta| Parceiro|
|---|---|---|---|---|---|---|
|Ver saldo do crédito, incluindo o Pré-pagamento do Azure|✔|✔|✘|✘|✘|✔|
|Ver quotas de despesas do departamento|✔|✔|✘|✘|✘|✔|
|Definir quotas de despesas do departamento|✔|✘|✘|✘|✘|✘|
|Ver folha de preços do EA da organização|✔|✔|✘|✘|✘|✔|
|Ver detalhes de utilização e custo|✔|✔|✔<sup>6</sup>|✔<sup>6</sup>|✔<sup>7</sup>|✔|
|Gerir recursos no portal do Azure|✘|✘|✘|✘|✔|✘|

- <sup>6</sup> Requer que o Administrador Empresarial ative a política **Os Administradores de Departamento podem ver os custos** no Enterprise Portal. O Administrador de Departamento pode assim ver os detalhes dos custos do departamento.
- <sup>7</sup> Requer que o Administrador Empresarial ative a política **Os Proprietários de Contas podem ver os custos** no Enterprise Portal. O Proprietário da Conta pode ver os detalhes dos custos da conta.

## <a name="see-pricing-for-different-user-roles"></a>Ver preços para diferentes funções de utilizador

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
- [Funções incorporadas do Azure](../../role-based-access-control/built-in-roles.md)
