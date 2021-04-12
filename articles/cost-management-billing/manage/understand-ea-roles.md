---
title: Compreender as funções de administrador de Contratos Enterprise (EA) no Azure
description: Saiba mais sobre as funções de administrador do Enterprise no Azure. Pode atribuir cinco funções administrativas distintas.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 7331f9a894d36ee15702a8fe53804efd53049762
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107284124"
---
# <a name="managing-azure-enterprise-agreement-roles"></a>Gerir as funções do Contrato Enterprise do Azure

Para ajudar a gerir o uso e gasto da sua organização, os clientes Azure com um Acordo Empresarial podem atribuir seis funções administrativas distintas:

- Administrador do Enterprise (EA)
- Administrador do Enterprise (só de leitura)<sup>1</sup>
- Comprador da EA
- Administrador de Departamento
- Administrador de Departamento (só de leitura)
- Proprietário da Conta <sup>2</sup>

<sup>1</sup> O contacto de faturação do contrato EA terá esta função.

<sup>2</sup> O contacto de faturação não pode ser adicionado nem alterado no portal EA do Azure e será adicionado à inscrição do EA com base no utilizador configurado como o contacto de faturação no nível do acordo. Para alterar o contacto de faturação, tem de ser feito um pedido através de um consultor parceiro/software ao Regional Operations Center (ROC).

O primeiro administrador da inscrição que é configurado durante o aprovisionamento da inscrição determina o tipo de autenticação da conta de faturação. Quando o contacto de faturação é adicionado ao Portal EA como administrador só de leitura, é-lhe dada autenticação da conta Microsoft. 

Por exemplo, se o tipo de autenticação inicial estiver configurado como Mista, o EA será adicionado como uma conta Microsoft e o contacto de faturação terá privilégios de administração de EA só de leitura. Se o administrador do EA não aprovar a autorização da conta Microsoft relativamente a um contacto de faturação já existente, esse administrador pode eliminar o utilizador em causa e pedir ao cliente que o volte a adicionar como administrador só de leitura com uma conta escolar ou profissional definida apenas ao nível da inscrição no Portal EA.

Estas funções são específicas para gerir Contratos Enterprise do Azure e são uma adição às funções incorporadas que o Azure possui para controlar o acesso aos recursos. Para obter mais informações, veja [Funções incorporadas do Azure](../../role-based-access-control/built-in-roles.md).

## <a name="azure-enterprise-portal-hierarchy"></a>Hierarquia do portal Azure Enterprise

A hierarquia do portal Azure Enterprise é composta por:

- **Portal Azure Enterprise**  – um portal de gestão online que ajuda a gerir os custos dos serviços do Azure EA. Pode:

  - Criar uma hierarquia do Azure EA com departamentos, contas e subscrições.
  - Reconciliar os preços dos serviços consumidos, transferir relatórios de utilização e ver listas de preços.
  - Criar chaves de API para a inscrição.

- Os **departamentos** ajudam a segmentar os custos em agrupamentos lógicos. Os departamentos permitem-lhe definir um orçamento ou uma quota no nível do departamento.

- As **contas** são unidades organizacionais no portal Azure Enterprise. Pode utilizar as contas para gerir subscrições e aceder a relatórios.

- As **subscrições** são a unidade mais pequena no portal Azure Enterprise. São contentores dos serviços do Azure geridos pelo administrador de serviços.

O diagrama a seguir ilustra hierarquias do Azure EA simples.

![Diagrama de hierarquias do Azure EA simples](./media/understand-ea-roles/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>Funções de utilizador corporativo

As seguintes funções de utilizador administrativo fazem parte da inscrição do Enterprise:

- Administrador do Enterprise
- Comprador da EA
- Administrador de Departamento
- Proprietário de Conta
- Administrador de serviço
- Contacto de notificação

As funções servem para concluir tarefas em dois portais diferentes. Utilize o [portal Azure Enterprise](https://ea.azure.com) para gerir a faturação e os custos e o [portal do Azure](https://portal.azure.com) para gerir os serviços do Azure.

As funções de utilizador estão associadas a uma conta de utilizador. Para validar a autenticidade do utilizador, cada um deve ter uma conta profissional, escolar ou Microsoft válida. Garanta que cada conta está associada a um endereço de e-mail que seja monitorizado ativamente. As notificações da conta são enviadas para o endereço de e-mail.

Ao configurar utilizadores, pode atribuir várias contas à função de administrador do Enterprise. No entanto, só uma conta pode conter a função de proprietário da conta. Além disso, pode atribuir as funções de administrador do Enterprise e de proprietário da conta a uma única conta.

### <a name="enterprise-administrator"></a>Administrador do Enterprise

Os utilizadores com esta função têm o nível de acesso mais alto. Podem:

- Gerir as contas e os proprietários das contas.
- Gerir outros administradores do Enterprise.
- Gerir os administradores de departamento.
- Gerir os contactos de notificação.
- Comprar serviços Azure, incluindo reservas.
- Ver a utilização em todas as contas.
- Ver custos não faturados em todas as contas.
- Ver e gerir todas as encomendas de reservas e as reservas que se aplicam ao Contrato Enterprise.
  - O administrador do Enterprise (só de leitura) pode ver as encomendas de reservas e as reservas. Não as pode gerir.

Pode ter vários administradores do Enterprise numa inscrição Enterprise. Pode conceder acesso só de leitura aos administradores do Enterprise. Todos eles herdam a função de administrador de departamento.

### <a name="ea-purchaser"></a>Comprador da EA

Os utilizadores com esta função têm permissões para comprar serviços Azure, mas não estão autorizados a gerir contas. Podem:

- Comprar serviços Azure, incluindo reservas.
- Ver a utilização em todas as contas.
- Ver custos não faturados em todas as contas.
- Ver e gerir todas as encomendas de reservas e as reservas que se aplicam ao Contrato Enterprise.

A função de comprador da EA está atualmente ativada apenas para o acesso baseado na SPN. Para aprender a atribuir o papel a um nome principal de serviço, consulte [atribuir funções aos nomes principais do serviço do Azure Enterprise Agreement](assign-roles-azure-service-principals.md).

### <a name="department-administrator"></a>Administrador de Departamento

Os utilizadores com esta função podem:

- Criar e gerir departamentos.
- Criar novos proprietários de contas.
- Ver os detalhes de utilização dos departamentos que gerem.
- Ver os custos, se tiverem as permissões necessárias.

Pode ter vários administradores de departamento para cada inscrição Enterprise.

Pode conceder aos administradores de departamento acesso só de leitura ao editar ou criar um novo administrador de departamento. Defina a opção só de leitura como **Sim**.

### <a name="account-owner"></a>Proprietário de Conta

Os utilizadores com esta função podem:

- Criar e gerir subscrições.
- Gerir os administradores de serviços.
- Ver a utilização das subscrições.

Cada conta requer uma conta profissional, escolar ou Microsoft exclusiva. Para obter mais informações sobre as funções administrativas do portal Azure Enterprise, veja [Compreender as funções administrativas do Contrato Enterprise no Azure](understand-ea-roles.md).

### <a name="service-administrator"></a>Administrador de serviço

A função de administrador de serviços tem permissões para gerir serviços no portal do Azure e atribuir utilizadores à função de coadministrador.

### <a name="notification-contact"></a>Contacto de notificação

O contacto de notificação recebe notificações de utilização relacionadas com a inscrição.

As seções seguintes descrevem as limitações e as capacidades de cada função.

## <a name="user-limit-for-admin-roles"></a>Limite de utilizador para funções de administrador

|Função| Limite de utilizador|
|---|---|
|Administrador do Enterprise (EA)|Ilimitado|
|Administrador do Enterprise (só de leitura)|Ilimitado|
| Comprador da EA atribuído a uma SPN | Ilimitado |
|Administrador de Departamento|Ilimitado|
|Administrador de Departamento (só de leitura)|Ilimitado|
|Proprietário da Conta|1 por conta<sup>3</sup>|

<sup>3</sup> Cada conta requer uma conta Microsoft ou uma conta escolar ou profissional exclusiva.

## <a name="organization-structure-and-permissions-by-role"></a>Estrutura e permissões da organização por função

|Tarefas| Administrador do Enterprise (EA)|Administrador do Enterprise (só de leitura)| Comprador da EA | Administrador de Departamento|Administrador de Departamento (só de leitura)|Proprietário da Conta| Parceiro|
|---|---|---|---|---|---|---|---|
|Ver Administradores do Enterprise|✔|✔| ✔|✘|✘|✘|✔|
|Adicionar ou remover Administradores do Enterprise|✔|✘|✘|✘|✘|✘|✘|
|Ver Contactos de Notificações<sup>4</sup> |✔|✔|✔|✘|✘|✘|✔|
|Adicionar ou remover Contactos de Notificações<sup>4</sup> |✔|✘|✘|✘|✘|✘|✘|
|Criar e gerir Departamentos |✔|✘|✘|✘|✘|✘|✘|
|Ver Administradores de Departamento|✔|✔|✔|✔|✔|✘|✔|
|Adicionar ou remover Administradores de Departamento|✔|✘|✘|✔|✘|✘|✘|
|Ver Contas durante a inscrição |✔|✔|✔|✔<sup>5</sup>|✔<sup>5</sup>|✘|✔|
|Adicionar Contas à inscrição e alterar o Proprietário da Conta|✔|✘|✘|✔<sup>5</sup>|✘|✘|✘|
|Reservas de compra|✔|✘|✔|✘|✘|✘|✘|
|Criar e gerir subscrições e permissões de subscrições|✘|✘|✘|✘|✘|✔|✘|

- <sup>4</sup> Os contactos de notificações recebem comunicações por e-mail sobre o Contrato Enterprise do Azure.
- <sup>5</sup> A tarefa está limitada às contas no seu departamento.

## <a name="add-a-new-enterprise-administrator"></a>Adicionar um novo administrador corporativo

Os administradores corporativos têm muitos privilégios ao gerir uma inscrição de EA do Azure. O administrador do EA do Azure inicial foi criado quando o contrato de EA foi configurado. No entanto, pode adicionar ou remover novos administradores a qualquer momento. Apenas são adicionados novos administradores por administradores existentes. Para obter mais informações sobre como adicionar administradores corporativos adicionais, veja [Criar outro administrador corporativo](ea-portal-administration.md#create-another-enterprise-administrator). Para obter mais informações sobre as funções e tarefas do perfil de faturação, veja [Funções e tarefas do perfil de faturação](understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="update-account-owner-state-from-pending-to-active"></a>Atualizar o estado do proprietário da conta de pendente para ativo

Quando são adicionados novos Proprietários de Conta (AO) a uma inscrição do EA do Azure pela primeira vez, o estado aparece como _Pendente_. Quando um novo proprietário de conta recebe o e-mail de boas-vindas de ativação, ele pode iniciar sessão para ativar a respetiva conta. Após ativar a conta, o estado da conta é atualizado de _Pendente_ para _Ativo_. O proprietário da conta tem de ler a mensagem “Aviso” e selecionar **Continuar**. Pode ser solicitado a novos utilizadores que introduzam o nome próprio e o apelido para criar uma Conta de Comércio. Nesse caso, têm de adicionar as informações necessárias para continuar e, em seguida, a conta é ativada.

## <a name="add-a-department-admin"></a>Adicionar um Administrador de Departamento

Após a criação de um departamento por um administrador do EA do Azure, o administrador corporativo do Azure pode adicionar administradores de departamento e associar cada um deles a um departamento. Um administrador de departamento pode criar novas contas. São necessárias novas contas para que as subscrições do EA do Azure sejam criadas.

Para obter mais informações sobre como adicionar um administrador de departamento, veja [Criar um administrador de departamento do EA do Azure](ea-portal-administration.md#add-a-department-administrator).

## <a name="usage-and-costs-access-by-role"></a>Acesso de utilização e custos por função

|Tarefas| Administrador do Enterprise (EA)|Administrador do Enterprise (só de leitura)|Comprador da EA|Administrador de Departamento|Administrador de Departamento (só de leitura) |Proprietário da Conta| Parceiro|
|---|---|---|---|---|---|---|---|
|Ver saldo do crédito, incluindo o Pré-pagamento do Azure|✔|✔|✔|✘|✘|✘|✔|
|Ver quotas de despesas do departamento|✔|✔|✔|✘|✘|✘|✔|
|Definir quotas de despesas do departamento|✔|✘|✘|✘|✘|✘|✘|
|Ver folha de preços do EA da organização|✔|✔|✔|✘|✘|✘|✔|
|Ver detalhes de utilização e custo|✔|✔|✔|✔<sup>6</sup>|✔<sup>6</sup>|✔<sup>7</sup>|✔|
|Gerir recursos no portal do Azure|✘|✘|✘|✘|✘|✔|✘|

- <sup>6</sup> Requer que o Administrador Empresarial ative a política **Os Administradores de Departamento podem ver os custos** no Enterprise Portal. O Administrador de Departamento pode assim ver os detalhes dos custos do departamento.
- <sup>7</sup> Requer que o Administrador Empresarial ative a política **Os Proprietários de Contas podem ver os custos** no Enterprise Portal. O Proprietário da Conta pode ver os detalhes dos custos da conta.

## <a name="see-pricing-for-different-user-roles"></a>Ver preços para diferentes funções de utilizador

Pode ver preços diferentes no portal do Azure, consoante a sua função administrativa e como as políticas de visualização de custos são definidas pelo Administrador do Enterprise. As duas políticas no Enterprise Portal que afetam os preços que vê no portal do Azure são:

- Os Administradores de Departamento podem ver os custos
- Os Proprietários de Contas podem ver os custos

Para saber como definir essas políticas, veja [Gerir o acesso às informações de faturação do Azure](manage-billing-access.md).

A tabela seguinte mostra a relação entre as funções de administrador do Contrato Enterprise, a política de visualização dos custos, a função do Azure no portal do Azure e os preços que vê no portal do Azure. O Administrador do Enterprise vê sempre os detalhes de utilização com base no preço do EA da organização. No entanto, o Administrador de Departamento e o Proprietário da Conta têm visualizações de preços diferentes com base na política de visualização de custos e na função do Azure. A função Administrador de Departamento listada na tabela seguinte refere-se às funções Administrador de Departamento e Administrador de Departamento (só de leitura).

|Função de administrador do Contrato Enterprise|Política de visualização dos custos por função|Função do Azure|Visualização dos preços|
|---|---|---|---|
|Proprietário da Conta OU Administrador de Departamento|✔ Ativada|Proprietário|Preço do EA da Organização|
|Proprietário da Conta OU Administrador de Departamento|✘ Desativada|Proprietário|Preços de retalho|
|Proprietário da Conta OU Administrador de Departamento|✔ Ativada |nenhuma|Sem preços|
|Proprietário da Conta OU Administrador de Departamento|✘ Desativada |nenhuma|Sem preços|
|Nenhuma|Não aplicável |Proprietário|Preços de retalho|

Deve definir a função de administrador do Enterprise e as políticas de visualização de custos no Enterprise Portal. A função do Azure pode ser atualizada no portal do Azure. Para obter mais informações, consulte [as funções De Atribuição Azure utilizando o portal Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passos seguintes

- [Gerir o acesso às informações de faturação do Azure](manage-billing-access.md)
- [Atribuir funções Azure usando o portal Azure](../../role-based-access-control/role-assignments-portal.md)
- [Funções incorporadas do Azure](../../role-based-access-control/built-in-roles.md)
