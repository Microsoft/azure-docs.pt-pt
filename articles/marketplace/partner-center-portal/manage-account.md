---
title: Como gerir uma conta de Marketplace comercial no Centro de parceiros
description: Saiba como gerir uma conta do Marketplace comercial no Centro de parceiros.
author: mattwojo
manager: evansma
ms.author: parthp
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: e0c9f1fcf2d9d7e806645f1bdf9e8a6c74245a60
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67619323"
---
# <a name="how-to-manage-your-commercial-marketplace-account-in-partner-center"></a>Como gerir a sua conta do Marketplace comercial no Centro de parceiros 

Assim que tiver [criou uma conta do Centro de parceiros](./create-account.md), pode gerir a sua conta e ofertas utilizando o [dashboard Marketplace comerciais](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).

Neste artigo, nos aprofundaremos nas como gerir a sua conta do Centro de parceiros, incluindo como: 

- [As definições da sua conta do Centro de parceiros de acesso](#access-your-account-settings)
- [Localizar sua ID de publicador, ID do vendedor, ID de utilizador e inquilinos do Azure AD](#account-details)
- [Atualizar informações de contacto](#contact-info)
- [Gerir dados financeiros (conta dividendos e impostos perfil, estado de espera de dividendos)](#financial-details)
- [Configurar o controlo GUIDs para monitorizar a utilização do cliente](#tracking-guids)
- [Utilizadores do Gestor](#manage-users)
- [Gestor de grupos](#manage-groups)
- [Aplicações Manager do Azure AD](#manage-azure-ad-applications)
- [Definir funções de utilizador e permissões](#define-user-roles-and-permissions)
- [Gerir inquilinos do Azure AD (contas de trabalho)](#manage-tenants)
- [Contratos de centro de parceiros do Gestor](#agreements)


## <a name="access-your-account-settings"></a>Definições da sua conta de acesso

Se ainda não o tiver feito, (ou o administrador da organização) deve acessar os [definições da conta](https://partner.microsoft.com/dashboard/account/management) para a sua conta do Centro de parceiros para:
- verificar o estado de verificação de conta da sua empresa
- Confirme a sua ID do vendedor, o MPN ID, o ID de publicador e, entre em contato com informações, incluindo o contacto da empresa de aprovador e vendedor
- configurar os detalhes financeiro da sua empresa, incluindo isenções de imposto sobre se tal for apropriado
- criar contas de utilizador para qualquer pessoa que irá utilizar a sua conta de empresa no Centro de parceiros

### <a name="open-developer-settings"></a>Abra as definições de programador

Definições da conta está localizada no canto superior direito da sua [dashboard Marketplace comerciais](https://partner.microsoft.com/dashboard/commercial-marketplace) no Centro de parceiros. Selecione o ícone de engrenagem (perto do canto superior direito do dashboard) e, em seguida, selecione **definições de programador**. 

![Menu de definições de conta no Centro de parceiros](./media/dashboard-developer-settings.png)

Dentro **definições da conta**, poderá ver sua:
- **Detalhes da conta**: Tipo de conta e o estado da conta
- **IDs de publicador**: ID do vendedor, ID de utilizador, ID de publicador, inquilinos do Azure AD, etc.
- **Informações de contacto**: Nome a apresentar do publicador, nome do contacto do vendedor, e-mail, telefone e endereço
- **Detalhes financeiros**: Conta dividendos, o perfil de imposto e o estado de espera de dividendos
- **dispositivos**: Quaisquer dispositivos de testes associados à sua conta
- **Controlo GUIDs**: Qualquer controlo GUIDs associado com a sua conta

### <a name="account-details"></a>Detalhes da conta

Na secção de detalhes de conta, pode ver informações básicas, como o seu **tipo de conta** (empresa ou indivíduo) e o **estado da verificação** da sua conta. Durante o processo de verificação de conta, estas definições apresentará cada passo é necessário, incluindo a verificação de e-mail, verificação de emprego e verificação de negócios. Também pode atualizar o seu e-mail aqui e reenviar a verificação, se necessário. 

### <a name="publisher-ids"></a>IDs de publicador

Na secção de IDs de publicador, pode ver seus **ID do vendedor**, **MPN ID**, e **ID de publicador**. Estes valores são atribuídos pela Microsoft para identificar exclusivamente a sua conta de desenvolvedor e não podem ser editados.

### <a name="contact-info"></a>Informações de contacto

Na secção de informações de contacto, pode ver seus **nome a apresentar do publicador**, **informações de contacto do vendedor** (o nome de contato, e-mail, número de telefone e endereço para o vendedor de empresa) e o **empresa aprovador** (o nome, e-mail e número de telefone do indivíduo com autoridade para aprovar decisões para a empresa). 

### <a name="financial-details"></a>Detalhes financeiros

Na secção de detalhes financeiros, pode fornecer ou atualizar as suas informações financeiras se publicar aplicações pagas, suplementos ou serviços. 

Se pretender apenas listar ofertas gratuitas, não precisa de configurar uma conta de dividendos ou preencher todos os formulários de imposto. Se mudar de ideias mais tarde e decidir que pretende vender através da Microsoft, pode configurar a sua conta de dividendos e preencher os formulários de imposto sobre nesse momento. 

#### <a name="payout-account"></a>Conta de dividendos

Uma conta de dividendos é a conta bancária para a qual receita é enviadas do suas vendas. Essa conta bancária tem de estar no mesmo país em que registrou a sua conta do Centro de parceiros.

Para configurar a sua conta de dividendos, precisa **associar a sua Account Microsoft**:
1. Na **definições da conta**, no **detalhes financeiros** secção, selecione **associar a sua Account Microsoft**. 
2. Quando lhe for pedido, inicie sessão com a sua conta Microsoft (MSA). Esta conta já não pode ser associada a outra conta do Centro de parceiros. 
3. Para concluir a configuração da sua conta de dividendos, terminar sessão completamente no Centro de parceiros, em seguida, inicie sessão novamente com sua Account Microsoft (em vez de sua conta profissional). 

Agora que sua Account Microsoft está associado, para adicionar uma conta de dividendos, terá:
- **Escolha um método de pagamento**: Conta bancária ou PayPal
- **Adicionar informações de pagamento**: Isto pode incluir a escolher um tipo de conta (a verificação ou poupanças), introduzir o nome do titular de conta, o número da conta e encaminhamento de número, o endereço para faturação, o número de telefone ou o endereço de e-mail de PayPal. \* Para obter mais informações sobre como utilizar PayPal, como o seu método de pagamento da conta e para saber se é suportada na sua região de mercado, consulte [PayPal informações](https://docs.microsoft.com/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info).

> [!IMPORTANT]
> Alterar a sua conta de dividendos pode atrasar os pagamentos pelo máximo de um ciclo de pagamento. Este atraso ocorre porque precisamos de verificar a alteração da conta, tal como fazemos quando configurar a conta de dividendos pela primeira vez. Ainda vai ser pago pela quantia total após a verificação da sua conta; qualquer pagamentos devido para pagamento atual ciclo será adicionado para o seguinte.  

#### <a name="tax-profile"></a>Perfil de imposto

Examinar seu status de perfil atual imposto, confirmar o correto **tipo de entidade** e **informações do certificado de imposto sobre** é apresentado. Selecione **editar** atualizar ou concluir qualquer necessário formulários.

Para estabelecer o estado imposto, tem de especificar o seu país de residência e cidadania e concluir os formulários de impostos apropriado associados com o seu país/região.

Independentemente do seu país de residência ou cidadania, deve preencher os formulários de imposto sobre Estados Unidos para vender quaisquer ofertas através do Microsoft. Parceiros que satisfazem determinados requisitos de residência dos Estados Unidos devem preencher um formulário de IRS W-9. Outros parceiros fora dos Estados Unidos devem preencher um formulário de IRS W-8. Pode preencher esses formulários online à medida que concluir o seu perfil de imposto.

Um número de identificação de Taxpayer individuais dos Estados Unidos (ou ITIN) não é necessário para receber os pagamentos da Microsoft ou para reclamar o imposto sobre treaty benefícios.

Pode concluir e enviar os formulários de imposto sobre eletronicamente no Centro de parceiros na maioria dos casos, não terá de impressão e correio de todos os formulários.

Diferentes países e regiões têm requisitos de imposto diferente. A quantidade exata de que tem de pagar impostos depende de países e regiões onde vende as suas ofertas. Microsoft remits vendas e imposto sobre o uso em seu nome em alguns países. Nestes países serão identificados no processo de listagem a sua oferta. Em outros países, consoante onde estão registadas, terá de remeter vendas e imposto sobre o uso de suas vendas diretamente à autoridade tributária local. Além disso, a receita de vendas que receberá pode ser tributáveis como renda. É altamente recomendável que entre em contato com a autoridade relevante para o seu país ou região que melhor pode ajudar a determinar as informações de imposto sobre certa para suas transações de vendas da Microsoft.

##### <a name="withholding-rates"></a>Taxas de retenção
As informações que submeter em seus formulários de imposto sobre determina fiscal competente, taxa de retenção. A taxa de retenção só se aplica a vendas efetuadas em Estados Unidos; vendas em localizações fora dos EUA não estão sujeitas a retenção. As taxas de retenção variam, mas para a maioria dos desenvolvedores registar fora dos Estados Unidos, a taxa de predefinição é 30 %). Tem a opção de reduzir esta tarifa se o seu país/região concordou em treaty um imposto de renda com os Estados Unidos.

##### <a name="tax-treaty-benefits"></a>Imposto treaty benefícios
Se estiver fora dos Estados Unidos, poderá tirar partido do imposto sobre benefícios treaty. Esses benefícios variam de país para país e permitem-lhe reduzir a quantidade de impostos que withholds da Microsoft. Pode solicitar o imposto sobre treaty benefícios ao concluir a parte II do formulário W 8BEN. Recomendamos que se comunica com recursos adequados no seu país ou região para determinar se esses benefícios se aplicar a.

[Saiba mais sobre os detalhes de imposto para programadores de aplicações/jogo do Windows e os publicadores do Azure Marketplace](https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps).

#### <a name="payout-hold-status"></a>Estado de espera de dividendos

Por predefinição, a Microsoft envia pagamentos mensalmente. No entanto, tem a opção de colocar seu dividendos em espera, o que evitará a enviar de pagamentos para a sua conta. Se optar por colocar sua dividendos em espera, vamos continuar a gravar qualquer receita que ganha e forneça os detalhes no seu **resumo de dividendos**. No entanto, apenas enviaremos qualquer pagamentos à sua conta até que remove a suspensão. 

Para colocar os pagamentos em espera, aceda a **definições da conta**. Sob **detalhes financeiros**, na **dividendos reter estado** secção, ativar/desativar o controlo de deslize para **no**. Pode alterar seu status de espera de dividendos em qualquer altura, mas lembre-se de que a sua decisão irá afetar os dividendos mensal seguinte. Por exemplo, se quiser manter dividendos de Abril, certifique-se de definir o estado de espera de dividendos para **no** antes do final de Março.

Depois de definir seu dividendos manter o estado para **no**, todos os dividendos estarão em espera até que a mudar o controlo de deslize para **desativar**. Ao fazê-lo, estará incluído durante o próximo ciclo de dividendos mensal (desde que tiverem sido cumpridos quaisquer limiares de pagamento aplicável). Por exemplo, se já teve seu dividendos em espera, mas seria gostam de ter um dividendos gerado em Junho, em seguida, certifique-se Ativar/desativar o dividendos reter estado para **desativar** antes do final de Maio.

> [!NOTE]
> Sua **dividendos manter o estado** seleção aplica-se ao **todos os** fontes de receita pagos por meio do Microsoft Partner Center, incluindo o Azure Marketplace, AppSource, Microsoft Store, publicidade, etc.). Não é possível selecionar Estados de suspensão diferentes para cada origem de receita.

### <a name="devices"></a>Dispositivos

As definições de gestão do dispositivo, aplicam-se apenas a publicação do UWP. [Saiba mais](https://docs.microsoft.com/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info).

### <a name="tracking-guids"></a>Os GUIDs de controlo

Globalmente identificadores exclusivos (GUIDs) são números de referência exclusivo (com 32 dígitos hexadecimais), que podem ser utilizados para controlar a utilização do Azure. 

Para criar os GUIDs para acompanhamento, deve utilizar um gerador GUID. A equipe de armazenamento do Azure criou um [formulário de gerador GUID](https://aka.ms/StoragePartners) que será enviada uma mensagem é um GUID no formato correto e pode ser reutilizado entre os sistemas de controle diferentes.

Recomendamos que crie um GUID exclusivo para cada oferta e a distribuição do canal para cada produto. Pode optar por utilizar um GUID único para vários canais de distribuição do produto se não pretender que o relatório ser dividida.

Se implementar um produto ao utilizar um modelo e está disponível no Marketplace do Azure e no GitHub, pode criar e registar 2 GUIDS distintos:

*   Produto A no Azure Marketplace
*   Produto A no GitHub

A comunicação é efetuada pelo valor de parceiro (ID de parceiro da Microsoft) e os GUIDs. Também pode controlar GUIDs num nível mais granular, alinhando a cada plano na sua oferta.

Para obter mais informações, consulte a [utilização do cliente de controlo do Azure com perguntas frequentes de GUIDs](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution#faq).



## <a name="multi-user-account-management"></a>Gestão de contas de vários utilizadores

Tira partido do Centro de parceiros [do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) para a gestão e acesso de conta de multiutilizador. Da sua organização do Azure AD é associado automaticamente a sua conta do Centro de parceiros como parte do processo de inscrição. 

## <a name="manage-users"></a>Gerir utilizadores

O **usuários** secção do Centro de parceiros (sob **definições de conta**) vamos utilizar o Azure AD para gerir os utilizadores, grupos e aplicações do Azure AD que têm acesso à sua conta do Centro de parceiros. Tenha em atenção que para Gerenciar usuários, precisa estar conectado com seu [conta profissional](./company-work-accounts.md) (inquilino do Azure AD associado). Para gerir utilizadores dentro de uma conta de trabalho diferentes / inquilino, terá de terminar e, em seguida, inicie sessão novamente como um utilizador com **Manager** que as permissões de conta profissional / inquilino. 

Depois de iniciar sessão com a sua conta profissional (inquilino do Azure AD), pode:
- [Adicionar ou remover utilizadores](#add-or-remove-users)
- [Alterar uma palavra-passe do utilizador](#change-a-user-password)
- [Adicionar ou remover grupos](#add-or-remove-users)
- [Adicionar ou remover aplicações do Azure AD](#add-new-azure-ad-applications)
- [Gerir as chaves de uma aplicação do Azure AD](#manage-keys-for-an-azure-ad-application)
- [Definir funções de utilizador e permissões](#define-user-roles-and-permissions)


Lembre-se de que todos os utilizadores do Centro de parceiros (incluindo os grupos e aplicações do Azure AD) tem de ter uma conta de trabalho ativo numa [inquilino do Azure AD](#manage-tenants) que está associado com a sua conta do Centro de parceiros. 

### <a name="add-or-remove-users"></a>Adicionar ou remover utilizadores

Sua conta tem de ter [ **ao nível do Gestor** ](#define-user-roles-and-permissions) permissões para o [conta (inquilino do Azure AD) profissional](./company-work-accounts.md) no qual pretende adicionar ou editar utilizadores.

#### <a name="add-existing-users"></a>Adicionar utilizadores existentes

Para adicionar utilizadores à sua conta do Centro de parceiros que já existem na sua empresa [(inquilino do Azure AD) de conta profissional](./company-work-accounts.md):

1. Aceda a **usuários** (sob **definições da conta**) e selecione **adicionar utilizadores**.
2. Selecione um ou mais utilizadores na lista apresentada. Pode utilizar a caixa de pesquisa para procurar utilizadores específicos.
\* Se selecionar mais do que um utilizador para adicionar à sua conta do Centro de parceiros, tem de o atribuir a mesma função ou conjunto de permissões personalizadas. Para adicionar vários usuários com permissões/funções diferentes, repita estes passos para cada função ou um conjunto de permissões personalizadas.
3.  Quando terminar de selecionar utilizadores, clique em **adicionar selecionado**.
5.  Na **funções** secção, especifique as funções ou permissões personalizadas para os utilizador ou utilizadores selecionados.
6.  Selecione **Guardar**.

#### <a name="create-new-users"></a>Criar novos utilizadores

Para criar contas de utilizador totalmente nova, tem de ter uma conta com [ **Administrador Global** ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) permissões. 

1. Aceda a **usuários** (sob **definições da conta**), selecione **adicionar utilizadores**, em seguida, selecione **criar novos utilizadores**.
1. Introduza um nome próprio, apelido e nome de utilizador para cada novo utilizador. 
1. Se pretender ao novo utilizador para ter uma conta de administrador global no diretório da sua organização, marque a caixa rotulada **tornar este utilizador um Administrador Global no seu Azure AD, com controlo total sobre todos os recursos de diretório**. Isso dará ao usuário de acesso total a todas as funcionalidades administrativas na sua empresa do Azure AD. Poderão adicionar e gerir os utilizadores na conta de trabalho da sua organização (inquilino do Azure AD), apesar de não parceiros do Datacenter, a menos que concede à conta as permissões/função apropriadas. 
1. Se tiver selecionado a caixa para **tornar este utilizador um Administrador Global**, terá de fornecer um **e-mail de recuperação de palavra-passe** para o usuário recuperar a palavra-passe, se necessário.
1. Na **associação de grupo** secção, selecione todos os grupos aos quais pretende que o novo utilizador pertence.
1. Na **funções** secção, especifique as funções ou permissões personalizadas para o utilizador.
1. Selecione **Guardar**.

Criar um novo utilizador no Centro de parceiros também irá criar uma conta para que o utilizador na conta profissional (inquilino do Azure AD) para o qual tem sessão iniciada. Efetuar alterações ao nome de um utilizador no Centro de parceiros fará com que as mesmas alterações na conta de trabalho da sua organização (inquilino do Azure AD).

#### <a name="invite-new-users-by-email"></a>Convidar novos utilizadores por e-mail

Para convidar utilizadores que atualmente não fazem parte da sua conta de trabalho da empresa (inquilino do Azure AD) através de e-mail, tem de ter uma conta com [ **Administrador Global** ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) permissões. 

1. Aceda a **usuários** (sob **definições da conta**), selecione **adicionar utilizadores**, em seguida, selecione **convidar utilizadores por e-mail**.
2. Introduza o e-mail de um ou mais endereços (até dez), separados por vírgulas ou ponto e vírgula.
3. Na **funções** secção, especifique as funções ou permissões personalizadas para o utilizador.
4. Selecione **Guardar**.

Os utilizadores que convidado irão receber um convite por e-mail para aderir a sua conta do Centro de parceiros. Será criada uma nova conta de utilizador convidado na sua conta profissional (inquilino do Azure AD). Cada utilizador tem de aceitar o convite, antes de poderem aceder a sua conta.

Se precisar de reenviar um convite, visite o **usuários** página, localize o convite na lista de utilizadores, selecione o respetivo endereço de e-mail (ou o texto que indica *convite pendente*). Em seguida, na parte inferior da página, selecione **reenviar convite**.
 

> [!NOTE]
> Se a sua organização utiliza [integração de diretórios](https://go.microsoft.com/fwlink/p/?LinkID=724033) para sincronizar o serviço de diretório no local com o Azure AD, não será possível criar novos utilizadores, grupos ou aplicações do Azure AD no Centro de parceiros. (Ou outro administrador no seu diretório no local) terá de criá-los diretamente no diretório no local, antes de que, poderá ver e adicioná-los no Centro de parceiros.

#### <a name="remove-a-user"></a>Remover um utilizador

Para remover um utilizador da sua conta profissional (inquilino do Azure AD), aceda a **usuários** (sob **definições da conta**), selecione o utilizador que pretende remover usando a caixa de verificação na coluna extrema direita, em seguida, escolha  **Remover** das ações disponíveis. Será apresentada uma janela de pop-up para que possa confirmar que pretende remover os utilizador ou utilizadores selecionados.

#### <a name="change-a-user-password"></a>Alterar uma palavra-passe do utilizador

Se um dos seus utilizadores tem de alterar a palavra-passe, ela poderá fazer isso se se tiver indicado um **e-mail de recuperação de palavra-passe** ao criar a conta de utilizador. Também pode atualizar a palavra-passe de um utilizador ao seguir os passos abaixo. Para alterar a palavra-passe de um utilizador na sua conta de trabalho da empresa (inquilino do Azure AD), tem de ser iniciada numa conta com [ **Administrador Global** ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) permissões. Tenha em atenção que isto vai alterar a senha do usuário no seu inquilino do Azure AD, juntamente com a palavra-passe que utilizam para aceder ao centro de parceiros.

1.  Do **usuários** página (sob **definições da conta**), selecione o nome da conta de utilizador que pretende editar.
2.  Selecione o **Repor palavra-passe** na parte inferior da página.
3.  Aparecerá uma página de confirmação que mostra as informações de início de sessão do utilizador, incluindo uma palavra-passe temporária. Não se esqueça de imprimir ou copiar estas informações e fornecê-lo ao usuário, pois não será possível aceder a palavra-passe temporária, depois de sair desta página.


## <a name="manage-groups"></a>Gerir grupos

Os grupos permitem-lhe controlar várias funções de utilizador e permissões em conjunto.

#### <a name="add-an-existing-group"></a>Adicionar um grupo existente

Para adicionar que um grupo que já existe na conta de trabalho da sua organização (inquilino do Azure AD) para a sua conta do Centro de parceiros: 

1.  Do **usuários** página (sob **definições da conta**), selecione **adicionar grupos**.
2.  Selecione um ou mais grupos na lista apresentada. Pode utilizar a caixa de pesquisa para procurar grupos específicos.
Se selecionar mais de um grupo para adicionar à sua conta do Centro de parceiros, tem de o atribuir a mesma função ou conjunto de permissões personalizadas. Para adicionar vários grupos de funções/permissões diferentes, repita estes passos para cada função ou um conjunto de permissões personalizadas.
3.  Quando terminar de selecionar os grupos, clique em **adicionar selecionado**.
4.  Na **funções** secção, especifique as funções ou permissões personalizadas para os grupos selecionados. Todos os membros do grupo será capazes de aceder à sua conta do Centro de parceiros com as permissões que se aplicam ao grupo, independentemente das funções e permissões associadas à sua conta individual.
5.  Selecione **Guardar**.

Quando adiciona um grupo existente, cada utilizador que seja um membro desse grupo será capaz de aceder à sua conta de centro de parceiros, com as permissões associadas à função atribuída do grupo.

#### <a name="add-a-new-group"></a>Adicionar um novo grupo

Para adicionar um grupo novo em folha para a sua conta do Centro de parceiros: 

1.  Do **usuários** página (sob **definições da conta**), selecione **adicionar grupos**.
2.  Na página seguinte, selecione **novo grupo**.
3.  Introduza o nome a apresentar para o novo grupo.
4.  Especifique o funções ou permissões personalizadas para o grupo. Todos os membros do grupo será capazes de aceder à sua conta do Centro de parceiros com as permissões que se aplicam aqui, independentemente das funções/permissões associadas a sua conta individual.
5.  Selecione o utilizador (es) para o novo grupo na lista apresentada. Pode utilizar a caixa de pesquisa para procurar utilizadores específicos.
6.  Quando tiver terminado de selecionar utilizadores, clique em **adicionar selecionado** para adicioná-los ao novo grupo.
7.  Selecione **Guardar**.

Tenha em atenção que este novo grupo será criado na conta de trabalho da sua organização (inquilino do Azure AD) Além disso, não apenas na sua conta do Centro de parceiros.

#### <a name="remove-a-group"></a>Remover um grupo

Para remover um grupo de sua conta profissional (inquilino do Azure AD), aceda a **usuários** (sob **definições da conta**), selecione o grupo que pretende remover usando a caixa de verificação na coluna extrema direita, em seguida, escolha  **Remover** das ações disponíveis. Será apresentada uma janela de pop-up para que possa confirmar que pretende remover os grupos selecionados.

## <a name="manage-azure-ad-applications"></a>Gerir aplicações do Azure AD

Pode permitir que aplicações ou serviços que fazem parte da Azure sua empresa AD para aceder à sua conta do Centro de parceiros. 

#### <a name="add-existing-azure-ad-applications"></a>Adicionar as aplicações existentes do Azure AD 

Para adicionar aplicações que já existem no Azure Active Directory da sua empresa: 

1.  Do **usuários** página (sob **definições da conta**), selecione **adicionar aplicações do Azure AD**.
2.  Selecione uma ou mais aplicações do Azure AD na lista apresentada. Pode utilizar a caixa de pesquisa para procurar específicos de aplicações do Azure AD. Se selecionar mais do que uma aplicação do Azure AD para adicionar à sua conta do Centro de parceiros, tem de o atribuir a mesma função ou conjunto de permissões personalizadas. Para adicionar várias aplicações do Azure AD com permissões/funções diferentes, repita estes passos para cada função ou um conjunto de permissões personalizadas.
3.  Quando tiver terminado de selecionar as aplicações do Azure AD, clique em **adicionar selecionado**.
5.  Na **funções** secção, especifique o funções ou permissões personalizadas selecionado para aplicações do Azure AD.
6.  Selecione **Guardar**.

#### <a name="add-new-azure-ad-applications"></a>Adicionar o novo Azure aplicações AD 

Se quiser conceder acesso de centro de parceiros para um Azure totalmente nova conta de aplicação do AD, pode criar uma no **utilizadores** secção. Tenha em atenção que isto irá criar uma nova conta na sua conta de trabalho da empresa (inquilino do Azure AD), não apenas na sua conta do Centro de parceiros. Se principalmente estiver a utilizar esta aplicação do Azure AD para autenticação de centro de parceiros e não é necessário que os utilizadores acedam diretamente a ele, pode introduzir qualquer endereço válido para o **URL de resposta** e **URI de ID de aplicação**, desde como esses valores não são utilizados por qualquer outra aplicação do Azure AD no seu diretório.

1.  Do **usuários** página (sob **definições da conta**), selecione **adicionar aplicações do Azure AD**.
2.  Na página seguinte, selecione **do Azure AD de nova aplicação**.
3.  Introduza o **URL de resposta** para a nova aplicação do Azure AD. Este é o URL em que os utilizadores podem iniciar sessão e utilizar a aplicação do Azure AD (por vezes, também conhecido como o URL da aplicação ou um URL de início de sessão). O **URL de resposta** não pode ter mais de 256 carateres e tem de ser exclusivo no diretório.
4.  Introduza o **URI de ID de aplicação** para a nova aplicação do Azure AD. Este é um identificador lógico para a aplicação do Azure AD que é apresentada quando é enviado um pedido de início de início de sessão único para o Azure AD. Tenha em atenção que o **URI de ID de aplicação** tem de ser exclusivo para cada aplicação do Azure AD no seu diretório. Este ID não pode ter mais de 256 carateres. Para mais informações sobre o URI de ID de aplicação, veja [integrar aplicações com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/quickstart-modify-supported-accounts#change-the-application-registration-to-support-different-accounts).
5.  Na **funções** secção, especifique as funções ou permissões personalizadas para a aplicação do Azure AD.
6.  Selecione **Guardar**.

Depois de adicionar ou criar uma aplicação do Azure AD, pode retornar para o **utilizadores** secção e selecione o nome da aplicação para rever as definições da aplicação, incluindo o ID do inquilino, o ID de cliente, o URL de resposta e o URI de ID de aplicação.

#### <a name="remove-an-application"></a>Remover uma aplicação

Para remover uma aplicação da sua conta profissional (inquilino do Azure AD), aceda a **usuários** (sob **definições da conta**), selecione a aplicação que pretende remover usando a caixa de verificação na coluna extrema direita, em seguida, escolha **remover** das ações disponíveis. Será apresentada uma janela de pop-up para que possa confirmar que pretende remover as aplicações selecionadas.

#### <a name="manage-keys-for-an-azure-ad-application"></a>Gerir as chaves de uma aplicação do Azure AD

Se a sua aplicação do Azure AD lê e escreve dados no Microsoft Azure AD, será necessário uma chave. Pode criar chaves para uma aplicação do Azure AD ao editar a informação no Centro de parceiros. Também pode remover as chaves que já não são necessários.

1.  Do **usuários** página (sob **definições da conta**), selecione o nome da aplicação do Azure AD. Verá todas as chaves de Active Directory para a aplicação do Azure AD, incluindo a data em que a chave foi criada e quando irá expirar. 
2. Para remover uma chave que já não é necessário, selecione **remover**.
3.  Para adicionar uma nova chave, selecione **Adicionar nova chave**.
4.  Irá ver uma apresentação de ecrã a **ID de cliente** e **valores da chave**. Não se esqueça de imprimir ou copiar estas informações, como não poderá acessá-lo novamente depois de sair desta página.
4.  Se quiser criar mais chaves, selecione **adicionar outra chave**.


### <a name="define-user-roles-and-permissions"></a>Definir funções de utilizador e permissões

Podem ser atribuídos a utilizadores da sua empresa das seguintes funções e permissões no programa de Marketplace comercial no Centro de parceiros. 

Tenha em atenção de que as funções de inquilino do Azure Active Directory (AAD) incluem administrador global, administrador de utilizadores e funções CSP. Funções do AAD não são dessas funções que não gerir o inquilino e eles incluem-se ao administrador, business perfil administrador, administrador de referência, incentivo administrador e utilizador incentivo MPN.


|**Função**|**Permissões**|
|----------------------------------|:---------------------------------|
|Administrador global|• Podem aceder a todos os Microsoft/Serviços de contas com privilégio total
|      |• Criar pedidos de suporte para o Centro de parceiros
||• Contratos de exibição, listas de preços e ofertas
||• Ver, criar e gerir utilizadores parceiros|
|Manager|• Podem aceder a todas as funcionalidades de conta Microsoft, exceto as definições de imposto e dividendos
|      |• Podem gerir utilizadores, funções e trabalhar contas (inquilinos)|
|Programador|• Pode carregar pacotes, submeter aplicações e suplementos e ver o relatório de utilização para obter detalhes de telemetria
|      |• Não é possível aceder a definições de conta ou informações financeiras|
|Contribuinte de negócios|• Pode aceder a informações financeiras e definir os detalhes dos preços
|      |• Não é possível criar ou submeter novos aplicativos e complementos|
|Contribuinte Financeiro|• Podem visualizar os relatórios de dividendos
|      |• Não é possível efetuar alterações a aplicações ou definições|
|Comerciante|• Pode responder a revisões de cliente e os relatórios não-financeiros
|      |• Não é possível efetuar alterações a aplicações ou definições|

Para obter mais informações sobre a gestão de funções e permissões no outras áreas do Centro de parceiros, como o Azure Active Directory (AD), fornecedor de soluções Cloud (CSP), o fornecedor de painel de controle (CPV), os utilizadores convidados ou Microsoft Partner Network (MPN), consulte [atribuir funções de utilizadores e permissões no Centro de parceiros](https://docs.microsoft.com/partner-center/permissions-overview).


## <a name="manage-tenants"></a>Gerir os inquilinos

Um inquilino do Azure Active Directory (AD), também conhecido como sua "conta profissional" ao longo desta documentação, é uma representação da sua organização configurar no portal do Azure e ajuda-o a gerir uma instância específica de serviços cloud da Microsoft para interno e os utilizadores externos. Se sua organização subscreveu um serviço cloud da Microsoft, como o Azure, Microsoft Intune ou do Office 365, o inquilino do Azure AD foi estabelecido para. 

Pode configurar vários inquilinos para utilizar com o Centro de parceiros. Qualquer utilizador com o **Manager** função na conta do Centro de parceiros terá a opção para adicionar e remover inquilinos do Azure AD a partir da conta.  

### <a name="add-an-existing-tenant"></a>Adicionar um inquilino existente

Para associar a outro inquilino do Azure AD com a sua conta do Centro de parceiros:

1.  Partir do **inquilinos** página (sob **definições da conta**), selecione **associar outro inquilino do Azure AD**.
2. Introduza as credenciais do Azure AD para o inquilino que pretende associar.
3. Reveja o nome de organização e o domínio para o seu inquilino do Azure AD. Para concluir a associação, selecione **confirmar**.

Se a associação é efetuada com êxito, em seguida, estará pronto para adicionar e gerir os utilizadores da conta no **utilizadores** secção no Centro de parceiros.

### <a name="create-a-new-tenant"></a>Criar um novo inquilino

Para criar um inquilino de marca novo do Azure AD com a sua conta do Centro de parceiros:

1.  Partir do **inquilinos** página (sob **definições da conta**), selecione **inquilino do Azure AD de criar uma nova**.
2. Introduza as informações de diretório do Azure AD nova:
    - **Nome de domínio**: O nome exclusivo que usaremos para o seu domínio do Azure AD, juntamente com ". onmicrosoft.com". Por exemplo, se tiver introduzido "exemplo", o seu domínio do Azure AD seria "example.onmicrosoft.com".
    - **E-mail de contacto**: Um endereço de e-mail onde poderemos contactá-lo sobre a sua conta se necessário.
    - **Informações de conta de utilizador de administrador global**: O nome próprio, último nome, nome de utilizador e palavra-passe que pretende utilizar para a nova conta de administrador global.
3. Selecione **criar** para confirmar as informações de domínio e a conta nova.
4. Inicie sessão com do Azure novo nome de utilizador de administrador global de AD e a palavra-passe para começar [adicionar e gerir os utilizadores](#manage-users).

Para obter mais informações sobre a criação de novos inquilinos no portal do Azure, em vez de através do portal do Centro de parceiros, consulte o artigo [criar um novo inquilino no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant).

### <a name="remove-a-tenant"></a>Remover um inquilino

Para remover um inquilino da sua conta do Centro de parceiros, encontrar o seu nome na **inquilinos** página (na **definições da conta**), em seguida, selecione **remover**. Será solicitado a confirmar que pretende remover o inquilino. Depois de fazer isso, não existem utilizadores nesse inquilino vão conseguir iniciar sessão na conta do Centro de parceiros e todas as permissões que configurou para os utilizadores serão removidas.

Quando remove um inquilino, todos os utilizadores que foram adicionados à conta do Centro de parceiros de inquilino já não será capazes de iniciar sessão conta.

> [!TIP]
> Não é possível remover um inquilino se tem atualmente sessão iniciada no Centro de parceiros através de uma conta no mesmo inquilino. Para remover um inquilino, tem de iniciar sessão Centro de parceiros como um **Manager** para outro inquilino que está associado a conta. Se houver apenas um inquilino associado à conta, esse inquilino só pode ser removido depois de iniciar sessão com a conta da Microsoft que abriu a conta.


## <a name="agreements"></a>Contratos

O **contratos** secção do Centro de parceiros (sob **definições de conta**) Vejamos uma lista dos contratos da publicação que autorizou. Estes contratos estão listados, de acordo com nome e número de versão, incluindo a data foi aceite e o nome do utilizador que aceite o contrato. 

**Ações necessárias** pode aparecer na parte superior desta página, se existem atualizações de contrato que precisam da sua atenção. Para aceitar um contrato atualizado, primeiro ler a versão de contrato de ligado, em seguida, selecione **aceitar o contrato**. 

Para obter informações sobre os contratos de fornecedor de soluções Cloud (CSP) no Centro de parceiros, visite [contratos de nuvem da Microsoft por região e idioma](https://docs.microsoft.com/partner-center/agreements).

## <a name="next-steps"></a>Passos seguintes

- [Criar uma nova oferta de SaaS](./create-new-saas-offer.md)
