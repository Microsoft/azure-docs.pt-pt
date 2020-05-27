---
title: Capacidades de transação de mercado comercial da Microsoft
description: Este artigo descreve as considerações de preços, faturação, faturação e pagamento para a opção transata do mercado comercial.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: 6cac7218c8f1fc48734b426c9e041831bf0b1f71
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800055"
---
# <a name="commercial-marketplace-transact-capabilities"></a>Capacidades transatas de mercado comercial

## <a name="transactions-by-publishing-option"></a>Transações por opção de publicação

Ou a editora ou a Microsoft são responsáveis pela gestão de transações de licenças de software para ofertas no mercado comercial. A opção de publicação que escolher para a sua oferta determinará quem gere a transação. Consulte [Determine a sua opção](./determine-your-listing-type.md#choose-a-publishing-option) de publicação para disponibilidade e explicações de cada opção de publicação.

### <a name="list-trial-and-byol-publishing-options"></a>Lista, julgamento e opções de publicação BYOL

As editoras com capacidades de comércio existentes podem escolher opções de publicação de listas, ensaios e de envio da sua própria licença (BYOL) para fins promocionais e de aquisição de utilizadores. Com estas opções, a Microsoft não participa diretamente nas transações de licenças de software da editora e não existe nenhuma taxa de transação associada. Os editores são responsáveis por apoiar todos os aspetos da transação de licençade software, incluindo, mas não se limitando à ordem, realização, medição, faturação, faturação, pagamento e cobrança. Com as opções de publicação da lista e do teste, as editoras mantêm 100% das taxas de licenciamento de software da editora cobradas ao cliente.

### <a name="transact-publishing-option"></a>Opção de publicação Transact

A opção de publicação transata tira partido das capacidades de comércio da Microsoft e proporciona uma experiência de ponta a ponta, desde a descoberta e avaliação até à compra e implementação. As ofertas transactsão são faturadas contra uma subscrição da Microsoft existente ou um cartão de crédito, permitindo à Microsoft hospedar transações de marketplace na nuvem em nome da editora.

Escolhe a opção de transação quando cria uma nova oferta no Partner Center. Na página **de configuração** da Oferta, segundo os detalhes da **Configuração,** selecione "Sim, gostaria de vender através da Microsoft e ter transações de anfitriões da Microsoft em meu nome." Esta opção só será mostrada se o transact estiver disponível para o seu tipo de oferta.

## <a name="transact-overview"></a>Visão geral transata

Ao utilizar a opção de publicação transata, a Microsoft permite a venda de software de terceiros e a implementação de alguns tipos de oferta para a subscrição do Cliente Azure. Você, o editor deve considerar a faturação das taxas de infraestrutura e as suas próprias taxas de licenciamento de software ao selecionar um modelo de faturação e tipo de oferta.

A opção de publicação transata é atualmente suportada para os seguintes tipos de oferta:

- Máquinas virtuais
- Aplicações do Azure
- Aplicações SaaS

### <a name="billing-infrastructure-costs"></a>Custos de infraestrutura de faturação

Para **Máquinas Virtuais e Aplicações Azure,** as taxas de utilização da infraestrutura Azure são faturadas para a subscrição azure do cliente. As taxas de utilização da infraestrutura são avaliadas e apresentadas separadamente das taxas de licenciamento do fornecedor de software na fatura do cliente.

Para **as Aplicações SaaS,** a editora deve prestar contas das taxas de utilização da infraestrutura Azure e das taxas de licenciamento de software como um item de custo único.  É representado como uma taxa fixa para o cliente. O uso da infraestrutura Azure é gerido e faturado diretamente para o parceiro. As taxas reais de utilização da infraestrutura não são vistas pelo cliente. Normalmente, os editores optam por agregar taxas de utilização da infraestrutura Azure nos seus preços de licença de software. As taxas de licenciamento de software não são medidas ou baseadas no consumo.

## <a name="transact-billing-models"></a>Modelos de faturação transata

Dependendo da opção de transação utilizada, as taxas de licença de software são as seguintes:

- **Grátis** – Sem custos para licenças de software.
- **Traga a sua própria licença** (BYOL) – Quaisquer encargos aplicáveis para licenças de software são geridos diretamente entre o editor e o cliente. A Microsoft só passa pelas taxas de utilização da infraestrutura Azure. Isto aplica-se apenas a Máquinas Virtuais e Aplicações Azure.
- **Pay-as-you-go** – As taxas de licença de software são apresentadas como uma taxa de preços por hora, por núcleo (vCPU) com base na infraestrutura Azure utilizada. Isto aplica-se apenas a Máquinas Virtuais e Aplicações Azure.
- **Preços de subscrição** – As taxas de licença de software são apresentadas como uma taxa mensal ou anual, recorrente, faturada como uma taxa fixa ou por assento. Isto aplica-se apenas às Aplicações SaaS e aplicações Azure - Aplicações Geridas.
- **Teste** gratuito de software – Não há custo para licenças de software durante 30 ou 90 dias.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Preços gratuitos e de trazer a sua própria licença (BYOL)

Ao publicar uma oferta de transação gratuita ou de trazer a sua própria licença, a Microsoft não tem um papel a desempenhar na facilitação da transação de vendas para as suas taxas de licença de software. Tal como a lista e as opções de publicação de testes, a editora mantém 100% das taxas de licença de software.

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Preços de pay-as-you-go e subscrição (baseado no site)

Ao publicar uma oferta de transação de pagamento ou subscrição, a Microsoft fornece a tecnologia e serviços para processar compras de licenças de software, devoluções e cobranças. Neste cenário, a editora autoriza a Microsoft a agir como um agente para estes fins. A editora permite que a Microsoft facilite a transação de licenciamento de software, mantendo a sua designação como vendedor, fornecedor, distribuidor e licenciante.

A Microsoft permite que os clientes encomendem, licenciem e utilizem o seu software, sujeitos aos termos e condições do mercado comercial da Microsoft e do seu contrato de licenciamento de utilizador final. Deve fornecer o seu próprio contrato de licenciamento de utilizador final ou selecionar o [Contrato Standard](./standard-contract.md) ao criar a oferta.

### <a name="free-software-trials"></a>Ensaios de software gratuitos

Para cenários de publicação transata, pode disponibilizar gratuitamente uma licença de software durante 30 ou 90 dias. Esta capacidade de desconto não inclui o custo da utilização da infraestrutura Azure impulsionada pela utilização da solução parceira.

### <a name="private-offers"></a>Ofertas privadas

Além de utilizar tipos de oferta e modelos de faturação para rentabilizar uma oferta, pode negociar uma oferta privada, completada com preços negociados, específicos do negócio ou configurações personalizadas. As ofertas privadas são apoiadas pelas três opções de publicação transata.

Esta opção permite preços mais altos ou mais baixos do que a oferta publicamente disponível. As ofertas privadas podem ser usadas para descontar ou adicionar um prémio a uma oferta. As ofertas privadas podem ser disponibilizadas a um ou mais clientes através da listagem branca da sua subscrição Azure ao nível da oferta.

### <a name="examples"></a>Exemplos

**Pay As You Go** 

Pay-As-You-Go tem a seguinte estrutura de custos:

|O custo da sua licença  | $1,00 por hora   |
|---------|---------|
|Custo de utilização do Azure (D1/1-Core)    |   $0,14 por hora     |
|*Cliente é cobrado pela Microsoft*    |  *$1,14 por hora*       |
||

Neste cenário, a Microsoft cobra 1,14 dólares por hora para uso da sua imagem VM publicada.

|Contas da Microsoft  | $1,14 por hora  |
|---------|---------|
|Microsoft paga-lhe 80% do custo da sua licença|   $0,80 por hora     |
|Microsoft mantém 20% do custo da sua licença  |  $0,20 por hora       |
|Microsoft mantém 100% do custo de utilização do Azure | $0,14 por hora |
||

**Traga a sua própria licença (BYOL)**

A BYOL tem a seguinte estrutura de custos:

|O custo da sua licença  | Taxa de licença negociada e cobrada por si  |
|---------|---------|
|Custo de utilização do Azure (D1/1-Core)    |   $0,14 por hora     |
|*Cliente é cobrado pela Microsoft*    |  *$0,14 por hora*       |
||

Neste cenário, a Microsoft cobra $0,14 por hora para uso da sua imagem VM publicada.

|Contas da Microsoft  | $0,14 por hora  |
|---------|---------|
|Microsoft mantém o custo de utilização do Azure    |   $0,14 por hora     |
|Microsoft mantém 0% do custo da sua licença   |  $0,00 por hora       |
||

**Subscrição da App SaaS**

Esta opção deve ser configurada para vender através da Microsoft e pode ser avaliada a uma taxa fixa ou por utilizador numa base mensal ou anual. Se ativar a opção **Vender através** da Microsoft para uma oferta SaaS, tem a seguinte estrutura de custos:

| O custo da sua licença       | $100,00 por mês  |
|--------------|---------|
| Custo de utilização do Azure (D1/1-Core)    | Faturado diretamente para o editor, não para o cliente |
| *Cliente é cobrado pela Microsoft*    |  *$100,00 por mês (o editor deve explicar quaisquer custos de infraestrutura incorridos ou pass-through na taxa de licença)*  |
||

Neste cenário, a Microsoft cobra $100,00 pela sua licença de software e paga $80,00 à editora.

Os parceiros que se qualificaram para a Taxa de Serviço de Mercado Reduzido verão uma taxa de transação reduzida nas ofertas saaS de maio de 2019 a junho de 2020.

Neste cenário, a Microsoft cobra $100,00 pela sua licença de software e paga $90,00 à editora:

|Contas da Microsoft  | $100,00 por mês  |
|---------|---------|
|Microsoft paga-lhe 80% do custo da sua licença <br> \*Microsoft paga-lhe 90% do custo da sua licença por quaisquer aplicações SaaS qualificadas   |   $80,00 por mês <br> \*$90,00 por mês    |
|Microsoft mantém 20% do custo da sua licença <br> \*A Microsoft mantém 10% do custo da sua licença para quaisquer aplicações SaaS qualificadas.  |  $20,00 por mês <br> \*$10,00     |

Para certos Produtos SaaS que publica no nosso Mercado Comercial, a Microsoft reduzirá a sua Taxa de Serviço de **Marketplace** de 20% (conforme descrito no Microsoft Publisher Agreement) para 10%. Para que a sua oferta se qualifique, pelo menos uma das suas ofertas deve ser designada pela Microsoft como co-venda ip pronta ou co-venda IP priorizada. Para receber esta taxa reduzida de serviço ao mercado para o mês, a elegibilidade deve ser satisfeita pelo menos cinco dias úteis antes do final do mês civil anterior. A taxa reduzida do Serviço de Mercado não se aplicará a VMs, Aplicações Geridas ou quaisquer outros produtos disponibilizados através do mercado comercial. Esta taxa reduzida estará disponível para ofertas qualificadas, com taxas de licença cobradas pela Microsoft entre 1 de maio de 2019 e 30 de junho de 2020. Após esse tempo, a taxa voltará ao seu valor normal.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Faturação do cliente, pagamento, faturação e cobranças

**Faturação e pagamento** – Pode utilizar o método de faturação preferido do cliente para entregar taxas de licença de subscrição ou de licença de software PAYGO.

**Acordo de Empresa** – Se o método de faturação preferido do cliente for o Microsoft Enterprise Agreement, as suas taxas de licença de software serão faturadas utilizando este método de faturação como um custo item, separado de quaisquer custos de utilização específicos do Azure.

**Cartões** de crédito e fatura mensal – Os clientes também podem pagar com cartão de crédito e uma fatura mensal. Neste caso, as suas taxas de licença de software serão cobradas tal como o cenário do Acordo de Empresa, como um custo item, separado de quaisquer custos de utilização específicos do Azure.

**Créditos gratuitos e compromisso monetário** – Alguns clientes optam por pré-pagar o Azure com um compromisso monetário no Acordo Empresarial ou receberam créditos gratuitos para uso com o Azure. Embora estes créditos possam ser usados para pagar o uso do Azure, não podem ser usados para pagar as taxas de licença de software da editora.

**Faturação e coleções** – A faturação da licença de software da Editora é apresentada utilizando o método de faturação selecionado pelo cliente e segue a linha temporal de faturação. Os clientes sem um Acordo de Empresa em vigor são cobrados mensalmente para licenças de software de marketplace. Os clientes com Um Acordo Empresarial são cobrados mensalmente através de uma fatura que é apresentada trimestralmente.

Quando são selecionados os modelos de preços pay-as-You-Go, a Microsoft atua como agente da editora e é responsável por todos os aspetos da faturação, pagamento e cobrança.

### <a name="publisher-payout-and-reporting"></a>Pagamento e reportagem da editora

Quaisquer taxas de licenciamento de software cobradas pela Microsoft como agente estão sujeitas a uma taxa de transação de 20%, salvo especificação em contrário e deduzidas no momento do pagamento do editor.

Os clientes normalmente compram usando o Acordo de Empresa ou um cartão de crédito habilitado a pagar como você-go acordo. O tipo de acordo determina a faturação, faturação, cobrança e pagamento.

>[!NOTE]
>Todos os relatórios e insights para a opção de publicação transacte estão disponíveis através da secção Analytics do Partner Center.

#### <a name="billing-questions-and-support"></a>Perguntas de faturação e apoio

Para obter mais informações e políticas legais, consulte o Acordo de [Editor](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt) (disponível no Partner Center).

Para obter ajuda em questões de faturação, contacte o suporte da [editora de marketplace comercial.](https://aka.ms/marketplacepublishersupport)

## <a name="transact-requirements"></a>Requisitos transatas

Esta secção cobre os requisitos de transação para diferentes tipos de oferta.

### <a name="requirements-for-all-offer-types"></a>Requisitos para todos os tipos de oferta

- Uma conta Microsoft e informações financeiras são necessárias para a opção de publicação transata, independentemente do modelo de preços da oferta.
- A informação financeira obrigatória inclui conta de pagamento e perfil fiscal.

Para obter mais informações sobre a configuração destas contas, consulte [Gerir a sua conta de mercado comercial no Partner Center).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/manage-account)

### <a name="requirements-for-specific-offer-types"></a>Requisitos para tipos de oferta específicos

A opção de publicação transata só está disponível para utilização com os seguintes tipos de oferta de mercado:

- **Máquina Virtual** – Selecione a partir de modelos gratuitos, bring-your-your-own ou pay-as-you-go-pricing e apresentar como SKUs definido ao nível da oferta. Na conta do cliente Azure, a Microsoft apresenta as taxas de licença de software da editora separadamente das taxas de infraestrutura azure subjacentes. As taxas de infraestrutura azure são impulsionadas pelo uso do software da editora.
- **Aplicações Azure: Modelo** de solução ou App Gerida – Deve fornecer uma ou mais máquinas virtuais e puxar através da soma dos preços da máquina virtual. Para aplicações geridas num único plano, uma subscrição mensal forfável pode ser selecionada como o modelo de preços, em vez do preço da máquina virtual. Em alguns casos, as taxas de utilização da infraestrutura Azure são passadas ao cliente separadamente das taxas de licença de software, mas na mesma declaração de faturação. No entanto, se configurar uma oferta de App Gerida para taxas de infraestrutura ISV, os recursos Do Azure são faturados para a editora, e o cliente recebe uma taxa fixa que inclui o custo de infraestrutura, licenças de software e serviços de gestão.

## <a name="next-steps"></a>Passos seguintes

- Reveja os requisitos de elegibilidade nas opções de publicação por secção de tipo de oferta para finalizar a seleção e configuração da sua oferta.
- Reveja os padrões de publicação por montra, por exemplo, sobre como a sua solução mapeia um tipo de oferta e configuração.
