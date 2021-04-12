---
title: Administração do Azure EA Portal
description: Este artigo explica as tarefas comuns que um administrador realiza no Azure EA Portal.
author: bandersmsft
ms.author: banders
ms.date: 03/19/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.custom: contperf-fy21q1
ms.openlocfilehash: 8f643336d50f5ade145454d408599215ed6fd31e
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259443"
---
# <a name="azure-ea-portal-administration"></a>Administração do Azure EA Portal

Este artigo explica as tarefas comuns que um administrador realiza no Azure EA Portal (https://ea.azure.com). O Azure EA Portal é um portal de gestão online que ajuda os clientes a gerir os custos dos seus serviços de EA do Azure. Para obter informações introdutórias sobre o Azure EA Portal, veja o artigo [Introdução ao Azure EA Portal](ea-portal-get-started.md).

## <a name="activate-your-enrollment"></a>Ativar a inscrição

Para ativar o serviço, o administrador do Enterprise inicial deve abrir o [portal Azure Enterprise](https://ea.azure.com) e iniciar sessão com o endereço de e-mail indicado no e-mail de convite.

Se tiver sido configurado como administrador do Enterprise, não precisa de esperar pelo e-mail de ativação. Aceda ao [portal Azure Enterprise](https://ea.azure.com) e inicie sessão com o endereço de e-mail da conta profissional, escolar ou Microsoft e a palavra-passe.

Caso tenha mais de uma inscrição, escolha uma para ativar. Por predefinição, só são mostradas as inscrição ativas. Para ver o histórico de inscrições, desmarque a opção **Ativa** no canto superior direito do portal Azure Enterprise.

Em **Inscrição**, o estado indica **Ativa**.

![O exemplo mostra uma inscrição ativa](./media/ea-portal-administration/ea-enrollment-status.png)

Apenas os administradores do Enterprise do Azure existentes podem criar outros administradores do Enterprise.

### <a name="create-another-enterprise-administrator"></a>Criar outro administrador do Enterprise

Utilize uma das seguintes opções, com base na sua situação.

#### <a name="if-youre-already-an-enterprise-administrator"></a>Se já é administrador da empresa

1. Inicie sessão no [portal Azure Enterprise](https://ea.azure.com).
1. Aceda a **Gerir** > **Detalhes da Inscrição**.
1. Selecione **+ Adicionar Administrador** no canto superior direito.

Garanta que tem os endereços de e-mail do utilizador e o método de autenticação preferencial, como uma conta profissional, escolar ou Microsoft.

#### <a name="if-youre-not-an-enterprise-administrator"></a>Se não é administrador da empresa

Se não for administrador da empresa, contacte um administrador da empresa para solicitar que o adicionem a uma inscrição. O administrador da empresa utiliza os passos anteriores para adicioná-lo como administrador da empresa. Depois de ser adicionado a uma inscrição, recebe um e-mail de ativação.

#### <a name="if-your-enterprise-administrator-cant-help-you"></a>Se o administrador da sua empresa não puder ajudá-lo

Se o administrador do Enterprise não puder ajudar, crie um [Pedido de suporte no portal Azure Enterprise](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Forneça as seguintes informações:

- Número de inscrição
- Endereço de e-mail a adicionar e tipo de autenticação (conta profissional, escolar ou Microsoft)
- Aprovação do e-mail de um administrador do Enterprise existente
  - Se o administrador do Enterprise existente não estiver disponível, contacte o seu parceiro ou assistente de software para pedir que alterem os detalhes de contacto através da ferramenta Centro de Serviços de Licenciamento em Volume (VLSC).

## <a name="create-an-azure-enterprise-department"></a>Criar um departamento do Azure Enterprise

Os administradores do Enterprise e os administradores de departamento utilizam departamentos para organizar e criar relatórios de serviços e de utilização do Azure empresarial por departamento e centro de custos. O administrador do Enterprise pode:

- Adicionar ou remover departamentos.
- Associar uma conta a um departamento.
- Criar administradores de departamento.
- Permitir que os administradores de departamento visualizem os preços e os custos.

Um administrador de departamento pode adicionar contas novas aos departamentos. Pode remover contas dos departamentos, mas não da inscrição.

Para adicionar um departamento:

1. Inicie sessão no portal Azure Enterprise.
1. No painel esquerdo, selecione **Gerir**.
1. Selecione o separador **Departamento** e, em seguida, selecione **+ Adicionar Departamento**.
1. Introduza as informações.
   O nome do departamento é o único campo obrigatório. Tem de ter pelo menos três carateres.
1. Quando terminar, selecione **Adicionar**.

## <a name="add-a-department-administrator"></a>Adicionar um administrador de departamento

Após criar um departamento, o administrador do Enterprise pode adicionar administradores de departamento e associar cada um deles a um departamento. Os administradores de departamentos podem executar as seguintes ações para os respetivos departamentos:

- Criar outros administradores de departamento
- Ver e editar as propriedades do departamento, como o nome ou o centro de custo
- Adicionar contas
- Remover contas
- Transferir detalhes de utilização
- Ver a utilização e os custos mensais <sup>1</sup>

> <sup>1</sup> Um administrador do Enterprise tem de conceder estas permissões. Caso tenha recebido permissão para ver a utilização e os custos mensais do departamento mas não os conseguir ver, contacte o seu parceiro.

### <a name="to-add-a-department-administrator"></a>Para adicionar um administrador de departamento

Como administrador do Enterprise:

1. Inicie sessão no portal Azure Enterprise.
1. No painel esquerdo, selecione **Gerir**.
1. Selecione o separador **Departamento** e, em seguida, selecione o departamento.
1. Selecione **+ Adicionar Administrador** e adicione as informações necessárias.
1. Para acesso só de leitura, defina a opção **Só de Leitura** como **Sim** e, em seguida, selecione **Adicionar**.

![Exemplo que mostra a caixa de diálogo Adicionar Administrador de Departamento](./media/ea-portal-administration/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>Para configurar o acesso só de leitura

Pode conceder acesso só de leitura aos administradores de departamento.

- Ao criar um novo administrador de departamento, defina a opção só de leitura como **Sim**.

- Para editar um administrador de departamento existente:
   1. Selecione um departamento e, em seguida, selecione o símbolo do lápis junto ao **Administrador de Departamento** que quer editar.
   1. Defina o espaço só de leitura como **Sim** e, em seguida, selecione **Guardar**.

Os administradores do Enterprise obtêm automaticamente permissões de administrador de departamento.

## <a name="add-an-account"></a>Adicionar uma conta

A estrutura das contas e das subscrições afeta a forma como elas são administradas e como são apresentadas nas faturas e nos relatórios. Exemplos de estruturas organizacionais típicas: divisões de negócio, equipas funcionais e localizações geográficas.

Para adicionar uma conta:

1. No portal Azure Enterprise, selecione **Gerir** no painel de navegação esquerdo.
1. Selecione o separador **Conta**. Na página **Conta**, selecione **+Adicionar Conta**.
1. Selecione um departamento ou deixe como não atribuído e, em seguida, selecione o tipo de autenticação desejado.
1. Introduza um nome amigável para identificar a conta nos relatórios.
1. Introduza o **E-mail do Proprietário da Conta** para associar à nova conta.
1. Confirme o endereço de e-mail e, em seguida, selecione **Adicionar**.

![Exemplo que mostra a lista de contas e a opção +Adicionar Conta](./media/ea-portal-administration/create-ea-add-an-account.png)

Para adicionar outra conta, selecione **Adicionar Outra Conta** ou selecione **Adicionar** no canto inferior direito da barra de ferramentas esquerda.

Para confirmar a propriedade da conta:

1. Inicie sessão no portal Azure Enterprise.
1. Veja o estado.

   O estado deverá mudar de **Pendente** para **Data de Início/Fim**. A Data de Início/Fim é a data em que o utilizador iniciou sessão pela primeira vez e a data de fim do contrato.
1. Quando a mensagem de **Aviso** é apresentada, o proprietário de conta tem de selecionar **Continuar** para ativar a conta ao iniciar sessão pela primeira vez no portal Azure Enterprise.

## <a name="change-azure-subscription-or-account-ownership"></a>Alterar a propriedade de uma subscrição ou conta do Azure

Os administradores do Enterprise podem utilizar o portal Azure Enterprise para transferir a propriedade da conta de uma determinada subscrição ou de todas as subscrições numa inscrição.

Quando concluir a transferência de uma subscrição ou conta, a Microsoft atualiza o proprietário da conta.

Antes de realizar a transferência da propriedade, compreenda as seguintes políticas do controlo de acesso baseado em funções do Azure (RBAC do Azure):

- Ao fazer transferências de propriedade de subscrições ou contas entre dois IDs organizacionais no mesmo inquilino, as políticas RBAC do Azure, o administrador do serviço atual e as funções de coadministradores são preservadas.
- As transferências de propriedade entre inquilinos ou contas originam a perda das políticas do RBAC do Azure e das atribuições de funções.
- As políticas e as funções de administrador não são transferidas entre diretórios diferentes. Os administradores de serviços são atualizados para o proprietário da conta de destino.
- Para evitar a perda das políticas de RBAC do Azure e das atribuições de funções ao transferir subscrições entre inquilinos, confirme que a caixa de verificação **Mover as subscrições para o inquilino do Azure AD do destinatário** permanece **por selecionar**. Desta forma, os serviços, as funções do Azure e as políticas são mantidas no inquilino do Azure AD atual e só é transferida a propriedade de faturação da conta.  
    :::image type="content" source="./media/ea-portal-administration/unselected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="Imagem que mostra a caixa de verificação não selecionada para mover subscrições para o inquilino do Azure AD" lightbox="./media/ea-portal-administration/unselected-checkbox-move-subscriptions-to-recipients-tenant.png" :::


Antes de alterar um proprietário de conta:

1. No portal Azure Enterprise, veja o separador **Conta** e identifique a conta de origem. A conta de origem tem de estar ativa.
1. Identifique a conta de destino e garanta que está ativa.

Para transferir a propriedade da conta para todas as subscrições:

1. Inicie sessão no portal Azure Enterprise.
1. Na área de navegação à esquerda, selecione **Gerir**.
1. Selecione o separador **Conta** e coloque o cursor sobre uma conta.
1. Selecione o ícone para alterar o proprietário da conta à direita. O ícone assemelha-se a uma pessoa.  
    ![Imagem que mostra o símbolo Alterar Proprietário da Conta](./media/ea-portal-administration/create-ea-create-sub-transfer-account-ownership-of-sub.png)
1. Escolha a conta de destino para a qual transferir e selecione **Seguinte**.
1. Se pretender transferir a propriedade da conta entre inquilinos do Azure AD, selecione a caixa de verificação **Mover as subscrições para o inquilino do Azure AD do destinatário**.  
    :::image type="content" source="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="Imagem que mostra a caixa de verificação selecionada para mover subscrições para o inquilino do Azure AD" lightbox="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" :::
1. Confirme a transferência e selecione **Submeter**.

Para transferir a propriedade da conta para uma subscrição individual:

1. Inicie sessão no portal Azure Enterprise.
1. Na área de navegação à esquerda, selecione **Gerir**.
1. Selecione o separador **Conta** e coloque o cursor sobre uma conta.
1. Selecione o ícone de transferência de subscrições à direita. O ícone assemelha-se a uma página.  
    ![Imagem que mostra o símbolo Transferir Subscrições](./media/ea-portal-administration/ea-transfer-subscriptions.png)
1. Escolha a conta de destino para transferir a subscrição e selecione **Seguinte**.
1. Se pretender transferir a propriedade da subscrição entre inquilinos do Azure AD, selecione a caixa de verificação **Mover as subscrições para o inquilino do Azure AD do destinatário**.  
    :::image type="content" source="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="Imagem que mostra a caixa de verificação selecionada para mover subscrições para o inquilino do Azure AD" lightbox="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" :::
1. Confirme a transferência e, em seguida, selecione **Submeter**.


## <a name="associate-an-account-to-a-department"></a>Associar uma conta a um departamento

Os Administradores do Enterprise podem associar contas existentes a Departamentos ao abrigo da inscrição.

### <a name="to-associate-an-account-to-a-department"></a>Para associar uma conta a um departamento

1. Inicie sessão no Azure EA Portal como administrador do Enterprise.
1. Selecione **Gerir** no painel de navegação esquerdo.
1. Selecione **Departamento**.
1. Passe o rato sobre a linha com a conta e selecione ícone de lápis à direita.
1. Selecione o departamento no menu pendente.
1. Selecione **Guardar**.

## <a name="associate-an-existing-account-with-your-pay-as-you-go-subscription"></a>Associar uma conta existente à subscrição Pay As You Go

Caso já possua uma conta existente do Microsoft Azure no portal do Azure, introduza a conta escolar, profissional ou Microsoft associada, de forma a associá-la à inscrição do Contrato Enterprise.

### <a name="associate-an-existing-account"></a>Associar uma conta existente

1. No portal Azure Enterprise, selecione **Gerir**.
1. Selecione o separador **Conta**.
1. Selecione **+Adicionar uma conta**.
1. Introduza a conta profissional, escolar ou Microsoft associada à conta do Azure existente.
1. Confirme a conta associada à conta do Azure existente.
1. Forneça o nome que gostaria de utilizar para identificar esta conta no relatório.
1. Selecione **Adicionar**.
1. Para adicionar uma conta adicional, pode selecionar a opção **+Adicionar uma Conta** novamente ou regressar à home page ao selecionar o botão **Admin**.
1. Se vir a página **Conta**, a conta recentemente adicionada será apresentada com um estado **Pendente**.

### <a name="confirm-account-ownership"></a>Confirmar a propriedade da conta

1. Inicie sessão na conta de e-mail associada à conta profissional, escolar ou Microsoft que indicou.
1. Abra a notificação de e-mail com o título _“Convite para Ativar a Conta no Serviço do Microsoft Azure do Licenciamento em Volume da Microsoft”_ .
1. Selecione a ligação **Iniciar sessão no Microsoft Azure Enterprise Portal** presente no convite.
1. Selecione **Iniciar sessão**.
1. Introduza a conta profissional, escolar ou Microsoft e a palavra-passe para iniciar sessão e confirmar a propriedade da conta.

### <a name="azure-marketplace"></a>Azure Marketplace

Embora a maioria das subscrições possa ser convertida do ambiente Pay as You Go para o Contrato Enterprise do Azure, o mesmo não acontece com os serviços do Azure Marketplace. Para ter uma vista única de todas as subscrições e custos, recomendamos que adicione os serviços do Azure Marketplace ao portal Azure Enterprise.

1. Inicie sessão no portal Azure Enterprise.
1. Selecione **Gerir** no painel de navegação esquerdo.
1. Selecione o separador **Inscrição**.
1. Veja a secção **Detalhes da Inscrição**.
1. À direita do campo Azure Marketplace, selecione o ícone do lápis para o ativar. Selecione **Guardar**.

Agora, o proprietário da conta pode comprar quaisquer serviços do Azure Marketplace anteriormente detidos na subscrição Pay As You Go.

Depois de as novas subscrições do Azure Marketplace terem sido ativadas na inscrição Azure EA, cancele os serviços do Azure Marketplace criados no ambiente Pay As You Go. Este passo é fundamental para que as subscrições do Marketplace não fiquem num estado inadequado quando o instrumento de pagamento Pay As You Go expirar.

### <a name="msdn"></a>MSDN

As subscrições MSDN são automaticamente convertidas em MSDN Dev/Test e a oferta Azure EA perde qualquer crédito monetário existente.

### <a name="azure-in-open"></a>Azure no Licenciamento Open

Se associar uma subscrição Azure no Open a um Contrato Enterprise, perderá todos os créditos não consumidos do Azure no Open. Assim sendo, recomendamos que consuma todos os créditos numa subscrição Azure no Open antes de adicionar a conta ao Contrato Enterprise.  

### <a name="accounts-with-support-subscriptions"></a>Contas com subscrições de suporte

Se o Contrato Enterprise não tiver uma subscrição de suporte e o utilizador adicionar uma conta existente com uma subscrição de suporte ao portal Azure Enterprise, a subscrição de suporte MOSA não será transferida automaticamente. Tem de voltar a comprar uma subscrição de suporte no Azure EA durante o período de tolerância – até ao final do mês subsequente.

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

Para obter mais informações sobre as funções de utilizador, veja [Funções de utilizador do Enterprise](./understand-ea-roles.md#enterprise-user-roles).

## <a name="add-an-azure-ea-account"></a>Adicionar uma conta do EA do Azure

A conta do EA do Azure é uma unidade organizacional no EA Portal do Azure. É utilizada para administrar subscrições e para gerar relatórios. Para aceder e usar os serviços do Azure, precisa de criar uma conta ou ter uma criada para si.

Para obter mais informações sobre as contas do Azure, veja [Adicionar uma conta](#add-an-account).

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

## <a name="create-a-subscription"></a>Criar uma subscrição

Os proprietários da conta podem ver e gerir subscrições. Pode utilizar subscrições para conceder às equipas da sua organização acesso a ambientes e projetos de desenvolvimento. Por exemplo: teste, produção, desenvolvimento e processos de teste.

Ao criar subscrições diferentes para cada ambiente de aplicação, está a ajudar a proteger cada ambiente.

- Também pode atribuir uma conta de administrador de serviços diferente para cada subscrição.
- Pode associar subscrições a quantos serviços quiser.
- O proprietário da conta cria subscrições e atribui uma conta de administrador de serviços a cada subscrição na conta.

### <a name="add-a-subscription"></a>Adicionar uma subscrição

Utilize as seguintes informações para adicionar uma subscrição.

Da primeira vez que adicionar uma subscrição à conta, é-lhe pedido que aceite o Contrato de Subscrição Online da Microsoft (MOSA) e um plano de tarifas. Apesar de não serem aplicáveis a clientes do Contrato Enterprise, o MOSA e o plano de tarifas são necessários para criar a subscrição. A Emenda da Inscrição do Contrato Microsoft Azure Enterprise prevalece sobre os itens acima e a sua relação contratual não é alterada. Quando lhe for pedido, selecione a caixa que indica que aceita os termos.

_Microsoft Azure Enterprise_ é o nome predefinido quando uma subscrição é criada. Pode alterar o nome para a diferenciar das outras subscrições na inscrição e garantir que é reconhecível nos relatórios ao nível da empresa.

Para adicionar uma subscrição:

1. No portal Azure Enterprise, inicie sessão na conta.
1. Selecione o separador **Administrador** e, em seguida, selecione **Subscrição** na parte superior da página.
1. Verifique se tem a sessão iniciada como proprietário da conta.
1. Selecione **+Adicionar Subscrição** e, em seguida, selecione **Comprar**.

   Da primeira vez que adicionar uma subscrição a uma conta, tem de fornecer as suas informações de contacto. Ao adicionar subscrições adicionais, as suas informações de contacto são adicionadas automaticamente.

1. Selecione **Subscrições** e, em seguida, selecione a subscrição que criou.
1. Selecione **Editar Detalhes da Subscrição**.
1. Edite o **Nome da Subscrição** e o **Administrador de Serviços** e, em seguida, selecione a marca de verificação.

   O nome da subscrição aparece nos relatórios. É o nome do projeto associado à subscrição no portal de desenvolvimento.

As novas subscrições podem demorar até 24 horas até surgirem na lista de subscrições. Depois de ter criado uma subscrição, pode:

- [Editar os detalhes da subscrição](https://portal.azure.com)
- [Gerir os serviços da subscrição](https://portal.azure.com/#home)

## <a name="delete-subscription"></a>Eliminar subscrição

Para eliminar uma subscrição em que é o proprietário da conta:

1. Inicie sessão no portal do Azure com as credenciais associadas à sua conta.
1. No menu Hub, selecione **Subscrições**.
1. No separador Subscrições no canto superior esquerdo da página, selecione a subscrição que quer cancelar e selecione **Cancelar Sub** para iniciar o separador de cancelamento.
1. Introduza o nome da subscrição, escolha o motivo de cancelamento e selecione o botão **Cancelar Sub**.

Apenas os administradores da conta podem cancelar subscrições.

Para obter mais informações, veja [O que acontece depois de cancelar a minha subscrição?](cancel-azure-subscription.md#what-happens-after-subscription-cancellation).

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

Os utilizadores do Azure Enterprise podem converter de um tipo de autenticação de Conta Microsoft (MSA ou Live ID) para uma Conta Escolar ou Profissional (que utiliza o Azure Active Directory).

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

### <a name="can-i-associate-my-existing-azure-account-to-azure-ea-enrollment"></a>Posso associar a minha conta do Azure existente à inscrição Azure EA?

Sim. Todas as subscrições do Azure das quais o cliente é o proprietário de conta são convertidas para o Contrato Enterprise. Estão incluídas as subscrições que utilizam crédito mensal, como Visual Studio, AzurePass, MPN, BizSpark, entre outras. Ao converter tais subscrições, perde o crédito mensal.

### <a name="how-many-azure-account-owners-can-you-have-per-subscription"></a>Quantos proprietários de conta do Azure pode ter por subscrição?

Apenas é permitido um proprietário de conta por subscrição.  Podem ser adicionadas funções adicionais com o Acesso Baseado em Funções ou o Controlo de Acesso (IAM) no separador Subscrição no canto superior esquerdo da página do [portal do Azure](https://portal.azure.com).

### <a name="is-it-possible-to-transfer-subscription-ownership-to-another-account"></a>É possível transferir a propriedade da subscrição para outra conta?

Sim, pode transferir a propriedade da subscrição para uma conta diferente. Por exemplo, se a Conta A tiver três subscrições, o administrador Enterprise pode transferir uma subscrição para a Conta B, uma para a Conta C e outra para a Conta D. Ou pode transferir todas as subscrições para a Conta E.

Para transferir subscrições:

1. No portal Azure Enterprise, selecione **Gerir** > **Conta**.
1. Coloque o cursor por cima de **Conta** na extremidade direita para ver as opções **Transferir Propriedade**  (ícone de uma pessoa) e **Transferir Subscrição** (ícone da lista). Estas opções só são visíveis para contas ativas.

### <a name="can-an-azure-account-owner-be-listed-under-more-than-one-department"></a>Um proprietário de conta do Azure pode estar listado em mais do que um departamento?

Não, um proprietário de conta só pode ser associado a um único departamento. A política ajuda a garantir a monitorização e divisão precisas dos custos e gastos associados ao departamento com o qual está alinhado de acordo com a inscrição EA no EA Portal do Azure.

### <a name="can-an-azure-account-owner-be-listed-as-a-security-group"></a>Um proprietário de conta do Azure pode ser listado como um grupo de segurança?

Não. Um proprietário de subscrição deve ter autenticação do Azure Active Directory (AAD) ou conta Microsoft (MSA) exclusiva. Para ter em conta a sucessão dentro da sua organização, pode considerar a criação de contas genéricas e a utilização do Azure AD para gerir o acesso à subscrição.

### <a name="can-an-individual-user-own-multiple-subscriptions"></a>Um utilizador individual pode ser proprietário de várias subscrições?

Um proprietário de conta do Azure pode criar e gerir um número ilimitado de subscrições.

### <a name="how-can-i-accessview-all-my-organizations-subscriptions"></a>Como posso aceder/ver todas as subscrições da minha organização?

Atualmente, deve aplicar uma política, ou seja, deve exigir que para cada subscrição criada a sua conta seja adicionada a uma função de subscrição com o acesso baseado em funções.

### <a name="where-do-i-go-to-create-a-subscription"></a>Onde devo ir para criar uma subscrição?

Para poder criar uma subscrição de oferta do Azure (EA), a conta deve ser adicionada à função de proprietário da conta pelo administrador da inscrição EA no EA Portal do Azure. Em seguida, tem de iniciar sessão no EA Portal do Azure para obter o direito a criar subscrições do tipo de oferta EA. Recomendamos que a primeira subscrição EA seja criada a partir da ligação “+ Adicionar Subscrição” no separador Subscrição do EA Portal.  No entanto, quando a conta tiver o direito, poderá ser mais fácil criar subscrições em portal.azure.com no separador Subscrição no canto superior esquerdo da página, onde pode criar e alterar o nome da subscrição num único passo.

### <a name="who-can-create-a-subscription"></a>Quem pode criar uma subscrição?

Para criar uma subscrição do tipo de oferta Azure Enterprise, deve ter direito à função de proprietário de conta no [EA Portal](https://ea.azure.com).

## <a name="azure-ea-term-glossary"></a>Glossário dos termos do Azure EA

- **Conta**: uma unidade organizacional no portal Azure Enterprise. É utilizada para administrar subscrições e para gerar relatórios.
- **Proprietário da conta**: a pessoa que gere as subscrições e os administradores de serviços no Azure. Podem ver os dados de utilização nessa conta e as subscrições.
- **Subscrição de emenda**: uma subscrição de um ano ou consubstancial ao abrigo da emenda da inscrição.
- **Pré-pagamento**: O Pré-pagamento de um montante monetário anual para os serviços do Azure a uma tarifa de Pré-pagamento com desconto para utilização em relação a este pré-pagamento.
- **Administrador de departamento**: a pessoa que gere departamentos, cria novas contas e proprietários de contas, vê detalhes de utilização dos departamentos que gerem e pode ver os custos quando forem concedidas permissões.
- **Número da inscrição**: um identificador exclusivo fornecido pela Microsoft para identificar a inscrição específica associada a um Contrato Enterprise.
- **Administrador do Enterprise**: a pessoa que gere departamentos, proprietários de departamentos, contas e proprietários de contas no Azure. Têm a capacidade de gerir administradores do Enterprise e de ver os dados de utilização, as quantidades faturadas e os custos não faturados em todas as contas e subscrições associadas à inscrição Enterprise.
- **Contrato Enterprise**: um contrato de licenciamento da Microsoft para clientes com compras centralizadas que querem padronizar toda a organização na tecnologia da Microsoft e manter uma infraestrutura de tecnologias de informação num padrão de software da Microsoft.
- **Inscrição do contrato Enterprise**: uma inscrição no programa de Contrato Enterprise que oferece produtos da Microsoft em volume a tarifas com desconto.
- **Conta Microsoft**: um serviço baseado na Web que permite que sites participantes autentiquem um utilizador com um único conjunto de credenciais.
- **Emenda da Inscrição Enterprise do Microsoft Azure (emenda da inscrição)** : uma emenda assinada por uma empresa, que lhes permite o acesso ao Azure como parte da inscrição Enterprise.
- **Portal Azure Enterprise**: o portal utilizado pelos nossos clientes Enterprise para gerir as contas do Azure e as subscrições relacionadas.
- **Quantidade de recursos consumida**: a quantidade de um serviço do Azure individual que foi utilizada num mês.
- **Administrador de serviços**: a pessoa que acede e gere subscrições e projetos de desenvolvimento no portal Azure Enterprise.
- **Subscrição**: representa uma subscrição do portal Azure Enterprise e é um contentor de serviços do Azure geridos pelo mesmo administrador de serviços.
- **Conta profissional ou escolar**: para as organizações que tenham configurado o Azure Active Directory com federação na cloud e que tenham todas as contas num único inquilino.

### <a name="enrollment-statuses"></a>Estados da inscrição

- **Novo**: este estado é atribuído a uma inscrição que tenha sido criada nas últimas 24 horas e é atualizado para o estado Pendente passadas 24 horas.
- **Pendente**: o administrador da inscrição tem de iniciar sessão no portal Azure Enterprise. Depois de a sessão ter sido iniciada, a inscrição muda para o estado Ativo.
- **Ativo**: a inscrição está Ativa e podem ser criadas contas e subscrições no portal Azure Enterprise. A inscrição permanece ativa até à data limite do Contrato Enterprise.
- **Período alargado indefinidamente**: o estado de período alargado indefinidamente ocorre depois de chegar à data limite do Contrato Enterprise. Permite que os clientes Azure EA que optaram pelo período alargado continuem a utilizar os serviços do Azure indefinidamente no final do Contrato Enterprise.

   Antes de a inscrição Azure EA atingir a data limite do Contrato Enterprise, o administrador da inscrição precisa de decidir qual das seguintes opções deve ser tomada:

  - Renove a inscrição ao adicionar um Pré-pagamento do Azure adicional.
  - Transferir para uma nova inscrição.
  - Migrar para o Programa de Subscrição Online da Microsoft (MOSP).
  - Confirmar a desativação de todos os serviços associados à inscrição.
- **Expirado**: o cliente Azure EA não optou pelo período alargado e a inscrição Azure EA atingiu a data limite do Contrato Enterprise. A inscrição vai expirar e todos os serviços associados vão ser desativados.
- **Transferido**: as inscrições em que todas as contas e serviços associados foram transferidos para uma nova inscrição são apresentadas com o estado transferido.
  >[!NOTE]
  > As inscrições não são transferidas automaticamente se for gerado um novo número de inscrição aquando da renovação. Tem de incluir o número de inscrição anterior na documentação de renovação para facilitar a transferência automática.

## <a name="next-steps"></a>Passos seguintes

- Leia sobre como [as reservas de máquina virtual](ea-portal-vm-reservations.md) podem ajudar a poupar dinheiro.
- Se precisar de ajuda na resolução de problemas com o Azure EA Portal, veja [Resolver problemas de acesso do Azure EA Portal](ea-portal-troubleshoot.md).