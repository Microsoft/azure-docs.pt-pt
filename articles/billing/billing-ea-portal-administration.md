---
title: Administração do Azure EA Portal
description: Este artigo explica as tarefas comuns que um administrador realiza no Azure EA Portal.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/25/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: 87947fb29d8fa7dd86818caef139e776c04c7650
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71308091"
---
# <a name="azure-ea-portal-administration"></a>Administração do Azure EA Portal

Este artigo explica as tarefas comuns que um administrador realiza no Azure EA Portal (https://ea.azure.com). O Azure EA Portal é um portal de gestão online que ajuda os clientes a gerir os custos dos seus serviços de EA do Azure. Para obter informações introdutórias sobre o Azure EA Portal, veja o artigo [Introdução ao Azure EA Portal](billing-ea-portal-get-started.md).

## <a name="add-a-new-enterprise-administrator"></a>Adicionar um novo administrador corporativo

Os administradores corporativos têm muitos privilégios ao gerir uma inscrição de EA do Azure. O administrador do EA do Azure inicial foi criado quando o contrato de EA foi configurado. No entanto, pode adicionar ou remover novos administradores a qualquer momento. Apenas são adicionados novos administradores por administradores existentes. Para obter mais informações sobre como adicionar administradores corporativos adicionais, veja [Criar outro administrador corporativo](billing-ea-portal-get-started.md#create-another-enterprise-admin). Para obter mais informações sobre as funções e tarefas do perfil de faturação, veja [Funções e tarefas do perfil de faturação](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="update-user-state-from-pending-to-active"></a>Atualizar o estado do utilizador de pendente para ativo

Quando são adicionados novos Proprietários de Conta (AO) a uma inscrição do EA do Azure pela primeira vez, o estado aparece como _Pendente_. Quando um novo proprietário de conta recebe o e-mail de boas-vindas de ativação, ele pode iniciar sessão para ativar a respetiva conta. Quando ativam a respetiva conta, o estado da conta é atualizado de _Pendente_ para _Ativo_. Pode ser solicitado a novos utilizadores que introduzam o nome próprio e o apelido para criar uma Conta de Comércio. Nesse caso, têm de adicionar as informações necessárias para continuar e, em seguida, a conta é ativada.

## <a name="add-a-department-admin"></a>Adicionar um Administrador de Departamento

Após a criação de um departamento por um administrador do EA do Azure, o administrador corporativo do Azure pode adicionar administradores de departamento e associar cada um deles a um departamento. Um administrador de departamento pode criar novas contas. São necessárias novas contas para que as subscrições do EA do Azure sejam criadas.

Para obter mais informações sobre como adicionar um administrador de departamento, veja [Criar um administrador de departamento do EA do Azure](billing-ea-portal-get-started.md#add-a-department-admin).

## <a name="enterprise-user-roles"></a>Funções de utilizador corporativo

O Azure EA Portal ajuda a administrar os seus custos e utilização do EA do Azure. Há três funções principais no Azure EA Portal:

- Administrador do EA
- Administrador de Departamento
- Proprietário de Conta

Cada função tem um nível diferente de acesso e autoridade.

Para obter mais informações sobre as funções de utilizador, veja Funções de utilizador corporativo.

## <a name="add-an-azure-ea-account"></a>Adicionar uma conta do EA do Azure

A conta do EA do Azure é uma unidade organizacional no Azure EA Portal que é usada para administrar subscrições e também é usada para relatórios. Para aceder e usar os serviços do Azure, precisa de criar uma conta ou ter uma criada para si.

Para obter mais informações sobre contas do Azure, veja Adicionar uma conta.

## <a name="transfer-an-enterprise-account-to-a-new-enrollment"></a>Transferir uma conta corporativa para uma nova inscrição

Tenha os seguintes pontos em mente ao transferir uma conta corporativa para uma nova inscrição:

- Apenas as contas especificadas no pedido são transferidas. Se todas as contas forem escolhidas, serão todas transferidas.
- A inscrição de origem retém o estado como Ativo ou Expandido. Pode continuar com a inscrição até que expire.

### <a name="effective-transfer-date"></a>Data de transferência efetiva

A data de transferência efetiva pode ser uma data na ou posterior à data de início da inscrição que pretende transferir. A inscrição que está a transferir é a _inscrição de destino_. Após a transferência da conta, todas as informações de utilização na conta antes da data de transferência efetiva permanecem na inscrição de que está a transferir. A inscrição de que está a transferir é a _inscrição de origem_.  A utilização da inscrição de origem é cobrada como alocação monetária ou utilização excedida. A utilização que ocorre após a data de transferência efetiva é transferida para a nova inscrição e cobrada em conformidade.

Pode voltar a uma transferência de conta até à data de início da inscrição de destino. Ou, até à data de início efetiva da inscrição de origem.

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
  - Em alguns casos, a Microsoft poderá pedir aprovação adicional de um administrador de EA da inscrição de origem
- Se uma transferência de conta não atender aos seus requisitos, considere uma transferência de inscrição.
- A transferência de conta transfere todos os serviços, subscrições, contas, departamentos e toda a estrutura de inscrição, incluindo todos os administradores do departamento do EA.
- A transferência de conta define o estado da inscrição de origem para _Transferida_. A conta transferida está disponível apenas para fins de relatório de histórico de utilização.
- Não pode adicionar funções ou subscrições a uma inscrição com o estado Transferida. O estado impede a utilização adicional em relação à inscrição.
- Qualquer saldo de alocação monetária restante no contrato de origem é perdido, incluindo os termos futuros.


## <a name="transfer-enterprise-enrollment-to-a-new-one"></a>Transferir inscrição corporativa para uma nova

Quando pede a transferência de uma inscrição corporativa inteira para uma inscrição, ocorrem as seguintes ações:

- Todos os serviços, subscrições, contas, departamentos e toda a estrutura de inscrição, incluindo todos os administradores do departamento do EA, são transferidos.
- O estado da inscrição é definido para _Transferida_. A inscrição transferida está disponível apenas para fins de relatório de histórico de utilização.
- Não pode adicionar funções ou subscrições a uma inscrição transferida. O estado Transferida impede a utilização adicional em relação à inscrição.
- Qualquer saldo de alocação monetária restante no contrato é perdido, incluindo os termos futuros.

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
  - Em alguns casos, a Microsoft poderá pedir aprovação adicional de um administrador de EA da inscrição de origem.
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



## <a name="close-an-azure-enterprise-enrollment"></a>Fechar uma inscrição corporativa do Azure

Se quiser fechar a sua inscrição de EA do Azure, pode:

- Cancelar todas as suas subscrições associadas ao seu EA do Azure no portal do Azure.
- Contactar consultor informático do cliente ou o seu parceiro ou solicitar que cessem o seu Contrato Enterprise do Azure.

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

## <a name="manage-partner-notifications"></a>Gerir notificações de parceiros

Os Administradores de Parceiros podem gerir a frequência com que recebem notificações de utilização das respetivas inscrições. Recebem automaticamente notificações semanais do respetivo saldo não faturado. Podem alterar a frequência de notificações individuais para mensal, semanal, diária ou desligá-las completamente.

Se uma notificação não for recebida por um utilizador, verifique se as definições de notificação do utilizador estão corretas com os passos a seguir.

1. Inicie sessão no Azure EA Portal como Administrador de Parceiro.
2. Clique em **Gerir** e, em seguida, clique no separador **Parceiro**.
3. Visualize a lista de administradores na secção **Administrador**.
4. Para editar as preferências de notificação, passe o rato sobre o administrador apropriado e clique no símbolo de lápis.
5. Aumente a frequência de notificação e as notificações de ciclo de vida conforme necessário.
6. Adicione um contacto, se necessário, e clique em **Adicionar**.
7. Clique em **Guardar**.

![Exemplo que mostra a caixa Adicionar Contacto ](./media/billing-ea-portal-administration/create-ea-manage-partner-notification.png)

## <a name="azure-sponsorship-offer"></a>Oferta do Azure Sponsorship
A oferta do Azure Sponsorship é uma conta do Microsoft Azure patrocinada limitada. Está disponível apenas por convite por e-mail para um número de clientes limitado que são selecionados pela Microsoft. Se for elegível para a oferta do Microsoft Azure Sponsorship, receberá um convite por e-mail para o seu ID de conta.
Para obter mais informações, consulte:

- Descrição geral da oferta do Sponsorship – https://azure.microsoft.com/en-us/offers/ms-azr-0143p/
- Portal de saldo do Sponsorship – https://www.microsoftazuresponsorships.com/balance  
- Perguntas frequentes externas sobre o Sponsorship – https://azuresponsorships-staging.azurewebsites.net/faq
- Pedido de suporte para a ativação do Sponsorship –  http://aka.ms/azrsponsorship

## <a name="next-steps"></a>Passos seguintes
- Leia sobre como [as reservas de máquina virtual](billing-ea-portal-vm-reservations.md) podem ajudar a poupar dinheiro.
- Se precisar de ajuda na resolução de problemas com o Azure EA Portal, veja [Resolver problemas de acesso do Azure EA Portal](billing-ea-portal-troubleshoot.md).
