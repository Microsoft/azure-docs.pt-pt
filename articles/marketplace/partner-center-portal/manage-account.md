---
title: Como gerir uma conta de marketplace comercial no Microsoft Partner Center
description: Saiba como gerir uma conta de marketplace comercial no Microsoft Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: parthpandyaMSFT
ms.author: parthp
ms.date: 11/02/2020
ms.openlocfilehash: a9d2c10d665af6899ec79312353676467a623735
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94628888"
---
# <a name="manage-your-commercial-marketplace-account-in-partner-center"></a>Gerir a sua conta de mercado comercial no Partner Center

Uma vez [criada uma conta Partner Center,](./create-account.md)pode utilizar o [painel de instrumentos do mercado comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) para gerir a sua conta e ofertas.

Neste artigo, vamos mergulhar na forma de gerir a sua conta partner Center, incluindo como:

- [Aceda às definições da conta do Centro parceiro](#access-your-account-settings)
- [Encontre os seus inquilinos editoriais ID, Symantec ID, Seller ID, User ID, MPN ID e Azure AD](#account-details)
- [Atualizar informações de contato](#contact-info)
- [Configurar GUIDs de rastreio para monitorizar o uso do cliente](#tracking-guids)
- [Gerir utilizadores](#manage-users)
- [Gerir grupos](#manage-groups)
- [Gerir aplicações AD AZure](#manage-azure-ad-applications)
- [Definir funções e permissões de utilizadores](#define-user-roles-and-permissions)
- [Gerir inquilinos da AD AZure (contas de trabalho)](#manage-tenants)
- [Gerir acordos de centro de parceiros](#agreements)

## <a name="access-your-account-settings"></a>Aceda às definições da sua conta

Se ainda não o fez, você (ou administrador da sua organização) deverá aceder às [definições](https://partner.microsoft.com/dashboard/account/management) de conta da sua conta Partner Center para:
- Verifique o estado de verificação da conta da sua empresa
- Confirme o seu ID Symantec, ID do Vendedor, Microsoft Partner Network (MPN) ID, ID do Editor e informações de contacto, incluindo o aprovador da empresa e o contacto do vendedor
- Crie contas de utilizador para quem utilizar a sua conta de negócios no Partner Center

### <a name="open-developer-settings"></a>Abra as definições do desenvolvedor

No painel [de instrumentos do seu mercado comercial](https://partner.microsoft.com/dashboard/commercial-marketplace) no Partner Center, selecione **Definições** (ícone de engrenagem) no canto superior direito e, em seguida, **as definições do Desenvolvedor**.

![Menu de definições de conta no Partner Center](./media/dashboard-developer-settings.png)

Nas **definições de Conta** pode ver o seu:
- **Detalhes da conta** : Tipo de conta e estado da conta
- **IDs editoriais** : ID do vendedor, ID do utilizador, ID editor, inquilinos AD Azure, e assim por diante
- **Informações de contacto** : Nome de exibição do editor, nome de contacto do vendedor, e-mail, telefone e endereço
- **GUIDs de rastreio** : Quaisquer GUIDs de rastreio associados à sua conta

### <a name="account-details"></a>Detalhes da conta

Na secção de Detalhes da Conta, pode ver informações básicas, como o **tipo de Conta** (Empresa ou Individual) e o estado de **Verificação** da sua conta. Durante o processo de verificação da sua conta, estas definições apresentarão cada passo necessário, incluindo verificação de e-mail, verificação de emprego e verificação de negócios. Também pode atualizar o seu e-mail aqui e reensirar a verificação se necessário.

### <a name="publisher-ids"></a>IDs de editor

Na secção de IDs editoriais, pode ver os seus inquilinos **Symantec ID,** **Seller ID,** **User ID,** **MPN ID** e **Ad AD.** Estes valores são atribuídos pela Microsoft para identificar exclusivamente a sua conta de programador e não podem ser editados.

### <a name="contact-info"></a>Informações de contacto

Na secção informações de Contacto, pode ver o **nome de exibição do Editor,** **informação de contacto do Vendedor** (o nome de contacto, e-mail, número de telefone e morada para o vendedor da empresa) e o autor da **empresa** (o nome, e-mail e número de telefone do indivíduo com autoridade para aprovar decisões para a empresa).

#### <a name="payout-account"></a>Conta de pagamento

Uma conta de pagamento é a conta bancária para a qual os lucros são enviados das suas vendas. Esta conta bancária deve estar no mesmo país/região onde registou a sua conta partner Center.

Para configurar a sua conta de pagamento:

1. Vá à [página de visão geral](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) do mercado comercial no Partner Center.
2. Na secção Perfil, ao lado **do Perfil de Pagamento,** selecione **Update**.
3. **Escolha um método de pagamento:** conta bancária ou PayPal.
4. **Adicionar informações de pagamento** : Isto pode incluir a escolha de um tipo de conta (verificação ou poupança), introduzir o nome do titular da conta, o número da conta e o número de encaminhamento, endereço de faturação, número de telefone ou PayPal endereço de e-mail. *Para obter mais informações sobre a utilização de PayPal como método de pagamento da sua conta e para saber se é apoiado na sua região de mercado, consulte [PayPal informações](/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info).

> [!IMPORTANT]
> Alterar a sua conta de pagamento pode atrasar os seus pagamentos até um ciclo de pagamento. Este atraso ocorre porque precisamos verificar a alteração da conta, tal como fazemos quando configuramos a conta de pagamento pela primeira vez. Continuará a ser pago pelo valor total depois de verificada a sua conta; quaisquer pagamentos devidos para o ciclo de pagamento em curso serão adicionados ao próximo.  

#### <a name="tax-profile"></a>Perfil fiscal

Reveja o seu estado atual do perfil fiscal, confirmando o **tipo de Entidade** correta e **Informações de Certificados Fiscais.** Selecione **Editar** para atualizar ou preencher os formulários necessários.

Para estabelecer o seu estatuto fiscal, deve especificar o seu país/região de residência e cidadania e preencher os formulários fiscais adequados associados ao seu país/região.

Independentemente do seu país/região de residência ou cidadania, deve preencher formulários fiscais dos Estados Unidos para vender quaisquer ofertas através da Microsoft. Os parceiros que satisfaçam determinados requisitos de residência dos Estados Unidos devem preencher um formulário IRS W-9. Outros parceiros fora dos Estados Unidos devem preencher um formulário IRS W-8. Pode preencher estes formulários online à medida que completa o seu perfil fiscal.

Um Número de Identificação Individual do Contribuinte dos Estados Unidos (ou ITIN) não é obrigado a receber pagamentos da Microsoft ou a reclamar benefícios do tratado fiscal.

Pode completar e submeter os seus formulários fiscais eletronicamente no Partner Center; na maioria dos casos, não precisa imprimir e enviar nenhum formulário.

Diferentes países e regiões têm diferentes requisitos fiscais. O valor exato que deve pagar em impostos depende dos países e regiões onde vende as suas ofertas. A Microsoft atribui vendas e utiliza impostos em seu nome em alguns países/regiões. Estes países/regiões serão identificados no processo de listagem da sua oferta. Noutros países/regiões, dependendo do local onde está registado, poderá ter de reencamular vendas e utilizar o imposto para as suas vendas diretamente para a autoridade tributária local. Além disso, os lucros de vendas que recebe podem ser tributáveis como rendimento. Encorajamo-lo vivamente a contactar a autoridade relevante para o seu país ou região que melhor o ajude a determinar a informação fiscal certa para as suas transações de vendas da Microsoft.

##### <a name="withholding-rates"></a>Taxas de retenção

A informação que submete nos seus formulários fiscais determina a taxa de retenção na fonte de imposto adequada. A taxa de retenção aplica-se apenas às vendas que faz nos Estados Unidos; as vendas efetuadas em locais não americanos não estão sujeitas a retenção. As taxas de retenção variam, mas para a maioria dos desenvolvedores que se registam fora dos Estados Unidos, a taxa de incumprimento é de 30%. Tem a opção de reduzir esta taxa se o seu país/região tiver concordado com um tratado de imposto sobre o rendimento com os Estados Unidos.

##### <a name="tax-treaty-benefits"></a>Benefícios do tratado fiscal

Se estiver fora dos Estados Unidos, poderá tirar partido dos benefícios do tratado fiscal. Estes benefícios variam de país/região para país/região, e podem permitir-lhe reduzir o montante de impostos que a Microsoft retém. Pode reclamar benefícios do tratado fiscal completando a Parte II do formulário W-8BEN. Recomendamos que comunique com os recursos apropriados no seu país ou região para determinar se estes benefícios se aplicam a si.

[Saiba mais sobre detalhes fiscais para programadores de aplicações/jogos do Windows e editores do Azure Marketplace.](/windows/uwp/publish/tax-details-for-paid-apps)

#### <a name="payout-hold-status"></a>Estatuto de detenção de pagamento

Por padrão, a Microsoft envia pagamentos mensalmente. No entanto, pode opcionalmente colocar os seus pagamentos em espera, o que impedirá o envio de pagamentos para a sua conta. Se optar por colocar os seus pagamentos em espera, continuaremos a registar qualquer receita que obtenha e forneceremos os detalhes no seu **resumo do Pagamento.** No entanto, não enviaremos quaisquer pagamentos para a sua conta até que remova o porão.

Para colocar os seus pagamentos em espera, vá para as **definições de Conta**. Em **detalhes financeiros** , na secção **de estado de detenção de pagamento,** alternar o slider para **On**. Pode alterar o seu estado de detenção a qualquer momento, mas esteja ciente de que a sua decisão terá impacto no próximo pagamento mensal. Por exemplo, se quiser realizar o pagamento de abril, certifique-se de definir o seu estado de detenção de pagamento para **On** antes do final de março.

Depois de definir o seu estado de detenção de pagamento para **On,** todos os pagamentos ficarão em espera até que altere o slider de volta para **Off**. Quando o fizer, será incluído durante o próximo ciclo mensal de pagamento (desde que os limiares de pagamento aplicáveis tenham sido cumpridos). Por exemplo, se já teve os seus pagamentos em espera, mas gostaria de ter um pagamento gerado em junho, então certifique-se de alternar o estado de detenção do pagamento para **Off** antes do final de maio.

> [!NOTE]
> A sua seleção **de status de detenção de pagamento** aplica-se a todas **as** fontes de receita que são pagas através do Microsoft Partner Center, incluindo o Azure Marketplace, AppSource, Microsoft Store, publicidade, etc.). Não é possível selecionar diferentes estados de porão para cada fonte de receita.

### <a name="devices"></a>Dispositivos

As definições de gestão do dispositivo aplicam-se apenas à publicação UWP. [Saiba mais](/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info).

### <a name="tracking-guids"></a>Rastreio de GUIDs

Os identificadores exclusivos globais (GUIDs) são números de referência únicos (com 32 dígitos hexadémicos) que podem ser usados para rastrear o seu uso de Azure. 

Para criar GUIDs para rastreio, deve utilizar um gerador GUID. A equipa de Armazenamento Azure criou um [formulário de gerador GUID](https://aka.ms/StoragePartners) que lhe enviará um GUID do formato correto e pode ser reutilizado através dos diferentes sistemas de rastreio.

Recomendamos que crie um GUID único para cada canal de oferta e distribuição para cada produto. Pode optar por utilizar um único GUID para os múltiplos canais de distribuição do produto se não quiser que o relatório seja dividido.

Se implementar um produto utilizando um modelo e estiver disponível tanto no Azure Marketplace como no GitHub, pode criar e registar dois GUIDs distintos:

- Produto A no Mercado Azure
- Produto A no GitHub

A reporte é feita pelo valor do parceiro (Microsoft Partner ID) e pelos GUIDs. Também pode rastrear GUIDs a um nível mais granular, alinhando-se com cada plano dentro da sua oferta.

Para obter mais informações, consulte o uso do [cliente Tracking Azure com AS FAQ GUIDs](../azure-partner-customer-usage-attribution.md#faq)).

## <a name="create-a-billing-profile"></a>Criar um perfil de faturação

Se estiver a publicar uma [Oferta Dinâmica 365 para o Envolvimento do Cliente & Power Apps](./create-new-customer-engagement-offer.md) ou a Oferta Dinâmica [365 para Operações,](./create-new-operations-offer.md) tem de completar o seu **perfil de faturação**.

O endereço de faturação é pré-preenchido da sua entidade legal, e pode atualizar este endereço mais tarde. Os campos de identificação do IRS e do IVA são facultativos.  O nome e o nome da empresa do país/região não podem ser editados.

## <a name="multi-user-account-management"></a>Gestão de conta multiutilizador

Partner Center utiliza [O Azure Ative Directory](../../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) para acesso e gestão de conta multiutilizador. O Azure AD da sua organização está automaticamente associado à sua conta Partner Center como parte do processo de inscrição.

## <a name="manage-users"></a>Gerir utilizadores

A secção de **Utilizadores** do Partner Center (em **Definições de Conta)** permite-lhe utilizar a Azure AD para gerir os utilizadores, grupos e aplicações AD Azure que tenham acesso à sua conta Partner Center. A sua conta deve ter permissões [**ao nível do Gestor**](#define-user-roles-and-permissions) para a conta de trabalho [(inquilino Azure AD)](./company-work-accounts.md) nas quais pretende adicionar ou editar utilizadores. Para gerir os utilizadores dentro de uma conta de trabalho/inquilino diferente, terá de se assinar e depois voltar a entrar como utilizador com permissões **do Manager** nessa conta de trabalho/inquilino.

Uma vez que você está assinado com a sua conta de trabalho (Azure AD inquilino), você pode:

- [Adicionar ou remover utilizadores](#add-existing-users)
- [Alterar uma palavra-passe do utilizador](#change-a-user-password)
- [Adicionar ou remover grupos](#manage-groups)
- [Adicionar ou remover aplicações AZure AD](#add-new-azure-ad-applications)
- [Gerir chaves para uma aplicação AD Azure](#manage-keys-for-an-azure-ad-application)
- [Definir funções e permissões de utilizadores](#define-user-roles-and-permissions)

Tenha em mente que todos os utilizadores do Partner Center (incluindo grupos e aplicações AD AZure) devem ter uma conta de trabalho ativa num [inquilino AZure AD](#manage-tenants) que esteja associado à sua conta partner Center.

### <a name="add-existing-users"></a>Adicionar utilizadores existentes

Para adicionar utilizadores à sua conta Partner Center que já existe na conta de trabalho da sua empresa [(Azure AD inquilino)](./company-work-accounts.md):

1. Aceda aos **Utilizadores** (nas **definições de Conta)** e selecione **Adicionar utilizadores**.
2. Selecione um ou mais utilizadores da lista que aparece. Pode utilizar a caixa de pesquisa para procurar utilizadores específicos.
*Se selecionar mais do que um utilizador para adicionar à sua conta Partner Center, deve atribuir-lhes a mesma função ou conjunto de permissões personalizadas. Para adicionar vários utilizadores com diferentes funções/permissões, repita estes passos para cada função ou conjunto de permissões personalizadas.
3. Quando terminar de escolher os utilizadores, **selecione Adicionar selecionado**.
4. Na secção **Funções,** especifique as funções ou permissões personalizadas para os utilizadores selecionados.
5. Selecione **Guardar**.

### <a name="create-new-users"></a>Criar novos utilizadores

Para criar novas contas de utilizador, tem de ter uma conta com permissões [**de administrador global.**](../../active-directory/roles/permissions-reference.md)

1. Ir para **os Utilizadores** (nas **definições de Conta),** selecionar **Adicionar utilizadores,** em seguida, escolher **Criar novos utilizadores**.
1. Introduza um nome próprio, apelido e nome de utilizador para cada novo utilizador. 
1. Se pretender que o novo utilizador tenha uma conta de administrador global no diretório da sua organização, verifique a caixa com a etiqueta **Faça deste utilizador um administrador Global no seu AD Azure, com controlo total sobre todos os recursos do diretório.** Isto dará ao utilizador acesso total a todas as funcionalidades administrativas do Azure AD da sua empresa. Eles poderão adicionar e gerir utilizadores na conta de trabalho da sua organização (inquilino Azure AD), embora não no Partner Center, a menos que você conceda à conta o papel/permissões apropriados.
1. Se verificou a caixa para **fazer deste utilizador um administrador Global,** terá de fornecer um e-mail de **recuperação de passwords** para que o utilizador recupere a sua palavra-passe, se necessário.
1. Na secção **de membros do Grupo,** selecione quaisquer grupos aos quais pretenda que o novo utilizador pertença.
1. Na secção **Peças,** especifique as funções ou permissões personalizadas para o utilizador.
1. Selecione **Guardar**.

A criação de um novo utilizador no Partner Center também criará uma conta para esse utilizador na conta de trabalho (Azure AD inquilina) à qual está inscrito. Fazer alterações no nome de um utilizador no Partner Center fará as mesmas alterações na conta de trabalho da sua organização (inquilino Azure AD).

### <a name="invite-new-users-by-email"></a>Convidar novos utilizadores por e-mail

Para convidar utilizadores que não fazem parte da sua conta de trabalho da empresa (inquilino Azure AD) por e-mail, você deve ter uma conta com permissões [**de administrador global.**](../../active-directory/roles/permissions-reference.md)

1. Aceda aos **Utilizadores** (nas **definições de Conta),** selecione **Adicionar utilizadores,** em seguida, escolha **Convidar os utilizadores por e-mail**.
2. Insira um ou mais endereços de e-mail (até 10), separados por vírgulas ou pontos-e-vírgulas.
3. Na secção **Peças,** especifique as funções ou permissões personalizadas para o utilizador.
4. Selecione **Guardar**.

Os utilizadores que convidou receberão um convite por e-mail para se juntarem à sua conta partner Center. Uma nova conta de utilizador de hóspedes será criada na sua conta de trabalho (Azure AD inquilino). Cada utilizador terá de aceitar o seu convite antes de poder aceder à sua conta.

Se precisar de reensirar um convite, visite a página **dos Utilizadores,** encontre o convite na lista de utilizadores, selecione o seu endereço de e-mail (ou o texto que diz *Convite pendente).* Em seguida, na parte inferior da página, selecione **o convite de Resend**.

> [!NOTE]
> Se a sua organização utilizar a integração de [diretórios](/previous-versions/azure/azure-services/jj573653(v=azure.100)) para sincronizar o serviço de diretório no local com o seu AD Azure, não poderá criar novos utilizadores, grupos ou aplicações AD AZure no Partner Center. Você (ou outro administrador no seu diretório no local) terá de criá-los diretamente no diretório no local antes de poder vê-los e adicioná-los no Partner Center.

### <a name="remove-a-user"></a>Remover um utilizador

Para remover um utilizador da sua conta de trabalho (inquilino Azure AD), vá aos **Utilizadores** (nas **definições de Conta),** selecione o utilizador que pretende remover utilizando a caixa de verificação na coluna de extrema-direita e, em seguida, escolha **Remover** das ações disponíveis. Aparecerá uma janela pop-up para confirmar que pretende remover os utilizadores selecionados.

### <a name="change-a-user-password"></a>Alterar uma palavra-passe do utilizador

Se um dos seus utilizadores precisar de alterar a sua palavra-passe, pode fazê-lo se tiver fornecido um **e-mail de recuperação de passwords** ao criar a conta de utilizador. Também pode atualizar a palavra-passe de um utilizador seguindo os passos abaixo. Para alterar a palavra-passe de um utilizador na sua conta de trabalho da empresa (inquilino Azure AD), tem de ser inscrito numa conta com permissões [**de administrador global.**](../../active-directory/roles/permissions-reference.md) Isto irá alterar a palavra-passe do utilizador no seu inquilino Azure AD, juntamente com a palavra-passe que usam para aceder ao Partner Center.

1. A partir da página **Utilizadores** (nas **definições de Conta),** selecione o nome da conta de utilizador que pretende editar.
2. Selecione o botão **de palavra-passe Reset** na parte inferior da página.
3. Uma página de confirmação aparecerá para mostrar as informações de login para o utilizador, incluindo uma senha temporária. Certifique-se de imprimir ou copiar esta informação e fornetilá-la ao utilizador, uma vez que não poderá aceder à senha temporária depois de sair desta página.

## <a name="manage-groups"></a>Gerir grupos

Os grupos permitem-lhe controlar várias funções e permissões de utilizador em conjunto.

### <a name="add-an-existing-group"></a>Adicione um grupo existente

Para adicionar um grupo que já existe na conta de trabalho da sua organização (inquilino AZure AD) à sua conta partner Center:

1. A partir da página **do Utilizadores** (nas **definições de Conta),** selecione **Grupos adicionar**.
2. Selecione um ou mais grupos da lista que aparece. Pode utilizar a caixa de pesquisa para procurar grupos específicos.
Se selecionar mais de um grupo para adicionar à sua conta Partner Center, deve atribuir-lhes a mesma função ou conjunto de permissões personalizadas. Para adicionar vários grupos com diferentes funções/permissões, repita estes passos para cada função ou conjunto de permissões personalizadas.
3. Quando terminar de escolher grupos, **selecione Adicionar selecionado**.
4. Na secção **Funções,** especifique as funções ou permissões personalizadas para o(s) grupo selecionado. Todos os membros do grupo poderão aceder à sua conta Partner Center com as permissões que aplica ao grupo, independentemente das funções e permissões associadas à sua conta individual.
5. Selecione **Guardar**.

Quando adicionar um grupo existente, todos os utilizadores que façam parte desse grupo poderão aceder à sua conta Partner Center, com as permissões associadas à função atribuída pelo grupo.

### <a name="add-a-new-group"></a>Adicionar um novo grupo

Para adicionar um grupo novo à sua conta Partner Center:

1. A partir da página **do Utilizadores** (nas **definições de Conta),** selecione **Grupos adicionar**.
2. Na página seguinte, selecione **Novo grupo**.
3. Introduza o nome de exibição para o novo grupo.
4. Especifique as funções ou permissões personalizadas para o grupo. Todos os membros do grupo poderão aceder à sua conta Partner Center com as permissões que aqui aplica, independentemente das funções/permissões associadas à sua conta individual.
5. Selecione o(s) utilizador(s) para o novo grupo da lista que aparece. Pode utilizar a caixa de pesquisa para procurar utilizadores específicos.
6. Quando terminar de selecionar os utilizadores, **selecione Adicionar selecionado** para os adicionar ao novo grupo.
7. Selecione **Guardar**.

Este novo grupo será criado na conta de trabalho da sua organização (inquilino Azure AD) também, e não apenas na sua conta partner Center.

### <a name="remove-a-group"></a>Remover um grupo

Para remover um grupo da sua conta de trabalho (inquilino Azure AD), vá aos **Utilizadores** (nas **definições de Conta),** selecione o grupo que pretende remover usando a caixa de verificação na coluna de extrema-direita e, em seguida, escolha **Remover** das ações disponíveis. Aparecerá uma janela pop-up para confirmar que pretende remover os grupos selecionados.

## <a name="manage-azure-ad-applications"></a>Gerir aplicações AD AZure

Pode permitir que aplicações ou serviços que fazem parte da Ad Azure da sua empresa acedam à sua conta Partner Center.

### <a name="add-existing-azure-ad-applications"></a>Adicionar aplicações AD Azure existentes

Para adicionar aplicações que já existem no Diretório Ativo Azure da sua empresa:

1. A partir da página **de Utilizadores** (nas **definições de Conta),** selecione **adicionar aplicações AD Azure**.
2. Selecione uma ou mais aplicações AD AZure da lista que aparece. Pode utilizar a caixa de pesquisa para procurar aplicações Azure AD específicas. Se selecionar mais de uma aplicação AD Azure para adicionar à sua conta Partner Center, deve atribuir-lhes a mesma função ou conjunto de permissões personalizadas. Para adicionar várias aplicações AD Azure com diferentes funções/permissões, repita estes passos para cada função ou conjunto de permissões personalizadas.
3. Quando terminar de selecionar aplicações AD Azure, **selecione Adicionar selecionado**.
4. Na secção **Funções,** especifique as funções ou permissões personalizadas para a(s) aplicação AD selecionada.
5. Selecione **Guardar**.

### <a name="add-new-azure-ad-applications"></a>Adicionar novas aplicações AD AZure

Se quiser conceder acesso ao Partner Center a uma nova conta de aplicação AZure AD, pode criar uma na secção **Utilizadores.** Isto criará uma nova conta na sua conta de trabalho da empresa (Azure AD inquilino), e não apenas na sua conta partner Center. Se estiver a utilizar principalmente esta aplicação AD AD para autenticação do Partner Center, e não precisar que os utilizadores acedam diretamente à sua informação, pode introduzir qualquer endereço válido para o **URL de Resposta** e App **ID URI,** desde que esses valores não sejam utilizados por qualquer outra aplicação AZure AD no seu diretório.

1. A partir da página **de Utilizadores** (nas **definições de Conta),** selecione **adicionar aplicações AD Azure**.
2. Na página seguinte, selecione **a aplicação New AD AZure**.
3. Introduza o **URL de resposta** para a nova aplicação AZure AD. Este é o URL onde os utilizadores podem iniciar sposição e utilizar a sua aplicação AD Azure (por vezes também conhecida como URL da app ou URL Sign-On). O **URL de resposta** não pode ter mais de 256 caracteres e deve ser único dentro do seu diretório.
4. Insira a **App ID URI** para a nova aplicação AZure AD. Este é um identificador lógico para a aplicação Azure AD que é apresentado quando um único pedido de inscrição é enviado para Azure AD. A **App ID URI** deve ser única para cada aplicação AD AZure no seu diretório. Esta identificação não pode ter mais de 256 caracteres. Para obter mais informações sobre o ID URI da app, consulte [integrar aplicações com o Azure Ative Directory).](../../active-directory/develop/quickstart-modify-supported-accounts.md#change-the-application-registration-to-support-different-accounts)
5. Na secção **Funções,** especifique as funções ou permissões personalizadas para a aplicação Azure AD.
6. Selecione **Guardar**.

Depois de adicionar ou criar uma aplicação AD Azure, pode voltar à secção **de Utilizadores** e selecionar o nome da aplicação para rever as definições da aplicação, incluindo o ID do Cliente, ID do Cliente, URL de resposta e App ID URI.

### <a name="remove-an-azure-ad-application"></a>Remover uma aplicação AD AZure

Para remover uma aplicação da sua conta de trabalho (inquilino Azure AD), vá aos **Utilizadores** (nas **definições de Conta),** selecione a aplicação que pretende remover utilizando a caixa de verificação na coluna de extrema-direita e, em seguida, escolha **Remover** das ações disponíveis. Aparecerá uma janela pop-up para confirmar que pretende remover as aplicações selecionadas.

### <a name="manage-keys-for-an-azure-ad-application"></a>Gerir chaves para uma aplicação AD Azure

Se a sua aplicação AD Azure ler e escrever dados no Microsoft Azure AD, precisará de uma chave. Pode criar chaves para uma aplicação AD Azure editando as suas informações no Partner Center. Também pode remover as chaves que já não são necessárias.

1. A partir da página **Utilizadores** (nas **definições de Conta),** selecione o nome da aplicação AD Azure. Você verá todas as chaves ativas para a aplicação AD Azure, incluindo a data em que a chave foi criada e quando expirará 50.
2. Para remover uma chave que já não é necessária, selecione **Remover**.
3. Para adicionar uma nova chave, **selecione Adicione nova tecla**.
4. Verá um ecrã que mostra os **valores** de **ID** e Chave do Cliente . Certifique-se de imprimir ou copiar esta informação, uma vez que não poderá aceder novamente a ela depois de sair desta página.
5. Se pretender criar mais teclas, **selecione Adicione outra tecla**.

## <a name="define-user-roles-and-permissions"></a>Definir funções e permissões de utilizadores

Os utilizadores da sua empresa podem ser atribuídos às seguintes funções e permissões para o programa de marketplace comercial no Partner Center:

- **Proprietário**
  - "Esta é a pessoa que criou a conta pela primeira vez e tem acesso total à mesma, incluindo a capacidade de criar e editar todos os utilizadores de contas e alterar todas as definições financeiras e de conta. Uma conta geralmente tem apenas um proprietário.
- **Gestor**
  - Pode aceder a todas as funcionalidades da conta da Microsoft, exceto definições de impostos e pagamentos
  - Pode gerir utilizadores, funções e contas de trabalho (inquilinos)
- **Programador**
  - Pode gerir e publicar ofertas
  - Pode ver alguns relatórios de editores

> [!NOTE]
> Para o programa Mercado Comercial, não são utilizadas as funções de administrador global, colaboradora de negócios, colaborador financeiro e marketer. Atribuir estas funções aos utilizadores não tem qualquer efeito. Apenas as funções de Gestor e Desenvolvedor concedem permissões aos utilizadores.

Para obter mais informações sobre a gestão de funções e permissões noutras áreas do Partner Center, tais como Azure Ative Directory (AD), Cloud Solution Provider (CSP), Control Panel Vendor (CPV), utilizadores convidados ou Microsoft Partner Network (MPN), consulte [atribuir papéis e permissões aos utilizadores no Partner Center.](/partner-center/permissions-overview)

## <a name="manage-tenants"></a>Gerir inquilinos

Um inquilino do Azure Ative Directory (AD), também referido como a sua "conta de trabalho" ao longo desta documentação, é uma representação da sua organização criada no portal Azure e ajuda-o a gerir uma instância específica dos serviços de cloud da Microsoft para os seus utilizadores internos e externos. Se a sua organização subscreveu um serviço de cloud da Microsoft, como a Azure, a Microsoft Intune ou a Microsoft 365, foi criado um inquilino AZure AD para si.

Você pode configurar vários inquilinos para usar com Partner Center. Qualquer utilizador com a função **Manager** na conta Partner Center terá a opção de adicionar e remover os inquilinos AD da conta.  

### <a name="add-an-existing-tenant"></a>Adicione um inquilino existente

Para associar outro inquilino AZure AD à sua conta Partner Center:

1. A partir da página **de Inquilinos** (em **definições de Conta),** selecione **Associar outro inquilino AD AZure.**
2. Insira as suas credenciais AD AZure para o inquilino que pretende associar.
3. Reveja a organização e o nome de domínio para o seu inquilino AZure AD. Para completar a associação, **selecione Confirmar**.

Se a associação tiver sucesso, estará pronto para adicionar e gerir utilizadores de conta na secção **Utilizadores** no Partner Center.

### <a name="create-a-new-tenant"></a>Criar um novo inquilino

Para criar um novo inquilino AZure AD com a sua conta Partner Center:

1. A partir da página **de Inquilinos** (em **definições de Conta),** selecione **Criar um novo inquilino AD Azure**.
2. Insira as informações do diretório para o seu novo AD Azure:
    - **Nome de domínio** : O nome único que usaremos para o seu domínio AZure AD, juntamente com ".onmicrosoft.com". Por exemplo, se inserisse "exemplo", o seu domínio AD Azure seria "example.onmicrosoft.com".
    - **E-mail de contato:** Um endereço de e-mail onde podemos contactá-lo sobre a sua conta, se necessário.
    - **Informações globais da conta de utilizador do administrador** : O primeiro nome, apelido, nome de utilizador e senha que pretende utilizar para a nova conta de administrador global.
3. Selecione **Criar** para confirmar o novo domínio e informações de conta.
4. Inicie sôr entrada com o seu novo nome de utilizador e palavra-passe do administrador global Azure AD para começar a [adicionar e gerir utilizadores.](#manage-users)

Para obter mais informações sobre a criação de novos inquilinos dentro do seu portal Azure, em vez de através do portal Partner Center, consulte o artigo [Criar um novo inquilino no Azure Ative Directory.](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

### <a name="remove-a-tenant"></a>Remova um inquilino

Para remover um inquilino da sua conta Partner Center, encontre o seu nome na página **dos Inquilinos** (nas **definições de Conta),** em seguida, selecione **Remover**. Será solicitado para confirmar que quer retirar o inquilino. Assim que o fizer, nenhum utilizadores nesse inquilino poderá entrar na conta do Centro de Parceiros, e quaisquer permissões que tenha configurado para esses utilizadores serão removidas.

Quando você remover um inquilino, todos os utilizadores que foram adicionados à conta do Centro de Parceiros desse inquilino deixarão de poder fazer o seu sôm.

> [!TIP]
> Não pode remover um inquilino se está atualmente assinado no Partner Center usando uma conta no mesmo inquilino. Para remover um inquilino, você deve se inscrever no Partner Center como **gerente** para outro inquilino que esteja associado à conta. Se houver apenas um inquilino associado à conta, esse inquilino só pode ser removido depois de iniciar sessão com a conta microsoft que abriu a conta.

## <a name="agreements"></a>Contratos

A secção **De Acordos** do Centro de Parceiros (em **Definições de Conta)** permite-lhe visualizar uma lista dos acordos de publicação que autorizou. Estes acordos estão listados de acordo com o nome e o número da versão, incluindo a data em que foi aceite e o nome do utilizador que aceitou o acordo.

**As ações necessárias** podem aparecer no topo desta página se houver atualizações de acordo que precisem da sua atenção. Para aceitar um acordo atualizado, leia primeiro a versão do Acordo ligado e, em seguida, **selecione Aceitar o acordo**.
