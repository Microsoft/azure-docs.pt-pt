---
title: Introdução ao Azure EA Portal
description: Este artigo explica como é que os clientes Azure EA utilizam o Azure EA Portal.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/03/2020
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: ace3c251d979a67666d2aaf01dca01e257bed66b
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75992234"
---
# <a name="get-started-with-the-azure-ea-portal"></a>Introdução ao Azure EA Portal

Este artigo ajuda os clientes diretos e indiretos do Azure EA a começarem a utilizar o [Azure EA Portal](https://ea.azure.com) com informações básicas sobre:

- Como está estruturado o Azure EA Portal.
- Funções utilizadas no Azure EA Portal.
- Como começar a criar subscrições.
- Analisar custos no Azure EA Portal e no portal do Azure.

Veja a seguir um vídeo que mostra uma sessão completa de inclusão do Azure EA Portal:

[Vídeo de inclusão do Azure EA Portal ](https://www.youtube.com/watch?v=OiZ1GdBpo-I)

>[!VIDEO https://www.youtube.com/embed/OiZ1GdBpo-I]

## <a name="azure-ea-portal-hierarchy"></a>Hierarquia do Azure EA Portal

Hierarquia do Azure EA Portal:

**Microsoft Azure EA Portal** – o Azure EA Portal é um portal de gestão online que ajuda o utilizador a gerir os custos relativos aos serviços do Azure EA. Utiliza o portal para criar uma hierarquia do Azure EA, incluindo departamentos, contas e subscrições. Também pode utilizá-lo para reconciliar os preços dos serviços consumidos, transferir relatórios de utilização e ver listas de preços. Além disso, pode criar chaves de API para a inscrição.

**Departamentos** – o utilizador cria departamentos para ajudar a segmentar os custos em agrupamentos lógicos e, em seguida, estabelece um orçamento ou uma quota no nível do departamento.

**Contas** – as contas são unidades organizacionais no Azure EA Portal e servem para gerir subscrições. As contas também servem para gerar relatórios.

**Subscrições** – as subscrições são a unidade mais pequena no Azure EA Portal. São contentores dos serviços do Azure geridos pelo administrador de serviços.

O diagrama a seguir ilustra hierarquias do Azure EA simples.

![Diagrama de hierarquias do Azure EA simples](./media/ea-portal-get-started/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>Funções de utilizador corporativo

Para administrar os serviços do Azure em seu registro, há cinco funções distintas de usuário administrativo corporativo:

- Administrador do Enterprise
- Administrador de Departamento
- Proprietário de Conta
- Administrador de serviço
- Contato de notificação

As funções servem para concluir tarefas em dois portais do Microsoft Azure diferentes. O Azure EA Portal (https://ea.azure.com) serve para ajudar o utilizador a gerir a faturação e os custos. O portal do Azure (https://portal.azure.com) serve para gerir os serviços do Azure.

As funções de utilizador estão associadas a uma conta de utilizador. Para validar a autenticidade do utilizador, cada um deve ter uma conta Profissional, Escolar ou Microsoft válida. Garanta que cada conta está associada a um endereço de e-mail que seja monitorizado ativamente. As notificações da conta são enviadas para o endereço de e-mail.

Ao configurar utilizadores, pode atribuir várias contas Profissionais, Escolares ou Microsoft à função de Administrador do Enterprise. Contudo, só pode atribuir uma conta Profissional, Escolar ou Microsoft à função de Proprietário da Conta. Além disso, pode aplicar as funções de Administrador do Enterprise e de Proprietário da Conta a uma única conta Profissional, Escolar ou Microsoft.

### <a name="enterprise-administrator"></a>Administrador do Enterprise

A função de administrador do Enterprise tem o nível de acesso mais alto. Os utilizadores com a função podem:

- Gerir as contas e os proprietários das contas
- Gerir outros administradores do Enterprise
- Gerir os administradores de departamento
- Gerir os contactos de notificação
- Ver a utilização em todas as contas
- Ver custos não faturados em todas as contas

Pode ter vários administradores do Enterprise numa inscrição Enterprise. Pode conceder acesso só de leitura aos administradores do Enterprise. Todos eles herdam a função de administrador de departamento.

### <a name="department-administrator"></a>Administrador de Departamento

Os utilizadores com a função podem:

- Criar e gerir departamentos
- Criar novos proprietários de contas
- Ver os detalhes de utilização dos departamentos que gerem
- Ver os custos, se tiverem as permissões necessárias

Pode ter vários administradores de departamento para cada inscrição Enterprise.

Pode conceder aos administradores de departamento acesso só de leitura. Para conceder acesso só de leitura, edite ou crie um novo administrador de departamento e defina a opção só de leitura como **Sim**.

### <a name="account-owner"></a>Proprietário de Conta

Os utilizadores com a função podem:

- Criar e gerir subscrições
- Gerir os administradores de serviços
- Ver a utilização das subscrições

Cada conta requer uma Conta Profissional, Escolar ou Microsoft exclusiva. Para obter mais informações sobre as funções administrativas do Azure EA Portal, veja [Compreender as funções administrativas do Contrato Enterprise no Azure](understand-ea-roles.md).

### <a name="service-administrator"></a>Administrador de serviço

O administrador de serviços tem permissões para gerir serviços no portal do Azure e atribuir utilizadores à função de coadministrador.

### <a name="notification-contact"></a>Contato de notificação

O contato de notificação recebe notificações de uso relacionadas ao registro.

## <a name="activate-your-enrollment"></a>Ativar a inscrição

Para ativar o serviço, o administrador do Enterprise inicial deve abrir o Azure EA Portal em [https://ea.azure.com](https://ea.azure.com) e iniciar sessão com o endereço de e-mail indicado no e-mail de convite.

Se você tiver configurado como o administrador do EA, não precisará receber o email de ativação para fazer logon no portal de EA do Azure. Você pode continuar a [https://ea.azure.com](https://ea.azure.com) e fazer logon com seu endereço de email (trabalho, escola ou Live ID) e a senha.

Caso tenha mais de uma inscrição, escolha uma para ativar. Por predefinição, só são mostradas as inscrição ativas. Para ver o histórico de inscrições, desmarque a opção **Ativa** no canto superior direito do Azure EA Portal.

Em Inscrição, o estado indica **Ativa**.

![O exemplo mostra uma inscrição ativa](./media/ea-portal-get-started/ea-enrollment-status.png)

Apenas os administradores do Enterprise do Azure existentes podem criar outros administradores do Enterprise.

### <a name="create-another-enterprise-admin"></a>Criar outro administrador do Enterprise

- Inicie sessão no [Azure EA Portal](https://ea.azure.com), navegue para **Gerir** > **Detalhes de Inscrição** e clique em **+ Adicionar Administrador** no canto superior da página.

Garanta que tem os endereços de e-mail do utilizador e o método de autenticação preferencial, como a autenticação Profissional ou Escolar ou conta Microsoft. Precisa destas informações para adicionar um utilizador.

Caso não seja o administrador EA, contacte um administrador EA para pedir que o adicionem a uma inscrição. Depois de ser adicionado a uma inscrição, recebe um e-mail de ativação.

Se o administrador EA não puder ajudar, crie um [Pedido de suporte no Azure EA Portal](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Forneça as seguintes informações:

- Número de inscrição
- Endereço de e-mail a adicionar e tipo de autenticação (conta Profissional, Escolar ou Microsoft)
- Aprovação do e-mail de um administrador EA existente
  - Se o administrador EA existente não estiver disponível, contacte o seu parceiro ou assistente de software para pedir que alterem os detalhes de contacto através da ferramenta VLSC.

Para obter mais informações sobre as funções administrativas do Enterprise, veja [Compreender as funções administrativas do Contrato Enterprise no Azure](understand-ea-roles.md).

## <a name="create-an-azure-ea-department"></a>Criar um departamento do Azure EA

Os administradores do Enterprise e os administradores de departamento utilizam departamentos para organizar e criar relatórios de serviços e de utilização do Azure empresarial por departamento e centro de custos. O administrador do Enterprise pode:

- Adicionar ou remover departamentos
- Associar uma conta a um departamento
- Criar administradores de departamento
- Permitir que os administradores de departamento visualizem os preços e os custos

Um administrador de departamento pode adicionar contas novas aos departamentos. Pode remover contas dos departamentos, mas não da inscrição.

Para adicionar um departamento:

1. Na área de navegação esquerda, clique em **Gerir**.
2. Clique no separador **Departamento**, clique em **+ Adicionar Departamento** e, em seguida, introduza as informações necessárias.
3. O nome do Departamento é o único campo obrigatório. Tem de ter pelo menos três carateres.
4. Quando terminar, clique em **Adicionar**.

## <a name="add-a-department-admin"></a>Adicionar um administrador de departamento

Após criar um departamento, o administrador do Enterprise pode adicionar administradores de departamento e associar cada um deles a um departamento. O administrador de departamento pode:

- Criar outros administradores de departamento
- Ver e editar as propriedades do departamento, como o nome ou o centro de custo
- Adicionar uma conta para os departamentos
- Remover contas dos departamentos
- Transferir os detalhes de utilização relativos aos departamentos
- Ver a utilização mensal e os custos relativos ao departamento, caso um administrador do Enterprise lhe tenha concedido permissão <sup>1</sup>

### <a name="to-add-a-department-admin"></a>Para adicionar um administrador de departamento

Como administrador do Enterprise:

1. Na área de navegação esquerda, clique em **Gerir**.
2. Clique no separador **Departamento** e, em seguida, clique no departamento.
3. Clique em **+ Adicionar Administrador** e adicione as informações necessárias.
4. Para acesso só de leitura, defina a opção **Só de Leitura** como **Sim** e, em seguida, clique em **Adicionar**.

![Exemplo que mostra a caixa de diálogo Adicionar Administrador de Departamento](./media/ea-portal-get-started/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>Para configurar o acesso só de leitura

Pode conceder acesso só de leitura aos administradores de departamento. Ao criar um novo administrador de departamento:

- Defina a opção só de leitura como **Sim**.

Para editar um administrador de departamento existente:

1. Selecione um departamento e, em seguida, clique no símbolo do lápis junto ao **Administrador de Departamento** que quer editar.
2. Defina o espaço só de leitura como **Sim**. Em seguida, clique em **Guardar**.

Os utilizadores com a função de administrador do Enterprise obtêm automaticamente permissões de administrador de departamento.

<sup>1</sup> Caso tenha recebido permissão para ver a utilização e os custos mensais do departamento mas não os conseguir ver, contacte o seu parceiro.

## <a name="add-an-account"></a>Adicionar uma conta

A estrutura da conta e da subscrição afeta a forma como elas são administradas e como são apresentadas nas faturas e nos relatórios. Exemplos de organização típica: estruturação por divisões de negócio, equipas funcionais e localizações geográficas.

Para adicionar uma conta:

1. No Azure EA Portal, clique em **Gerir** na área de navegação esquerda.
2. Clique no separador **Conta** e, em seguida, na página **Conta**, clique em **+Adicionar Conta**.
3. Selecione um departamento ou deixe como não atribuído e, em seguida, selecione o tipo de autenticação pretendido.
4. Escreva um nome amigável a ser utilizado para identificar a conta nos relatórios.
5. Escreva o **E-mail do Proprietário da Conta** para associar à nova conta.
6. Confirme o endereço de e-mail e, em seguida, clique em **Adicionar**.

![Exemplo que mostra a lista de contas e a opção +Adicionar Conta](./media/ea-portal-get-started/create-ea-add-an-account.png)

Pode adicionar outra conta ao clicar em **Adicionar Outra Conta**. Pode também clicar em **Adicionar** no canto inferior direito da barra de ferramentas esquerda.

Para confirmar a propriedade da conta:

1. Inicie sessão no Azure EA Portal.
1. Confirme a propriedade da conta através da verificação do estado. O estado deverá mudar de **Pendente** para **Data de Início/Fim**. A Data de Início/Fim é a data em que o utilizador iniciou sessão pela primeira vez e a data de fim do contrato.
1. Quando a mensagem de “Aviso” é apresentada, um proprietário de conta tem de clicar em **Continuar** para ativar a conta ao iniciar sessão pela primeira vez no Azure EA Portal.


## <a name="change-account-owner"></a>Alterar o proprietário da conta

Os administradores do Enterprise podem utilizar o Azure EA Portal para transferir a propriedade da conta da subscrição numa inscrição. A ação move todas as subscrições de uma conta de utilizador de origem para uma conta de utilizador de destino.

Pontos importantes sobre a transferência de informações da conta de utilizador:

- São suportadas transferências de uma conta Profissional ou Escolar para outra conta Profissional ou Escolar.
- São suportadas transferências de uma conta Microsoft para uma conta Profissional ou Escolar.
- Não são suportadas transferências de uma conta Profissional ou Escolar para uma conta Microsoft.
- São suportadas transferências de uma conta Microsoft para outra conta Microsoft. A conta de destino tem de ser uma Conta comercial do Azure válida, para que seja um destino válido para as transferências. Para contas novas, é-lhe pedido que crie uma Conta comercial do Azure ao iniciar sessão no Azure EA Portal. Para contas existentes, tem de criar primeiro uma nova subscrição do Azure para a conta ficar elegível.
- Quando concluir a transferência de uma subscrição, a Microsoft atualiza o proprietário da conta.

Políticas de controlo de acesso baseado em funções:

- Apenas as transferências de subscrições do Azure entre dois IDs organizacionais no mesmo inquilino preservam as políticas existentes de controlo de acesso baseado em funções (RBAC) do Azure, as atribuições de função de administrador de serviços e as atribuições de função de coadministrador. As outras transferências de subscrições resultam na perda das políticas RBAC e das atribuições de função de administrador de serviços e de coadministrador. As políticas e as funções de administrador não são transferidas entre diretórios diferentes. Os administradores de serviços são atualizados para o proprietário da conta de destino.
- Quando efetua transferências de subscrições entre dois IDs organizacionais no mesmo inquilino, as políticas RBAC e as funções de administrador de serviços e de coadministrador são preservadas.

Antes de alterar um proprietário de conta:

1. Veja o separador **Conta** e identifique a conta de origem. A conta de origem tem de estar ativa.
2. Identifique a conta de destino. Tem de estar ativa.

Para transferir a propriedade da conta para todas as subscrições:

1. Na área de navegação esquerda, clique em **Gerir**.
2. Clique no separador **Conta** e coloque o cursor sobre uma conta.
3. Clique no símbolo para alterar o proprietário da conta à direita. O símbolo assemelha-se a uma pessoa.
4. Selecione uma conta elegível e clique em **Seguinte**.
5. Confirme a transferência e clique em **Submeter**.

![Imagem que mostra o símbolo Alterar Proprietário da Conta](./media/ea-portal-get-started/create-ea-create-sub-transfer-account-ownership-of-sub.png)

Para transferir a propriedade da conta para uma subscrição individual:

1. Na área de navegação esquerda, clique em **Gerir**.
2. Clique no separador **Conta** e coloque o cursor sobre uma conta.
3. Clique no símbolo de transferência de subscrições à direita. O símbolo assemelha-se a uma página.
4. Selecione uma subscrição elegível e clique em **Seguinte**.
5. Confirme a transferência e clique em **Submeter**.

![Imagem que mostra o símbolo Transferir Subscrições](./media/ea-portal-get-started/ea-transfer-subscriptions.png)

Veja a seguir um vídeo que mostra a gestão de utilizadores do Azure EA Portal:

[Vídeo de gestão de utilizadores do Azure EA Portal](https://www.youtube.com/watch?v=621jVkvmwm8)

>[!VIDEO https://www.youtube.com/embed/621jVkvmwm8]

## <a name="create-a-subscription"></a>Criar uma subscrição

Os proprietários da conta podem ver e gerir subscrições. Pode utilizar subscrições para conceder às equipas da sua organização acesso a ambientes e projetos de desenvolvimento. Por exemplo, teste, produção, desenvolvimento e ensaios. Ao criar subscrições diferentes para cada ambiente de aplicação, está a ajudar a proteger cada ambiente. Também pode atribuir uma conta de administrador de serviços diferente para cada subscrição. Pode associar subscrições a quantos serviços quiser. O proprietário da conta cria subscrições e atribui uma conta de administrador de serviços a cada subscrição na conta.

### <a name="add-a-subscription"></a>Adicionar uma subscrição

Utilize as seguintes informações para adicionar uma subscrição.

Da primeira vez que adicionar uma subscrição à conta, ser-lhe-á pedido que aceite o Contrato de Subscrição Online da Microsoft (MOSA) e um plano de tarifas. Apesar de não serem aplicáveis a clientes do Contrato Enterprise, são necessários para criar a subscrição. A Emenda da Inscrição do Contrato Microsoft Azure Enterprise prevalece sobre os itens acima e a sua relação contratual não é alterada. Quando lhe for pedido, selecione a caixa para indicar que aceita os termos.

Todas as novas subscrições são criadas com o nome de subscrição predefinido _Microsoft Azure Enterprise_. Pode atualizar o nome da subscrição de forma a diferenciá-la de outras subscrições na sua inscrição. E, também para garantir que é reconhecível nos relatórios ao nível da empresa.

Para adicionar uma subscrição:

1. No Azure EA Portal, inicie sessão na conta.
2. Clique no separador **Administrador** e, em seguida, clique em **Subscrição** na parte superior da página.
2. Verifique se tem a sessão iniciada como proprietário da conta.
3. Clique em **+Adicionar Subscrição** e, em seguida, clique em **Comprar**.
  Da primeira vez que adicionar uma subscrição a uma conta, tem de fornecer as suas informações de contacto. Ao adicionar subscrições adicionais, as suas informações de contacto são adicionadas automaticamente.
4. Clique em **Subscrições**, selecione a subscrição que criou e, em seguida, clique em **Editar Detalhes da Subscrição**.
5. Atualize o **Nome da Subscrição** e o **Administrador de Serviços** e, em seguida, selecione a marca de verificação.
  O nome da subscrição aparece nos relatórios e é o nome do projeto associado à subscrição no portal de desenvolvimento.

As novas subscrições podem demorar até 24 horas até surgirem na lista de subscrições. Depois de ter criado uma subscrição, pode:

- [Editar os detalhes da subscrição](https://account.azure.com/Subscriptions)
- [Gerir os serviços da subscrição](https://portal.azure.com/#home)

## <a name="transfer-ea-subscription-to-pay-as-you-go-subscription"></a>Transferir a subscrição EA para a subscrição pay as you go

Para transferir uma subscrição EA para uma subscrição individual com tarifas pay as you go, tem de criar um novo pedido de suporte no Azure EA Portal. Para criar um pedido de suporte, clique em **+ Novo pedido de suporte** na área Ajuda e Suporte.

## <a name="associate-an-existing-account-with-your-pay-as-you-go-subscription"></a>Associar uma conta existente à subscrição pay as you go

Caso já possua uma conta existente do Microsoft Azure no portal do Microsoft Azure, introduza a conta Microsoft ou a conta escolar ou profissional associada, de forma a associá-la à inscrição do Contrato Enterprise.

### <a name="associate-an-existing-account"></a>Associar uma conta existente

1. No Enterprise Portal, clique em **Gerir**.
1. Clique no separador **Conta**.
1. Clique em **+Adicionar uma conta**.
1. Introduza a conta Microsoft ou a conta escolar ou profissional associada à conta existente.
1. Confirme a conta Microsoft ou a conta escolar ou profissional associada à conta existente.
1. Forneça o nome que gostaria de utilizar para identificar esta conta no relatório.
1. Clique em **Adicionar**.
1. Pode adicionar uma conta adicional ao selecionar a opção **+Adicionar uma Conta** novamente ou regressar à home page ao selecionar o botão **Admin**.
1. Se clicar para ver a página **Conta**, a conta recentemente adicionada será apresentada com um estado **Pendente**.

### <a name="confirm-account-ownership"></a>Confirmar a propriedade da conta

1. Inicie sessão na conta de e-mail associada à conta Microsoft ou à conta escolar ou profissional que forneceu.
1. Abra a notificação de e-mail com o título _“Convite para Ativar a Conta no Serviço do Microsoft Azure do Licenciamento em Volume da Microsoft”_ .
1. Clique na ligação **Iniciar sessão no Microsoft Azure Enterprise Portal** presente no convite.
1. Clique em **Iniciar sessão**.
1. Introduza a conta Microsoft ou a conta escolar ou profissional e a palavra-passe para iniciar sessão e confirmar a propriedade da conta.

### <a name="azure-marketplace"></a>Azure Marketplace

Embora a maioria das subscrições seja convertida do ambiente Pay as You Go para o Azure Enterprise, o mesmo não acontece com os serviços do Azure Marketplace. Para ter uma vista única de todas as subscrições e custos, recomendamos que adicione os serviços do Azure Marketplace ao Enterprise Portal:

1. Clique em **Gerir** no painel de navegação à esquerda.
1. Clique no separador **Inscrição**.
1. Veja a secção Detalhes da Inscrição.
1. À direita do campo Azure Marketplace, clique no ícone do lápis para ativar e prima **Guardar**.

Agora, o proprietário da conta pode comprar subscrições do Azure Marketplace anteriormente detidas como pay as you go.

Depois de as novas subscrições do Azure Marketplace terem sido ativadas na inscrição, cancele as subscrições do Marketplace criadas no ambiente pay as you go. Este passo é fundamental para que as subscrições do Marketplace não fiquem num estado inadequado quando o instrumento de pagamento pay as you go expirar.

### <a name="msdn"></a>MSDN

As subscrições MSDN são automaticamente convertidas em MSDN Dev/Test e a oferta EA perderá qualquer crédito monetário existente.

### <a name="azure-in-open"></a>Azure no Licenciamento Open

A associação de uma subscrição Azure no Open a um EA fará com que quaisquer créditos não consumidos do Azure no Licenciamento Open sejam perdidos. Para evitar qualquer perda potencial de créditos, é recomendado que os clientes consumam todos os créditos numa subscrição Azure no Open antes de adicionar a conta ao respetivo EA.  

### <a name="accounts-with-support-subscriptions"></a>Contas com Subscrições de Suporte

Ao adicionar contas existentes ao Enterprise Portal que tenham uma subscrição de suporte (e ainda não tenham uma subscrição de suporte EA), tenha em atenção que a subscrição de suporte MOSA não será transferida automaticamente e será necessário voltar a comprar suporte no EA. Será fornecido um período de tolerância para cobertura de suporte até ao fim do mês subsequente, de forma a dar tempo para voltar a comprar suporte.

## <a name="view-usage-summary-and-download-reports"></a>Ver o resumo de utilização e os relatórios de transferências

Os administradores do Enterprise podem ver um resumo dos dados de utilização, da alocação monetária consumida e dos custos associados a utilização adicional no Azure EA Portal. Os custos são apresentados ao nível do resumo em todas as contas e subscrições.

Para ver a utilização detalhada de contas específicas:

Transfira o relatório Detalhes de Utilização. Clique em **relatórios** e, em seguida, clique na guia **uso de download** . Na lista de relatórios, clique em **baixar** para o relatório mensal que deseja obter.

O relatório não inclui os impostos aplicáveis. Poderá existir uma latência de cerca de oito horas entre a hora em que a utilização foi incorrida e a hora que está refletida no relatório.

Para ver relatórios e grafos do resumo da utilização:

1. No Azure EA Portal, na área de navegação esquerda, clique em **Relatórios** e veja o separador **Resumo de Utilização**.  
  ![Criar e ver o resumo da utilização e transferir os relatórios](./media/ea-portal-get-started/create-ea-view-usage-summary-and-download-reports.png)
2. Selecione um termo de alocação.
3. Alterne entre **M** (Mensal) e **P** (Personalizado) na parte superior direita da página para ver o **Resumo de Utilização** com datas de início e de fim.  
  ![Criar e ver o resumo da utilização e transferir os relatórios na vista personalizada](./media/ea-portal-get-started/create-ea-view-usage-summary-and-download-reports-custom-view.png)
4. Selecione um período ou mês no grafo para ver detalhes adicionais.
5. O grafo mostra a utilização por mês com uma discriminação da utilização consumida, da sobretaxa de serviços, dos custos cobrados separadamente e dos custos do marketplace.
6. Para o mês selecionado, filtre por departamentos, contas e subscrições por baixo do grafo.
7. Alterne entre **Custo por Serviços** e **Custo por Hierarquia**.
8. Expanda e feche as opções **Serviço Azure**, **Custos Cobrados Separadamente** e **Azure Marketplace** para ver detalhes.

veja a seguir um vídeo que demonstra como visualizar a utilização:

[Vídeo de utilização do Azure EA Portal](https://www.youtube.com/watch?v=Cv2IZ9QCn9E)

>[!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>Transferir relatórios CSV

Os administradores do Enterprise utilizam a página Transferência de Relatórios Mensais para transferir vários relatórios como ficheiros CSV. Os relatórios incluem:

- Saldo e Custo
- Detalhes de Utilização
- Encargos do Marketplace
- Folha de Preços

Para transferir os relatórios:


1. No EA Portal, clique em **Relatórios**.
2. Clique em **Transferir Utilização** na parte superior da página.
3. Selecione **Transferir** ao lado do relatório do mês desejado.

Poderá existir uma latência de cerca de cinco dias entre a data de utilização incorrida e a data em que a utilização é mostrada nos relatórios.

Os utilizadores que transferirem os ficheiros CSV para o Excel com o Safari poderão deparar-se com erros de formatação. Para evitar esses erros, abra o ficheiro com um editor de texto.

![Exemplo que mostra página Transferir Utilização](./media/ea-portal-get-started/create-ea-download-csv-reports.png)

Veja a seguir um vídeo que mostra como transferira s informações de utilização:

[Vídeo de utilização do Azure EA Portal](https://www.youtube.com/watch?v=eY797htT1qg)

>[!VIDEO https://www.youtube.com/embed/eY797htT1qg]

### <a name="advanced-report-download"></a>Transferência de relatórios avançados

Para relatórios em intervalos de datas ou contas específicas, pode utilizar a transferência de relatórios avançados. A partir de 30 de agosto de 2016, o formato do ficheiro de saída foi alterado de .xlsx para .csv, de forma a acomodar conjuntos de registos maiores.

1. Selecione **Transferência de Relatórios Avançados**.
1. Selecione o **Intervalo de Datas Adequado**.
1. Selecione as **Contas Adequadas**.
1. Selecione **Pedir Dados de Utilização**.
1. Selecione o botão **Atualizar** até que o estado do relatório seja atualizado para **Transferir**.
1. Transfira o relatório.

## <a name="ea-term-glossary"></a>Glossário dos termos do EA

- **Conta**: uma unidade organizacional no portal de ea do Azure usada para administrar assinaturas e utilizadas para relatórios.
- **Proprietário da conta**: a pessoa identificada para gerenciar assinaturas e administradores de serviço no Microsoft Azure. Podem ver os dados de utilização nessa conta e as subscrições.
- **Assinatura de emenda**: um único ano ou uma assinatura coextensiva sob a emenda de registro.
- **Compromisso**: compromisso de um valor monetário anual para serviços de Microsoft Azure a uma taxa de compromisso com desconto para uso em relação a esse pagamento antecipado.
- **Administrador do departamento**: as pessoas identificadas para gerenciar departamentos, criar novas contas e proprietários de conta, exibir detalhes de uso dos departamentos que gerenciam e exibir custos quando concedidas permissões.
- **Número de registro**: um identificador exclusivo fornecido pela Microsoft para identificar o registro específico associado a um Enterprise Agreement.
- **Administrador corporativo**: as pessoas identificadas para gerenciar departamentos e proprietários de departamento e contas e proprietários de conta no Microsoft Azure. Têm a capacidade de gerir administradores do Enterprise e de ver os dados de utilização, as quantidades faturadas e os custos não faturados em todas as contas e subscrições associadas à inscrição Enterprise.
- **Enterprise Agreement**: um contrato de licenciamento da Microsoft para clientes com compras centralizadas que desejam padronizar toda a sua organização na tecnologia da Microsoft e manter uma infra-estrutura de tecnologia da informação em um padrão de software da Microsoft.
- **Registro do Enterprise Agreement**: um registro no programa Enterprise Agreement que fornece produtos da Microsoft em volume com tarifas com desconto.
- **Conta Microsoft**: um serviço baseado na Web que permite que sites participantes autentiquem um usuário com um único conjunto de credenciais.
- **Microsoft Azure alteração de registro de empresa (emenda de registro)** : uma emenda assinada por uma empresa, que fornece acesso a Microsoft Azure como parte de seu registro corporativo.
- **Portal de ea do Azure**: o portal usado por nossos clientes empresariais para gerenciar suas contas de Microsoft Azure e suas assinaturas relacionadas.
- **Quantidade de recursos consumida**: a quantidade de um serviço de Microsoft Azure individual que foi utilizado em um mês.
- **Administrador de serviços**: a pessoa identificada para acessar e gerenciar assinaturas e projetos de desenvolvimento no portal de ea do Azure.
- **Assinatura**: representa uma assinatura do portal de ea do Azure e é um contêiner de serviços de Microsoft Azure gerenciados pelo mesmo administrador de serviços.
- **Conta corporativa ou de estudante**: para organizações que configuraram o Active Directory com Federação para a nuvem e todas as contas estão em um único locatário.

### <a name="enrollment-statuses"></a>Estados da inscrição:

- **Pendente**: o administrador de registro precisa fazer logon no portal de ea do Azure. Depois de a sessão ter sido iniciada, a inscrição mudará para o estado Ativo.
- **Ativo**: o registro está ativo e as contas e assinaturas podem ser criadas no portal de ea do Azure. A inscrição permanecerá ativa até à data limite do contrato Enterprise.
- **Termo estendido indefinido**: um termo estendido indefinido ocorre depois que a data de término do contrato Enterprise é passada. Permite que os clientes EA que optaram pelo período alargado continuem a utilizar o Azure indefinidamente no final do contrato Enterprise. Antes de a inscrição EA atingir a data limite do contrato Enterprise, o administrador da inscrição deve decidir se vai renovar a inscrição através da adição de alocação monetária adicional, transferir para uma nova inscrição, migrar para o Programa de Subscrição Online da Microsoft (MOSP) ou confirmar a desativação de todos os serviços associados à inscrição.
- **Expirado**: o cliente ea é recusado do termo estendido e o registro ea atingiu a data de término do contrato Enterprise, o registro expirará e todos os serviços associados serão desabilitados.
- **Transferidos**: registros em que todas as contas e serviços associados foram transferidos para um novo registro aparecerão com um status transferido. Tenha em consideração que as inscrições não serão transferidas automaticamente se for gerado um novo número de inscrição na renovação. O número de inscrição anterior tem de ser incluído na documentação de renovação do cliente para facilitar a transferência automática.

## <a name="get-started-on-azure-ea-faq"></a>FAQ sobre a introdução ao Azure EA

Este documento fornece detalhes sobre as perguntas normalmente colocadas pelos clientes durante o processo de inclusão.  

### <a name="can-i-associate-my-existing-azure-account-to-enterprise-enrollment"></a>Posso associar a minha Conta do Azure existente à Inscrição Enterprise?

Sim, pode. É importante referir que todas as subscrições do Azure das quais o cliente é o proprietário de conta serão convertidas para o contrato Enterprise. Tal inclui as subscrições que utilizam crédito mensal (por exemplo, Visual Studio, AzurePass, MPN, BizSpark e etc.), o que significa que, ao fazer isto, perderá o crédito mensal.

### <a name="i-accidentally-associated-my-existing-azure-account-with-enterprise-enrollment-as-a-result-i-lost-my-monthly-credit-is-it-possible-to-get-my-monthly-credit-back"></a>Associei acidentalmente a minha conta do Azure existente à inscrição Enterprise. Como resultado, perdi o meu crédito mensal. É possível ter o meu crédito mensal de volta?

Para recuperar o benefício individual do Azure da subscrição do Visual Studio depois de se autenticar como um proprietário de conta EA, após ter utilizado o mesmo início de sessão para o EA que a subscrição do Visual Studio, deve:
1. Eliminar este proprietário de conta do EA Portal, depois de remover ou mover quaisquer subscrições do Azure que possuam, e fazer com que se inscrevam nos benefícios do Azure do Visual Studio novamente.
 OU
1. Eliminar o subscritor do Visual Studio do site de Administração no VLSC e reatribuir a subscrição, desta vez, com a utilização de um início de sessão diferente. Em seguida, poderão inscrever-se nos benefícios individuais do Azure do Visual Studio novamente.

### <a name="what-type-of-subscription-should-i-create"></a>Que tipo de subscrição devo criar?

O EA Portal oferece dois tipos de subscrições para clientes Enterprise:

- Microsoft Azure Enterprise – ideal para:
  - Todas as utilizações de produção
  - Os melhores preços com base no gasto da infraestrutura
  - Pode encontrar mais informações em https://azure.microsoft.com/pricing/enterprise-agreement/
- Enterprise Dev/Test – ideal para:
  - Todas as cargas de trabalho de programador/teste de equipa
  - Cargas de trabalho de programador/teste individuais médias a pesadas
  - Acesso a imagens especiais do MSDN e taxas de serviço preferenciais
  - Pode encontrar mais informações em https://azure.microsoft.com/offers/ms-azr-0148p/

### <a name="is-it-possible-to-transfer-subscription-ownership-to-another-account"></a>É possível transferir a propriedade da subscrição para outra conta?

Sim, é possível transferir a propriedade da subscrição para uma conta diferente. Por exemplo, se a Conta A tiver três subscrições, o administrador Enterprise poderá transferir uma subscrição para a Conta B, uma para a Conta C e outra para a Conta D ou todas para a Conta E.

Pode aceder ao EA e clicar em Gerir > Conta, colocar o cursor sobre **Conta** (no lado direito) e verá as opções Transferir Propriedade (ícone do alvo) e Transferir Subscrição (ícone da lista).

Esta opção só será visível para contas ativas.

### <a name="i-see-subscription-name-defaults-to-offer-name-should-i-change-the-subscription-name-to-something-meaningful-to-my-organization"></a>Vejo que o nome da subscrição é predefinido para o nome da oferta, devo alterar o nome da subscrição para algo significativo para a minha organização?

Qualquer subscrição criada será predefinida para o tipo da oferta que escolhe. Recomendamos que altere o nome da subscrição para algo que facilite o controlo da subscrição.

**Para alterar o nome:**
1. Inicie sessão em [https://account.windowsazure.com](https://account.windowsazure.com).
1. Clique na Lista de subscrições.
1. Selecione a Subscrição.
1. Clique no ícone **Gerir Subscrição**.
1. Edite os detalhes da subscrição.

### <a name="how-can-i-track-cost-incurred-by-cost-center"></a>Como posso controlar o custo incorrido pelo Centro de Custos?

Para controlar o custo pelo Centro de Custos, precisa de definir o Centro de Custos para qualquer um dos seguintes níveis:
- Departamento
- Conta
- Subscrição

Consoante a necessidade, pode utilizar o mesmo Centro de Custos para controlar a utilização e o custo associado a um Centro de Custos em particular.

Por exemplo, para controlar o custo de um projeto especial em que estão envolvidos vários departamentos, talvez deva utilizar o Centro de Custo ao nível da subscrição para controlar a utilização e o custo.

Não pode definir o Centro de Custos ao nível do Serviço e, caso pretenda controlar a utilização ao nível de serviço, pode utilizar a funcionalidade “Etiqueta” disponível ao nível do serviço.

### <a name="how-do-i-track-usage-and-spend-by-different-departments-in-my-organization"></a>Como posso controlar a utilização e o gasto por diferentes departamentos na minha organização?

Pode criar a quantidade de departamentos que quiser na sua inscrição EA. Para controlar a utilização de forma correta, precisa de garantir que as subscrições não são partilhadas entre os departamentos.

Uma vez concluída a criação do departamento e da subscrição, pode ver as informações a fluir no relatório de utilização que ajudarão a controlar a utilização e a gerir o custo/gasto ao nível do departamento.

Também pode aceder à utilização através da API; informações detalhadas e código de exemplo disponíveis em [https://ea.azure.com/helpdocs/reportingAPI](https://ea.azure.com/helpdocs/reportingAPI).

### <a name="can-i-set-the-spending-quota-and-get-alerts-as-i-approach-my-limit"></a>Posso definir a quota de gastos e obter alertas à medida que me aproximo do meu limite?

Pode definir a quota de gastos ao nível do departamento. O sistema vai notificá-lo automaticamente à medida que os limites de gastos atinjam os 50%, 75%, 90% e 100% da quota definida por si.

Para definir a quota de gastos, clique no departamento ao qual pretende adicionar um limite de gastos e clique no ícone para editar. Clique em **Guardar** para guardar os detalhes.

### <a name="i-used-resource-groups-rgs-to-implement-rbac-and-track-usage-how-can-i-view-the-associated-usage-details"></a>Utilizei Grupos de Recursos (RGs) para implementar o RBAC e controlar a utilização, como posso ver os detalhes de utilização associados?

As informações como “Grupos de Recursos” e “Etiqueta”, se utilizada, são controladas ao nível do serviço. As informações são disponibilizadas no ficheiro de transferência de utilização detalhada (CSV), o qual pode ser transferido no Azure EA Portal [https://ea.azure.com/report/downloadusage](https://ea.azure.com/report/downloadusage).

Também pode aceder à utilização através da API; informações detalhadas e código de exemplo disponíveis em [https://ea.azure.com/helpdocs/reportingAPI](https://ea.azure.com/helpdocs/reportingAPI).

Tenha em consideração que só pode aplicar etiquetas em recursos que suportem operações do Resource Manager. Caso tenha criado uma máquina virtual, uma rede virtual ou um armazenamento através do modelo de implementação clássica (por exemplo, através do portal clássico), não poderá aplicar etiquetas a esse recurso. Deve implementar esses recursos novamente através do Resource Manager para suportar as etiquetas. Todos os outros recursos suportam etiquetas.

### <a name="can-i-perform-analyses-using-power-bi"></a>Posso executar análises com o Power BI?

Sim. Com o pacote de conteúdos do Microsoft Azure Enterprise para o Power BI, pode rapidamente importar e analisar o consumo do Azure para a sua inscrição Enterprise, saber qual o departamento, a conta ou a subscrição que consumiu mais utilização, qual o serviço mais utilizado pela sua organização ou monitorizar as tendências de utilização e gastos.

**Navegue para o site do Power BI:**

 1. Inicie sessão com uma conta escolar ou profissional válida.
    - A conta escolar ou profissional pode ser a mesma ou diferente da que é utilizada para aceder à inscrição através do Azure EA Portal.
 1. No Dashboard dos serviços, escolha:
    - Mosaico Microsoft Azure Enterprise.
    - Clique em **Ligar**.
 1. No ecrã “Ligar ao Azure Enterprise”, escolha:
    - URL do Ambiente do Azure: [https://ea.azure.com](https://ea.azure.com).
    - Número de Meses: escolha entre 1 e 36.
    - Número da Inscrição: introduza o número da inscrição.
    - Clique em **Seguinte**.
 1. Introduza a Chave de API na Caixa da Chave de Autenticação. Pode obter a chave de API no Azure EA Portal no separador “Transferir Utilização” e clicar em **Chave de Acesso da API**.
    - Copie e cole a chave na caixa “Chave da Conta”.
    - O carregamento dos dados no Power BI demora cerca de 5 a 30 minutos, dependendo do tamanho do conjunto de dados.

Os Relatórios do Power BI estão disponíveis para clientes EA diretos, parceiros e indiretos que possam ver as informações de faturação.

## <a name="next-steps"></a>Passos seguintes

- Os administradores do Azure EA Portal devem ler o artigo [Azure EA portal administration](ea-portal-administration.md) (Administração do Azure EA Portal) para saber mais sobre as tarefas administrativas comuns.
- Se precisar de ajuda para resolver problemas com o Azure EA Portal, veja [Troubleshoot Azure EA portal access](ea-portal-troubleshoot.md) (Resolver problemas de acesso do Azure EA Portal).
- Para obter um guia de inclusão do Azure EA, veja o [Azure EA Onboarding Guide](https://ea.azure.com/api/v3Help/v2AzureEAOnboardingGuide) (Guia de Inclusão do Azure EA).
