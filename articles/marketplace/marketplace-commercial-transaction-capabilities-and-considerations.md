---
title: Capacidades de transação de mercado comercial da Microsoft
description: Este artigo descreve os preços, a faturação, a faturação e as considerações de pagamento para a opção de transação do mercado comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 5058f37fcf96c7c556b55f0afb8753390f51f4d1
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607343"
---
# <a name="commercial-marketplace-transact-capabilities"></a>Capacidades de transação de mercado comercial

## <a name="transactions-by-publishing-option"></a>Transações por opção de publicação

A editora ou a Microsoft são responsáveis pela gestão de transações de licenças de software para ofertas no mercado comercial. A opção de publicação que escolher para a sua oferta determinará quem gere a transação. Consulte [Determine a sua opção de publicação](./determine-your-listing-type.md#choose-a-call-to-action) para disponibilidade e explicações de cada opção de publicação.

### <a name="list-trial-and-byol-publishing-options"></a>Opções de publicação de listas, julgamentos e BYOL

Os editores com capacidades de comércio existentes podem escolher opções de publicação de lista, teste e "bring-your-your-own-license" (BYOL) para fins promocionais e de aquisição de utilizadores. Com estas opções, a Microsoft não participa diretamente nas transações de licenças de software da editora e não há nenhuma taxa de transação associada. Os editores são responsáveis por apoiar todos os aspetos da transação de licença de software, incluindo, mas não se limitando à ordem, realização, medição, faturação, faturação, pagamento e cobrança. Com as opções de publicação de listas e testes, as editoras mantêm 100% das taxas de licenciamento de software de editores cobradas ao cliente.

### <a name="transact-publishing-option"></a>Opção de publicação transacionar

A opção de publicação de transações tira partido das capacidades de comércio da Microsoft e proporciona uma experiência de ponta a ponta desde a descoberta e avaliação até à compra e implementação. As ofertas transact são faturadas contra uma subscrição da Microsoft ou um cartão de crédito existente, permitindo à Microsoft hospedar transações de mercado na nuvem em nome da editora.

Escolha a opção de transação quando criar uma nova oferta no Partner Center. Na página **de configuração da Oferta** nos detalhes da **Configuração**, selecione "Sim, gostaria de vender através da Microsoft e ter transações de anfitriões da Microsoft em meu nome." Esta opção só será mostrada se o transact estiver disponível para o seu tipo de oferta.

## <a name="transact-overview"></a>Visão geral do Transt

Ao utilizar a opção de publicação de transações, a Microsoft permite a venda de software de terceiros e a implementação de alguns tipos de oferta para a subscrição Azure do cliente. Você, o editor, deve considerar a faturação das taxas de infraestrutura e as suas próprias taxas de licenciamento de software ao selecionar um modelo de faturação e tipo de oferta.

A opção de publicação de transações é atualmente suportada para os seguintes tipos de oferta:

- Máquinas virtuais
- Aplicações do Azure
- Aplicações SaaS

### <a name="billing-infrastructure-costs"></a>Custos de infraestrutura de faturação

Para **máquinas virtuais** e **aplicações Azure,** as taxas de utilização da infraestrutura Azure são cobradas à subscrição Azure do cliente. As taxas de utilização da infraestrutura são avaliadas e apresentadas separadamente das taxas de licenciamento do fornecedor de software na fatura do cliente.

Para **as Aplicações SaaS,** o editor deve prestar contas às taxas de utilização da infraestrutura Azure e às taxas de licenciamento de software como um único item de custo.  É representado como uma taxa fixa para o cliente. O uso da infraestrutura Azure é gerido e faturado diretamente ao parceiro. As taxas reais de utilização da infraestrutura não são vistas pelo cliente. Os editores normalmente optam por agregar as taxas de utilização da infraestrutura Azure nos seus preços de licença de software. As taxas de licenciamento de software não são baseadas em medições ou consumo.

## <a name="transact-billing-models"></a>Modelos de faturação transato

Dependendo da opção de transação utilizada, as taxas de licença de software são as seguintes:

- **Grátis** – Sem custo para licenças de software.
- **Traga a sua própria licença** (BYOL) – Quaisquer encargos aplicáveis para licenças de software são geridos diretamente entre o editor e o cliente. A Microsoft só passa pelas taxas de utilização da infraestrutura Azure. Isto aplica-se apenas a máquinas virtuais e aplicações Azure.
- **Pay-as-you-go** – As taxas de licença de software são apresentadas como uma taxa de preços por hora, por núcleo (vCPU) com base na infraestrutura Azure utilizada. Isto aplica-se apenas a máquinas virtuais e aplicações Azure.
- **Preços de subscrição** – As taxas de licença de software são apresentadas como uma taxa mensal ou anual, recorrente cobrada como uma taxa fixa ou por assento. Isto aplica-se apenas às aplicações SaaS (mensal ou anual) e Azure - aplicações geridas (mensalmente).
- **Teste de software gratuito** – Sem custo para licenças de software por 30 ou 90 dias.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Preços gratuitos e de entrega da sua própria licença (BYOL)

Ao publicar uma oferta de transação gratuita ou de entrega de licença própria, a Microsoft não desempenha um papel na facilitação da transação de vendas para as suas taxas de licença de software. Tal como a lista e as opções de publicação de testes, a editora mantém 100% das taxas de licença de software.

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Preços de pagamento e subscrição (baseado no site)

Ao publicar uma oferta de transação pay-as-you-go ou subscrição, a Microsoft fornece a tecnologia e serviços para processar compras, devoluções e cobranças de licenças de software. Neste cenário, a editora autoriza a Microsoft a agir como agente para estes fins. O editor permite à Microsoft facilitar a transação de licenciamento de software, mantendo ao mesmo tempo a sua designação como vendedor, fornecedor, distribuidor e licenciante.

A Microsoft permite que os clientes encomendem, licenciem e utilizem o seu software, sujeito aos termos e condições do mercado comercial da Microsoft e do seu acordo de licenciamento de utilizadores finais. Deve fornecer o seu próprio contrato de licenciamento de utilizador final ou selecionar o [Contrato Padrão](./standard-contract.md) ao criar a oferta.

### <a name="free-software-trials"></a>Ensaios de software gratuitos

Para cenários de publicação de transações, pode disponibilizar gratuitamente uma licença de software durante 30 ou 90 dias. Esta capacidade de desconto não inclui o custo da utilização da infraestrutura Azure impulsionada pela utilização da solução parceira.

### <a name="private-offers"></a>Ofertas privadas

Além de usar tipos de oferta e modelos de faturação para rentabilizar uma oferta, você pode transagir uma oferta privada, completa com preços negociados, específicos do negócio ou configurações personalizadas. As ofertas privadas são apoiadas pelas três opções de publicação de transações.

Esta opção permite preços mais altos ou mais baixos do que a oferta publicamente disponível. As ofertas privadas podem ser usadas para descontar ou adicionar um prémio a uma oferta. As ofertas privadas podem ser disponibilizadas a um ou mais clientes, listando a sua subscrição Azure ao nível da oferta.

### <a name="examples"></a>Exemplos

**Pay As You Go** 

Pay-As-You-Go tem a seguinte estrutura de custos:

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

Os parceiros que se qualificaram para a Taxa reduzida de Serviço do Mercado verão uma taxa de transação reduzida nas ofertas do SaaS de maio de 2019 até junho de 2020.

Neste cenário, a Microsoft fatura $100,00 pela sua licença de software e paga $90,00 à editora:

|Contas da Microsoft  | $100,00 por mês  |
|---------|---------|
|Microsoft paga-lhe 80% do custo da sua licença <br> \* A Microsoft paga-lhe 90% do custo da sua licença por quaisquer aplicações SaaS qualificadas   |   $80,00 por mês <br> \* $90,00 por mês    |
|Microsoft mantém 20% do custo da sua licença <br> \* A Microsoft mantém 10% do custo da sua licença para quaisquer aplicações SaaS qualificadas.  |  $20,00 por mês <br> \* $10,00     |

Para certas ofertas que publica no mercado comercial, a Microsoft reduzirá a sua Taxa de Serviço de Marketplace de 20% (conforme descrito no Microsoft Publisher Agreement) para 10%. Para que a sua(s) oferta(s) se qualifique, a sua(s) oferta(s) deve ter sido designada pela Microsoft como Azure IP Co-venda incentivado. A elegibilidade deve ser cumprida pelo menos cinco (5) dias úteis antes do final de cada mês civil para receber a Taxa reduzida de Serviço do Mercado para o mês. A Taxa reduzida de Serviço de Marketplace aplica-se à Azure IP Co-vender SaaS, VMs, Apps Geridas e quaisquer outras ofertas de iass transacionáveis qualificadas disponibilizadas através do mercado comercial.

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

Para obter mais informações e políticas legais, consulte o [Acordo de Editor](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4xqkx) (disponível no Partner Center).

Para obter ajuda em questões de faturação, contacte [o suporte de editores de marketplace comercial.](https://aka.ms/marketplacepublishersupport)

## <a name="transact-requirements"></a>Requisitos de transação

Esta secção abrange os requisitos de transação para diferentes tipos de oferta.

### <a name="requirements-for-all-offer-types"></a>Requisitos para todos os tipos de oferta

- Uma conta Microsoft e informações financeiras são necessárias para a opção de publicação de transações, independentemente do modelo de preços da oferta.
- A informação financeira obrigatória inclui conta de pagamento e perfil fiscal.

Para obter mais informações sobre a configuração destas contas, consulte [Gerir a sua conta de mercado comercial no Partner Center.](partner-center-portal/manage-account.md)

### <a name="requirements-for-specific-offer-types"></a>Requisitos para tipos específicos de oferta

A opção de publicação de transações só está disponível para utilização com os seguintes tipos de oferta de mercado:

- **Máquina virtual** – Selecione de modelos gratuitos, tragam a sua própria licença ou os modelos de preços pay-as-you-go e apresentem os planos definidos ao nível da oferta. Na conta Azure do cliente, a Microsoft apresenta as taxas de licença de software da editora separadamente das taxas de infraestrutura Azure subjacentes. As taxas de infraestrutura Azure são impulsionadas pelo uso do software da editora.

- **Aplicação Azure: modelo de solução ou app gerida** – Deve providenciar uma ou mais máquinas virtuais e puxar através da soma do preço da máquina virtual. Para aplicações geridas num único plano, uma subscrição mensal forfecária pode ser selecionada como o modelo de preços em vez do preço da máquina virtual. Em alguns casos, as taxas de utilização da infraestrutura Azure são passadas ao cliente separadamente das taxas de licença de software, mas na mesma declaração de faturação. No entanto, se configurar uma oferta de aplicações geridas para taxas de infraestrutura ISV, os recursos do Azure são faturados à editora, e o cliente recebe uma taxa fixa que inclui o custo de infraestruturas, licenças de software e serviços de gestão.

- **Aplicação SaaS** - Deve ser uma solução multitenante, utilizar [o Azure Ative Directory](https://azure.microsoft.com/services/active-directory/) para autenticação e integrar-se com as [APIs de Cumprimento do SaaS](partner-center-portal/pc-saas-fulfillment-api-v2.md). O uso da infraestrutura Azure é gerido e faturado diretamente para você (o parceiro), por isso você deve prestar contas das taxas de utilização da infraestrutura Azure e taxas de licenciamento de software como um item de custo único. Para obter orientações detalhadas, consulte [Criar uma nova oferta saaS no mercado comercial.](partner-center-portal/create-new-saas-offer.md)

## <a name="next-steps"></a>Passos seguintes

- Reveja os requisitos de elegibilidade nas opções de publicação por secção de tipo oferta para finalizar a seleção e configuração da sua oferta.
- Reveja os padrões de publicação por loja online por exemplos sobre como a sua solução mapeia para um tipo de oferta e configuração.
