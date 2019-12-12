---
title: Administração do Azure EA Portal
description: Este artigo explica as tarefas comuns que um administrador realiza no Azure EA Portal.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/04/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: c53a051df0a0100d9209530490d910612be2f30d
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849926"
---
# <a name="azure-ea-portal-administration"></a>Administração do Azure EA Portal

Este artigo explica as tarefas comuns que um administrador realiza no Azure EA Portal (https://ea.azure.com). O Azure EA Portal é um portal de gestão online que ajuda os clientes a gerir os custos dos seus serviços de EA do Azure. Para obter informações introdutórias sobre o Azure EA Portal, veja o artigo [Introdução ao Azure EA Portal](billing-ea-portal-get-started.md).

## <a name="add-a-new-enterprise-administrator"></a>Adicionar um novo administrador corporativo

Os administradores corporativos têm muitos privilégios ao gerir uma inscrição de EA do Azure. O administrador do EA do Azure inicial foi criado quando o contrato de EA foi configurado. No entanto, pode adicionar ou remover novos administradores a qualquer momento. Apenas são adicionados novos administradores por administradores existentes. Para obter mais informações sobre como adicionar administradores corporativos adicionais, veja [Criar outro administrador corporativo](billing-ea-portal-get-started.md#create-another-enterprise-admin). Para obter mais informações sobre as funções e tarefas do perfil de faturação, veja [Funções e tarefas do perfil de faturação](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="update-user-state-from-pending-to-active"></a>Atualizar o estado do utilizador de pendente para ativo

Quando são adicionados novos Proprietários de Conta (AO) a uma inscrição do EA do Azure pela primeira vez, o estado aparece como _Pendente_. Quando um novo proprietário de conta recebe o e-mail de boas-vindas de ativação, ele pode iniciar sessão para ativar a respetiva conta. Após ativar a conta, o estado da conta é atualizado de _Pendente_ para _Ativo_. O proprietário da conta deve ler a mensagem “Aviso” e clicar em **Continuar**. Pode ser solicitado a novos utilizadores que introduzam o nome próprio e o apelido para criar uma Conta de Comércio. Nesse caso, têm de adicionar as informações necessárias para continuar e, em seguida, a conta é ativada.

## <a name="add-a-department-admin"></a>Adicionar um Administrador de Departamento

Após a criação de um departamento por um administrador do EA do Azure, o administrador corporativo do Azure pode adicionar administradores de departamento e associar cada um deles a um departamento. Um administrador de departamento pode criar novas contas. São necessárias novas contas para que as subscrições do EA do Azure sejam criadas.

Para obter mais informações sobre como adicionar um administrador de departamento, veja [Criar um administrador de departamento do EA do Azure](billing-ea-portal-get-started.md#add-a-department-admin).

## <a name="associate-an-account-to-a-department"></a>Associar uma conta a um departamento

Os Administradores do Enterprise podem associar contas existentes a Departamentos ao abrigo da inscrição.

### <a name="to-associate-an-account-to-a-department"></a>Para associar uma conta a um departamento

1. Inicie sessão no Azure EA Portal como administrador do Enterprise.
1. Clique em **Gerir** no painel de navegação esquerdo.
1. Clique em **Departamento**.
1. Passe o rato sobre a linha com a Conta desejada e clique no ícone de lápis à direita.
1. Selecione o Departamento desejado no menu pendente.
1. Clique em **Guardar**.

## <a name="department-spending-quotas"></a>Quotas de despesas do departamento

Os clientes EA podem definir ou alterar as quotas de despesas de cada departamento numa inscrição. O valor das quotas de despesas é definido para o atual período de alocação. No final do período de alocação atual, o sistema prolongará a quota de despesas existente para o próximo período de alocação, a menos que os valores sejam atualizados.

O administrador do departamento pode ver a quota de despesas, mas apenas o administrador do Enterprise pode atualizar o valor da quota. O administrador do Enterprise e o administrador do departamento receberão notificações assim que a cota atingir 50%, 75%, 90% e 100%.

### <a name="enterprise-administrator-to-set-the-quota"></a>Como administrador do Enterprise para definir a quota, faça o seguinte:

 1. Abra o Azure EA Portal.
 1. Clique em **Gerir** no painel de navegação esquerdo.
 1. Clique no separador **Departamento**.
 1. Clique no Departamento desejado.
 1. Clique no ícone de lápis na secção Detalhes do Departamento ou clique no botão **+ Adicionar Departamento** para adicionar uma quota de despesas juntamente com um novo departamento.
 1. Em Detalhes do Departamento, introduza um valor de quota de despesas na moeda da inscrição na caixa $ Quota de Despesas (deve ser superior a 0).
    - Pode também aproveitar para editar o Nome do Departamento e o Centro de Custos.
 1. Prima **Guardar**.

A quota de despesas do departamento estará agora visível na vista Lista do Departamento no separador Departamento. No final da alocação atual, o Azure EA Portal manterá as quotas de despesas para o próximo período de alocação.

O valor da quota do departamento é independente da alocação monetária atual. O valor da quota e os alertas aplicam-se apenas à utilização do proprietário. A quota de despesas do departamento destina-se apenas a fins informativos e não impõe limites de gastos.

### <a name="department-administrator-to-view-the-quota"></a>Como administrador do departamento para visualizar a quota, faça o seguinte:

1. Abra o Azure EA Portal.
1. Clique em **Gerir** no painel de navegação esquerdo.
1. Clique no separador **Departamento** e consulte a vista Lista do Departamento com as quotas de despesas.

Se for um cliente indireto, as funcionalidades dos custos deverão ser ativadas pelo parceiro do canal.

## <a name="enterprise-user-roles"></a>Funções de utilizador corporativo

O Azure EA Portal ajuda a administrar os seus custos e utilização do EA do Azure. Há três funções principais no Azure EA Portal:

- Administrador do EA
- Administrador de Departamento
- Proprietário de Conta

Cada função tem um nível diferente de acesso e autoridade.

Para obter mais informações sobre as funções de utilizador, veja [Funções de utilizador do Enterprise](https://docs.microsoft.com/azure/billing/billing-ea-portal-get-started#enterprise-user-roles).

## <a name="add-an-azure-ea-account"></a>Adicionar uma conta do EA do Azure

A conta do EA do Azure é uma unidade organizacional no Azure EA Portal que é usada para administrar subscrições e também é usada para relatórios. Para aceder e usar os serviços do Azure, precisa de criar uma conta ou ter uma criada para si.

Para obter mais informações sobre contas do Azure, veja Adicionar uma conta.

## <a name="enterprise-devtest-offer"></a>Oferta do Enterprise Dev/Test

Como administrador do Azure Enterprise, pode agora permitir que os proprietários da conta na sua organização criem subscrições com base na oferta do EA Dev/Test. Para tal, marque a caixa Dev/Test desse proprietário de conta no Azure EA Portal.

Após marcar a caixa Dev/Test, informe o proprietário da conta de que pode configurar as subscrições EA Dev/Test necessárias para as suas equipas de subscritores do Dev/Test.

Esta oferta permite que os subscritores ativos do Visual Studio executem cargas de trabalho de desenvolvimento e de teste no Azure com taxas de Dev/Test especiais, com acesso à galeria de imagens de Dev/Test completa, incluindo Windows 8.1 e Windows 10.

### <a name="to-set-up-the-enterprise-devtest-offer"></a>Para configurar a oferta Enterprise Dev/Test:

1. Inicie sessão como o administrador do Enterprise.
1. Clique em **Gerir** no painel de navegação esquerdo.
1. Clique no separador **Conta**.
1. Clique na linha da conta pretendida para ativar o acesso Dev/Test.
1. Clique no ícone de Lápis à direita da linha.
1. Marque a caixa de verificação Dev/Test.
1. Prima **Guardar**.

Quando um utilizador é adicionado como um proprietário da conta através do Azure EA Portal, todas as subscrições do Azure associadas ao proprietário da conta baseadas na oferta Dev/Test PAYG ou nas ofertas de crédito mensais dos subscritores do Visual Studio serão convertidas em oferta EA Dev/Test. As subscrições baseadas noutros tipos de oferta, como PAYG, associadas ao Proprietário da Conta serão convertidas em ofertas Microsoft Azure Enterprise.

De momento, a Oferta Dev/Test não é aplicável aos clientes Azure Gov.

## <a name="transfer-an-enterprise-account-to-a-new-enrollment"></a>Transferir uma conta corporativa para uma nova inscrição

Tenha os seguintes pontos em mente ao transferir uma conta corporativa para uma nova inscrição:

- Apenas as contas especificadas no pedido são transferidas. Se todas as contas forem escolhidas, serão todas transferidas.
- A inscrição de origem retém o estado como Ativo ou Expandido. Pode continuar com a inscrição até que expire.

### <a name="effective-transfer-date"></a>Data de transferência efetiva

A data de transferência efetiva pode ser uma data na ou posterior à data de início da inscrição que pretende transferir. A inscrição que está a transferir é a _inscrição de destino_. Após a transferência da conta, todas as informações de utilização na conta antes da data de transferência efetiva permanecem na inscrição de que está a transferir. A inscrição de que está a transferir é a _inscrição de origem_.  A utilização da inscrição de origem é cobrada como alocação monetária ou utilização excedida. A utilização que ocorre após a data de transferência efetiva é transferida para a nova inscrição e cobrada em conformidade.

Pode voltar a uma transferência de inscrição anterior até à data de início da inscrição de destino. Ou, até à data de início efetiva da inscrição de origem.

### <a name="monetary-commitment"></a>Alocação monetária

A alocação monetária não é transferível entre inscrições. Os saldos de alocação monetária são associados contratualmente à inscrição em que foi pedida. A alocação monetária não é transferida como parte do processo de transferência de inscrição ou conta.

### <a name="services-affected"></a>Serviços afetados

Não há tempo de inatividade durante a transferência da conta. Poderá ser concluída no mesmo dia do pedido se todas as informações necessárias forem fornecidas.

### <a name="prerequisites"></a>Pré-requisitos

Ao pedir uma transferência de conta, forneça as seguintes informações:


- Nome da conta e ID do proprietário da conta a ser transferida
- Relativamente à inscrição de origem, o número de inscrição e a conta a ser transferida
- Relativamente à inscrição de destino, o número de inscrição para que se transfere
- Relativamente à data efetiva da transferência de conta, pode ser uma data na ou após a data de início da inscrição de destino

Outros pontos a ter em mente antes de uma transferência de conta:

- É necessária a aprovação de um administrador de EA para a inscrição de origem e de destino
- Se uma transferência de conta não atender aos seus requisitos, considere uma transferência de inscrição.
- A transferência de conta transfere todos os serviços e subscrições relacionados com as contas específicas.
- Uma vez concluída a transferência, a conta transferida aparece inativa na inscrição de origem.
- Uma transferência de conta pode ser pré-datada para qualquer data no âmbito da data de início da inscrição de destino.
- A conta mostra a data de fim que corresponde à data de transferência efetiva na inscrição de origem e aparece como data de início na inscrição de destino.
- Qualquer utilização ocorrida para a conta antes da data de transferência efetiva permanece na inscrição de origem.


## <a name="transfer-enterprise-enrollment-to-a-new-one"></a>Transferir inscrição corporativa para uma nova

Quando pede a transferência de uma inscrição corporativa inteira para uma inscrição, ocorrem as seguintes ações:

- Todos os serviços, subscrições, contas, departamentos e toda a estrutura de inscrição, incluindo todos os administradores do departamento do EA, são transferidos.
- O estado da inscrição é definido para _Transferida_. A inscrição transferida está disponível apenas para fins de relatório de histórico de utilização.
- Não pode adicionar funções ou subscrições a uma inscrição transferida. O estado Transferida impede a utilização adicional em relação à inscrição.
- Qualquer saldo de alocação monetária restante no contrato é perdido, incluindo os termos futuros.
-   Se a inscrição da qual esta a transferir possuir compras de RIs, a taxa das compras de RIs permanecerá na inscrição de origem. No entanto, os benefícios das RIs serão transferidos para utilização na nova inscrição.
-   A taxa de compra única do marketplace e todas as taxas fixas mensais já incorridas na antiga inscrição não serão transferidas para a nova inscrição. As taxas do marketplace baseadas no consumo serão transferidas.
-   Após a transferência de uma inscrição retroativa, as taxas únicas das compras permanecerão na inscrição de origem.

### <a name="effective-transfer-date"></a>Data de transferência efetiva

O dia de transferência efetiva pode ser uma data na ou posterior à data de início da inscrição que pretende transferir para a inscrição de destino.

A utilização da inscrição de origem é cobrada como alocação monetária ou utilização excedida. A utilização que ocorre após a data de transferência efetiva é transferida para a nova inscrição e cobrada em conformidade.

### <a name="effective-transfer-date-in-the-past"></a>Data de transferência efetiva no passado

Pode voltar a uma transferência de conta até à data de início da inscrição de destino. Ou, até à data de início efetiva da inscrição de origem.

### <a name="monetary-commitment"></a>Alocação monetária

A alocação monetária não é transferível entre inscrições. Os saldos de alocação monetária são associados contratualmente à inscrição em que foi pedida. A alocação monetária não é transferida como parte do processo de transferência de inscrição ou conta.

### <a name="services-affected"></a>Serviços afetados

Não há tempo de inatividade durante a transferência da conta. Pode ser concluída no mesmo dia do pedido se todas as informações necessárias forem fornecidas.

### <a name="prerequisites"></a>Pré-requisitos

Ao pedir uma transferência de inscrição, forneça as seguintes informações:

- Relativamente à inscrição de origem, o número de inscrição e a conta a transferir.
- Relativamente à inscrição de destino, o número de inscrição para o qual transferir.
- Relativamente à data efetiva da transferência de inscrição, pode ser uma data na ou após a data de início da inscrição de destino. A data escolhida não pode afetar a utilização de nenhuma fatura de utilização excedida já emitida.

Outros pontos a ter em mente antes de uma transferência de inscrição:

- É necessária a aprovação de um administrador de EA para as inscrições de origem e de destino.
- Se uma transferência de inscrição não atender aos seus requisitos, considere uma transferência de conta.
- Apenas as contas que especificar serão transferidas. Pode pedir a transferência de todas as suas contas.
- A inscrição de origem retém o estado como Ativo/Expandido. Pode continuar com a inscrição até que expire.

## <a name="change-account-owner"></a>Alterar o Proprietário da Conta

O Azure EA Portal pode transferir subscrições de um proprietário de conta para outro. Para obter mais informações, veja [Criar Proprietário de Conta](billing-ea-portal-get-started.md#change-account-owner).

## <a name="subscription-transfer-effects"></a>Efeitos da transferência da subscrição

Quando uma subscrição do Azure é transferida para uma conta no mesmo inquilino do AAD, todos os utilizadores, grupos e principais de serviço que tinham um [controlo de acesso baseado em funções (RBAC)](../role-based-access-control/overview.md) para gerir recursos mantêm o respetivo acesso.

Para visualizar os utilizadores com acesso RBAC à subscrição:

1. No portal do Azure, abra as **Subscrições**.
2. Selecione a subscrição que pretende visualizar e, em seguida, selecione **Controlo de acesso (IAM)** .
3. Selecione **Atribuições de funções**. A página de atribuições de funções lista todos os utilizadores que têm acesso RBAC à subscrição.

Se a subscrição for transferida para uma conta num inquilino do AAD diferente, todos os utilizadores, grupos e principais de serviço que tinham [RBAC](../role-based-access-control/overview.md) para gerir recursos _perdem_ o respetivo acesso. Embora o acesso RBAC não esteja presente, o acesso à subscrição poderá estar disponível por meio de mecanismos de segurança, incluindo:

- Certificados de gestão que concedem ao utilizador direitos de administrador aos recursos da subscrição. Para obter mais informações, veja [Criar e carregar um certificado de gestão do Azure](../cloud-services/cloud-services-certs-create.md).
- Chaves de acesso dos serviços como o Armazenamento. Para mais informações, veja [Visão geral de conta de armazenamento do Azure](../storage/common/storage-account-overview.md).
- Credenciais de Acesso Remoto dos serviços como máquinas virtuais do Azure.

Se o destinatário precisar de restringir o acesso aos recursos do Azure, deverá considerar a atualização de todos os segredos associados ao serviço. A maioria dos recursos pode ser atualizada com os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. No menu Hub, selecione **Todos os recursos**.
3. Selecione o recurso.
4. Na página de recursos, clique em **Definições** para visualizar e atualizar os segredos existentes.

## <a name="delete-subscription"></a>Eliminar subscrição

Para eliminar uma subscrição em que é o proprietário da conta:

1. Inicie sessão no portal do Azure com as credenciais associadas à sua conta.
1. No menu Hub, selecione **Subscrições**.
1. No separador Subscrições no canto superior esquerdo da página, selecione a subscrição que quer cancelar e clique em **Cancelar Sub** para iniciar o separador de cancelamento.
1. Introduza o nome da subscrição, escolha o motivo de cancelamento e clique no botão **Cancelar Sub**.

Nota: apenas os administradores da conta podem cancelar subscrições.

## <a name="delete-an-account"></a>Eliminar uma conta

A remoção da conta só é possível para contas ativas sem subscrições ativas.

1. No Enterprise Portal, selecione **Gerir** no painel de navegação esquerdo.
1. Clique no separador **Conta**.
1. Na tabela Contas, selecione a Conta que quer eliminar.
1. Clique no ícone X à direita da linha Conta.
1. Quando não existirem subscrições ativas na conta, clique no botão **Sim** sob a linha Conta para confirmar a remoção da Conta.

## <a name="update-notification-settings"></a>Atualizar definições de notificação

Os administradores corporativos são inscritos automaticamente para receber notificações de utilização associadas à inscrição. Cada administrador corporativo pode alterar o intervalo das notificações individuais ou pode desativá-las completamente.

Os contactos de notificação são mostrados no Azure EA Portal na secção **Contato de Notificação**. Gerir os seus contactos de notificação garante que as pessoas certas na sua organização recebem notificações do EA do Azure.

Para visualizar as definições de notificações atuais:

1. No Azure EA Portal, navegue até **Gerir** > **Contacto de Notificação**.
2. Endereço de e-mail – o endereço de e-mail associado à Conta da Microsoft, Conta de Trabalho ou Conta de Escola do administrador corporativo que recebe as notificações.
3. Frequência de notificação de saldo não faturado – a frequência com que as notificações são definidas para enviar para cada administrador corporativo individual.

Para adicionar um contacto:

1. Clique em **+Adicionar contacto**.
2. Introduza o endereço de e-mail e, em seguida, confirme-o.
3. Clique em **Guardar**.

O novo contacto de notificação é apresentado na secção **Contato de Notificação**. Para alterar a frequência de notificação, selecione o contacto de notificação e clique no símbolo de lápis à direita do registo selecionado. Defina a frequência como **diária**, **semanal**, **mensal** ou **nenhuma**.

Pode suprimir _a aproximação da data de término do período de cobertura_ e _desativar e desaprovisionar as notificações do ciclo de vida de aproximação de data_. Desativar notificações de ciclo de vida suprime notificações sobre o período de cobertura e a data de término do contrato.

## <a name="manage-partner-administrators"></a>Gerir administradores parceiros

Cada administrador parceiro no Azure EA Portal pode adicionar ou remover outros administradores parceiros. Os administradores parceiros são associados às organizações parceiras das inscrições indiretas e não associados diretamente às inscrições.

### <a name="add-a-partner-administrator"></a>Adicionar um administrador parceiro

Para ver uma lista de todas as inscrições associadas à mesma organização parceira que o atual utilizador, clique no separador **Inscrição** e selecione a caixa de inscrição desejada.

1. Inicie sessão como administrador parceiro.
1. Clique em **Gerir** no painel de navegação esquerdo.
1. Clique no separador **Parceiro**.
1. Clique em **+ Adicionar Administrador** e preencha o endereço de e-mail, o contato de notificação e os detalhes de notificação.
1. Prima **Adicionar**.

### <a name="remove-a-partner-administrator"></a>Remover um administrador parceiro

Para ver uma lista de todas as inscrições associadas à mesma organização parceira que o atual utilizador, clique no separador **Inscrição** e selecione a caixa de inscrição desejada.

1. Inicie sessão como administrador parceiro.
1. Clique em **Gerir** no painel de navegação esquerdo.
1. Clique no separador **Parceiro**.
1. Na secção Administrador, selecione a linha apropriada para o administrador que quer remover.
1. Prima o ícone X à direita.
1. Confirme a eliminação.

## <a name="manage-partner-notifications"></a>Gerir notificações de parceiros

Os Administradores de Parceiros podem gerir a frequência com que recebem notificações de utilização das respetivas inscrições. Recebem automaticamente notificações semanais do respetivo saldo não faturado. Podem alterar a frequência de notificações individuais para mensal, semanal, diária ou desligá-las completamente.

Se uma notificação não for recebida por um utilizador, verifique se as definições de notificação do utilizador estão corretas com os passos a seguir.

1. Inicie sessão no Azure EA Portal como Administrador de Parceiro.
2. Clique em **Gerir** e, em seguida, clique no separador **Parceiro**.
3. Veja a lista de administradores na secção Administrador.
4. Para editar as preferências de notificação, passe o rato sobre o administrador apropriado e clique no símbolo de lápis.
5. Aumente a frequência de notificação e as notificações de ciclo de vida conforme necessário.
6. Adicione um contacto, se necessário, e clique em **Adicionar**.
7. Clique em **Guardar**.

![Exemplo que mostra a caixa Adicionar Contacto ](./media/billing-ea-portal-administration/create-ea-manage-partner-notification.png)

## <a name="view-enrollments-for-partner-administrators"></a>Ver as inscrições do administradores parceiros

Os administradores parceiros podem ver uma vista de lista de todas as inscrições diretas e indiretas no Azure EA Portal. As caixas que contêm uma descrição geral de cada inscrição serão apresentadas com o número da inscrição, o nome da inscrição, o saldo e os valores de utilização excedida.

### <a name="view-a-list-of-enrollments"></a>Ver uma Lista de Inscrições

1. Inicie sessão como administrador parceiro.
1. Clique em **Gerir** no painel de navegação esquerdo da página.
1. Clique no separador **Inscrição**.
1. Selecione a caixa da inscrição desejada.

Uma vista de todas as inscrições permanece no topo da página, com caixas para cada inscrição. Adicionalmente, pode navegar entre as inscrições ao clicar no número da inscrição atual no painel de navegação esquerdo da página. Será apresentado um destaque que lhe permitirá procurar inscrições ou selecionar uma inscrição diferente ao clicar na caixa adequada.

## <a name="azure-sponsorship-offer"></a>Oferta Azure Sponsorship

A oferta Azure Sponsorship é uma conta do Microsoft Azure patrocinada limitada. Está disponível apenas por convite por e-mail para um número de clientes limitado que são selecionados pela Microsoft. Se for elegível para a oferta Microsoft Azure Sponsorship, receberá um convite por e-mail para o seu ID de conta.

Para obter mais informações, crie um [pedido de suporte para a ativação do Sponsorship](https://aka.ms/azrsponsorship).

## <a name="conversion-to-work-or-school-account-authentication"></a>Conversão para autenticação de conta escolar ou profissional

Os utilizadores do Azure Enterprise podem converter de um tipo de autenticação de Conta Microsoft (MSA ou Live ID) para uma Conta Escolar ou Profissional (que utiliza o Active Directory no Azure).

Para começar:

1. Adicione a conta escolar ou profissional ao Azure EA Portal nas funções necessárias.
1. Se receber erros, a conta poderá não ser válida no Active Directory.  O Azure utiliza o Nome Principal de Utilizador (UPN), que nem sempre idêntico ao endereço de e-mail.
1. Efetue a autenticação no Azure EA Portal com a conta escolar ou profissional.

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>Para converter subscrições de contas Microsoft em contas escolares ou profissionais:

1. Inicie sessão no portal de gestão com a conta Microsoft proprietária das subscrições.
1. Utilize a transferência de propriedade da conta para mover para a nova conta.
1. Agora, a conta Microsoft já não deverá ter subscrições ativas e poderá ser eliminada.
1. Qualquer conta eliminada permanecerá visível no portal num estado inativo por motivos de faturação histórica.  Pode utilizar os filtros para a remover da vista ao selecionar uma caixa de verificação para mostrar apenas as contas ativas.

## <a name="account-subscription-ownership-faq"></a>FAQ sobre a propriedade das subscrições da conta

Este documento responde às perguntas frequentes relacionadas com a propriedade das subscrições da conta.

### <a name="how-many-azure-account-owners-can-you-have-per-subscription"></a>Quantos proprietários de conta do Azure pode ter por subscrição?

Apenas é permitido um proprietário de conta por subscrição.  Podem ser adicionadas funções adicionais com o Acesso Baseado em Funções ou o Controlo de Acesso (IAM) no separador Subscrição no canto superior esquerdo da página em [portal.azure.com]] (https://portal.azure.com).

### <a name="can-an-azure-account-owner-be-listed-under-more-than-one-department"></a>Um proprietário de conta do Azure pode estar listado em mais do que um departamento?

Um proprietário de conta só pode ser associado a um único departamento.  Desta forma, é garantida a monitorização e divisão precisas dos custos/gastos associados ao departamento com o qual está alinhado de acordo com a inscrição EA no Azure EA Portal

### <a name="can-an-azure-account-owner-be-listed-as-a-security-group"></a>Um proprietário de conta do Azure pode ser listado como um grupo de segurança?

Não, um proprietário de subscrição deve ter autenticação do Azure Active Directory (AAD) ou conta Microsoft (MSA) exclusiva. Para ter em conta a sucessão dentro da sua organização, pode considerar a criação de contas genéricas e a utilização do AAD para gerir o acesso à subscrição.

### <a name="can-an-individual-user-own-multiple-subscriptions"></a>Um utilizador individual pode ser proprietário de várias subscrições?

Um proprietário de conta do Azure pode criar e gerir um número ilimitado de subscrições.

### <a name="how-can-i-accessview-all-my-organizations-subscriptions"></a>Como posso aceder/ver todas as subscrições da minha organização?

Atualmente, deve aplicar uma política, ou seja, deve exigir que para cada subscrição criada a sua conta seja adicionada a uma função de subscrição com o acesso baseado em funções.

### <a name="where-do-i-go-to-create-a-subscription"></a>Onde devo ir para criar uma subscrição?

Para poder criar uma subscrição de oferta do Azure (EA), a conta deve ser adicionada à função de proprietário da conta pelo administrador da inscrição EA no Azure EA Portal. Em seguida, deve iniciar sessão no Azure EA Portal para obter o direito a criar subscrições do tipo de oferta EA. Recomendamos que a primeira subscrição EA seja criada a partir da ligação “+ Adicionar Subscrição” no separador Subscrição do EA Portal.  No entanto, quando a conta tiver o direito, poderá ser mais fácil criar subscrições em portal.azure.com no separador Subscrição no canto superior esquerdo da página, onde pode criar e alterar o nome da subscrição num único passo.

### <a name="who-can-create-a-subscription"></a>Quem pode criar uma subscrição?

Para criar uma subscrição do tipo de oferta Azure Enterprise, deve ter direito à função de proprietário de conta no [EA Portal](https://ea.azure.com).

## <a name="next-steps"></a>Passos seguintes

- Leia sobre como [as reservas de máquina virtual](billing-ea-portal-vm-reservations.md) podem ajudar a poupar dinheiro.
- Se precisar de ajuda na resolução de problemas com o Azure EA Portal, veja [Resolver problemas de acesso do Azure EA Portal](billing-ea-portal-troubleshoot.md).
