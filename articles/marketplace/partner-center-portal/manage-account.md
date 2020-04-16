---
title: Como gerir uma conta de mercado comercial no Microsoft Partner Center
description: Saiba como gerir uma conta de marketplace comercial no Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/06/2019
ms.openlocfilehash: 256ea1a03cd22df443362623177f91893f28b664
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419158"
---
# <a name="manage-your-commercial-marketplace-account-in-partner-center"></a>Gerencie a sua conta de mercado comercial no Partner Center

Depois de [criar uma conta partner center,](./create-account.md)pode usar o [dashboard do mercado comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) para gerir a sua conta e ofertas.

Neste artigo, vamos mergulhar em como gerir a sua conta de Partner Center, incluindo como:

- [Aceda às definições da sua conta Partner Center](#access-your-account-settings)
- [Encontre o seu ID editor, id symantec, ID do vendedor, ID do utilizador, ID MPN e inquilinos da AD Azure](#account-details)
- [Atualizar informações de contacto](#contact-info)
- [Configurar GUIDs de rastreio para monitorizar o uso do cliente](#tracking-guids)
- [Gerir utilizadores](#manage-users)
- [Gerir grupos](#manage-groups)
- [Gerir aplicações da AD Azure](#manage-azure-ad-applications)
- [Definir funções e permissões de utilizadores](#define-user-roles-and-permissions)
- [Gerir os inquilinos da AD Azure (contas de trabalho)](#manage-tenants)
- [Gerir acordos de centro de parceiros](#agreements)

## <a name="access-your-account-settings"></a>Aceda às definições da sua conta

Se ainda não o fez, você (ou administrador da sua organização) deve aceder às [definições](https://partner.microsoft.com/dashboard/account/management) da conta para a sua conta Partner Center para:
- Verifique o estado de verificação da conta da sua empresa
- Confirme o seu ID Symantec, Id do Vendedor, Id da Microsoft Partner Network (MPN), ID da Editora e informações de contacto, incluindo o aprovador da empresa e o contacto com o vendedor
- Crie contas de utilizador para quem utilizar a sua conta de negócios no Partner Center

### <a name="open-developer-settings"></a>Configurações de desenvolvedores abertos

**As definições** de conta estão localizadas no canto superior direito do seu [painel de instrumentos de marketplace comercial](https://partner.microsoft.com/dashboard/commercial-marketplace) no Partner Center. Selecione o ícone de engrenagem (perto do canto superior direito do painel de instrumentos) e, em seguida, selecione **as definições**do Desenvolvedor .

![Menu de definições de conta no Partner Center](./media/dashboard-developer-settings.png)

Definições de **Conta**Interior, poderá visualizar as suas:
- **Detalhes da conta**: Tipo de conta e estado da conta
- **IDs de editor:** ID do vendedor, ID do utilizador, ID da editora, inquilinos da AD Azure, etc.
- **Informações de contacto**: Editor nome de exibição, nome de contacto do vendedor, e-mail, telefone e endereço
- **Rastreio de GUIDs**: Qualquer GUIDs de rastreio associado à sua conta

### <a name="account-details"></a>Detalhes da conta

Na secção de dados da Conta, pode ver informações básicas, como o tipo de **conta** (Empresa ou Indivíduo) e o estado de **Verificação** da sua conta. Durante o processo de verificação da sua conta, estas configurações apresentarão cada passo necessário, incluindo verificação de e-mail, verificação de emprego e verificação de negócios. Também pode atualizar o seu e-mail aqui e reenviar a verificação se necessário.

### <a name="publisher-ids"></a>IDs de editor

Na secção de IDs da Editora, pode ver o seu **ID Symantec,** ID do **vendedor,** **ID do utilizador,** **ID MPN**e **inquilinos da AD Azure**. Estes valores são atribuídos pela Microsoft para identificar exclusivamente a sua conta de desenvolvedor e não podem ser editados.

### <a name="contact-info"></a>Informações de contacto

Na secção de informações de Contacto, pode ver o nome de **exibição**do Editor, **informações** de contacto do Vendedor (nome de contacto, e-mail, número de telefone e endereço para o vendedor da empresa) e o **aprovador** da Empresa (nome, e-mail e número de telefone do indivíduo com autoridade para aprovar decisões para a empresa).

#### <a name="payout-account"></a>Conta de pagamento

Uma conta de pagamento é a conta bancária para a qual os lucros são enviados das suas vendas. Esta conta bancária deve estar no mesmo país onde registou a sua conta de Partner Center.

Para configurar a sua conta de pagamento, precisa de associar a **sua Conta Microsoft:**
1. Vá à [página de visão geral](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) do mercado comercial no Partner Center.
2. Na secção Perfil, selecione Associar a **sua Conta Microsoft**.
3. Quando solicitado, inscreva-se na sua Conta Microsoft (MSA). Esta conta já não pode estar associada a outra conta do Partner Center.
4. Para completar a configuração da sua conta de pagamento, faça login no Partner Center e, em seguida, faça login na sua Conta Microsoft (em vez da sua conta de trabalho).

Agora que a sua Conta Microsoft está associada, para adicionar uma conta de pagamento, terá de:
- **Escolha um método de pagamento**: Conta Bancária ou PayPal
- **Adicionar informações de pagamento**: Isto pode incluir escolher um tipo de conta (verificação ou poupança), introduzir o nome do titular da conta, número de conta e número de encaminhamento, endereço de faturação, número de telefone ou endereço de e-mail PayPal. *Para obter mais informações sobre a utilização do PayPal como método de pagamento da sua conta e para saber se é suportado na sua região de mercado, consulte a [informação do PayPal](https://docs.microsoft.com/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info).

> [!IMPORTANT]
> Alterar a sua conta de pagamento pode atrasar os seus pagamentos até um ciclo de pagamento. Este atraso ocorre porque precisamos verificar a mudança de conta, tal como fazemos quando estabelecemos a conta de pagamento. Ainda será pago o valor total depois de verificada a sua conta; quaisquer pagamentos devidos para o ciclo de pagamento em curso serão adicionados ao próximo.  

#### <a name="tax-profile"></a>Perfil fiscal

Reveja o seu estado de perfil fiscal atual, confirmando o tipo de **Entidade** correta e a **Informação** do Certificado Fiscal é apresentada. Selecione **Editar** para atualizar ou preencher os formulários necessários.

Para estabelecer o seu estatuto fiscal, deve especificar o seu país de residência e cidadania e preencher os formulários fiscais adequados associados ao seu país/região.

Independentemente do seu país de residência ou cidadania, deve preencher formulários fiscais dos Estados Unidos para vender quaisquer ofertas através da Microsoft. Os parceiros que satisfaçam determinados requisitos de residência dos Estados Unidos devem preencher um formulário de IRS W-9. Outros parceiros fora dos Estados Unidos devem preencher um formulário W-8 do IRS. Pode preencher estes formulários online à medida que completa o seu perfil fiscal.

Um Número de Identificação Individual dos Contribuintes (ou ITIN) dos Estados Unidos não é obrigado a receber pagamentos da Microsoft ou a reclamar benefícios do tratado fiscal.

Pode preencher e submeter os seus formulários fiscais eletronicamente no Partner Center; na maioria dos casos, não é necessário imprimir e enviar formulários.

Diferentes países e regiões têm diferentes requisitos fiscais. O valor exato que deve pagar em impostos depende dos países e regiões onde vende as suas ofertas. A Microsoft atribui vendas e utiliza impostos em seu nome em alguns países. Estes países serão identificados no processo de listagem da sua oferta. Noutros países, dependendo do local onde está registado, poderá ter de remeter as vendas e utilizar o imposto para as suas vendas diretamente para a autoridade tributária local. Além disso, as receitas de venda que recebe podem ser tributáveis como rendimento. Encorajamo-lo vivamente a contactar a autoridade relevante para o seu país ou região que melhor o ajude a determinar as informações fiscais certas para as suas transações de vendas da Microsoft.

##### <a name="withholding-rates"></a>Taxas de retenção
A informação que apresenta nos seus formulários fiscais determina a taxa de retenção de impostos adequada. A taxa de retenção aplica-se apenas às vendas que faz nos Estados Unidos; as vendas efetuadas em locais não americanos não estão sujeitas a retenção. As taxas de retenção variam, mas para a maioria dos desenvolvedores que se registam fora dos Estados Unidos, a taxa de incumprimento é de 30%. Tem a opção de reduzir esta taxa se o seu país tiver concordado com um tratado de impostos sobre o rendimento com os Estados Unidos.

##### <a name="tax-treaty-benefits"></a>Benefícios do tratado fiscal
Se estiver fora dos Estados Unidos, poderá aproveitar-se dos benefícios do tratado fiscal. Estes benefícios variam de país para país, e podem permitir-lhe reduzir o montante dos impostos que a Microsoft retém. Pode reclamar benefícios do tratado fiscal preenchendo a Parte II do formulário W-8BEN. Recomendamos que comunique com os recursos apropriados no seu país ou região para determinar se esses benefícios se aplicam a si.

[Saiba mais sobre detalhes fiscais para desenvolvedores de aplicações/jogos do Windows e editores do Azure Marketplace.](https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps)

#### <a name="payout-hold-status"></a>Estatuto de detenção de pagamento

Por padrão, a Microsoft envia pagamentos mensalmente. No entanto, tem a opção de colocar os seus pagamentos em espera, o que impedirá o envio de pagamentos para a sua conta. Se optar por colocar os seus pagamentos em espera, continuaremos a registar qualquer receita que ganhe e forneceros os detalhes do seu resumo do **Pagamento.** No entanto, não enviaremos quaisquer pagamentos para a sua conta até que retire o porão. 

Para colocar os seus pagamentos em espera, vá às **definições da Conta.** De acordo com **os detalhes financeiros,** na secção **de estado de pagamento,** alternar o slider para **On**. Pode alterar o seu estado de retenção de pagamento a qualquer momento, mas esteja ciente de que a sua decisão terá impacto no próximo pagamento mensal. Por exemplo, se quiser realizar o pagamento de abril, certifique-se de definir o seu estatuto de retenção de pagamento para **On** antes do final de março.

Depois de definir o seu estado de reserva de pagamento para **On,** todos os pagamentos estarão em espera até que você altere o slider de volta para **Off**. Quando o fizer, será incluído durante o próximo ciclo mensal de pagamento (desde que tenham sido cumpridos quaisquer limiares de pagamento aplicáveis). Por exemplo, se teve os seus pagamentos em espera, mas gostaria de ter um pagamento gerado em junho, então certifique-se de alternar o estado de retenção de pagamento para **Off** antes do final de maio.

> [!NOTE]
> A sua seleção de estatuto de **detenção** de pagamento aplica-se a **todas as** fontes de receita que são pagas através do Microsoft Partner Center, incluindo o Azure Marketplace, AppSource, Microsoft Store, publicidade, etc.). Não é possível selecionar diferentes estatutos de espera para cada fonte de receita.

### <a name="devices"></a>Dispositivos

As definições de gestão do dispositivo aplicam-se apenas à publicação da UWP. [Saiba mais](https://docs.microsoft.com/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info).

### <a name="tracking-guids"></a>GuiDs de rastreio

Globalmente Exclusivos Identificadores (GUIDs) são números de referência únicos (com 32 dígitos hexadecimais) que podem ser usados para rastrear o seu uso Azure. 

Para criar GUIDs para rastreio, deve utilizar um gerador GUID. A equipa de Armazenamento Azure criou um [formulário de gerador GUIA](https://aka.ms/StoragePartners) que lhe enviará um GUIA do formato correto e pode ser reutilizado nos diferentes sistemas de rastreio.

Recomendamos que crie um GUID único para cada canal de oferta e distribuição para cada produto. Pode optar por utilizar um único GUID para os múltiplos canais de distribuição do produto se não quiser que o relatório seja dividido.

Se implementar um produto utilizando um modelo e estiver disponível tanto no Azure Marketplace como no GitHub, pode criar e registar dois GUIDS distintos:

*    Produto A no Mercado Azure
*    Produto A no GitHub

O reporte é feito pelo valor do parceiro (Microsoft Partner ID) e pelos GUIDs. Também pode rastrear GUIDs a um nível mais granular alinhado com cada plano dentro da sua oferta.

Para mais informações, consulte o uso do [cliente Tracking Azure com GUIDs FAQ](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution#faq).

## <a name="create-a-billing-profile"></a>Criar um perfil de faturação

Se estiver a publicar uma [Oferta De Sem](./create-new-customer-engagement-offer.md) Acordo para o Envolvimento com o Cliente & Power Apps ou [A Dinâmica 365 para Operações,](./create-new-operations-offer.md) precisa de completar o seu perfil de **faturação**.

O endereço de faturação é pré-povoado da sua entidade legal, e você pode atualizar este endereço mais tarde. Os campos de IDENTIFICAÇÃO DE IVA são opcionais.  O nome do país e o nome da empresa não podem ser editados.

## <a name="multi-user-account-management"></a>Gestão de conta multiutilizador

Partner Center alavanca [o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) para acesso e gestão de contas multiutilizadores. O Azure AD da sua organização está automaticamente associado à sua conta partner Center como parte do processo de inscrição.

## <a name="manage-users"></a>Gerir utilizadores

A secção **de Utilizadores** do Partner Center (em Definições de **Conta)** permite utilizar o Azure AD para gerir os utilizadores, grupos e aplicações Azure AD que têm acesso à sua conta Partner Center. A sua conta deve ter permissões [**de nível de Gestor**](#define-user-roles-and-permissions) para a conta de trabalho [(inquilino DaA Azure)](./company-work-accounts.md) na qual pretende adicionar ou editar utilizadores. Para gerir os utilizadores dentro de uma conta de trabalho/inquilino diferente, terá de assinar e depois voltar a inscrever-se como utilizador com permissões do **Gestor** nessa conta de trabalho/inquilino.

Uma vez que você é inscrito na sua conta de trabalho (inquilino Da AD Azure), você pode:

- [Adicionar ou remover utilizadores](#add-existing-users)
- [Alterar uma palavra-passe do utilizador](#change-a-user-password)
- [Adicionar ou remover grupos](#manage-groups)
- [Adicionar ou remover aplicações da AD Azure](#add-new-azure-ad-applications)
- [Gerir chaves para uma aplicação Azure AD](#manage-keys-for-an-azure-ad-application)
- [Definir funções e permissões de utilizadores](#define-user-roles-and-permissions)

Tenha em mente que todos os utilizadores do Partner Center (incluindo grupos e aplicações Azure AD) devem ter uma conta de trabalho ativa num [inquilino da Azure AD](#manage-tenants) que esteja associado à sua conta partner Center.

### <a name="add-existing-users"></a>Adicionar utilizadores existentes

Para adicionar utilizadores à sua conta Partner Center que já existem na conta de trabalho da sua empresa [(inquilino da AD Azure)](./company-work-accounts.md):

1. Vá aos **Utilizadores** (sob **as definições da Conta)** e selecione **Adicionar utilizadores**.
2. Selecione um ou mais utilizadores da lista que aparece. Pode utilizar a caixa de pesquisa para procurar utilizadores específicos.
*Se selecionar mais do que um utilizador para adicionar à sua conta Partner Center, deve atribuir-lhes a mesma função ou conjunto de permissões personalizadas. Para adicionar vários utilizadores com diferentes funções/permissões, repita estes passos para cada função ou conjunto de permissões personalizadas.
3. Quando terminar de escolher os utilizadores, clique em **Adicionar selecionado**.
4. Na secção **Funções,** especifique as funções ou permissões personalizadas para o utilizador ou os utilizadores selecionados.
5. Selecione **Guardar**.

### <a name="create-new-users"></a>Criar novos utilizadores

Para criar novas contas de utilizador, deve ter uma conta com permissões de [**administrador global.**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 

1. Vá para **utilizadores** (em **definições de Conta),** selecione **Adicionar utilizadores**e, em seguida, escolher Criar **novos utilizadores**.
1. Introduza um primeiro nome, apelido e nome de utilizador para cada novo utilizador. 
1. Se quiser que o novo utilizador tenha uma conta de administrador global no diretório da sua organização, verifique a caixa rotulada **Faça deste utilizador um administrador global no seu Azure AD, com total controlo sobre todos os recursos de diretório.** Isto dará ao utilizador acesso total a todas as funcionalidades administrativas do Azure AD da sua empresa. Eles poderão adicionar e gerir os utilizadores na conta de trabalho da sua organização (inquilino DaA Azure), embora não no Partner Center, a menos que conceda a conta o papel/permissões apropriadas.
1. Se verificou a caixa para **tornar este utilizador um administrador Global,** terá de fornecer um **e-mail** de recuperação de passwords para o utilizador recuperar a sua palavra-passe, se necessário.
1. Na secção **de adesão** ao Grupo, selecione quaisquer grupos aos quais pretenda que o novo utilizador pertença.
1. Na secção **Funções,** especifique as funções ou permissões personalizadas para o utilizador.
1. Selecione **Guardar**.

A criação de um novo utilizador no Partner Center também criará uma conta para esse utilizador na conta de trabalho (inquilino Azure AD) para a qual está inscrito. Fazer alterações no nome de um utilizador no Partner Center fará as mesmas alterações na conta de trabalho da sua organização (inquilino da AD Azure).

### <a name="invite-new-users-by-email"></a>Convide novos utilizadores por e-mail

Para convidar utilizadores que não fazem parte da sua conta de trabalho da empresa (inquilino da AD Azure) por e-mail, você deve ter uma conta com permissões [**de administrador global.**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

1. Vá aos **Utilizadores** (em **definições de Conta),** selecione **Adicionar utilizadores**e, em seguida, escolha utilizadores de convidar **por e-mail**.
2. Introduza um ou mais endereços de e-mail (até dez), separados por vírgulas ou pontos evívias.
3. Na secção **Funções,** especifique as funções ou permissões personalizadas para o utilizador.
4. Selecione **Guardar**.

Os utilizadores que convidou receberão um convite por e-mail para se juntarem à sua conta partner center. Na sua conta de trabalho será criada uma nova conta de hóspedes-utilizador (inquilino da AD Azure). Cada utilizador terá de aceitar o seu convite antes de poder aceder à sua conta.

Se precisar de reenviar um convite, visite a página **dos Utilizadores,** encontre o convite na lista de utilizadores, selecione o seu endereço de e-mail (ou o texto que diz *Convite pendente).* Em seguida, na parte inferior da página, selecione **convite Reenviar**.

> [!NOTE]
> Se a sua organização utilizar a integração de [diretórios](https://go.microsoft.com/fwlink/p/?LinkID=724033) para sincronizar o serviço de diretório no local com o seu Anúncio Azure, não poderá criar novos utilizadores, grupos ou aplicações Azure AD no Partner Center. Você (ou outro administrador no seu diretório no local) terá de criá-los diretamente no diretório no local antes de poder vê-los e adicioná-los no Partner Center.

### <a name="remove-a-user"></a>Remover um utilizador

Para remover um utilizador da sua conta de trabalho (inquilino da AD Azure), vá para **utilizadores** (segundo **as definições da Conta),** selecione o utilizador que gostaria de remover utilizando a caixa de verificação na coluna extrema-direita e, em seguida, escolha **Remover** das ações disponíveis. Aparecerá uma janela pop-up para confirmar que pretende remover o ou o utilizador selecionado.

### <a name="change-a-user-password"></a>Alterar uma palavra-passe do utilizador

Se um dos seus utilizadores precisar de alterar a sua palavra-passe, pode fazê-lo por si mesmo se forneceu um **e-mail** de recuperação de passwords ao criar a conta de utilizador. Também pode atualizar a palavra-passe de um utilizador seguindo os passos abaixo. Para alterar a palavra-passe de um utilizador na sua conta de trabalho da empresa (inquilino da Azure AD), deve ser inscrito numa conta com permissões de [**administrador global.**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) Note que isso irá alterar a palavra-passe do utilizador no seu inquilino Azure AD, juntamente com a palavra-passe que usam para aceder ao Partner Center.

1. Na página **Utilizadores** (em definições de **Conta),** selecione o nome da conta de utilizador que pretende editar.
2. Selecione o botão de **palavra-passe Reset** na parte inferior da página.
3. Aparecerá uma página de confirmação que mostra as informações de login para o utilizador, incluindo uma senha temporária. Certifique-se de imprimir ou copiar esta informação e fornecê-la ao utilizador, uma vez que não poderá aceder à senha temporária depois de sair desta página.

## <a name="manage-groups"></a>Gerir grupos

Os grupos permitem controlar várias funções e permissões de utilizadores em conjunto.

### <a name="add-an-existing-group"></a>Adicione um grupo existente

Para adicionar um grupo que já existe na conta de trabalho da sua organização (inquilino DaA Azure) à sua conta Partner Center:

1. A partir da página **Utilizadores** (em **definições de Conta),** selecione **Adicionar grupos**.
2. Selecione um ou mais grupos da lista que aparece. Pode utilizar a caixa de pesquisa para procurar grupos específicos.
Se selecionar mais de um grupo para adicionar à sua conta Partner Center, deve atribuir-lhes a mesma função ou conjunto de permissões personalizadas. Para adicionar vários grupos com diferentes papéis/permissões, repita estes passos para cada função ou conjunto de permissões personalizadas.
3. Quando terminar de escolher grupos, clique em **Adicionar selecionado**.
4. Na secção **Funções,** especifique as funções ou permissões personalizadas para os grupos selecionados. Todos os membros do grupo poderão aceder à sua conta Partner Center com as permissões que aplica ao grupo, independentemente das funções e permissões associadas à sua conta individual.
5. Selecione **Guardar**.

Quando adicionar um grupo existente, todos os utilizadores que forem membros desse grupo poderão aceder à sua conta Partner Center, com as permissões associadas à função atribuída pelo grupo.

### <a name="add-a-new-group"></a>Adicionar um novo grupo

Para adicionar um novo grupo à sua conta Partner Center:

1. A partir da página **Utilizadores** (em **definições de Conta),** selecione **Adicionar grupos**.
2. Na página seguinte, selecione **Novo grupo**.
3. Introduza o nome de exibição para o novo grupo.
4. Especifique as funções ou permissões personalizadas para o grupo. Todos os membros do grupo poderão aceder à sua conta Partner Center com as permissões que aplica aqui, independentemente das funções/permissões associadas à sua conta individual.
5. Selecione o(s) utilizador(s) para o novo grupo da lista que aparece. Pode utilizar a caixa de pesquisa para procurar utilizadores específicos.
6. Quando terminar de selecionar os utilizadores, clique em **Adicionar selecionado** para adicioná-los ao novo grupo.
7. Selecione **Guardar**.

Note que este novo grupo será criado na conta de trabalho da sua organização (inquilino da Azure AD) também, e não apenas na sua conta partner Center.

### <a name="remove-a-group"></a>Remover um grupo

Para remover um grupo da sua conta de trabalho (inquilino DaD Azure), vá para **utilizadores** (em definições de **Conta),** selecione o grupo que gostaria de remover usando a caixa de verificação na coluna extrema-direita e, em seguida, escolha **Remover** das ações disponíveis. Aparecerá uma janela pop-up para confirmar que pretende remover os grupos selecionados.

## <a name="manage-azure-ad-applications"></a>Gerir aplicações da AD Azure

Pode permitir aplicações ou serviços que façam parte do Anúncio Azure da sua empresa para aceder à sua conta Partner Center.

### <a name="add-existing-azure-ad-applications"></a>Adicione as aplicações adad azure existentes

Para adicionar aplicações que já existem no Diretório Ativo Azure da sua empresa:

1. Na página **Utilizadores** (em **definições de Conta),** selecione **Adicionar aplicações AD Azure**.
2. Selecione uma ou mais aplicações Azure AD da lista que aparece. Pode utilizar a caixa de pesquisa para procurar aplicações específicas do Azure AD. Se selecionar mais de uma aplicação Azure AD para adicionar à sua conta Partner Center, deve atribuir-lhes a mesma função ou conjunto de permissões personalizadas. Para adicionar várias aplicações Azure AD com diferentes funções/permissões, repita estes passos para cada função ou conjunto de permissões personalizadas.
3. Quando terminar de selecionar aplicações Azure AD, clique em **Adicionar selecionado**.
4. Na secção **Funções,** especifique as funções ou permissões personalizadas para as aplicações ads do Azure selecionadas.
5. Selecione **Guardar**.

### <a name="add-new-azure-ad-applications"></a>Adicione novas aplicações da Azure AD

Se quiser conceder ao Partner Center acesso a uma nova conta de aplicação Azure AD, pode criar uma na secção **Utilizadores.** Note que isto criará uma nova conta na sua conta de trabalho da empresa (inquilino da Azure AD), e não apenas na sua conta partner Center. Se estiver a utilizar principalmente esta aplicação Azure AD para autenticação do Partner Center, e não precisar de utilizadores para aceder diretamente, pode introduzir qualquer endereço válido para o URL de **Resposta** e **Id app URI**, desde que esses valores não sejam utilizados por qualquer outra aplicação Azure AD no seu diretório.

1. Na página **Utilizadores** (em **definições de Conta),** selecione **Adicionar aplicações AD Azure**.
2. Na página seguinte, selecione **aplicação New Azure AD**.
3. Introduza o **URL de resposta** para a nova aplicação Azure AD. Este é o URL onde os utilizadores podem iniciar sessão e usar a sua aplicação Azure AD (por vezes também conhecida como URL da App ou URL de Início de Sinal). O **URL de resposta** não pode ter mais de 256 caracteres e deve ser único dentro do seu diretório.
4. Insira o **APP ID URI** para a nova aplicação Azure AD. Este é um identificador lógico para o pedido de AD Azure que é apresentado quando um único pedido de inscrição é enviado para a AD Azure. Note que o **APP ID URI** deve ser único para cada aplicação Azure AD no seu diretório. Esta identificação não pode ter mais de 256 caracteres. Para mais informações sobre o App ID URI, consulte A Integração de [Aplicações com o Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-modify-supported-accounts#change-the-application-registration-to-support-different-accounts).
5. Na secção **Funções,** especifique as funções ou permissões personalizadas para a aplicação Azure AD.
6. Selecione **Guardar**.

Depois de adicionar ou criar uma aplicação Azure AD, pode voltar à secção **Utilizadores** e selecionar o nome da aplicação para rever as definições da aplicação, incluindo o ID do Inquilino, ID do Cliente, URL de resposta e ID da aplicação URI.

### <a name="remove-an-azure-ad-application"></a>Remover uma aplicação Azure AD

Para remover uma aplicação da sua conta de trabalho (inquilino da AD Azure), vá para **utilizadores** (segundo **as definições da Conta),** selecione a aplicação que gostaria de remover utilizando a caixa de verificação na coluna extrema-direita e, em seguida, escolha **Remover** das ações disponíveis. Aparecerá uma janela pop-up para confirmar que pretende remover as aplicações selecionadas.

### <a name="manage-keys-for-an-azure-ad-application"></a>Gerir chaves para uma aplicação Azure AD

Se a sua aplicação Azure AD ler e escrever dados no Microsoft Azure AD, precisará de uma chave. Pode criar chaves para uma aplicação Azure AD editando as suas informações no Partner Center. Também pode remover as teclas que já não são necessárias.

1. Na página **Utilizadores** (em **definições de Conta),** selecione o nome da aplicação Azure AD. Você verá todas as chaves ativas para a aplicação Azure AD, incluindo a data em que a chave foi criada e quando expirará. 
2. Para remover uma chave que já não é necessária, **selecione Remover**.
3. Para adicionar uma nova tecla, **selecione Adicionar nova tecla**.
4. Verá um ecrã mostrando o ID do **cliente** e **valores chave.** Certifique-se de imprimir ou copiar esta informação, uma vez que não poderá aceder novamente depois de deixar esta página.
5. Se quiser criar mais teclas, selecione **Adicionar outra tecla**.

## <a name="define-user-roles-and-permissions"></a>Definir funções e permissões de utilizadores

Os utilizadores da sua empresa podem ser atribuídos as seguintes funções e permissões para o programa Mercado Comercial no Partner Center:

- **Gestor**
  - Pode aceder a todas as funcionalidades da conta da Microsoft, exceto definições de impostos e pagamentos
  - Pode gerir utilizadores, funções e contas de trabalho (inquilinos)
- **Programador**
  - Pode gerir e publicar ofertas
  - Pode ver alguns relatórios de editores

> [!NOTE]
> Para o programa Mercado Comercial, não são utilizados os papéis de administração global, business contributor, financial contributor e marketer. Atribuir estas funções aos utilizadores não tem qualquer efeito. Apenas as funções de Gestor e Desenvolvedor concedem permissões aos utilizadores.

Para obter mais informações sobre a gestão de funções e permissões noutras áreas do Partner Center, tais como o Azure Ative Directory (AD), Cloud Solution Provider (CSP), Control Panel Vendor (CPV), Guest users ou Microsoft Partner Network (MPN), consulte as [funções e permissões](https://docs.microsoft.com/partner-center/permissions-overview)dos utilizadores no Partner Center.

## <a name="manage-tenants"></a>Gerir inquilinos

Um inquilino azure Ative Directory (AD), também referido como a sua "conta de trabalho" ao longo desta documentação, é uma representação da sua organização criada no portal Azure e ajuda-o a gerir uma instância específica dos serviços de nuvem da Microsoft para os seus utilizadores internos e externos. Se a sua organização subscreveu um serviço de cloud da Microsoft, como o Azure, o Microsoft Intune ou o Office 365, foi criado um inquilino da Azure AD para si.

Você pode configurar vários inquilinos para usar com partner center. Qualquer utilizador com a função **de Gestor** na conta Partner Center terá a opção de adicionar e remover os inquilinos da Azure AD da conta.  

### <a name="add-an-existing-tenant"></a>Adicione um inquilino existente

Para associar outro inquilino da AD Azure à sua conta Partner Center:

1. A partir da página **Tenants** (em **definições**de Conta), selecione **Associar outro inquilino Azure AD**.
2. Insira as suas credenciais Azure AD para o inquilino que pretende associar.
3. Reveja a organização e o nome de domínio para o seu inquilino Azure AD. Para completar a associação, selecione **Confirmar**.

Se a associação tiver sucesso, estará pronto para adicionar e gerir os utilizadores de conta na secção **Utilizadores** no Partner Center.

### <a name="create-a-new-tenant"></a>Criar um novo inquilino

Para criar um novo inquilino Azure AD com a sua conta Partner Center:

1. A partir da página **Tenants** (em **definições**de Conta), selecione **Criar um novo inquilino Azure AD**.
2. Insira as informações de diretório para o seu novo Anúncio Azure:
    - **Nome de domínio**: O nome único que usaremos para o seu domínio Azure AD, juntamente com ".onmicrosoft.com". Por exemplo, se entrasse em "exemplo", o seu domínio Azure AD seria "example.onmicrosoft.com".
    - **E-mail de contacto**: Um endereço de e-mail onde podemos contactá-lo sobre a sua conta, se necessário.
    - **Informação**da conta de utilizador do administrador global : O primeiro nome, apelido, nome de utilizador e palavra-passe que pretende utilizar para a nova conta de administrador global.
3. Selecione **Criar** para confirmar o novo domínio e informação da conta.
4. Inicie sessão com o seu novo nome de utilizador e senha de administrador global da AD Azure para começar a [adicionar e gerir os utilizadores.](#manage-users)

Para mais informações sobre a criação de novos inquilinos dentro do seu portal Azure, em vez de através do portal Partner Center, consulte o artigo [Criar um novo inquilino no Diretório Ativo azure.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

### <a name="remove-a-tenant"></a>Remova um inquilino

Para remover um inquilino da sua conta Partner Center, encontre o seu nome na página **dos Inquilinos** (nas **definições da Conta),** em seguida, selecione **Remover**. Serão solicitados a confirmar que quer remover o inquilino. Assim que o fizer, nenhum utilizador nesse inquilino poderá entrar na conta do Partner Center, e quaisquer permissões que tenha configurado para esses utilizadores serão removidas.

Quando você remover um inquilino, todos os utilizadores que foram adicionados à conta Do Partner Center desse inquilino deixarão de poder inscrever-se na conta.

> [!TIP]
> Não pode remover um inquilino se estiver atualmente inscrito no Partner Center usando uma conta no mesmo inquilino. Para remover um inquilino, deve inscrever-se no Partner Center como **Gerente** para outro inquilino que esteja associado à conta. Se houver apenas um inquilino associado à conta, esse inquilino só pode ser removido depois de iniciar sessão com a conta microsoft que abriu a conta.

## <a name="agreements"></a>Contratos

A secção de **Acordos** do Partner Center (em Definições de **Conta)** permite-lhe visualizar uma lista dos contratos de publicação que autorizou. Estes acordos estão listados de acordo com o nome e o número da versão, incluindo a data em que foi aceite e o nome do utilizador que aceitou o acordo.

**As ações necessárias** podem aparecer no topo desta página se houver atualizações de acordo que precisem da sua atenção. Para aceitar um acordo atualizado, leia primeiro a versão do Acordo vinculado e, em seguida, **selecione aceitar o acordo**.
