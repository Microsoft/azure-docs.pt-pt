---
title: Introdução ao Azure EA Portal
description: Este artigo explica como é que os clientes Azure EA utilizam o Azure EA Portal.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: 1882b283f376a1bb8706132263c83e1a24ec0705
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70900935"
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

![Diagrama de hierarquias do Azure EA simples](./media/billing-ea-portal-get-started/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>Funções de utilizador do Enterprise

Para administrar os serviços do Azure na sua inscrição, existem quatro funções distintas de utilizador administrativo do Enterprise:

- Administrador do Enterprise
- Administrador de departamento
- Proprietário da conta
- Administrador de serviço

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

### <a name="department-administrator"></a>Administrador de departamento

Os utilizadores com a função podem:

- Criar e gerir departamentos
- Criar novos proprietários de contas
- Ver os detalhes de utilização dos departamentos que gerem
- Ver os custos, se tiverem as permissões necessárias

Pode ter vários administradores de departamento para cada inscrição Enterprise.

Pode conceder aos administradores de departamento acesso só de leitura. Para conceder acesso só de leitura, edite ou crie um novo administrador de departamento e configure a opção só de leitura como <bpt id="p1">**</bpt>Sim**.

### <a name="account-owner"></a>Proprietário da conta

Os utilizadores com a função podem:

- Criar e gerir subscrições
- Gerir os administradores de serviços
- Ver a utilização das subscrições

Cada conta requer uma Conta Profissional, Escolar ou Microsoft exclusiva. Para obter mais informações sobre as funções administrativas do Azure EA Portal, veja [Compreender as funções administrativas do Contrato Enterprise no Azure](billing-understand-ea-roles.md).

### <a name="service-administrator"></a>Administrador de serviço

O administrador de serviços tem permissões para gerir serviços no portal do Azure e atribuir utilizadores à função de coadministrador.

## <a name="activate-your-enrollment"></a>Ativar a inscrição

Para ativar o serviço, o administrador do Enterprise inicial deve abrir o Azure EA Portal em [https://ea.azure.com](https://ea.azure.com) e iniciar sessão com o endereço de e-mail indicado no e-mail de convite.

Caso tenha mais de uma inscrição, escolha uma para ativar. Por predefinição, só são mostradas as inscrição ativas. Para ver o histórico de inscrições, desmarque a opção **Ativa** no canto superior direito do Azure EA Portal.

Em Inscrição, o estado indica **Ativa**.

![O exemplo mostra uma inscrição ativa](./media/billing-ea-portal-get-started/ea-enrollment-status.png)

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

Para obter mais informações sobre as funções administrativas do Enterprise, veja [Compreender as funções administrativas do Contrato Enterprise no Azure](billing-understand-ea-roles.md).

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

![Exemplo que mostra a caixa de diálogo Adicionar Administrador de Departamento](./media/billing-ea-portal-get-started/ea-create-add-department-admin.png)

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

![Exemplo que mostra a lista de contas e a opção +Adicionar Conta](./media/billing-ea-portal-get-started/create-ea-add-an-account.png)

Pode adicionar outra conta ao clicar em **Adicionar Outra Conta**. Pode também clicar em **Adicionar** no canto inferior direito da barra de ferramentas esquerda.

Para confirmar a propriedade da conta:

1. Inicie sessão no Azure EA Portal.
2. Confirme a propriedade da conta através da verificação do estado. O estado deverá mudar de **Pendente** para **Data de Início/Fim**. A Data de Início/Fim é a data em que o utilizador iniciou sessão pela primeira vez e a data de fim do contrato.


## <a name="change-account-owner"></a>Alterar o proprietário da conta

Os administradores do Enterprise podem utilizar o Azure EA Portal para transferir a propriedade da conta da subscrição numa inscrição. A ação move todas as subscrições de uma conta de utilizador de origem para uma conta de utilizador de destino.

Pontos importantes sobre a transferência de informações da conta de utilizador:

- São suportadas transferências de uma conta Profissional ou Escolar para outra conta Profissional ou Escolar.
- São suportadas transferências de uma conta Microsoft para uma conta Profissional ou Escolar.
- Não são suportadas transferências de uma conta Profissional ou Escolar para uma conta Microsoft.
- São suportadas transferências de uma conta Microsoft para outra conta Microsoft. A conta de destino tem de ser uma Conta comercial do Azure válida, para que seja um destino válido para as transferências. Para contas novas, é-lhe pedido que crie uma Conta comercial do Azure ao iniciar sessão no Azure EA Portal. Para contas existentes, tem de criar primeiro uma nova subscrição do Azure para a conta ficar elegível.
- Quando concluir a transferência de uma subscrição, a Microsoft atualiza o proprietário da conta.

Políticas RBAC:

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

![Imagem que mostra o símbolo Alterar Proprietário da Conta](./media/billing-ea-portal-get-started/create-ea-create-sub-transfer-account-ownership-of-sub.png)

Para transferir a propriedade da conta para uma subscrição individual:

1. Na área de navegação esquerda, clique em **Gerir**.
2. Clique no separador **Conta** e coloque o cursor sobre uma conta.
3. Clique no símbolo de transferência de subscrições à direita. O símbolo assemelha-se a uma página.
4. Selecione uma subscrição elegível e clique em **Seguinte**.
5. Confirme a transferência e clique em **Submeter**.

![Imagem que mostra o símbolo Transferir Subscrições](./media/billing-ea-portal-get-started/ea-transfer-subscriptions.png)

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

## <a name="transfer-pay-as-you-go-subscription-to-ea-subscription"></a>Transferir a subscrição pay as you go para a subscrição EA

Para transferir uma subscrição individual com tarifas pay as you go para uma subscrição EA, tem de criar um novo pedido de suporte no portal do Azure. Para criar um pedido de suporte, clique em **+ Novo pedido de suporte** na área Ajuda e Suporte.


## <a name="view-usage-summary-and-download-reports"></a>Ver o resumo de utilização e os relatórios de transferências

Os administradores do Enterprise podem ver um resumo dos dados de utilização, da alocação monetária consumida e dos custos associados a utilização adicional no Azure EA Portal. Os custos são apresentados ao nível do resumo em todas as contas e subscrições.

Para ver a utilização detalhada de contas específicas

Transfira o relatório Detalhes de Utilização. Clique em **Relatórios** e, em seguida, clique no separador **Transferir Utilização**. Na lista de relatórios, clique em **Transferir** para o relatório mensal que pretende obter.

O relatório não inclui os impostos aplicáveis. Poderá existir uma latência de cerca de oito horas entre a hora em que a utilização foi incorrida e a hora que está refletida no relatório.

Para ver relatórios e grafos do Resumo de Utilização:

1. No Azure EA Portal, na área de navegação esquerda, clique em **Relatórios** e veja o separador **Resumo de Utilização**.  
  ![](./media/billing-ea-portal-get-started/create-ea-view usage-summary-and-download-reports.png)
2. Selecione um termo de alocação.
3. Alterne entre **M** (Mensal) e **P** (Personalizado) na parte superior direita da página para ver o **Resumo de Utilização** com datas de início e de fim.  
  ![](./media/billing-ea-portal-get-started/create-ea-view-usage-summary-and-download-reports-custom-view.png)
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

![Exemplo que mostra página Transferir Utilização](./media/billing-ea-portal-get-started/create-ea-download-csv-reports.png)

Veja a seguir um vídeo que mostra como transferira s informações de utilização:

[Vídeo de utilização do Azure EA Portal](https://www.youtube.com/watch?v=eY797htT1qg)

>[!VIDEO https://www.youtube.com/embed/eY797htT1qg]

## <a name="schedule-an-onboarding-call"></a>Agendar uma chamada de inclusão

Caso pretenda agendar uma sessão de inclusão presencial com o cliente, crie um pedido de suporte no [Suporte do Azure EA Portal](https://support.microsoft.com/supportrequestform/e114582c-4e51-af46-10b1-1f0cc141e133). Selecione **Inclusão** como a **Categoria do Problema**.

## <a name="next-steps"></a>Passos seguintes
- Os administradores do Azure EA Portal devem ler o artigo [Azure EA portal administration](billing-ea-portal-administration.md) (Administração do Azure EA Portal) para saber mais sobre as tarefas administrativas comuns.
- Se precisar de ajuda para resolver problemas com o Azure EA Portal, veja [Troubleshoot Azure EA portal access](billing-ea-portal-troubleshoot.md) (Resolver problemas de acesso do Azure EA Portal).
