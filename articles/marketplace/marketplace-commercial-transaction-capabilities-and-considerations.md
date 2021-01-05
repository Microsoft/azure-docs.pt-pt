---
title: Capacidades de transação de mercado comercial da Microsoft
description: Este artigo descreve os preços, a faturação, a faturação e as considerações de pagamento para a opção de transação do mercado comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: d602687ea623b90ea5dcae4ece09a1ccc438ea57
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/30/2020
ms.locfileid: "97822391"
---
# <a name="commercial-marketplace-transact-capabilities"></a>Capacidades de transação de mercado comercial

Este artigo descreve os preços, a faturação, a faturação e as considerações de pagamento para o mercado comercial da Microsoft.

## <a name="transactions-by-listing-option"></a>Transações por opção de listagem

A editora ou a Microsoft são responsáveis pela gestão de transações de licenças de software para ofertas no mercado comercial. A opção de listagem que escolhe para a sua oferta determina quem gere a transação. Para disponibilidade e explicações de cada opção de publicação, consulte [Introdução às opções de listagem](determine-your-listing-type.md)

### <a name="contact-me-free-trial-and-byol-options"></a>Contacte-me, teste gratuito e opções BYOL

Os editores podem escolher o _teste Contact me_ e _Free,_ opções para fins promocionais e de aquisição de utilizadores. Para alguns tipos de oferta, os editores podem escolher a opção trazer a sua própria licença (BYOL) para permitir que os clientes comprem uma subscrição da sua oferta usando uma licença que compraram diretamente de si. Com estas opções, a Microsoft não participa diretamente nas transações de licenças de software da editora e não há nenhuma taxa de transação associada.

Os editores são responsáveis por apoiar todos os aspetos da transação de licença de software. Isto inclui, mas não se limita à ordem, cumprimento, medição, faturação, faturação, pagamento e cobrança. Com a opção de listagem Contacte-me, as editoras mantêm 100% das taxas de licenciamento de software de editores cobradas ao cliente.

### <a name="transact-publishing-option"></a>Opção de publicação transacionar

Optar por vender através da Microsoft tira partido das capacidades de comércio da Microsoft e proporciona uma experiência de ponta a ponta desde a descoberta e avaliação até à compra e implementação. Uma oferta que seja transacionável é aquela em que a Microsoft facilita a troca de dinheiro por uma licença de software em nome da editora. As ofertas transactable são faturadas contra uma subscrição da Microsoft ou um cartão de crédito existente, permitindo à Microsoft hospedar transações de mercado na nuvem em nome da editora.

Escolha a opção de transação quando criar uma nova oferta no Partner Center. Esta opção só será mostrada se o transact estiver disponível para o seu tipo de oferta.

## <a name="transact-overview"></a>Visão geral do Transt

Ao utilizar a opção transact, a Microsoft permite a venda de software de terceiros e a implementação de alguns tipos de oferta para a subscrição Azure do cliente. Você, o editor, deve considerar a faturação das taxas de infraestrutura e as suas próprias taxas de licenciamento de software ao selecionar um modelo de preços para uma oferta.

A opção de publicação de transações é atualmente suportada para os seguintes tipos de oferta:

- Máquinas virtuais
- Aplicações do Azure
- Aplicações SaaS

### <a name="billing-infrastructure-costs"></a>Custos de infraestrutura de faturação

Para **máquinas virtuais** e **aplicações Azure,** as taxas de utilização da infraestrutura Azure são cobradas à subscrição Azure do cliente. As taxas de utilização da infraestrutura são avaliadas e apresentadas separadamente das taxas de licenciamento do fornecedor de software na fatura do cliente.

Para **as Aplicações SaaS,** o editor deve prestar contas às taxas de utilização da infraestrutura Azure e às taxas de licenciamento de software como um único item de custo.  É representado como uma taxa fixa para o cliente. O uso da infraestrutura Azure é gerido e faturado diretamente ao editor. As taxas reais de utilização da infraestrutura não são vistas pelo cliente. Os editores normalmente optam por agregar as taxas de utilização da infraestrutura Azure nos seus preços de licença de software. As taxas de licenciamento de software não são medidos ou baseados no consumo do utilizador.

## <a name="pricing-models"></a>Modelos de preços

Dependendo da opção de transação utilizada, os encargos de subscrição são os seguintes:

- **Obtenha-o agora (Grátis)** – Sem custos para licenças de software. Os clientes não são cobrados taxas Azure Marketplace por usar uma oferta gratuita. Ofertas gratuitas não podem ser convertidas para uma oferta paga. Os clientes devem pedir uma oferta paga.
- **Traga a sua própria licença** (BYOL) – Quaisquer encargos aplicáveis para licenças de software são geridos diretamente entre o editor e o cliente. A Microsoft só passa pelas taxas de utilização da infraestrutura Azure. Se uma oferta for listada no mercado comercial, os clientes que obtêm acesso ou utilização da oferta fora do mercado comercial não são cobrados taxas de mercado comercial.
- **Preços de subscrição** – As taxas de licença de software são apresentadas como uma taxa de subscrição mensal ou anual, recorrente, faturada como uma taxa fixa ou por assento. As taxas de subscrição recorrentes não são procidas para cancelamentos de clientes intercalares ou serviços não reutilizados. As taxas de subscrição recorrentes podem ser promovidas se o cliente atualizar ou desvalorizar a sua subscrição a meio do período de subscrição.
- **Preços baseados na utilização** – Para ofertas de Máquina Virtual Azure, os clientes são cobrados com base na extensão do seu uso da oferta. Para imagens de máquinas virtuais, os clientes são cobrados uma taxa de Azure Marketplace de hora em hora, conforme definido pelas editoras, para uso de máquinas virtuais implantadas a partir das imagens VM. A taxa horária pode ser uniforme ou variada em tamanhos de máquinas virtuais. As horas parciais são cobradas ao minuto. Os planos são cobrados mensalmente.
- **Preços medidos** – Para ofertas de aplicações Azure e ofertas saaS, os editores podem usar o [serviço de medição do Marketplace](./partner-center-portal/marketplace-metering-service-apis.md) para faturar para consumo com base nas dimensões do contador que escolherem. Por exemplo, largura de banda, bilhetes ou e-mails processados. Os editores podem definir uma ou mais dimensões de metro para cada plano. Os editores são responsáveis por acompanhar o uso de clientes individuais, com cada metro definido na oferta. Os eventos devem ser comunicados à Microsoft dentro de uma hora. A Microsoft cobra aos clientes com base nas informações de utilização comunicadas pelos editores para o período de faturação aplicável.
- **Teste gratuito** – Sem custo para licenças de software que variam entre 30 dias e seis meses, dependendo do tipo de oferta. Se os editores fornecerem um teste gratuito em vários planos dentro da mesma oferta, os clientes podem mudar para um teste gratuito em outro plano, mas o período experimental não reinicia. Para ofertas de máquinas virtuais, os clientes são cobrados custos de infraestrutura Azure para usar a oferta durante um período experimental. Após o termo do período experimental, os clientes são automaticamente cobrados pelo último plano que tentaram com base em tarifas padrão, a menos que cancelem antes do final do período experimental.

> [!NOTE]
> As ofertas que são faturadas de acordo com o consumo depois de uma solução ter sido utilizada não são elegíveis para reembolsos.

Os editores que pretendam alterar as taxas de utilização associadas a uma oferta, devem primeiro remover a oferta (ou o plano específico dentro da oferta) do mercado comercial. A remoção deve ser feita de acordo com os requisitos do [Microsoft Publisher Agreement](https://go.microsoft.com/fwlink/?LinkID=699560). Em seguida, o editor pode publicar uma nova oferta (ou plano dentro de uma oferta) que inclui as novas taxas de utilização. Para obter informações, sobre a remoção de uma oferta ou plano, consulte [Parar de vender uma oferta ou plano.](./partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan)

### <a name="free-contact-me-and-bring-your-own-license-byol-pricing"></a>Livre, Contacte-me e traga a sua própria licença (BYOL) preços

Ao publicar uma oferta com a opção Get it now (Free), Contact me, ou BYOL, a Microsoft não desempenha um papel na facilitação da transação de vendas para as suas taxas de licença de software. Tal como a lista e as opções de publicação gratuitas de testes, a editora mantém 100% das taxas de licença de software.

### <a name="usage-based-and-subscription-pricing"></a>Preços baseados na utilização e subscrição

Ao publicar uma oferta com base no utilizador ou numa transação de subscrição, a Microsoft fornece a tecnologia e serviços para processar compras, devoluções e cobranças de licenças de software. Neste cenário, a editora autoriza a Microsoft a agir como agente para estes fins. O editor permite à Microsoft facilitar a transação de licenciamento de software, mantendo ao mesmo tempo a sua designação como vendedor, fornecedor, distribuidor e licenciante.

A Microsoft permite que os clientes encomendem, licenciem e utilizem o seu software, sujeito aos termos e condições do mercado comercial da Microsoft e do seu acordo de licenciamento de utilizadores finais. Deve fornecer o seu próprio contrato de licenciamento de utilizador final ou selecionar o [Contrato Padrão](./standard-contract.md) ao criar a oferta.

### <a name="free-software-trials"></a>Ensaios de software gratuitos

Para cenários de publicação de transações, pode disponibilizar gratuitamente uma licença de software durante 30 a 120 dias, dependendo da subscrição. Esta capacidade de desconto não inclui o custo da utilização da infraestrutura Azure impulsionada pela utilização da solução parceira.

### <a name="private-offers"></a>Ofertas privadas

Além de usar tipos de oferta e modelos de faturação para rentabilizar uma oferta, você pode transagir uma oferta privada, completa com preços negociados, específicos do negócio ou configurações personalizadas. As ofertas privadas são apoiadas pelas três opções de publicação de transações.

Esta opção permite preços mais altos ou mais baixos do que a oferta publicamente disponível. Você pode usar ofertas privadas para desconto ou adicionar um prémio a uma oferta. Pode disponibilizar ofertas privadas a um ou mais clientes, listando a sua subscrição Azure ao nível da oferta.

### <a name="commercial-marketplace-service-fees"></a>Taxas de serviço de mercado comercial

Cobramos uma taxa de serviço de loja padrão de 20% quando os clientes compram a sua oferta de transação no mercado comercial. Para mais informações sobre esta taxa, consulte a secção 5c do [Microsoft Publisher Agreement](https://go.microsoft.com/fwlink/?LinkID=699560).

Para certas ofertas transacionáveis que publica no mercado comercial, poderá beneficiar de uma taxa reduzida de serviço de loja de 10%. Para uma oferta de qualificação, deve ter sido designada pela Microsoft como Azure IP Co-sell incentivado. A elegibilidade deve ser cumprida pelo menos cinco dias úteis antes do final de cada mês civil para receber a Taxa reduzida de Serviço do Mercado. Uma vez cumprida a elegibilidade, a taxa de serviço reduzida é atribuída a todas as transações com efeito no primeiro dia do mês seguinte até que o estatuto de Co-venda de IP Azure seja perdido. Para obter mais informações sobre a elegibilidade de co-venda de IP, consulte [requisitos para o estado de co-venda](https://aka.ms/CertificationPolicies#3000-requirements-for-co-sell-status).

A Taxa reduzida de Serviço de Marketplace aplica-se à Azure IP Co-vender saaS, VMs, apps geridas e quaisquer outras soluções de IaAS transacionáveis qualificadas disponibilizadas através do mercado comercial. As ofertas pagas do SaaS associadas a uma aplicação da Microsoft Teams ou a pelo menos dois add-ins microsoft 365 (Excel, PowerPoint, Word, Outlook e SharePoint) e publicadas no Microsoft AppSource também recebem este desconto.

### <a name="examples"></a>Exemplos

**Baseada na utilização** 

Os preços baseados na utilização têm a seguinte estrutura de custos:

|O custo da sua licença  | $1,00 por hora   |
|---------|---------|
|Custo de utilização azul (D1/1-Core)    |   $0,14 por hora     |
|*O cliente é faturado pela Microsoft*    |  *$1,14 por hora*       |
||

Neste cenário, a Microsoft fatura 1,14 dólares por hora para utilização da sua imagem em VM publicada.

|Contas da Microsoft  | $1,14 por hora  |
|---------|---------|
|Microsoft paga-lhe 80% do custo da sua licença|   $0,80 por hora     |
|Microsoft mantém 20% do custo da sua licença  |  $0,20 por hora       |
|Microsoft mantém 100% do custo de utilização do Azure | $0,14 por hora |
||

**Traga a Sua Própria Licença (BYOL)**

A BYOL tem a seguinte estrutura de custos:

|O custo da sua licença  | Taxa de licença negociada e faturada por si  |
|---------|---------|
|Custo de utilização azul (D1/1-Core)    |   $0,14 por hora     |
|*O cliente é faturado pela Microsoft*    |  *$0,14 por hora*       |
||

Neste cenário, a Microsoft fatura $0,14 por hora para utilização da sua imagem VM publicada.

|Contas da Microsoft  | $0,14 por hora  |
|---------|---------|
|Microsoft mantém o custo de utilização do Azure    |   $0,14 por hora     |
|Microsoft mantém 0% do custo da sua licença   |  $0,00 por hora       |
||

**Subscrição de aplicativo SaaS**

Esta opção deve ser configurada para ser vendida através da Microsoft e pode ser avaliada a uma taxa fixa ou por utilizador numa base mensal ou anual. Se ativar a opção **Vender através** da Microsoft para uma oferta SaaS, tem a seguinte estrutura de custos:

| O custo da sua licença       | $100,00 por mês  |
|--------------|---------|
| Custo de utilização azul (D1/1-Core)    | Cobrado diretamente para o editor, não o cliente |
| *O cliente é faturado pela Microsoft*    |  *$100,00 por mês (o editor deve ter em conta quaisquer custos de infraestrutura incorridas ou pass-through na taxa de licença)*  |
||

Neste cenário, a Microsoft cobra 100,00 dólares pela sua licença de software e paga 80,00 dólares à editora.

Neste cenário, a Microsoft fatura $100,00 pela sua licença de software e paga $90,00 à editora:

|Contas da Microsoft  | $100,00 por mês  |
|---------|---------|
|Microsoft paga-lhe 80% do custo da sua licença <br> \* A Microsoft paga-lhe 90% do custo da sua licença por quaisquer aplicações SaaS qualificadas   |   $80,00 por mês <br> \* $90,00 por mês    |
|Microsoft mantém 20% do custo da sua licença <br> \* A Microsoft mantém 10% do custo da sua licença para quaisquer aplicações SaaS qualificadas.  |  $20,00 por mês <br> \* $10,00     |

### <a name="customer-invoicing-payment-billing-and-collections"></a>Faturação, pagamento, faturação e cobranças de clientes

**Faturação e pagamento** – Pode utilizar o método de faturação preferido do cliente para entregar taxas de subscrição ou licença de software PAYGO.

**Enterprise Agreement** – Se o método de faturação preferido do cliente for o Microsoft Enterprise Agreement, as suas taxas de licença de software serão faturadas usando este método de faturação como um custo itemtado, separado de quaisquer custos de utilização específicos do Azure.

**Cartões de crédito e fatura mensal** – Os clientes também podem pagar com cartão de crédito e uma fatura mensal. Neste caso, as suas taxas de licença de software serão faturadas tal como o cenário do Acordo Empresarial, como um custo itemtado, separado de quaisquer custos de utilização específicos do Azure.

**Créditos gratuitos e compromisso monetário** – Alguns clientes optam por pré-pagar a Azure com um compromisso monetário no Acordo Empresarial ou receberam créditos gratuitos para uso com a Azure. Embora estes créditos possam ser usados para pagar o uso do Azure, eles não podem ser usados para pagar as taxas de licença de software de editor.

**Faturação e coleções** – A faturação da licença de software da Editora é apresentada utilizando o método de faturação selecionado pelo cliente e segue a linha temporal de faturação. Os clientes sem um Acordo Empresarial em vigor são cobrados mensalmente para licenças de software de marketplace. Os clientes com Contrato Empresarial são faturados mensalmente através de uma fatura que é apresentada trimestralmente.

Quando os modelos de preços de subscrição ou Pay-as-You-Go são selecionados, a Microsoft atua como agente da editora e é responsável por todos os aspetos da faturação, pagamento e cobrança.

### <a name="publisher-payout-and-reporting"></a>Pagamento e reporte de editores

Quaisquer taxas de licenciamento de software cobradas pela Microsoft como agente estão sujeitas a uma taxa de transação de 20%, salvo especificação em contrário e deduzidas no momento do pagamento do editor.

Normalmente, os clientes compram usando o Contrato Empresarial ou um contrato de pagamento habilitado para o pagamento. O tipo de acordo determina a faturação, faturação, cobrança e tempo de pagamento.

>[!NOTE]
>Todos os relatórios e insights para a opção de publicação de transações estão disponíveis através da secção analytics do Partner Center.

#### <a name="billing-questions-and-support"></a>Perguntas de faturação e apoio

Para obter mais informações e políticas legais, consulte o [Microsoft Publisher Agreement](https://go.microsoft.com/fwlink/?LinkID=699560) (disponível no Partner Center).

Para obter ajuda em questões de faturação, contacte [o suporte de editores de marketplace comercial.](https://aka.ms/marketplacepublishersupport)

## <a name="transact-requirements"></a>Requisitos de transação

Esta secção abrange os requisitos de transação para diferentes tipos de oferta.

### <a name="requirements-for-all-offer-types"></a>Requisitos para todos os tipos de oferta

- Uma conta Microsoft e informações financeiras são necessárias para a opção de publicação de transações, independentemente do modelo de preços da oferta.
- A informação financeira obrigatória inclui conta de pagamento e perfil fiscal.
- O editor deve viver num [país ou região apoiado.](sell-from-countries.md)

Para obter mais informações sobre a configuração destas contas, consulte [Gerir a sua conta de mercado comercial no Partner Center.](partner-center-portal/manage-account.md)

### <a name="requirements-for-specific-offer-types"></a>Requisitos para tipos específicos de oferta

A opção de publicação de transações só está disponível para utilização com os seguintes tipos de oferta de mercado:

- **Azure Virtual Machine** – Selecione de modelos de preços gratuitos, de trazer a sua própria licença ou baseados em uso e apresente-se como planos definidos ao nível da oferta. Na conta Azure do cliente, a Microsoft apresenta as taxas de licença de software da editora separadamente das taxas de infraestrutura Azure subjacentes. As taxas de infraestrutura Azure são impulsionadas pelo uso do software da editora.

- **Aplicação Azure: modelo de solução ou app gerida** – Deve providenciar uma ou mais máquinas virtuais e puxar através da soma do preço da máquina virtual. Para aplicações geridas num único plano, uma subscrição mensal forfecária pode ser selecionada como o modelo de preços em vez do preço da máquina virtual. Em alguns casos, as taxas de utilização da infraestrutura Azure são passadas ao cliente separadamente das taxas de licença de software, mas na mesma declaração de faturação. No entanto, se configurar uma oferta de aplicações geridas para taxas de infraestrutura ISV, os recursos do Azure são faturados à editora, e o cliente recebe uma taxa fixa que inclui o custo de infraestruturas, licenças de software e serviços de gestão.

- **Aplicação SaaS** - Deve ser uma solução multitenante, utilizar [o Azure Ative Directory](https://azure.microsoft.com/services/active-directory/) para autenticação e integrar-se com as [APIs de Cumprimento do SaaS](partner-center-portal/pc-saas-fulfillment-api-v2.md). O uso da infraestrutura Azure é gerido e faturado diretamente para você (o parceiro), por isso você deve prestar contas das taxas de utilização da infraestrutura Azure e taxas de licenciamento de software como um item de custo único. Para obter orientações detalhadas, consulte [Criar uma nova oferta saaS no mercado comercial.](./create-new-saas-offer.md)

## <a name="next-steps"></a>Passos seguintes

- Reveja os requisitos de elegibilidade nas opções de publicação por secção de tipo oferta para finalizar a seleção e configuração da sua oferta.
- Reveja os padrões de publicação por loja online por exemplos sobre como a sua solução mapeia para um tipo de oferta e configuração.
