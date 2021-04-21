---
title: Como gerir uma conta de marketplace comercial no Microsoft Partner Center - Azure Marketplace
description: Saiba como gerir uma conta de marketplace comercial no Microsoft Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: varsha-sarah
ms.author: vavargh
ms.date: 04/07/2021
ms.openlocfilehash: 6b721e7acb7907743c0696aff6c11ad59f5ceba9
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812575"
---
# <a name="manage-your-commercial-marketplace-account-in-partner-center"></a>Gerir a sua conta de mercado comercial no Partner Center

**Funções adequadas**

- Proprietário
- Gestor

Uma vez [criada uma conta Partner Center,](./create-account.md)pode utilizar o [painel de instrumentos do mercado comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) para gerir a sua conta e ofertas.

## <a name="access-your-account-settings"></a>Aceda às definições da sua conta

Se ainda não o fez, você (ou administrador da sua organização) deverá aceder às [definições](https://partner.microsoft.com/dashboard/account/management) de conta da sua conta Partner Center.

1. Inscreva-se no [painel de instrumentos do mercado comercial](https://partner.microsoft.com/dashboard/commercial-marketplace) no Partner Center com a conta a que pretende aceder. Se fizer parte de várias contas e tiver assinado com outra, pode [trocar de conta](switch-accounts.md).
1. No topo-direito, selecione **Definições** (ícone de engrenagem) e, em seguida, selecione **as definições de Conta**.

    [![Screenshot do menu de definições de conta no Partner Center. ](./media/manage-accounts/settings-account.png)](./media/manage-accounts/settings-account.png#lightbox)

1. Nas **definições de Conta** selecione **Legal**. Em seguida, selecione o **separador Developer** para ver detalhes relacionados com a sua conta de mercado comercial.

    [![Screenshot do separador do desenvolvedor na página legal nas definições de Conta. ](./media/manage-accounts/developer-tab.png)](./media/manage-accounts/developer-tab.png#lightbox)

### <a name="account-settings-page"></a>Página de definições de conta

Quando seleciona **Definições** e expande **as definições de Conta,** a vista padrão é **informação legal**. Esta página pode ter até três separadores, dependendo dos programas em que se inscreveu: _Partner,_ _Revendedor_ e _Developer_.

O **separador Parceiro,** para parceiros inscritos na MPN, inclui:

- Todas as informações legais de negócios, tais como nome legal registado e morada para a sua empresa
- Contacto primário
- Locais de negócios.

O **separador Revendedor,** para parceiros que fazem negócios com CSP, inclui:

- Informações de contacto primários
- Perfil de apoio ao cliente
- Informação do programa

O **separador Developer,** para parceiros inscritos em qualquer programador, tem as seguintes informações:

- **Detalhes da conta**: Tipo de conta e estado da conta
- **IDs editoriais**: ID do vendedor, ID do utilizador, ID editor, inquilinos AD Azure, e muito mais
- **Informações de contacto**: Nome de exibição do editor, contacto do vendedor (nome, e-mail, telefone e endereço) e aprovador da Empresa (nome, e-mail, telefone)

### <a name="account-settings---developer-tab"></a>Definições de conta - Separador de programadores

As seguintes informações descrevem as informações no separador Developer.

#### <a name="account-details"></a>Detalhes da conta

Na secção de detalhes da _Conta_ do _separador Programador,_ pode ver informações básicas, como o **tipo de Conta** (Empresa ou Individual) e o estado de **Verificação** da sua conta. Durante o processo de verificação da sua conta, estas definições apresentarão cada passo necessário, incluindo verificação de e-mail, verificação de emprego e verificação de negócios.

#### <a name="publisher-ids"></a>IDs de editor

Na secção de IDs editoriais, pode ver o seu **ID Symantec** (se aplicável), **ID do Vendedor,** **ID do Utilizador,** **ID MPN** e **AD AD.** Estes valores são atribuídos pela Microsoft para identificar exclusivamente a sua conta de programador e não podem ser editados.

Se não tiver um ID Symantec, pode selecionar o link para solicitar um.

### <a name="contact-info"></a>Informações de contacto

Na secção _informações de Contacto,_ pode ver o **nome de exibição do Editor,** **informação de contacto do Vendedor** (o nome de contacto, e-mail, número de telefone e morada para o vendedor da empresa) e o autor da **empresa** (o nome, e-mail e número de telefone do indivíduo com autoridade para aprovar decisões para a empresa).

Também pode selecionar o link **'Atualização'** para alterar as suas informações de contacto, como o nome de exibição do editor e o endereço de e-mail.

### <a name="account-settings-identifiers"></a>Identificadores de definições de conta

Em **Definições conta** o perfil da  >  **Organização**, selecione **Identifiers** para ver as seguintes informações:

- **IDs MPN**: Quaisquer IDs MPN associados à sua conta
- **CSP**: IDs MPN associados ao programa CSP para esta conta.
- **Editor**: IDs de vendedor associados à sua conta
- **GUIDs de rastreio**: Quaisquer GUIDs de rastreio associados à sua conta

#### <a name="tracking-guids"></a>Rastreio de GUIDs

Os identificadores exclusivos globais (GUIDs) são números de referência únicos (com 32 dígitos hexadémicos) que podem ser usados para rastrear o seu uso de Azure.

Para criar GUIDs para rastreio, deve utilizar um gerador GUID. A equipa de Armazenamento Azure criou um [formulário de gerador GUID](https://aka.ms/StoragePartners) que lhe enviará um GUID do formato correto e pode ser reutilizado através dos diferentes sistemas de rastreio.

Recomendamos que crie um GUID único para cada canal de oferta e distribuição para cada produto. Pode optar por utilizar um único GUID para os múltiplos canais de distribuição do produto se não quiser que o relatório seja dividido.

Se implementar um produto utilizando um modelo e estiver disponível tanto no Azure Marketplace como no GitHub, pode criar e registar dois GUIDs distintos:

- Produto A no Mercado Azure
- Produto A no GitHub

A reporte é feita pelo valor do parceiro (Microsoft Partner ID) e pelos GUIDs. Também pode rastrear GUIDs a um nível mais granular, alinhando-se com cada plano dentro da sua oferta.

Para obter mais informações, consulte o uso do [cliente Tracking Azure com AS FAQ GUIDs](azure-partner-customer-usage-attribution.md#faq)).

### <a name="agreements"></a>Contratos

A página **Acordos** permite-lhe ver uma lista dos acordos de publicação que autorizou. Estes acordos estão listados de acordo com o nome e o número da versão, incluindo a data em que foi aceite e o nome do utilizador que aceitou o acordo.

Para aceder à página De Acordos:

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home).
1. No topo-direito, selecione **definições** de Conta de  >  **Definições**.
1. Nas **definições de Conta**, selecione **Acordos**.

**As ações necessárias** podem aparecer no topo desta página se houver atualizações de acordo que precisem da sua atenção. Para aceitar um acordo atualizado, leia primeiro a versão do Acordo ligado e, em seguida, **selecione Aceitar o acordo**.

## <a name="set-up-a-payout-profile"></a>Configurar um perfil de pagamento

Um perfil de pagamento é a conta bancária para a qual os lucros são enviados das suas vendas. Esta conta bancária deve estar no mesmo país ou região onde registou a sua conta partner Center. Para obter mais informações sobre um perfil de pagamento, consulte [Criar e gerir os perfis de pagamento de incentivos e perfis fiscais no Partner Center](/partner-center/incentives-create-and-manage-your-payout-and-tax-profiles) e [configurar a sua conta de pagamento e formulários fiscais.](/partner-center/set-up-your-payout-account)

Para configurar o seu perfil de pagamento:

1. Vá à [página de visão geral](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) do mercado comercial no Partner Center.
2. Na secção **Perfil,** ao lado **do Perfil de Pagamento,** selecione **Update**.
3. **Escolha um método de pagamento:** conta bancária ou PayPal.
4. **Adicionar informações de pagamento**: Isto pode incluir a escolha de um tipo de conta (verificação ou poupança), introduzir o nome do titular da conta, o número da conta e o número de encaminhamento, endereço de faturação, número de telefone ou PayPal endereço de e-mail. Para obter mais informações sobre a utilização PayPal como método de pagamento da sua conta e para saber se é suportado no seu mercado ou região, consulte [PayPal informações.](/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info)

> [!IMPORTANT]
> Alterar a sua conta de pagamento pode atrasar os seus pagamentos até um ciclo de pagamento. Este atraso ocorre porque precisamos verificar a alteração da conta, tal como fazemos quando configuramos a conta de pagamento pela primeira vez. Continuará a ser pago pelo valor total depois de verificada a sua conta; quaisquer pagamentos devidos para o ciclo de pagamento em curso serão adicionados ao próximo.  

## <a name="tax-profile"></a>Perfil fiscal

Reveja o seu estado atual do perfil fiscal, confirmando o **tipo de Entidade** correta e **Informações de Certificados Fiscais.** Selecione **Editar** para atualizar ou preencher os formulários necessários.

Para estabelecer o seu estatuto fiscal, deve especificar o seu país ou região de residência e cidadania e preencher os formulários fiscais adequados associados ao seu país ou região.

Independentemente do seu país ou região de residência ou cidadania, deve preencher formulários fiscais dos Estados Unidos para vender quaisquer ofertas através da Microsoft. Os parceiros que satisfaçam determinados requisitos de residência dos Estados Unidos devem preencher um formulário IRS W-9. Outros parceiros fora dos Estados Unidos devem preencher um formulário IRS W-8. Pode preencher estes formulários online à medida que completa o seu perfil fiscal.

Um Número de Identificação Individual do Contribuinte dos Estados Unidos (ou ITIN) não é obrigado a receber pagamentos da Microsoft ou a reclamar benefícios do tratado fiscal.

Pode completar e submeter os seus formulários fiscais eletronicamente no Partner Center; na maioria dos casos, não precisa imprimir e enviar nenhum formulário.

Diferentes países e regiões têm diferentes requisitos fiscais. O valor exato que deve pagar em impostos depende dos países e regiões onde vende as suas ofertas. A Microsoft atribui vendas e utiliza impostos em seu nome em alguns países e regiões. Estes países e regiões serão identificados no processo de listagem da sua oferta. Noutros países e regiões, dependendo do local onde está registado, poderá ter de reencamitá-lo e utilizar o imposto para as suas vendas diretamente para a autoridade tributária local. Além disso, os lucros de vendas que recebe podem ser tributáveis como rendimento. Encorajamo-lo vivamente a contactar a autoridade relevante para o seu país ou região que melhor o ajude a determinar a informação fiscal certa para as suas transações de vendas da Microsoft.

Para obter mais informações sobre um perfil fiscal, consulte [Criar e gerir os perfis de pagamento de incentivos e perfis fiscais no Partner Center](/partner-center/incentives-create-and-manage-your-payout-and-tax-profiles) e [configurar a sua conta de pagamento e formulários fiscais.](/partner-center/set-up-your-payout-account)

### <a name="withholding-rates"></a>Taxas de retenção

A informação que submete nos seus formulários fiscais determina a taxa de retenção na fonte de imposto adequada. A taxa de retenção aplica-se apenas às vendas que faz nos Estados Unidos; as vendas efetuadas em locais não americanos não estão sujeitas a retenção. As taxas de retenção variam, mas para a maioria dos desenvolvedores que se registam fora dos Estados Unidos, a taxa de incumprimento é de 30%. Tem a opção de reduzir esta taxa se o seu país ou região tiver concordado com um tratado de imposto sobre o rendimento com os Estados Unidos.

### <a name="tax-treaty-benefits"></a>Benefícios do tratado fiscal

Se estiver fora dos Estados Unidos, poderá tirar partido dos benefícios do tratado fiscal. Estes benefícios variam de país/região para país/região, e podem permitir reduzir o montante de impostos que a Microsoft retém. Pode reclamar benefícios do tratado fiscal completando a Parte II do formulário W-8BEN. Recomendamos que comunique com os recursos apropriados no seu país ou região para determinar se estes benefícios se aplicam a si.

[Saiba mais sobre detalhes fiscais para programadores de aplicações/jogos do Windows e editores do Azure Marketplace.](/windows/uwp/publish/tax-details-for-paid-apps)

### <a name="payout-hold-status"></a>Estatuto de detenção de pagamento

Por padrão, a Microsoft envia pagamentos mensalmente. No entanto, pode opcionalmente colocar os seus pagamentos em espera, o que impedirá o envio de pagamentos para a sua conta. Se optar por colocar os seus pagamentos em espera, continuaremos a registar qualquer receita que obtenha e forneceremos os detalhes no seu **resumo do Pagamento.** No entanto, não enviaremos quaisquer pagamentos para a sua conta até que remova o porão.

**Para colocar os seus pagamentos em espera:**

1. Ir para as **definições de Conta**. 
1. No left-nav expandir **o Payout e os impostos** e selecionar Payout e perfis **fiscais.**
1. Selecione o programa para o qual pretende realizar pagamentos e, em seguida, selecione a caixa de verificação **Hold my Payment.**

Pode alterar o seu estado de detenção a qualquer momento, mas esteja ciente de que a sua decisão terá impacto no próximo pagamento mensal. Por exemplo, se quiser realizar o pagamento de abril, certifique-se de definir o seu estado de detenção de pagamento para **On** antes do final de março.

Depois de definir o seu estado de detenção de pagamento para **On,** todos os pagamentos ficarão em espera até que altere o slider de volta para **Off**. Quando o fizer, será incluído durante o próximo ciclo mensal de pagamento (desde que os limiares de pagamento aplicáveis tenham sido cumpridos). Por exemplo, se já teve os seus pagamentos em espera, mas gostaria de ter um pagamento gerado em junho, então certifique-se de alternar o estado de detenção do pagamento para **Off** antes do final de maio.

> [!NOTE]
> A sua seleção **de status de detenção de pagamento** aplica-se a todas **as** fontes de receita que são pagas através do Microsoft Partner Center, incluindo o Azure Marketplace, Microsoft AppSource, Microsoft Store, publicidade, e assim por diante. Não é possível selecionar diferentes estados de porão para cada fonte de receita.

## <a name="devices"></a>Dispositivos

As definições de gestão do dispositivo aplicam-se apenas à publicação universal da plataforma Windows (UWP). [Saiba mais](/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info).

## <a name="create-a-billing-profile"></a>Criar um perfil de faturação

Se estiver a publicar uma [Oferta Dinâmica 365 para o Envolvimento do Cliente & Power Apps](dynamics-365-customer-engage-offer-setup.md) ou a Oferta Dinâmica [365 para Operações,](./partner-center-portal/create-new-operations-offer.md) tem de completar o seu *perfil de faturação*.

O endereço de faturação é pré-preenchido da sua entidade legal, e pode atualizar este endereço mais tarde. Os campos de identificação do IRS e do IVA são necessários para alguns países e opcionais para outros. O nome e o nome da empresa do país/região não podem ser editados.

1. Ir para as **definições de Conta**.
1. Em seguida, no perfil de organização de **expansão** do navegador esquerdo e selecione **o perfil de Billing**.


## <a name="multi-user-account-management"></a>Gestão de conta multiutilizador

Partner Center utiliza [O Azure Ative Directory](/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) para acesso e gestão de conta multiutilizador. O Azure AD da sua organização está automaticamente associado à sua conta Partner Center como parte do processo de inscrição.

## <a name="next-steps"></a>Passos seguintes

- [Adicionar e gerir utilizadores](add-manage-users.md)
