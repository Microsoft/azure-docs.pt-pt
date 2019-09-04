---
title: Como gerenciar uma conta do Marketplace comercial no Partner Center
description: Saiba como gerenciar uma conta do Marketplace comercial no Partner Center.
author: ChJenk
manager: evansma
ms.author: parthp
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: c28469e15841e2675c20868b74cf044159c4e9ca
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259181"
---
# <a name="how-to-manage-your-commercial-marketplace-account-in-partner-center"></a>Como gerenciar sua conta do Marketplace comercial no Partner Center 

Depois de [criar uma conta do Partner Center](./create-account.md), você poderá gerenciar sua conta e ofertas usando o [painel do Marketplace comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).

Neste artigo, vamos nos aprofundar em como gerenciar sua conta do Partner Center, incluindo como: 

- [Acessar as configurações de conta do Partner Center](#access-your-account-settings)
- [Localizar sua ID do editor, ID do vendedor, ID de usuário e locatários do Azure AD](#account-details)
- [Atualizar informações de contato](#contact-info)
- [Gerenciar detalhes financeiros (conta de pagamento, perfil de imposto, status de espera de pagamento)](#financial-details)
- [Configurar GUIDs de acompanhamento para monitorar o uso do cliente](#tracking-guids)
- [Gerir utilizadores](#manage-users)
- [Gerir grupos](#manage-groups)
- [Gerenciar aplicativos do Azure AD](#manage-azure-ad-applications)
- [Definir funções e permissões de usuário](#define-user-roles-and-permissions)
- [Gerenciar locatários do Azure AD (contas de trabalho)](#manage-tenants)
- [Gerenciar contratos do Partner Center](#agreements)


## <a name="access-your-account-settings"></a>Acessar as configurações da sua conta

Se você ainda não tiver feito isso, você (ou o administrador da sua organização) deverá acessar as [configurações de conta](https://partner.microsoft.com/dashboard/account/management) da sua conta do Partner Center para:
- verificar o status de verificação da conta da sua empresa
- Confirme sua ID do vendedor, ID do MPN, ID do editor e informações de contato, incluindo o aprovador da empresa e o contato do vendedor
- Configure os detalhes financeiros da sua empresa, incluindo isenção de impostos, se apropriado
- criar contas de usuário para qualquer pessoa que usará sua conta de negócios no Partner Center

### <a name="open-developer-settings"></a>Abrir configurações do desenvolvedor

As configurações de conta estão localizadas no canto superior direito do [painel do Marketplace comercial](https://partner.microsoft.com/dashboard/commercial-marketplace) no Partner Center. Selecione o ícone de engrenagem (próximo ao canto superior direito do painel) e, em seguida, selecione **configurações do desenvolvedor**. 

![Menu de configurações de conta no Partner Center](./media/dashboard-developer-settings.png)

Dentro **das configurações de conta**, você poderá exibir seu:
- **Detalhes da conta**: Tipo de conta e status da conta
- **IDs**do Publicador: ID do vendedor, ID de usuário, ID de editor, locatários do Azure AD, etc.
- **Informações de contato**: Nome de exibição do editor, nome do contato do vendedor, email, telefone e endereço
- **Detalhes financeiros**: Conta de pagamento, perfil de imposto e status de retenção de pagamento
- **Dispositivos**: Todos os dispositivos de teste associados à sua conta
- **GUIDs de acompanhamento**: Quaisquer GUIDs de acompanhamento associados à sua conta

### <a name="account-details"></a>Detalhes da conta

Na seção detalhes da conta, você pode ver informações básicas, como o **tipo de conta** (empresa ou indivíduo) e o **status de verificação** da sua conta. Durante o processo de verificação de sua conta, essas configurações exibirão cada etapa necessária, incluindo verificação de email, verificação de emprego e verificação de negócios. Você também pode atualizar seu email aqui e reenviar a verificação, se necessário. 

### <a name="publisher-ids"></a>IDs do Publicador

Na seção IDs do Publicador, você pode ver sua **ID do vendedor**, ID do **MPN**e ID do **Editor**. Esses valores são atribuídos pela Microsoft para identificar exclusivamente sua conta de desenvolvedor e não podem ser editados.

### <a name="contact-info"></a>Informação de contacto

Na seção informações de contato, você pode ver o **nome de exibição do editor**, **informações de contato do vendedor** (o nome do contato, email, número de telefone e endereço para o vendedor da empresa) e o aprovador **da empresa** (o nome, o email e o número de telefone do indivíduo com autoridade para aprovar decisões para a empresa). 

### <a name="financial-details"></a>Detalhes financeiros

Na seção detalhes financeiros, você pode fornecer ou atualizar suas informações financeiras se publicar aplicativos, suplementos ou serviços pagos. 

Se você planeja listar ofertas gratuitas, não precisa configurar uma conta de pagamento nem preencher os formulários de impostos. Se você mudar de ideia mais tarde e decidir que deseja vender pela Microsoft, poderá configurar sua conta de pagamento e preencher os formulários de impostos nesse momento. 

#### <a name="payout-account"></a>Conta de pagamento

Uma conta de pagamento é a conta bancária para a qual os prosseguimentos são enviados de suas vendas. Essa conta bancária deve estar no mesmo país em que você registrou sua conta do Partner Center.

Para configurar sua conta de pagamento, você precisa **associar sua conta da Microsoft**:
1. Em **configurações da conta**, na seção **detalhes financeiros** , selecione **associar sua conta da Microsoft**. 
2. Quando solicitado, entre com sua conta da Microsoft (MSA). Esta conta já não pode estar associada a outra conta do centro do parceiro. 
3. Para concluir a configuração da sua conta de pagamento, faça o log completamente fora do Partner Center e, em seguida, faça logon novamente com sua conta da Microsoft (em vez de sua conta corporativa). 

Agora que sua conta da Microsoft está associada, para adicionar uma conta de pagamento, você precisará:
- **Escolha um método de pagamento**: Conta bancária ou PayPal
- **Adicionar informações de pagamento**: Isso pode incluir escolher um tipo de conta (verificação ou economia), inserir o nome do titular da conta, o número da conta e o número de roteamento, endereço de cobrança, número de telefone ou endereço de email do PayPal. \* Para obter mais informações sobre como usar o PayPal como seu método de pagamento de conta e descobrir se há suporte em sua região de mercado, consulte [informações de PayPal](https://docs.microsoft.com/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info).

> [!IMPORTANT]
> Alterar sua conta de pagamento pode atrasar seus pagamentos em até um ciclo de pagamento. Esse atraso ocorre porque precisamos verificar a alteração da conta, assim como fazemos ao configurar a conta de pagamento pela primeira vez. Você ainda será pago pelo valor total depois que sua conta tiver sido verificada; todos os pagamentos devidos ao ciclo de pagamento atual serão adicionados ao próximo.  

#### <a name="tax-profile"></a>Perfil de imposto

Examine o status do perfil de imposto atual, confirmando o **tipo de entidade** correto e **as informações do certificado de imposto** são exibidas. Selecione **Editar** para atualizar ou concluir todos os formulários necessários.

Para estabelecer o status do imposto, você deve especificar seu país de residência e cidadania e concluir os formulários de impostos apropriados associados ao seu país/região.

Independentemente do seu país de residência ou cidadania, você deve preencher formulários de impostos de Estados Unidos para vender quaisquer ofertas por meio da Microsoft. Os parceiros que atendem a determinados requisitos de residência de Estados Unidos devem preencher um formulário IRS W-9. Outros parceiros fora do Estados Unidos devem preencher um formulário IRS W-8. Você pode preencher esses formulários online enquanto conclui seu perfil de imposto.

Um Estados Unidos número de identificação do contribuinte individual (ou certa) não é necessário para receber pagamentos da Microsoft ou para reivindicar benefícios de tratados de impostos.

Você pode concluir e enviar seus formulários de impostos eletronicamente no Partner Center; na maioria dos casos, você não precisa imprimir e enviar nenhum formulário.

Diferentes países e regiões têm requisitos de imposto diferentes. O valor exato que você deve pagar em impostos depende dos países e regiões em que você vende suas ofertas. A Microsoft remete as vendas e o imposto sobre o uso em alguns países. Esses países serão identificados no processo de listagem de sua oferta. Em outros países, dependendo de onde você está registrado, talvez seja necessário remeter vendas e usar impostos para suas vendas diretamente para a autoridade fiscal local. Além disso, as vendas continuarão a receber podem ser tributáveis como renda. Recomendamos que você entre em contato com a autoridade relevante para seu país ou região que possa ajudá-lo a determinar as informações de impostos certas para suas transações de vendas da Microsoft.

##### <a name="withholding-rates"></a>Taxas de retenção
As informações que você envia em seus formulários de imposto determinam a taxa de retenção de imposto apropriada. A taxa de retenção se aplica somente às vendas que você faz na Estados Unidos; as vendas feitas em locais fora dos EUA não estão sujeitas à retenção. As taxas de retenção variam, mas para a maioria dos desenvolvedores que se registram fora do Estados Unidos, a taxa padrão é 30%. Você tem a opção de reduzir essa taxa se o seu país tiver acordado uma Tratado de imposto de renda com o Estados Unidos.

##### <a name="tax-treaty-benefits"></a>Benefícios de tratados de imposto
Se você estiver fora do Estados Unidos, poderá aproveitar os benefícios de tratados de impostos. Esses benefícios variam de país para país e podem permitir que você reduza a quantidade de impostos que a Microsoft retém. Você pode reivindicar benefícios de tratados de imposto concluindo a parte II do formulário W-8BEN. Recomendamos que você se comunique com os recursos apropriados em seu país ou região para determinar se esses benefícios se aplicam a você.

[Saiba mais sobre os detalhes de impostos para desenvolvedores de aplicativos/jogos do Windows e publicadores do Azure Marketplace](https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps).

#### <a name="payout-hold-status"></a>Status de espera de pagamento

Por padrão, a Microsoft envia pagamentos mensalmente. No entanto, você tem a opção de colocar seus pagamentos em espera, o que impedirá o envio de pagamento à sua conta. Se você optar por colocar seus pagamentos em espera, continuaremos registrando qualquer receita obtida e forneceremos os detalhes em seu **Resumo de pagamento**. No entanto, não enviaremos nenhum pagamento à sua conta até que você remova a espera. 

Para posicionar seus pagamentos em espera, vá para **configurações de conta**. Em **detalhes financeiros**, na seção **status de retenção de pagamento** , alterne o controle deslizante para **ativado**. Você pode alterar o status de espera de pagamento a qualquer momento, mas lembre-se de que sua decisão afetará o pagamento mensal seguinte. Por exemplo, se você quiser manter o pagamento de abril, certifique-se de definir seu status de espera de pagamento como **ativado** antes do fim de março.

Depois de definir o status de suspensão de pagamento como **ativado**, todos os pagamentos estarão em espera até que você alterne o controle deslizante de volta para **desativado**. Ao fazer isso, você será incluído durante o próximo ciclo de pagamento mensal (desde que todos os limites de pagamentos aplicáveis tenham sido atendidos). Por exemplo, se você tiver seus pagamentos em espera, mas quiser ter um pagamento gerado em junho, certifique-se de alternar o status de espera de pagamento para **desativado** antes do final de maio.

> [!NOTE]
> A seleção de **status de retenção de pagamento** se aplica a **todas as** fontes de receita pagas por meio do Microsoft Partner Center, incluindo o Azure Marketplace, AppSource, Microsoft Store, publicidade, etc.). Não é possível selecionar diferentes status de retenção para cada fonte de receita.

### <a name="devices"></a>Dispositivos

As configurações de gerenciamento de dispositivo se aplicam somente à publicação UWP. [Saiba mais](https://docs.microsoft.com/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info).

### <a name="tracking-guids"></a>GUIDs de acompanhamento

Os GUIDs (identificadores globalmente exclusivos) são números de referência exclusivos (com 32 dígitos hexadecimais) que podem ser usados para controlar o uso do Azure. 

Para criar GUIDs para acompanhamento, você deve usar um gerador de GUID. A equipe de armazenamento do Azure criou um [formulário de gerador de GUID](https://aka.ms/StoragePartners) que enviará um email a um GUID do formato correto e poderá ser reutilizado nos diferentes sistemas de controle.

Recomendamos que você crie um GUID exclusivo para cada oferta e canal de distribuição para cada produto. Você pode optar por usar um único GUID para vários canais de distribuição do produto se não quiser que os relatórios sejam divididos.

Se você implantar um produto usando um modelo e ele estiver disponível no Azure Marketplace e no GitHub, você poderá criar e registrar 2 GUIDs distintos:

*   Produto A no Azure Marketplace
*   Produto A no GitHub

O relatório é feito pelo valor do parceiro (ID do parceiro da Microsoft) e pelos GUIDs. Você também pode acompanhar GUIDs em um nível mais granular, alinhando a cada plano dentro de sua oferta.

Para obter mais informações, consulte as [perguntas frequentes sobre como controlar o uso do cliente do Azure com GUIDs](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution#faq).



## <a name="multi-user-account-management"></a>Gerenciamento de contas de vários usuários

O Partner Center aproveita o [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) para acesso e gerenciamento de contas de vários usuários. O Azure AD da sua organização é associado automaticamente à sua conta do Partner Center como parte do processo de registro. 

## <a name="manage-users"></a>Gerir utilizadores

A seção **usuários** do Partner Center (em **configurações de conta**) permite que você use o Azure ad para gerenciar usuários, grupos e aplicativos do Azure AD que têm acesso à sua conta do Partner Center. Para gerenciar usuários, você deve estar conectado com sua [conta corporativa](./company-work-accounts.md) (o locatário do Azure ad associado). Para gerenciar usuários em uma conta de trabalho/locatário diferente, você precisará sair e entrar novamente como um usuário com permissões de **gerente** nessa conta/locatário de trabalho. 

Depois de entrar com sua conta corporativa (locatário do Azure AD), você pode:
- [Adicionar ou remover usuários](#add-or-remove-users)
- [Alterar uma senha de usuário](#change-a-user-password)
- [Adicionar ou remover grupos](#add-or-remove-users)
- [Adicionar ou remover aplicativos do Azure AD](#add-new-azure-ad-applications)
- [Gerenciar chaves para um aplicativo do Azure AD](#manage-keys-for-an-azure-ad-application)
- [Definir funções e permissões de usuário](#define-user-roles-and-permissions)


Tenha em mente que todos os usuários do Partner Center (incluindo grupos e aplicativos do Azure AD) devem ter uma conta corporativa ativa em um [locatário do Azure ad](#manage-tenants) associado à sua conta do Partner Center. 

### <a name="add-or-remove-users"></a>Adicionar ou remover usuários

Sua conta deve ter permissões de [**nível de gerente**](#define-user-roles-and-permissions) para a [conta de trabalho (locatário do Azure AD)](./company-work-accounts.md) na qual você deseja adicionar ou editar usuários.

#### <a name="add-existing-users"></a>Adicionar usuários existentes

Para adicionar usuários à sua conta do Partner Center que já existe na conta corporativa da sua empresa [(locatário do Azure AD)](./company-work-accounts.md):

1. Vá para **usuários** (em **configurações de conta**) e selecione **Adicionar usuários**.
2. Selecione um ou mais usuários na lista que aparece. Você pode usar a caixa de pesquisa para procurar usuários específicos.
\* Se você selecionar mais de um usuário para adicionar à sua conta do Partner Center, deverá atribuir a mesma função ou conjunto de permissões personalizadas. Para adicionar vários usuários com diferentes funções/permissões, repita essas etapas para cada função ou conjunto de permissões personalizadas.
3.  Quando terminar de escolher usuários, clique em **Adicionar selecionado**.
5.  Na seção **funções** , especifique a (s) função (ões) ou as permissões personalizadas para os usuários selecionados.
6.  Selecione **Guardar**.

#### <a name="create-new-users"></a>Criar novos usuários

Para criar contas de usuário totalmente novas, você deve ter uma conta com permissões de [**administrador global**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) . 

1. Vá para **usuários** (em **configurações de conta**), selecione **Adicionar usuários**e, em seguida, escolha **criar novos usuários**.
1. Insira um nome, o sobrenome e o nome de usuário para cada novo usuário. 
1. Se você quiser que o novo usuário tenha uma conta de administrador global no diretório da sua organização, marque a caixa rotulada **tornar esse usuário um administrador global no Azure AD, com controle total sobre todos os recursos de diretório**. Isso dará ao usuário acesso completo a todos os recursos administrativos no Azure AD da sua empresa. Eles poderão adicionar e gerenciar usuários na conta corporativa da sua organização (locatário do Azure AD), embora não estejam no Partner Center, a menos que você conceda à conta as permissões/funções apropriadas. 
1. Se tiver marcado a caixa para **tornar esse usuário um administrador global**, você precisará fornecer um **email de recuperação de senha** para que o usuário recupere sua senha, se necessário.
1. Na seção **Associação de grupo** , selecione os grupos aos quais você deseja que o novo usuário pertença.
1. Na seção **funções** , especifique a (s) função (ões) ou as permissões personalizadas para o usuário.
1. Selecione **Guardar**.

A criação de um novo usuário no Partner Center também criará uma conta para esse usuário na conta de trabalho (locatário do Azure AD) na qual você está conectado. Fazer alterações no nome de um usuário no Partner Center fará as mesmas alterações na conta corporativa da sua organização (locatário do Azure AD).

#### <a name="invite-new-users-by-email"></a>Convidar novos usuários por email

Para convidar usuários que atualmente não fazem parte de sua conta de trabalho da empresa (locatário do Azure AD) por email, você deve ter uma conta com permissões de [**administrador global**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) . 

1. Vá para **usuários** (em **configurações de conta**), selecione **Adicionar usuários**e, em seguida, escolha **convidar usuários por email**.
2. Insira um ou mais endereços de email (até dez), separados por vírgulas ou pontos-e-vírgulas.
3. Na seção **funções** , especifique a (s) função (ões) ou as permissões personalizadas para o usuário.
4. Selecione **Guardar**.

Os usuários que você convidou receberão um convite por email para ingressar na sua conta do Partner Center. Uma nova conta de usuário convidado será criada em sua conta corporativa (locatário do Azure AD). Cada usuário precisará aceitar seu convite antes que possa acessar sua conta.

Se você precisar reenviar um convite, visite a página **usuários** , localize o convite na lista de usuários, selecione seu endereço de email (ou o texto que informa o *convite pendente*). Em seguida, na parte inferior da página, selecione **reenviar convite**.
 

> [!NOTE]
> Se sua organização usar a [integração de diretórios](https://go.microsoft.com/fwlink/p/?LinkID=724033) para sincronizar o serviço de diretório local com o Azure AD, você não poderá criar novos usuários, grupos ou aplicativos do Azure AD no Partner Center. Você (ou outro administrador em seu diretório local) precisará criá-los diretamente no diretório local antes que você possa vê-los e adicioná-los no Partner Center.

#### <a name="remove-a-user"></a>Remover um utilizador

Para remover um usuário de sua conta de trabalho (locatário do Azure AD), vá para **usuários** (em **configurações de conta**), selecione o usuário que você deseja remover usando a caixa de seleção na coluna mais à direita e escolha **remover** das ações disponíveis. Uma janela pop-up será exibida para que você confirme que deseja remover os usuários selecionados.

#### <a name="change-a-user-password"></a>Alterar uma senha de usuário

Se um dos seus usuários precisar alterar sua senha, ele poderá fazê-lo se você tiver fornecido um **email de recuperação de senha** ao criar a conta de usuário. Você também pode atualizar a senha de um usuário seguindo as etapas abaixo. Para alterar a senha de um usuário em sua conta corporativa corporativa (locatário do Azure AD), você deve estar conectado a uma conta com permissões de [**administrador global**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) . Observe que isso alterará a senha do usuário em seu locatário do Azure AD, juntamente com a senha que eles usam para acessar o Partner Center.

1.  Na página **usuários** (em **configurações de conta**), selecione o nome da conta de usuário que você deseja editar.
2.  Selecione o botão **Redefinir senha** na parte inferior da página.
3.  Uma página de confirmação será exibida mostrando as informações de logon para o usuário, incluindo uma senha temporária. Certifique-se de imprimir ou copiar essas informações e fornecê-las ao usuário, pois você não poderá acessar a senha temporária depois de sair dessa página.


## <a name="manage-groups"></a>Gerir grupos

Os grupos permitem controlar várias funções de usuário e todas as permissões juntas.

#### <a name="add-an-existing-group"></a>Adicionar um grupo existente

Para adicionar um grupo que já existe na conta corporativa da sua organização (locatário do Azure AD) à sua conta do Partner Center: 

1.  Na página **usuários** (em **configurações de conta**), selecione **Adicionar grupos**.
2.  Selecione um ou mais grupos na lista que aparece. Você pode usar a caixa de pesquisa para procurar grupos específicos.
Se você selecionar mais de um grupo para adicionar à sua conta do Partner Center, deverá atribuir a mesma função ou conjunto de permissões personalizadas. Para adicionar vários grupos com diferentes funções/permissões, repita essas etapas para cada função ou conjunto de permissões personalizadas.
3.  Quando terminar de escolher grupos, clique em **Adicionar selecionado**.
4.  Na seção **funções** , especifique a (s) função (ões) ou as permissões personalizadas para os grupos selecionados. Todos os membros do grupo poderão acessar sua conta do Partner Center com as permissões que você aplicar ao grupo, independentemente das funções e permissões associadas à sua conta individual.
5.  Selecione **Guardar**.

Quando você adiciona um grupo existente, todos os usuários que são membros desse grupo poderão acessar sua conta do Partner Center, com as permissões associadas à função atribuída do grupo.

#### <a name="add-a-new-group"></a>Adicionar um novo grupo

Para adicionar um grupo novo à marca à sua conta do Partner Center: 

1.  Na página **usuários** (em **configurações de conta**), selecione **Adicionar grupos**.
2.  Na página seguinte, selecione **novo grupo**.
3.  Insira o nome de exibição para o novo grupo.
4.  Especifique a (s) função (ões) ou as permissões personalizadas para o grupo. Todos os membros do grupo poderão acessar sua conta do Partner Center com as permissões que você aplicar aqui, independentemente das funções/permissões associadas à sua conta individual.
5.  Selecione usuário (s) para o novo grupo na lista que aparece. Você pode usar a caixa de pesquisa para procurar usuários específicos.
6.  Quando terminar de selecionar usuários, clique em **Adicionar selecionado** para adicioná-los ao novo grupo.
7.  Selecione **Guardar**.

Observe que esse novo grupo será criado na conta corporativa da sua organização (locatário do Azure AD) também, não apenas na sua conta do Partner Center.

#### <a name="remove-a-group"></a>Remover um grupo

Para remover um grupo de sua conta corporativa (locatário do Azure AD), vá para **usuários** (em **configurações de conta**), selecione o grupo que você deseja remover usando a caixa de seleção na coluna mais à direita e escolha **remover** das ações disponíveis. Uma janela pop-up será exibida para que você confirme que deseja remover os grupos selecionados.

## <a name="manage-azure-ad-applications"></a>Gerenciar aplicativos do Azure AD

Você pode permitir que aplicativos ou serviços que fazem parte do Azure AD da sua empresa acessem sua conta do Partner Center. 

#### <a name="add-existing-azure-ad-applications"></a>Adicionar aplicativos existentes do Azure AD 

Para adicionar aplicativos que já existem no Azure Active Directory da sua empresa: 

1.  Na página **usuários** (em **configurações de conta**), selecione **adicionar aplicativos do Azure ad**.
2.  Selecione um ou mais aplicativos do Azure AD na lista exibida. Você pode usar a caixa de pesquisa para procurar aplicativos específicos do Azure AD. Se você selecionar mais de um aplicativo do Azure AD para adicionar à sua conta do Partner Center, deverá atribuir a mesma função ou conjunto de permissões personalizadas. Para adicionar vários aplicativos do Azure AD com diferentes funções/permissões, repita essas etapas para cada função ou conjunto de permissões personalizadas.
3.  Quando terminar de selecionar aplicativos do Azure AD, clique em **Adicionar selecionado**.
5.  Na seção **funções** , especifique a (s) função (ões) ou permissões personalizadas para os aplicativos do Azure ad selecionados.
6.  Selecione **Guardar**.

#### <a name="add-new-azure-ad-applications"></a>Adicionar novos aplicativos do Azure AD 

Se você quiser conceder acesso à central de parceiros a uma conta de aplicativo do Azure AD de marca nova, poderá criar uma na seção **usuários** . Observe que isso criará uma nova conta em sua conta corporativa corporativa (locatário do Azure AD), não apenas na sua conta do Partner Center. Se você estiver usando principalmente esse aplicativo do Azure AD para a autenticação do Partner Center e não precisar que os usuários o acessem diretamente, poderá inserir qualquer endereço válido para a **URL de resposta** e o URI da **ID do aplicativo**, desde que esses valores não sejam usados por nenhum outro Azure Aplicativo do AD em seu diretório.

1.  Na página **usuários** (em **configurações de conta**), selecione **adicionar aplicativos do Azure ad**.
2.  Na página seguinte, selecione **novo aplicativo Azure ad**.
3.  Insira a **URL de resposta** para o novo aplicativo do Azure AD. Essa é a URL em que os usuários podem entrar e usar seu aplicativo do Azure AD (às vezes, também conhecido como URL do aplicativo ou URL de logon). A **URL de resposta** não pode ter mais de 256 caracteres e deve ser exclusiva em seu diretório.
4.  Insira o **URI da ID do aplicativo** para o novo aplicativo do Azure AD. Esse é um identificador lógico para o aplicativo do Azure AD que é apresentado quando uma solicitação de logon único é enviada ao Azure AD. Observe que o **URI da ID do aplicativo** deve ser exclusivo para cada aplicativo do Azure AD em seu diretório. Esta ID não pode ter mais de 256 caracteres. Para obter mais informações sobre o URI da ID do aplicativo, consulte [integrando aplicativos com Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/quickstart-modify-supported-accounts#change-the-application-registration-to-support-different-accounts).
5.  Na seção **funções** , especifique a (s) função (ões) ou as permissões personalizadas para o aplicativo do Azure AD.
6.  Selecione **Guardar**.

Depois de adicionar ou criar um aplicativo do Azure AD, você pode retornar à seção **usuários** e selecionar o nome do aplicativo para examinar as configurações do aplicativo, incluindo a ID do locatário, a ID do cliente, a URL de resposta e o URI da ID do aplicativo.

#### <a name="remove-an-application"></a>Remover uma aplicação

Para remover um aplicativo de sua conta de trabalho (locatário do Azure AD), vá para **usuários** (em **configurações de conta**), selecione o aplicativo que você deseja remover usando a caixa de seleção na coluna mais à direita e, em seguida, escolha **remover** da ações disponíveis. Uma janela pop-up será exibida para que você confirme que deseja remover os aplicativos selecionados.

#### <a name="manage-keys-for-an-azure-ad-application"></a>Gerenciar chaves para um aplicativo do Azure AD

Se o seu aplicativo do Azure AD lê e grava dados em Microsoft Azure AD, ele precisará de uma chave. Você pode criar chaves para um aplicativo do Azure AD editando suas informações no Partner Center. Você também pode remover as chaves que não são mais necessárias.

1.  Na página **usuários** (em **configurações de conta**), selecione o nome do aplicativo do Azure AD. Você verá todas as chaves ativas para o aplicativo do Azure AD, incluindo a data em que a chave foi criada e a expiração. 
2. Para remover uma chave que não é mais necessária, selecione **remover**.
3.  Para adicionar uma nova chave, selecione **Adicionar nova chave**.
4.  Você verá uma tela mostrando os valores de ID e **chave**do **cliente** . Certifique-se de imprimir ou copiar essas informações, pois você não poderá acessá-las novamente depois de sair desta página.
4.  Se você quiser criar mais chaves, selecione **adicionar outra chave**.


## <a name="define-user-roles-and-permissions"></a>Definir funções e permissões de usuário

Os usuários da sua empresa podem receber as seguintes funções e permissões para o programa comercial do Marketplace no Partner Center:

- **Manager**
  - Pode acessar todos os recursos do conta Microsoft, exceto as configurações de imposto e pagamento
  - Pode gerenciar usuários, funções e contas de trabalho (locatários)
- **Programador**
  - Pode gerenciar e publicar ofertas
  - Pode exibir alguns relatórios do Publicador

> [!NOTE]
> Para o programa do Marketplace comercial, o administrador global, colaborador de negócios, colaborador financeiro e funções de mercado não são usados. A atribuição dessas funções a usuários não tem nenhum efeito. Somente as funções gerente e desenvolvedor concedem permissões aos usuários.

Para obter mais informações sobre como gerenciar funções e permissões em outras áreas do Partner Center, como Azure Active Directory (AD), provedor de soluções de nuvem (CSP), fornecedor do painel de controle (CPV), usuários convidados ou Microsoft Partner Network (MPN), consulte [atribuir funções de usuários e permissões no Partner Center](https://docs.microsoft.com/partner-center/permissions-overview).


## <a name="manage-tenants"></a>Gerenciar locatários

Um locatário do Azure Active Directory (AD), também conhecido como sua "conta de trabalho", em toda esta documentação, é uma representação da sua organização configurada na portal do Azure e ajuda a gerenciar uma instância específica dos serviços de nuvem da Microsoft para seu interno e usuários externos. Se sua organização assinou um serviço de nuvem da Microsoft, como o Azure, Microsoft Intune ou o Office 365, um locatário do Azure AD foi estabelecido para você. 

Você pode configurar vários locatários para usar com o Partner Center. Qualquer usuário com a função de **gerente** na conta do Partner Center terá a opção de adicionar e remover locatários do Azure ad da conta.  

### <a name="add-an-existing-tenant"></a>Adicionar um locatário existente

Para associar outro locatário do Azure AD à sua conta do Partner Center:

1.  Na página **locatários** (em **configurações de conta**), selecione **associar outro locatário do Azure ad**.
2. Insira suas credenciais do Azure AD para o locatário que você deseja associar.
3. Examine a organização e o nome de domínio do seu locatário do Azure AD. Para concluir a associação, selecione **confirmar**.

Se a associação for bem-sucedida, você estará pronto para adicionar e gerenciar usuários da conta na seção **usuários** no Partner Center.

### <a name="create-a-new-tenant"></a>Criar um novo locatário

Para criar um novo locatário do Azure AD com sua conta do Partner Center:

1.  Na página **locatários** (em **configurações de conta**), selecione **criar um novo locatário do Azure ad**.
2. Insira as informações de diretório para seu novo Azure AD:
    - **Nome de domínio**: O nome exclusivo que usaremos para seu domínio do Azure AD, junto com ". onmicrosoft.com". Por exemplo, se você inseriu "example", seu domínio do Azure AD seria "example.onmicrosoft.com".
    - **Email de contato**: Um endereço de email no qual podemos contatá-lo sobre sua conta, se necessário.
    - **Informações da conta de usuário de administrador global**: Nome, sobrenome, nome de usuário e senha que você deseja usar para a nova conta de administrador global.
3. Selecione **criar** para confirmar as novas informações de domínio e conta.
4. Entre com seu novo nome de usuário e senha de administrador global do Azure AD para começar a [Adicionar e gerenciar usuários](#manage-users).

Para obter mais informações sobre como criar novos locatários dentro de seu portal do Azure, em vez de por meio do portal do Partner Center, consulte o artigo [criar um novo locatário no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant).

### <a name="remove-a-tenant"></a>Remover um locatário

Para remover um locatário de sua conta do Partner Center, localize seu nome na página locatários (em **configurações da conta**) e selecione **remover**. Você será solicitado a confirmar que deseja remover o locatário. Depois de fazer isso, nenhum usuário nesse locatário poderá entrar na conta do Partner Center e todas as permissões configuradas para esses usuários serão removidas.

Quando você remove um locatário, todos os usuários que foram adicionados à conta do Partner Center desse locatário não poderão mais entrar na conta.

> [!TIP]
> Você não poderá remover um locatário se estiver conectado no momento ao Partner Center usando uma conta no mesmo locatário. Para remover um locatário, você deve entrar no Partner Center como um **gerente** para outro locatário que está associado à conta. Se houver apenas um locatário associado à conta, esse locatário só poderá ser removido depois de entrar com o conta Microsoft que abriu a conta.


## <a name="agreements"></a>Contratos

A seção **contratos** do Partner Center (em **configurações de conta**) permite que você veja uma lista dos acordos de publicação que você autorizou. Esses contratos são listados de acordo com o nome e o número de versão, incluindo a data em que foi aceito e o nome do usuário que aceitou o contrato. 

As **ações necessárias** podem aparecer na parte superior desta página se houver atualizações de contrato que precisam de sua atenção. Para aceitar um contrato atualizado, primeiro leia a versão do contrato vinculado e, em seguida, selecione **aceitar contrato**. 


## <a name="next-steps"></a>Passos Seguintes

- [Criar uma nova oferta de SaaS](./create-new-saas-offer.md)
