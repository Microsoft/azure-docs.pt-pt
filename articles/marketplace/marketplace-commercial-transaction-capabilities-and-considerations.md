---
title: Capacidades de transação de mercado comercial da Microsoft
description: Este artigo descreve os preços, a faturação, a faturação e as considerações de pagamento para a opção de transação do mercado comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2021
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 0a25e1b50455cad5bdbe5b76b2a291f2a1c11940
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107011"
---
# <a name="commercial-marketplace-transact-capabilities"></a>Capacidades de transação de mercado comercial

Este artigo descreve os preços, a faturação, a faturação e as considerações de pagamento para o mercado comercial da Microsoft.

## <a name="transactions-by-listing-option"></a>Transações por opção de listagem

A editora ou a Microsoft são responsáveis pela gestão de transações de licenças de software para ofertas no mercado comercial. A opção de listagem que escolhe para a sua oferta determina quem gere a transação. Para disponibilidade e explicações de cada opção de publicação, consulte [Introdução às opções de listagem](determine-your-listing-type.md)

### <a name="contact-me-free-trial-and-byol-options"></a>Contacte-me, teste gratuito e opções BYOL

Os editores podem escolher o _teste Contact me_ e _Free,_ opções para fins promocionais e de aquisição de utilizadores. Para alguns tipos de oferta, os editores podem escolher a opção trazer a _sua própria licença_ (BYOL) para permitir que os clientes comprem uma subscrição da sua oferta usando uma licença que compraram diretamente de si. Com estas opções, a Microsoft não participa diretamente nas transações de licenças de software da editora e não há taxa de transação associada, pelo que os editores mantêm todas essas receitas.

Com estas opções, os editores são responsáveis por suportar todos os aspetos da transação de licença de software. Isto inclui, mas não se limita à ordem, cumprimento, medição, faturação, faturação, pagamento e cobrança. Com a opção de listagem Contacte-me, as editoras mantêm 100% das taxas de licenciamento de software de editores cobradas ao cliente.

### <a name="transact-publishing-option"></a>Opção de publicação transacionar

Optar por vender através da Microsoft tira partido das capacidades de comércio da Microsoft e proporciona uma experiência de ponta a ponta desde a descoberta e avaliação até à compra e implementação. Uma oferta _transacionável_ é aquela em que a Microsoft facilita a troca de dinheiro por uma licença de software em nome da editora. As ofertas transact são faturadas contra uma subscrição ou cartão de crédito da Microsoft existente, permitindo à Microsoft hospedar transações de mercado na nuvem em nome da editora.

Escolha a opção de transação quando criar uma nova oferta no Partner Center. Esta opção só aparecerá se o transact estiver disponível para o seu tipo de oferta.

## <a name="transact-overview"></a>Visão geral do Transt

Ao utilizar a opção transact, a Microsoft permite a venda de software de terceiros e a implementação de alguns tipos de oferta para a subscrição Azure do cliente. O editor deve considerar a faturação das taxas de infraestrutura e as suas próprias taxas de licenciamento de software ao selecionar um modelo de preços para uma oferta.

A opção de publicação de transações é atualmente suportada para os seguintes tipos de oferta:

| Tipo de oferta | Cadência de faturação | Faturação medido | Modelo preços |
| ------------ | ------------- | ------------- | ------------- |
| Aplicação do Azure<br>(Aplicação gerida) | Mensalmente | Yes | Baseada na utilização |
| Máquina Virtual do Azure | Mensalmente * | No | Baseada em utilização, BYOL |
| Software como serviço (SaaS) | Mensal e anual | Yes | Taxa fixa, por utilizador, baseada na utilização. |
|||||

`*` A Azure Virtual Machine oferece planos de faturação baseados no uso. Estes planos são faturados mensalmente para uso horário da subscrição com base em por núcleo, por tamanho do núcleo, ou por mercado e tamanho de núcleo.

### <a name="metered-billing"></a>Faturação medido

O _serviço de medição do Marketplace_ permite especificar os encargos pay-as-you-go (baseado no consumo) para além dos encargos mensais ou anuais incluídos no contrato (direito). Você pode cobrar custos de utilização para as dimensões do serviço de medição de mercado que você especifica, tais como largura de banda, bilhetes ou e-mails processados. Para obter mais informações sobre a faturação medido para ofertas saaS, consulte [a faturação medido para o SaaS utilizando o serviço de medição de mercado comercial.](./partner-center-portal/saas-metered-billing.md) Para obter mais informações sobre a faturação medido para ofertas de aplicações Azure, consulte [a faturação de medimento de aplicação gerida.](./partner-center-portal/azure-app-metered-billing.md)

### <a name="billing-infrastructure-costs"></a>Custos de infraestrutura de faturação

Para **máquinas virtuais** e **aplicações Azure,** as taxas de utilização da infraestrutura Azure são cobradas à subscrição Azure do cliente. As taxas de utilização da infraestrutura são calculadas e apresentadas separadamente das taxas de licenciamento do fornecedor de software na fatura do cliente.

Para **as Aplicações SaaS,** o editor deve ter em conta as taxas de utilização da infraestrutura Azure e as taxas de licenciamento de software como um único item de custo. É representado como uma taxa fixa para o cliente. O uso da infraestrutura Azure é gerido e faturado diretamente ao editor. As taxas reais de utilização da infraestrutura não são vistas pelo cliente. Os editores normalmente optam por agregar as taxas de utilização da infraestrutura Azure nos seus preços de licença de software. As taxas de licenciamento de software não são medidos ou baseados no consumo do utilizador.

## <a name="pricing-models"></a>Modelos de preços

Dependendo da opção de transação utilizada, os encargos de subscrição são os seguintes:

- **Obtenha-o agora (Grátis)**: Sem custos para licenças de software. Ofertas gratuitas não podem ser convertidas para uma oferta paga. Os clientes devem pedir uma oferta paga.
- **Traga a sua própria licença** (BYOL): Se uma oferta estiver listada no mercado comercial, quaisquer encargos aplicáveis para licenças de software são geridos diretamente entre o editor e o cliente. A Microsoft apenas cobra taxas de utilização de infraestrutura Azure aplicáveis à conta de subscrição Azure do cliente.
- **Preços de subscrição**: As taxas de licença de software são apresentadas como uma taxa de subscrição mensal ou anual, recorrente, faturada como uma taxa fixa ou por assento. As taxas de subscrição recorrentes não são procidas para cancelamentos de clientes intercalares ou serviços não reutilizados. As taxas de subscrição recorrentes podem ser promovidas se o cliente atualizar ou desvalorizar a sua subscrição a meio do período de subscrição.
- **Preços baseados na utilização**: Para ofertas de Máquina Virtual Azure, os clientes são cobrados com base na extensão da sua utilização da oferta. Para imagens de Máquina Virtual, os clientes são cobrados uma taxa de Azure Marketplace de hora em hora, conforme definido pela editora, para uso de máquinas virtuais implantadas a partir das imagens VM. A taxa horária pode ser uniforme ou variada em tamanhos de máquinas virtuais. As horas parciais são cobradas ao minuto. Os planos são cobrados mensalmente.
- **Preços medidos**: Para ofertas de aplicações Azure e ofertas saaS, os editores podem usar o [serviço de medição do Marketplace](./partner-center-portal/marketplace-metering-service-apis.md) para faturar para consumo com base nas dimensões personalizadas do medidor que configuram. Estas alterações juntam-se aos encargos mensais ou anuais incluídos no contrato (direito). Exemplos de dimensões personalizadas dos medidores são largura de banda, bilhetes ou e-mails processados. Os editores podem definir uma ou mais dimensões para cada plano, mas um máximo de 30 por oferta. Os editores são responsáveis por rastrear o uso individual do cliente, com cada metro definido na oferta. Os eventos devem ser comunicados à Microsoft dentro de uma hora. A Microsoft cobra aos clientes com base nas informações de utilização comunicadas pelos editores para o período de faturação aplicável.
- **Ensaio gratuito**: Sem custo para licenças de software que variam entre 30 dias e seis meses, dependendo do tipo de oferta. Se os editores fornecerem um teste gratuito em vários planos dentro da mesma oferta, os clientes podem mudar para um teste gratuito em outro plano, mas o período experimental não reinicia. Para ofertas de máquinas virtuais, os clientes são cobrados custos de infraestrutura Azure para usar a oferta durante um período experimental. Após o termo do período experimental, os clientes são automaticamente cobrados pelo último plano que tentaram com base em tarifas padrão, a menos que cancelem antes do final do período experimental.

> [!NOTE]
> As ofertas que são faturadas de acordo com o consumo depois de uma solução ter sido utilizada não são elegíveis para reembolsos.

Os editores que pretendam alterar as taxas de utilização associadas a uma oferta, devem primeiro remover a oferta (ou o plano específico dentro da oferta) do mercado comercial. A remoção deve ser feita de acordo com os requisitos do [Microsoft Publisher Agreement](https://go.microsoft.com/fwlink/?LinkID=699560). Em seguida, o editor pode publicar uma nova oferta (ou plano dentro de uma oferta) que inclui as novas taxas de utilização. Para obter informações, sobre a remoção de uma oferta ou plano, consulte [Parar de vender uma oferta ou plano.](./partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan)

### <a name="free-contact-me-and-bring-your-own-license-byol-pricing"></a>Livre, Contacte-me e traga a sua própria licença (BYOL) preços

Ao publicar uma oferta com a opção Get it now (Free), Contact me, ou BYOL, a Microsoft não desempenha um papel na facilitação da transação de vendas para as suas taxas de licença de software. A editora fica com 100% das taxas de licença de software.

### <a name="usage-based-and-subscription-pricing"></a>Preços baseados na utilização e subscrição

Ao publicar uma oferta como uma transação baseada em uso ou subscrição, a Microsoft fornece a tecnologia e serviços para processar compras, devoluções e backs de licença de software. Neste cenário, a editora autoriza a Microsoft a agir como agente para estes fins. A editora permite à Microsoft facilitar a transação de licenciamento de software. O editor, mantenha a sua designação como vendedor, fornecedor, distribuidor e licenciante.

A Microsoft permite que os clientes encomendem, licenciem e utilizem o seu software, sujeito aos termos e condições do mercado comercial da Microsoft e do seu acordo de licenciamento de utilizadores finais. Você deve fornecer o seu próprio contrato de licenciamento de utilizador final ou selecionar o [Contrato Padrão](./standard-contract.md) ao criar a oferta.

### <a name="free-software-trials"></a>Ensaios de software gratuitos

Para cenários de publicação de transações, pode disponibilizar gratuitamente uma licença de software durante 30 a 120 dias, dependendo da subscrição. Os clientes serão alterados para o uso da infraestrutura Azure aplicável.

### <a name="examples-of-pricing-and-store-fees"></a>Exemplos de preços e taxas de loja

**Baseada na utilização**

Os preços baseados na utilização têm a seguinte estrutura de custos:

| **O custo da sua licença** | **$1,00 por hora** |
|---------|---------|
| Custo de utilização azul (D1/1-Core) | $0,14 por hora |
| _O cliente é faturado pela Microsoft_ | _$1,14 por hora_ |
||

Neste cenário, a Microsoft fatura 1,14 dólares por hora para utilização da sua imagem em VM publicada.

| **Contas da Microsoft** | **$1,14 por hora**  |
|---------|---------|
| Microsoft paga-lhe 80% do custo da sua licença | $0,80 por hora |
| Microsoft mantém 20% do custo da sua licença  |  $0,20 por hora |
| Microsoft mantém 100% do custo de utilização do Azure | $0,14 por hora |
||

**Traga a sua própria licença (BYOL)**

A BYOL tem a seguinte estrutura de custos:

| **O custo da sua licença** | **Taxa de licença negociada e faturada por si** |
|---------|---------|
|Custo de utilização azul (D1/1-Core)    |   $0,14 por hora     |
| _O cliente é faturado pela Microsoft_ | _$0,14 por hora_ |
||

Neste cenário, a Microsoft fatura $0,14 por hora para utilização da sua imagem VM publicada.

| **Contas da Microsoft** | **$0,14 por hora** |
|---------|---------|
| Microsoft mantém o custo de utilização do Azure | $0,14 por hora |
| Microsoft mantém 0% do custo da sua licença | $0,00 por hora |
||

**Subscrição de aplicativo SaaS**

As subscrições saaS podem ser avaliadas a uma taxa fixa ou por utilizador numa base mensal ou anual. Se ativar a opção  **Vender através** da Microsoft para uma oferta SaaS, tem a seguinte estrutura de custos:

| **O custo da sua licença** | **$100,00 por mês** |
|--------------|---------|
| Custo de utilização azul (D1/1-Core) | Cobrado diretamente para o editor, não o cliente |
| _O cliente é faturado pela Microsoft_ | _$100,00 por mês (o editor deve ter em conta quaisquer custos de infraestrutura incorridas ou pass-through na taxa de licença)_ |
||

Neste cenário, a Microsoft fatura $100,00 pela sua licença de software e paga 80,00 dólares ou 90,00 dólares a si, dependendo se a oferta se qualifica para uma taxa reduzida de serviço à loja.

| **Contas da Microsoft** | **$100,00 por mês** |
|---------|---------|
| Microsoft paga-lhe 80% do custo da sua licença <br> \* A Microsoft paga-lhe 90% do custo da sua licença por quaisquer aplicações SaaS qualificadas | $80,00 por mês <br> \* $90,00 por mês |
| Microsoft mantém 20% do custo da sua licença <br> \* A Microsoft mantém 10% do custo da sua licença para quaisquer aplicações SaaS qualificadas. | $20,00 por mês <br> \* $10,00 |

### <a name="commercial-marketplace-service-fees"></a>Taxas de serviço de mercado comercial

Cobramos uma taxa de serviço de loja padrão de 20% quando os clientes compram a sua oferta de transação no mercado comercial. Para mais informações sobre esta taxa, consulte a secção 5c do [Microsoft Publisher Agreement](https://go.microsoft.com/fwlink/?LinkID=699560).

Para certas ofertas transactais que publica no mercado comercial, poderá beneficiar de uma taxa reduzida de serviço de loja de 10%. Para uma oferta de qualificação, deve ter sido designada pela Microsoft como _Azure IP Co-venda incentivada_. A elegibilidade deve ser cumprida pelo menos cinco dias úteis antes do final de cada mês civil para receber a Taxa reduzida de Serviço do Mercado. Uma vez cumprida a elegibilidade, a taxa de serviço reduzida é atribuída a todas as transações com efeito no primeiro dia do mês seguinte até que o estatuto _de Co-venda de IP Azure seja_ perdido. Para obter mais informações sobre a elegibilidade de co-venda de IP, consulte [requisitos para o estado de co-venda](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status).

A Taxa reduzida de Serviço de Marketplace aplica-se à Azure IP Co-vender saaS, VMs, apps geridas e quaisquer outras soluções de IaAS transacionáveis qualificadas disponibilizadas através do mercado comercial. As ofertas pagas do SaaS associadas a uma aplicação da Microsoft Teams ou a pelo menos dois add-ins microsoft 365 (Excel, PowerPoint, Word, Outlook e SharePoint) e publicadas no Microsoft AppSource também podem ser elegíveis para este desconto.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Faturação, pagamento, faturação e cobranças de clientes

**Faturação e pagamento**: Pode utilizar o método de faturação preferido do cliente para entregar taxas de subscrição ou licença de software [PAYGO.](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)

**Acordo de Empresa**: Se o método de faturação preferido do cliente for o Microsoft Enterprise Agreement, as suas taxas de licença de software serão faturadas usando este método de faturação como um custo itemtado, separado de quaisquer custos de utilização específicos do Azure.

**Cartões de crédito e fatura mensal**: Os clientes podem pagar com cartão de crédito e uma fatura mensal. Neste caso, as suas taxas de licença de software serão faturadas tal como o cenário do Acordo Empresarial, como um custo itemtado, separado de quaisquer custos de utilização específicos do Azure.

**Créditos gratuitos e compromisso monetário**: Alguns clientes optam por pré-pagar a Azure com um compromisso monetário no Acordo Empresarial ou receberam créditos gratuitos para usar para uso Azure. Embora estes créditos possam ser usados para pagar o uso do Azure, eles não podem ser usados para pagar as taxas de licença de software de editor.

**Faturação e coleções**: A faturação da licença de software da editora é apresentada utilizando o método de faturação selecionado pelo cliente e segue a linha temporal de faturação. Os clientes sem um Acordo Empresarial em vigor são cobrados mensalmente para licenças de software de marketplace. Os clientes com Contrato Empresarial são faturados mensalmente através de uma fatura que é apresentada trimestralmente.

Quando os modelos de preços de subscrição ou Pay-as-You-Go (também denominados baseados em uso) são selecionados, a Microsoft atua como agente da editora e é responsável por todos os aspetos da faturação, pagamento e cobrança.

### <a name="publisher-payout-and-reporting"></a>Pagamento e reporte de editores

Quaisquer taxas de licenciamento de software cobradas pela Microsoft como agente estão sujeitas a uma taxa de transação de 20%, salvo especificação em contrário e deduzidas no momento do pagamento do editor.

Normalmente, os clientes compram usando o Contrato Empresarial ou um contrato de pagamento habilitado para o pagamento. O tipo de acordo determina a faturação, faturação, cobrança e tempo de pagamento.

>[!NOTE]
>Todos os relatórios e insights para a opção de publicação de transações estão disponíveis através da secção analytics do Partner Center.

#### <a name="billing-questions-and-support"></a>Perguntas de faturação e apoio

Para obter mais informações e políticas legais, consulte o [Microsoft Publisher Agreement](https://go.microsoft.com/fwlink/?LinkID=699560). Para obter ajuda com questões de faturação, contacte [o suporte de editores de marketplace comercial.](https://aka.ms/marketplacepublishersupport)

## <a name="transact-requirements"></a>Requisitos de transação

Esta secção abrange os requisitos de transação para diferentes tipos de oferta.

### <a name="requirements-for-all-offer-types"></a>Requisitos para todos os tipos de oferta

- Uma conta Microsoft e informações financeiras são necessárias para a opção de publicação de transações, independentemente do modelo de preços da oferta.
- A informação financeira obrigatória inclui conta de pagamento e perfil fiscal.

Para obter mais informações sobre a configuração destas contas, consulte [Gerir a sua conta de mercado comercial no Partner Center.](manage-account.md)

### <a name="requirements-for-specific-offer-types"></a>Requisitos para tipos específicos de oferta

A capacidade de transacionar através da Microsoft está disponível apenas para os seguintes tipos de oferta de mercado comercial. Esta lista fornece os requisitos para tornar estes tipos de oferta transacionáveis no mercado comercial.

- **Aplicação Azure (modelo de solução e planos de aplicação geridos**: Em alguns casos, as taxas de utilização da infraestrutura Azure são passadas ao cliente separadamente das taxas de licença de software, mas na mesma declaração de faturação. No entanto, se configurar um plano de aplicações gerido para taxas de infraestrutura ISV, os recursos do Azure são faturados à editora, e o cliente recebe uma taxa fixa que inclui o custo de infraestruturas, licenças de software e serviços de gestão.

- **Azure Virtual Machine**: Selecione de modelos de preços gratuitos, BYOL ou baseados em utilização. Na conta Azure do cliente, a Microsoft apresenta as taxas de licença de software da editora separadamente das taxas de infraestrutura Azure subjacentes. As taxas de infraestrutura Azure são impulsionadas pela utilização do software da editora.

- **Aplicação SaaS**: Deve ser uma solução multitenante, utilizar [o Azure Ative Directory](https://azure.microsoft.com/services/active-directory/) para autenticação e integrar-se com as [APIs de Cumprimento do SaaS](partner-center-portal/pc-saas-fulfillment-api-v2.md). O uso da infraestrutura Azure é gerido e faturado diretamente para si (o editor), pelo que deve ter em conta as taxas de utilização da infraestrutura Azure e as taxas de licenciamento de software como um único item de custo. Para obter orientações [detalhadas, consulte como planear uma oferta SaaS para o mercado comercial.](plan-saas-offer.md#plans)

## <a name="private-plans"></a>Planos privados

Você pode criar um plano privado para uma oferta, completo com preços negociados, específicos do negócio ou configurações personalizadas.

Os planos privados permitem-lhe fornecer preços mais altos ou mais baixos a clientes específicos do que a oferta publicamente disponível. Os planos privados podem ser usados para descontar ou adicionar um prémio a uma oferta. Os planos privados podem ser disponibilizados a um ou mais clientes, listando a sua subscrição Azure ao nível do plano.

## <a name="next-steps"></a>Passos seguintes

- Reveja os padrões de publicação por loja online por exemplos sobre como a sua solução mapeia para um tipo de oferta e configuração.
- [Guia de publicação por tipo de oferta.](publisher-guide-by-offer-type.md)
