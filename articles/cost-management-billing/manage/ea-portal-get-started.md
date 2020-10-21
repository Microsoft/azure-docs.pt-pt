---
title: Introdução ao portal Azure Enterprise
description: Este artigo explica como é que os clientes do Contrato Enterprise do Azure (Azure EA) utilizam o portal Azure Enterprise.
author: bandersmsft
ms.author: banders
ms.date: 08/20/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 617351d5d1bdf9b6528803552e2d34766e9ab217
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132657"
---
# <a name="get-started-with-the-azure-enterprise-portal"></a>Introdução ao portal Azure Enterprise

Este artigo ajuda os clientes diretos e indiretos do Contrato Enterprise do Azure (Azure EA) a começarem a utilizar o [portal Azure Enterprise](https://ea.azure.com). Obtenha informações sobre:

- A estrutura do portal Azure Enterprise.
- Funções utilizadas no portal Azure Enterprise.
- Criação de subscrição.
- Análise de custos no portal Azure Enterprise e no portal do Azure.

Assista ao vídeo para ver uma sessão de inclusão completa do portal Azure Enterprise:

> [!VIDEO https://www.youtube.com/embed/OiZ1GdBpo-I]

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

![Diagrama de hierarquias do Azure EA simples](./media/ea-portal-get-started/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>Funções de utilizador corporativo

As seguintes funções de utilizador administrativo fazem parte da inscrição do Enterprise:

- Administrador do Enterprise
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
- Ver a utilização em todas as contas.
- Ver custos não faturados em todas as contas.

Pode ter vários administradores do Enterprise numa inscrição Enterprise. Pode conceder acesso só de leitura aos administradores do Enterprise. Todos eles herdam a função de administrador de departamento.

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

## <a name="activate-your-enrollment"></a>Ativar a inscrição

Para ativar o serviço, o administrador do Enterprise inicial deve abrir o [portal Azure Enterprise](https://ea.azure.com) e iniciar sessão com o endereço de e-mail indicado no e-mail de convite.

Se tiver sido configurado como administrador do Enterprise, não precisa de esperar pelo e-mail de ativação. Aceda ao [portal Azure Enterprise](https://ea.azure.com) e inicie sessão com o endereço de e-mail da conta profissional, escolar ou Microsoft e a palavra-passe.

Caso tenha mais de uma inscrição, escolha uma para ativar. Por predefinição, só são mostradas as inscrição ativas. Para ver o histórico de inscrições, desmarque a opção **Ativa** no canto superior direito do portal Azure Enterprise.

Em **Inscrição**, o estado indica **Ativa**.

![O exemplo mostra uma inscrição ativa](./media/ea-portal-get-started/ea-enrollment-status.png)

Apenas os administradores do Enterprise do Azure existentes podem criar outros administradores do Enterprise.

### <a name="create-another-enterprise-administrator"></a>Criar outro administrador do Enterprise

Para adicionar um novo administrador do Enterprise:

1. Inicie sessão no [portal Azure Enterprise](https://ea.azure.com).
1. Aceda a **Gerir** > **Detalhes da Inscrição**.
1. Selecione **+ Adicionar Administrador** no canto superior direito.

Garanta que tem os endereços de e-mail do utilizador e o método de autenticação preferencial, como uma conta profissional, escolar ou Microsoft.

Caso não seja o administrador do Enterprise, contacte um administrador do Enterprise para pedir que o adicionem a uma inscrição. Depois de ser adicionado a uma inscrição, recebe um e-mail de ativação.

Se o administrador do Enterprise não puder ajudar, crie um [Pedido de suporte no portal Azure Enterprise](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Forneça as seguintes informações:

- Número de inscrição
- Endereço de e-mail a adicionar e tipo de autenticação (conta profissional, escolar ou Microsoft)
- Aprovação do e-mail de um administrador do Enterprise existente
  - Se o administrador do Enterprise existente não estiver disponível, contacte o seu parceiro ou assistente de software para pedir que alterem os detalhes de contacto através da ferramenta Centro de Serviços de Licenciamento em Volume (VLSC).

Para obter mais informações sobre as funções administrativas do Enterprise, veja [Compreender as funções administrativas do Contrato Enterprise no Azure](understand-ea-roles.md).

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

![Exemplo que mostra a caixa de diálogo Adicionar Administrador de Departamento](./media/ea-portal-get-started/ea-create-add-department-admin.png)

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

![Exemplo que mostra a lista de contas e a opção +Adicionar Conta](./media/ea-portal-get-started/create-ea-add-an-account.png)

Para adicionar outra conta, selecione **Adicionar Outra Conta** ou selecione **Adicionar** no canto inferior direito da barra de ferramentas esquerda.

Para confirmar a propriedade da conta:

1. Inicie sessão no portal Azure Enterprise.
1. Veja o estado.

   O estado deverá mudar de **Pendente** para **Data de Início/Fim**. A Data de Início/Fim é a data em que o utilizador iniciou sessão pela primeira vez e a data de fim do contrato.
1. Quando a mensagem de **Aviso** é apresentada, o proprietário de conta tem de selecionar **Continuar** para ativar a conta ao iniciar sessão pela primeira vez no portal Azure Enterprise.

## <a name="change-account-owner"></a>Alterar o Proprietário da Conta

Os administradores do Enterprise podem utilizar o portal Azure Enterprise para transferir a propriedade da conta da subscrição numa inscrição. A ação move todas as subscrições de uma conta de utilizador de origem para uma conta de utilizador de destino.

Ao transferir contas, tenha em atenção as seguintes informações importantes:

- Pode efetuar estas transferências:
  - De uma conta escolar ou profissional para outra conta escolar ou profissional.
  - De uma conta Microsoft para uma conta escolar ou profissional.
  - De uma conta Microsoft para outra conta Microsoft.

    A conta de destino tem de ser uma Conta comercial do Azure válida, para que seja um destino válido para as transferências. Para contas novas, é-lhe pedido que crie uma Conta comercial do Azure ao iniciar sessão no portal Azure Enterprise. Para contas existentes, tem de criar primeiro uma nova subscrição do Azure para a conta ficar elegível.

- Não pode transferir de uma conta escolar ou profissional para uma conta Microsoft.

- Quando concluir a transferência de uma subscrição, a Microsoft atualiza o proprietário da conta.

Compreenda as seguintes políticas de controlo de acesso baseado em funções do Azure (RBAC do Azure):

- Quando faz transferências de subscrições entre dois IDs organizacionais no mesmo inquilino, as políticas RBAC do Azure e as funções de administrador de serviços e de coadministrador são preservadas.
- As outras transferências de subscrições resultam na perda das políticas RBAC do Azure e das atribuições de função.
- As políticas e as funções de administrador não são transferidas entre diretórios diferentes. Os administradores de serviços são atualizados para o proprietário da conta de destino.

Antes de alterar um proprietário de conta:

1. No portal Azure Enterprise, veja o separador **Conta** e identifique a conta de origem. A conta de origem tem de estar ativa.
1. Identifique a conta de destino e garanta que está ativa.

Para transferir a propriedade da conta para todas as subscrições:

1. Inicie sessão no portal Azure Enterprise.
1. Na área de navegação à esquerda, selecione **Gerir**.
1. Selecione o separador **Conta** e coloque o cursor sobre uma conta.
1. Selecione o ícone para alterar o proprietário da conta à direita. O ícone assemelha-se a uma pessoa.
1. Escolha uma conta elegível e, em seguida, selecione **Seguinte**.
1. Confirme a transferência e selecione **Submeter**.

![Imagem que mostra o símbolo Alterar Proprietário da Conta](./media/ea-portal-get-started/create-ea-create-sub-transfer-account-ownership-of-sub.png)

Para transferir a propriedade da conta para uma subscrição individual:

1. Inicie sessão no portal Azure Enterprise.
1. Na área de navegação à esquerda, selecione **Gerir**.
1. Selecione o separador **Conta** e coloque o cursor sobre uma conta.
1. Selecione o ícone de transferência de subscrições à direita. O ícone assemelha-se a uma página.
1. Escolha uma subscrição elegível e, em seguida, selecione **Seguinte**.
1. Confirme a transferência e, em seguida, selecione **Submeter**.

![Imagem que mostra o símbolo Transferir Subscrições](./media/ea-portal-get-started/ea-transfer-subscriptions.png)

Assista a este vídeo para ver a gestão de utilizadores do portal Azure Enterprise:

> [!VIDEO https://www.youtube.com/embed/621jVkvmwm8]

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

- [Editar os detalhes da subscrição](https://account.azure.com/Subscriptions)
- [Gerir os serviços da subscrição](https://portal.azure.com/#home)

## <a name="transfer-an-enterprise-subscription-to-a-pay-as-you-go-subscription"></a>Transferir uma subscrição Enterprise para uma subscrição Pay As You Go

Para transferir uma subscrição Enterprise para uma subscrição individual com tarifas Pay As You Go, tem de criar um novo pedido de suporte no portal Azure Enterprise. Para criar um pedido de suporte, selecione **+ Novo pedido de suporte** na área **Ajuda e Suporte**.

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

## <a name="view-usage-summary-and-download-reports"></a>Ver o resumo de utilização e os relatórios de transferências

Os administradores de Enterprise podem ver um resumo dos dados de utilização, dos Pré-pagamentos consumidos do Azure e dos custos associados à utilização adicional no portal Azure Enterprise. Os custos são apresentados ao nível do resumo em todas as contas e subscrições.

Para ver a utilização detalhada de contas específicas, transfira o relatório de detalhes de utilização:

1. Inicie sessão no portal Azure Enterprise.
1. Selecione **Relatórios**.
1. Selecione o separador **Transferir Utilização**.
1. Na lista de relatórios, selecione **Transferir** para o relatório mensal que quer obter.

   > [!NOTE]
   > O relatório de detalhes de utilização não inclui os impostos aplicáveis.
   >
   > Pode existir uma latência de até oito horas entre a hora em que a utilização foi incorrida e a hora que está refletida no relatório.

Para ver relatórios e grafos do resumo da utilização:

1. Inicie sessão no portal Azure Enterprise.

1. Selecione um termo de Pré-pagamento.

   Para alterar o intervalo de datas do **Resumo de Utilização**, pode alternar entre **M** (Mensal) e **P** (Personalizado) no canto superior direito da página e, em seguida, introduzir datas de início e de fim personalizadas.

   ![Criar e ver o resumo da utilização e transferir os relatórios na vista personalizada](./media/ea-portal-get-started/create-ea-view-usage-summary-and-download-reports-custom-view.png)
1. Para ver detalhes adicionais, pode selecionar um período ou mês no grafo.

   - O grafo mostra a utilização por mês com uma discriminação da utilização consumida, da sobretaxa de serviços, dos custos cobrados separadamente e dos custos do Azure Marketplace.
   - Para o mês selecionado, pode utilizar os campos abaixo do grafo para filtrar por departamentos, contas e subscrições.
   - Pode alternar entre **Custo por Serviços** e **Custo por Hierarquia**.
   - Veja detalhes do **Serviço Azure**, dos **Custos Cobrados Separadamente** e do **Azure Marketplace** ao expandir as secções relevantes.

Assista a este vídeo para perceber como ver a utilização:

> [!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>Transferir relatórios CSV

Os administradores do Enterprise utilizam a página Transferência de Relatórios Mensais para transferir os seguintes relatórios como ficheiros CSV:

- Saldo e custo
- Detalhes de utilização
- Custos do Azure Marketplace
- Folha de preços

Para transferir os relatórios:

1. No portal Azure Enterprise, selecione **Relatórios**.
2. Selecione **Transferir Utilização** na parte superior da página.
3. Selecione **Transferir** ao lado do relatório do mês desejado.

   > [!NOTE]
   > Poderá existir uma latência de cerca de cinco dias entre a data de utilização incorrida e a data em que a utilização é mostrada nos relatórios.
   >
   > Os utilizadores que transferirem os ficheiros CSV para o Excel com o Safari poderão deparar-se com erros de formatação. Para evitar esses erros, abra o ficheiro com um editor de texto.

![Exemplo que mostra página Transferir Utilização](./media/ea-portal-get-started/create-ea-download-csv-reports.png)

Assista a este vídeo para ver como transferir as informações de utilização:

> [!VIDEO https://www.youtube.com/embed/eY797htT1qg]

### <a name="advanced-report-download"></a>Transferência de relatórios avançados

Pode utilizar a transferência de relatórios avançados para obter relatórios que cubram contas ou intervalos de datas específicos. O ficheiro de saída tem o formato CSV de forma a suportar grandes conjunto de registos.

1. No portal Azure Enterprise, selecione **Transferência de Relatório Avançado**.
1. Selecione um intervalo de data adequado e as contas adequadas.
1. Selecione **Pedir Dados de Utilização**.
1. Selecione o botão **Atualizar** até que o estado do relatório atualize para **Transferir**.
1. Transfira o relatório.

### <a name="download-usage-reports-and-billing-information-for-a-prior-enrollment"></a>Transferir relatórios de utilização e informações de faturação de uma inscrição anterior

Pode transferir relatórios de utilização e informações de faturação de uma inscrição anterior após ter ocorrido uma transferência de inscrição. Os relatórios históricos ficam disponíveis no portal Azure Enterprise e na gestão de custos.

O portal Azure Enterprise filtra as inscrições inativas de forma a não serem apresentadas na vista. Precisa de desmarcar a caixa **Ativa** para ver as inscrições transferidas inativas.  

![A desmarcação da caixa Ativa permite ao utilizador ver as inscrições inativas](./media/ea-portal-get-started/unchecked-active-box.png)

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
- **Conta profissional ou escolar**: para as organizações que tenham configurado o active directory com federação na cloud e que tenham todas as contas num único inquilino.

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

## <a name="get-started-on-azure-ea---faq"></a>Introdução ao Azure EA – FAQ

Esta secção apresenta os detalhes sobre as perguntas normalmente colocadas pelos clientes durante o processo de inclusão.  

### <a name="can-i-associate-my-existing-azure-account-to-azure-ea-enrollment"></a>Posso associar a minha conta do Azure existente à inscrição Azure EA?

Sim. Todas as subscrições do Azure das quais o cliente é o proprietário de conta são convertidas para o Contrato Enterprise. Estão incluídas as subscrições que utilizam crédito mensal, como Visual Studio, AzurePass, MPN, BizSpark, entre outras. Ao converter tais subscrições, perde o crédito mensal.

### <a name="i-accidentally-associated-my-existing-azure-account-with-azure-ea-enrollment-as-a-result-i-lost-my-monthly-credit-can-i-get-my-monthly-credit-back"></a>Associei acidentalmente a minha conta do Azure existente à inscrição Azure EA. Como resultado, perdi o meu crédito mensal. Posso ter o meu crédito mensal de volta?

Se tiver iniciado sessão como um proprietário de conta Azure EA com as mesmas credenciais da subscrição do Visual Studio, poderá recuperar o benefício individual do Azure da subscrição do Visual Studio ao executar uma das seguintes ações:

- Elimine o proprietário da conta do portal Azure Enterprise, depois de remover ou mover quaisquer subscrições do Azure associadas. Em seguida, adira novamente aos benefícios individuais do Azure do Visual Studio.
- Elimine o subscritor do Visual Studio do site de administração no VLSC e reatribua a subscrição, desta vez, a uma conta com credenciais diferentes. Em seguida, adira novamente aos benefícios individuais do Azure do Visual Studio.

### <a name="what-type-of-subscription-should-i-create"></a>Que tipo de subscrição devo criar?

O portal Azure Enterprise oferece dois tipos de subscrições para clientes Enterprise:

- Microsoft Azure Enterprise, que é ideal para:
  - Todas as utilizações de produção
  - Os melhores preços com base no gasto da infraestrutura

  Para obter mais informações, [contacte as vendas do Azure](https://azure.microsoft.com/pricing/enterprise-agreement/).

- Enterprise Dev/Test, que é ideal para:
  - Todas as cargas de trabalho de programador/teste da equipa
  - Cargas de trabalho de programador/teste individuais médias a pesadas
  - Acesso a imagens especiais do MSDN e taxas de serviço preferenciais

  Para obter mais informações, veja a [oferta Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/).

### <a name="is-it-possible-to-transfer-subscription-ownership-to-another-account"></a>É possível transferir a propriedade da subscrição para outra conta?

Sim, pode transferir a propriedade da subscrição para uma conta diferente. Por exemplo, se a Conta A tiver três subscrições, o administrador Enterprise pode transferir uma subscrição para a Conta B, uma para a Conta C e outra para a Conta D. Ou pode transferir todas as subscrições para a Conta E.

Para transferir subscrições:

1. No portal Azure Enterprise, selecione **Gerir** > **Conta**.
1. Coloque o cursor por cima de **Conta** na extremidade direita para ver as opções **Transferir Propriedade**  (ícone de uma pessoa) e **Transferir Subscrição** (ícone da lista). Estas opções só são visíveis para contas ativas.

### <a name="my-subscription-name-is-the-same-as-the-offer-name-should-i-change-the-subscription-name-to-something-meaningful-to-my-organization"></a>O nome da minha subscrição é o mesmo que o nome da oferta. Devo alterar o nome da subscrição para algo significativo para a minha organização?

Quando cria uma subscrição, o nome é predefinido para o tipo de oferta que escolher. Recomendamos que altere o nome da subscrição para algo que facilite o controlo da subscrição.

Para alterar o nome:

1. Inicie sessão em [https://account.windowsazure.com](https://account.windowsazure.com).
1. Selecione a lista de subscrições.
1. Selecione a subscrição que quer editar.
1. Selecione o ícone **Gerir Subscrição**.
1. Edite os detalhes da subscrição.

### <a name="how-can-i-track-costs-incurred-by-a-cost-center"></a>Como posso controlar os custos incorridos por um centro de custos?

Para controlar o custo pelo centro de custos, precisa de definir o centro de custos para um dos seguintes níveis:

- Departamento
- Conta
- Subscrição

Consoante a necessidade, pode utilizar o mesmo centro de custos para controlar a utilização e os custos associados a um centro de custos em particular.

Por exemplo, para controlar os custos de um projeto especial em que estão envolvidos vários departamentos, talvez deva utilizar o centro de custo ao nível da subscrição para controlar a utilização e os custos.

Não pode definir um centro de custos ao nível do serviço. Se quiser controlar a utilização ao nível de serviço, poderá utilizar a funcionalidade _Etiqueta_ disponível ao nível do serviço.

### <a name="how-do-i-track-usage-and-spend-by-different-departments-in-my-organization"></a>Como posso controlar a utilização e o gasto por diferentes departamentos na minha organização?

Pode criar a quantidade de departamentos que quiser na inscrição Azure EA. Para controlar a utilização de forma correta, precisa de garantir que não está a partilhar as subscrições entre os departamentos.

Depois de ter criado departamentos e subscrições, pode ver os dados no relatório de utilização. Estas informações podem ajudar a controlar a utilização e a gerir o custo e os gastos ao nível do departamento.

Também pode aceder aos dados de utilização através da API de relatórios. Para obter informações detalhadas e código de exemplo, veja as [APIs REST Empresariais do Azure](./ea-portal-rest-apis.md).

### <a name="can-i-set-a-spending-quota-and-get-alerts-as-i-approach-my-limit"></a>Posso definir uma quota de gastos e obter alertas à medida que me aproximo do meu limite?

Pode definir uma quota de gastos ao nível do departamento. O sistema vai notificá-lo automaticamente à medida que os limites de gastos atinjam os 50%, 75%, 90% e 100% da quota definida por si.

Para definir a quota de gastos, selecione um departamento e, em seguida, selecione o ícone para editar. Depois de editar os detalhes do limite de gastos, selecione **Guardar**.

### <a name="i-used-resource-groups-to-implement-azure-rbac-and-track-usage-how-can-i-view-the-associated-usage-details"></a>Utilizei grupos de recursos para implementar o RBAC do Azure e controlar a utilização. Como posso ver os detalhes de utilização associados?

Se utilizar _grupos de recursos_ e _etiquetas_, estas informações são controladas ao nível do serviço e pode aceder às mesmas no ficheiro de transferência de utilização detalhada (CSV). Veja o [relatório de utilização de transferências](https://ea.azure.com/report/downloadusage) no portal Azure Enterprise.

Também pode aceder à utilização através da API. Para obter informações detalhadas e código de exemplo, veja as [APIs REST Empresariais do Azure](./ea-portal-rest-apis.md).

> [!NOTE]
> Só pode aplicar etiquetas em recursos que suportem operações do Azure Resource Manager. Caso tenha criado uma máquina virtual, uma rede virtual ou um armazenamento através do modelo de implementação clássica (por exemplo, através do portal clássico), não poderá aplicar etiquetas a esse recurso. Deve implementar esses recursos novamente através do Resource Manager para suportar as etiquetas. Todos os outros recursos suportam etiquetas.

### <a name="can-i-perform-analyses-using-power-bi"></a>Posso executar análises com o Power BI?

Sim. Com o pacote de conteúdos do Microsoft Azure Enterprise para o Power BI, pode:

- Importar e analisar rapidamente o consumo do Azure para a inscrição Enterprise.
- Saber qual o departamento, a conta ou a subscrição que consumiu mais utilização.
- Saber qual o serviço mais utilizado pela organização.
- Monitorizar as tendências de utilização e gastos.

Para utilizar o Power BI:

1. Aceda ao site do Power BI.
1. Inicie sessão com uma conta escolar ou profissional válida.

   A conta escolar ou profissional pode ser a mesma ou diferente da que é utilizada para aceder à inscrição através do portal Azure Enterprise.
1. No dashboard de serviços, escolha o mosaico Microsoft Azure Enterprise e selecione **Ligar**.
1. No ecrã **Ligar ao Azure Enterprise**, introduza:
    - URL do Ambiente do Azure: [https://ea.azure.com](https://ea.azure.com)
    - Número de Meses: entre 1 e 36
    - Número da Inscrição: o seu número de inscrição
1. Selecione **Seguinte**.
1. Introduza a Chave de API na caixa da **Chave de Autenticação**.

   Encontra a chave de API no portal Azure Enterprise. Procure no separador **Transferir Utilização** e, em seguida, selecione **Chave de Acesso da API**. Copie e cole a chave na caixa **Chave da Conta** no Power BI.

Dependendo do tamanho do conjunto de dados, o carregamento dos dados no Power BI pode demorar entre cinco a 30 minutos.

Os relatórios do Power BI estão disponíveis para clientes Azure EA diretos, parceiros e indiretos que possam ver as informações de faturação.

## <a name="next-steps"></a>Passos seguintes

- Os administradores do portal Azure Enterprise devem ler o artigo [Azure Enterprise portal administration](ea-portal-administration.md) (Administração do portal Azure Enterprise) para saber mais sobre as tarefas administrativas comuns.
- Se precisar de ajuda na resolução de problemas com o portal Azure Enterprise, veja [Troubleshoot Azure Enterprise portal access](ea-portal-troubleshoot.md) (Resolver problemas de acesso do portal Azure Enterprise).
- Para obter um guia de inclusão do Azure EA, veja o [Azure EA Onboarding Guide](https://ea.azure.com/api/v3Help/v2AzureEAOnboardingGuide) (Guia de Inclusão do Azure EA).