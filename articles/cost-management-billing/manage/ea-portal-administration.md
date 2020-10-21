---
title: Administração do Azure EA Portal
description: Este artigo explica as tarefas comuns que um administrador realiza no Azure EA Portal.
author: bandersmsft
ms.author: banders
ms.date: 09/03/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 33a53fa46d7d07183b77f2608d44f8ea5d0d2804
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132266"
---
# <a name="azure-ea-portal-administration"></a>Administração do Azure EA Portal

Este artigo explica as tarefas comuns que um administrador realiza no Azure EA Portal (https://ea.azure.com). O Azure EA Portal é um portal de gestão online que ajuda os clientes a gerir os custos dos seus serviços de EA do Azure. Para obter informações introdutórias sobre o Azure EA Portal, veja o artigo [Introdução ao Azure EA Portal](ea-portal-get-started.md).

## <a name="associate-an-account-to-a-department"></a>Associar uma conta a um departamento

Os Administradores do Enterprise podem associar contas existentes a Departamentos ao abrigo da inscrição.

### <a name="to-associate-an-account-to-a-department"></a>Para associar uma conta a um departamento

1. Inicie sessão no Azure EA Portal como administrador do Enterprise.
1. Selecione **Gerir** no painel de navegação esquerdo.
1. Selecione **Departamento**.
1. Passe o rato sobre a linha com a conta e selecione ícone de lápis à direita.
1. Selecione o departamento no menu pendente.
1. Selecione **Guardar**.

## <a name="department-spending-quotas"></a>Quotas de despesas do departamento

Os clientes EA podem definir ou alterar as quotas de despesas de cada departamento numa inscrição. O montante das quotas de despesas é definido para o atual período de Pré-pagamento. No final do período de Pré-pagamento atual, o sistema prolongará a quota de despesas existente para o próximo período de Pré-pagamento, a menos que os valores sejam atualizados.

O administrador do departamento pode ver a quota de despesas, mas apenas o administrador do Enterprise pode atualizar o valor da quota. O administrador do Enterprise e o administrador do departamento receberão notificações assim que a cota atingir 50%, 75%, 90% e 100%.

### <a name="enterprise-administrator-to-set-the-quota"></a>Como administrador do Enterprise para definir a quota, faça o seguinte:

 1. Abra o Azure EA Portal.
 1. Selecione **Gerir** no painel de navegação esquerdo.
 1. Selecione o separador **Departamento**.
 1. Selecione o Departamento.
 1. Selecione o ícone de lápis na secção Detalhes do Departamento ou selecione o símbolo **+ Adicionar Departamento** para adicionar uma quota de despesas juntamente com um novo departamento.
 1. Em Detalhes do Departamento, introduza um valor de quota de despesas na moeda da inscrição na caixa $ Quota de Despesas (tem de ser superior a 0).
    - Pode também aproveitar para editar o Nome do Departamento e o Centro de Custos.
 1. Selecione **Guardar**.

A quota de despesas do departamento estará agora visível na vista Lista do Departamento no separador Departamento. No final do período de Pré-pagamento atual, o Azure EA Portal manterá as quotas de despesas para o próximo período de Pré-pagamento.

O montante da quota do departamento é independente do Pré-pagamento do Azure atual. O montante da quota e os alertas aplicam-se apenas à utilização do proprietário. A quota de despesas do departamento destina-se apenas a fins informativos e não impõe limites de gastos.

### <a name="department-administrator-to-view-the-quota"></a>Como administrador do departamento para visualizar a quota, faça o seguinte:

1. Abra o Azure EA Portal.
1. Selecione **Gerir** no painel de navegação esquerdo.
1. Selecione o separador **Departamento** e veja a vista Lista do Departamento com as quotas de despesas.

Se for um cliente indireto, as funcionalidades dos custos têm de ser ativadas pelo parceiro de canal.

## <a name="enterprise-user-roles"></a>Funções de utilizador corporativo

O Azure EA Portal ajuda a administrar os seus custos e utilização do EA do Azure. Há três funções principais no Azure EA Portal:

- Administrador do EA
- Administrador de Departamento
- Proprietário de Conta

Cada função tem um nível diferente de acesso e autoridade.

Para obter mais informações sobre as funções de utilizador, veja [Funções de utilizador do Enterprise](./ea-portal-get-started.md#enterprise-user-roles).

## <a name="add-an-azure-ea-account"></a>Adicionar uma conta do EA do Azure

A conta do EA do Azure é uma unidade organizacional no EA Portal do Azure. É utilizada para administrar subscrições e para gerar relatórios. Para aceder e usar os serviços do Azure, precisa de criar uma conta ou ter uma criada para si.

Para obter mais informações sobre as contas do Azure, veja [Adicionar uma conta](./ea-portal-get-started.md#add-an-account).

## <a name="enterprise-devtest-offer"></a>Oferta do Enterprise Dev/Test

Como administrador empresarial do Azure, pode permitir que os proprietários da conta na sua organização criem subscrições com base na oferta EA Dev/Test. Para tal, selecione a caixa Dev/Test relativa ao proprietário da conta no EA Portal do Azure.

Depois de selecionar a caixa Dev/Test, informe o proprietário da conta de que pode configurar as subscrições EA Dev/Test necessárias para as equipas de subscritores de Dev/Test dele.

A oferta permite que os subscritores ativos do Visual Studio executem cargas de trabalho de desenvolvimento e teste no Azure a tarifas especiais de Dev/Test. Proporciona acesso à galeria completa de imagens de Dev/Test, incluindo Windows 8.1 e Windows 10.

### <a name="to-set-up-the-enterprise-devtest-offer"></a>Para configurar a oferta Enterprise Dev/Test:

1. Inicie sessão como o administrador do Enterprise.
1. Selecione **Gerir** no painel de navegação esquerdo.
1. Selecione o separador **Conta**.
1. Selecione a linha da conta na qual pretende ativar o acesso Dev/Test.
1. Selecione o ícone de lápis à direita da linha.
1. Marque a caixa de verificação Dev/Test.
1. Selecione **Guardar**.

Quando um utilizador é adicionado como um proprietário da conta através do Azure EA Portal, todas as subscrições do Azure associadas ao proprietário da conta baseadas na oferta Dev/Test PAYG ou nas ofertas de crédito mensais dos subscritores do Visual Studio serão convertidas em oferta EA Dev/Test. As subscrições baseadas noutros tipos de oferta, como PAYG, associadas ao Proprietário da Conta serão convertidas em ofertas Microsoft Azure Enterprise.

De momento, a Oferta Dev/Test não é aplicável aos clientes Azure Gov.

## <a name="delete-subscription"></a>Eliminar subscrição

Para eliminar uma subscrição em que é o proprietário da conta:

1. Inicie sessão no portal do Azure com as credenciais associadas à sua conta.
1. No menu Hub, selecione **Subscrições**.
1. No separador Subscrições no canto superior esquerdo da página, selecione a subscrição que quer cancelar e selecione**Cancelar Sub** para iniciar o separador de cancelamento.
1. Introduza o nome da subscrição, escolha o motivo de cancelamento e selecione o botão **Cancelar Sub**.

Apenas os administradores da conta podem cancelar subscrições.

Para obter mais informações, veja [O que acontece depois de cancelar a minha subscrição?](cancel-azure-subscription.md#what-happens-after-i-cancel-my-subscription).

## <a name="delete-an-account"></a>Eliminar uma conta

A remoção da conta só é possível para contas ativas sem subscrições ativas.

1. No Enterprise Portal, selecione **Gerir** no painel de navegação esquerdo.
1. Selecione o separador **Conta**.
1. Na tabela Contas, selecione a Conta que quer eliminar.
1. Selecione o símbolo X à direita da linha Conta.
1. Quando não existirem subscrições ativas na conta, selecione **Sim** na linha Conta para confirmar a remoção da Conta.

## <a name="update-notification-settings"></a>Atualizar definições de notificação

Os administradores corporativos são inscritos automaticamente para receber notificações de utilização associadas à inscrição. Cada administrador corporativo pode alterar o intervalo das notificações individuais ou pode desativá-las completamente.

Os contactos de notificação são mostrados no Azure EA Portal na secção **Contato de Notificação**. Gerir os seus contactos de notificação garante que as pessoas certas na sua organização recebem notificações do EA do Azure.

Para visualizar as definições de notificações atuais:

1. No Azure EA Portal, navegue até **Gerir** > **Contacto de Notificação**.
2. Endereço de e-mail – o endereço de e-mail associado à Conta da Microsoft, Conta de Trabalho ou Conta de Escola do administrador corporativo que recebe as notificações.
3. Frequência de notificação de saldo não faturado – a frequência com que as notificações são definidas para enviar para cada administrador corporativo individual.

Para adicionar um contacto:

1. Selecione **+Adicionar Contacto**.
2. Introduza o endereço de e-mail e, em seguida, confirme-o.
3. Selecione **Guardar**.

O novo contacto de notificação é apresentado na secção **Contato de Notificação**. Para alterar a frequência de notificação, selecione o contacto de notificação e selecione o símbolo de lápis à direita da linha selecionada. Defina a frequência como **diária**, **semanal**, **mensal** ou **nenhuma**.

Pode suprimir _a aproximação da data de término do período de cobertura_ e _desativar e desaprovisionar as notificações do ciclo de vida de aproximação de data_. Desativar notificações de ciclo de vida suprime notificações sobre o período de cobertura e a data de término do contrato.

## <a name="azure-sponsorship-offer"></a>Oferta Azure Sponsorship

A oferta Azure Sponsorship é uma conta do Microsoft Azure patrocinada limitada. Está disponível apenas por convite por e-mail para um número de clientes limitados que são selecionados pela Microsoft. Se for elegível para a oferta Microsoft Azure Sponsorship, receberá um convite por e-mail para o seu ID de conta.

Para obter mais informações, crie um [pedido de suporte para a ativação do Sponsorship](https://aka.ms/azrsponsorship).

## <a name="conversion-to-work-or-school-account-authentication"></a>Conversão para autenticação de conta escolar ou profissional

Os utilizadores do Azure Enterprise podem converter de um tipo de autenticação de Conta Microsoft (MSA ou Live ID) para uma Conta Escolar ou Profissional (que utiliza o Active Directory no Azure).

Para começar:

1. Adicione a conta escolar ou profissional ao Azure EA Portal nas funções necessárias.
1. Se receber erros, a conta poderá não ser válida no Active Directory.  O Azure utiliza o Nome Principal de Utilizador (UPN), que nem sempre é idêntico ao endereço de e-mail.
1. Efetue a autenticação no Azure EA Portal com a conta escolar ou profissional.

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>Para converter subscrições de contas Microsoft em contas escolares ou profissionais:

1. Inicie sessão no portal de gestão com a conta Microsoft proprietária das subscrições.
1. Utilize a transferência de propriedade da conta para mover para a nova conta.
1. Agora, a conta Microsoft já não deverá ter subscrições ativas e poderá ser eliminada.
1. Qualquer conta eliminada permanecerá visível no portal num estado inativo por motivos de faturação histórica.  Pode utilizar os filtros para a remover da vista ao selecionar uma caixa de verificação para mostrar apenas as contas ativas.

## <a name="account-subscription-ownership-faq"></a>FAQ sobre a propriedade das subscrições da conta

Este documento responde às perguntas frequentes relacionadas com a propriedade das subscrições da conta.

### <a name="how-many-azure-account-owners-can-you-have-per-subscription"></a>Quantos proprietários de conta do Azure pode ter por subscrição?

Apenas é permitido um proprietário de conta por subscrição.  Podem ser adicionadas mais funções mediante a utilização do controlo de acesso baseado em funções do Azure (RBAC do Azure) no separador da subscrição no canto superior esquerdo da página do [portal do Azure](https://portal.azure.com).

### <a name="can-an-azure-account-owner-be-listed-under-more-than-one-department"></a>Um proprietário de conta do Azure pode estar listado em mais do que um departamento?

Não, um proprietário de conta só pode ser associado a um único departamento. A política ajuda a garantir a monitorização e divisão precisas dos custos e gastos associados ao departamento com o qual está alinhado de acordo com a inscrição EA no EA Portal do Azure.

### <a name="can-an-azure-account-owner-be-listed-as-a-security-group"></a>Um proprietário de conta do Azure pode ser listado como um grupo de segurança?

Não, um proprietário de subscrição deve ter autenticação do Azure Active Directory (AAD) ou conta Microsoft (MSA) exclusiva. Para ter em conta a sucessão dentro da sua organização, pode considerar a criação de contas genéricas e a utilização do AAD para gerir o acesso à subscrição.

### <a name="can-an-individual-user-own-multiple-subscriptions"></a>Um utilizador individual pode ser proprietário de várias subscrições?

Um proprietário de conta do Azure pode criar e gerir um número ilimitado de subscrições.

### <a name="how-can-i-accessview-all-my-organizations-subscriptions"></a>Como posso aceder/ver todas as subscrições da minha organização?

Atualmente, o acesso/visualização tem de ser feito por uma política, ou seja, deve exigir que, para cada subscrição criada, a sua conta seja adicionada a uma função de subscrição com o controlo de acesso baseado em funções do Azure (RBAC do Azure).

### <a name="where-do-i-go-to-create-a-subscription"></a>Onde devo ir para criar uma subscrição?

Para poder criar uma subscrição de oferta do Azure (EA), a conta deve ser adicionada à função de proprietário da conta pelo administrador da inscrição EA no EA Portal do Azure. Em seguida, tem de iniciar sessão no EA Portal do Azure para obter o direito a criar subscrições do tipo de oferta EA. Recomendamos que a primeira subscrição EA seja criada a partir da ligação “+ Adicionar Subscrição” no separador Subscrição do EA Portal.  No entanto, quando a conta tiver o direito, poderá ser mais fácil criar subscrições em portal.azure.com no separador Subscrição no canto superior esquerdo da página, onde pode criar e alterar o nome da subscrição num único passo.

### <a name="who-can-create-a-subscription"></a>Quem pode criar uma subscrição?

Para criar uma subscrição do tipo de oferta Azure Enterprise, deve ter direito à função de proprietário de conta no [EA Portal](https://ea.azure.com).

## <a name="next-steps"></a>Passos seguintes

- Leia sobre como [as reservas de máquina virtual](ea-portal-vm-reservations.md) podem ajudar a poupar dinheiro.
- Se precisar de ajuda na resolução de problemas com o Azure EA Portal, veja [Resolver problemas de acesso do Azure EA Portal](ea-portal-troubleshoot.md).