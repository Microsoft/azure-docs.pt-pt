---
title: Considerações e funcionalidades de transação comercial do Marketplace | Azure
description: Este artigo descreve as considerações sobre preços, cobrança, faturamento e pagamento do Transact para um tipo de oferta.
services: Azure, Marketplace, Compute, Storage, Networking, Transact Offer Type
author: yijenj
manager: nuno costa
ms.service: marketplace
ms.topic: article
ms.date: 10/29/2018
ms.author: pabutler
ms.openlocfilehash: 6991ebfa58099a42b09e482d11c0d3c3983decce
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69535053"
---
# <a name="commercial-marketplace-transaction-capabilities-and-considerations"></a>Funcionalidades e considerações de transação do Marketplace comercial

Este artigo aborda os seguintes tópicos relacionados ao commerce para o Marketplace comercial

* Opções de publicação do Marketplace
* Visão geral do Transact
* Modelos de cobrança de transação
* Requisitos do Transact

## <a name="marketplace-publishing-options"></a>Opções de publicação do Marketplace

As opções de publicação a seguir estão disponíveis para editores do Marketplace comercial.

### <a name="list--trial-publishing-options"></a>Listar & opções de publicação de avaliação

Os editores podem aproveitar as opções de publicação de lista, avaliação e BYOL para fins promocionais e de aquisição do usuário. Com essas opções, a Microsoft não participa diretamente das transações de licença de software do editor e não há nenhuma taxa de transação associada. Os Publicadores são responsáveis por dar suporte a todos os aspectos da transação de licença de software, incluindo, entre outros: ordem, preenchimento, medição, cobrança, faturamento, pagamento e coleção. Com as opções de publicação de lista e de avaliação, os editores mantêm 100% das taxas de licenciamento de software do Publicador coletadas do cliente. 

### <a name="transact-publishing-option"></a>Opção de publicação Transact

Além das opções de publicação de lista e de avaliação, a opção Transact Publishing está disponível para editores. Isso aproveita os recursos de comércio globalmente disponíveis da Microsoft e permite que a Microsoft hospede transações de Marketplace de nuvem em nome do Publicador.

## <a name="transact-general-overview"></a>Visão geral do Transact

Ao usar a opção Transact Publishing, a Microsoft permite a venda de software de terceiros e a implantação de alguns tipos de oferta na assinatura do Azure do cliente. O Publicador deve considerar a cobrança de taxas de infraestrutura e as próprias tarifas de licenciamento de software do editor, ao selecionar um modelo de cobrança e um tipo de oferta.

Atualmente, a opção Transact Publishing tem suporte para os seguintes tipos de oferta: Máquinas virtuais, aplicativos do Azure e aplicativos SaaS.


![[Transagindo negócios corporativos no Azure Marketplace]](./media/marketplace-publishers-guide/Transact-enterprise-deals.png)

### <a name="billing-infrastructure-costs"></a>Custos de infraestrutura de cobrança

**Para máquinas virtuais e aplicativos do Azure**

Para máquinas virtuais e aplicativos do Azure, as tarifas de uso da infraestrutura do Azure são cobradas na assinatura do Azure do cliente.  As tarifas de uso de infraestrutura são cobradas e apresentadas separadamente das tarifas de licenciamento do provedor de software na fatura do cliente.

**Para aplicativos SaaS**

Para aplicativos SaaS, o Publicador deve considerar as tarifas de uso da infraestrutura do Azure e as taxas de licenciamento de software como um único item de custo.  Ele é representado como uma taxa fixa para o cliente. O uso da infraestrutura do Azure é gerenciado e cobrado diretamente para o parceiro.  As tarifas de uso de infraestrutura reais não são vistas pelo cliente.  Os editores normalmente optam por agrupar taxas de uso de infraestrutura do Azure em seus preços de licença de software.  As taxas de licenciamento de software não são limitadas ou com base no consumo.

## <a name="transact-billing-models"></a>Modelos de cobrança de transação

Dependendo da opção de transação usada, as taxas de licença de software do Publicador podem ser apresentadas da seguinte maneira:  

* Gratuito: Sem custos para licenças de software. 

* BYOL (traga sua própria licença): Todos os encargos aplicáveis para as licenças de software são gerenciados diretamente entre o Publicador e o cliente. A Microsoft passa apenas pelas tarifas de uso de infraestrutura do Azure. (Somente máquinas virtuais e aplicativos do Azure.)

* Pré-pago: As taxas de licença de software são apresentadas como uma taxa de preço por hora, por núcleo (vCPU) com base na infraestrutura do Azure usada. Isso se aplica somente a máquinas virtuais e aplicativos do Azure.

* • Preço da assinatura: As taxas de licença de software são apresentadas como uma taxa de recorrência mensal ou anual, cobrada como uma taxa fixa ou por estação. Isso se aplica somente a aplicativos SaaS e aplicativos do Azure.

* Avaliação de software gratuita: Nenhum encargo para licenças de software por 30 dias ou 90 dias.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Preços gratuitos e BYOL (traga sua própria licença)

Ao publicar uma oferta de transação gratuita ou traga sua própria licença, a Microsoft não desempenha uma função na facilitação da transação de vendas para suas taxas de licença de software. Assim como as opções de publicação de lista e de avaliação, o Publicador mantém de 100% das taxas de licença de software. 

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Preço pago conforme o uso e assinatura (baseado em site)

Preço do WPay e da assinatura ao publicar uma oferta de transação pré-paga ou de assinatura, a Microsoft fornece a tecnologia e os serviços para processar compras, Devoluções e estorno de licenças de software. Nesse cenário, o Publicador autoriza a Microsoft a atuar como um agente para essas finalidades. O Publicador permite que a Microsoft facilite a transação de licenciamento de software, preservando sua designação como vendedor, provedor, distribuidor e licenciante.

A Microsoft permite aos clientes solicitar, licenciar e usar o software do Publicador, sujeito aos termos e condições do mercado comercial da Microsoft e do contrato de licenciamento do usuário final do editor. Os editores devem fornecer seu contrato de licenciamento de usuário final ou selecionar o [contrato padrão](https://docs.microsoft.com/azure/marketplace/standard-contract) ao criar a oferta.


### <a name="free-software-trials"></a>Avaliações de software gratuitas

Para cenários de publicação de transações, o Publicador pode disponibilizar gratuitamente uma licença de software por 30 dias ou 90 dias. Esse recurso de descontação não inclui o custo do uso de infraestrutura do Azure que é controlado pelo uso da solução de parceiro.

### <a name="private-offers"></a>Ofertas privadas

Além de usar tipos de oferta e modelos de cobrança para monetizar uma oferta, os editores podem realizar transações de uma oferta privada, completa com preços negociados, específicos do negócio ou configurações personalizadas. As ofertas privadas têm suporte de todas as três opções de publicação Transact.

Essa opção permite um preço mais alto ou mais baixo do que a oferta publicamente disponível. As ofertas privadas podem ser usadas para descontos ou adicionar um Premium para uma oferta. As ofertas privadas podem ser disponibilizadas para um ou mais clientes, listando suas assinaturas do Azure no nível da oferta.


### <a name="examples"></a>Exemplos

**Pré-pago** 

* Se você habilitar a opção pré-paga, terá a seguinte estrutura de custo.

|Seu custo de licença  | $1 por hora  |
|---------|---------|
|Custo de uso do Azure (D1/1-núcleo)    |   $0.14 por hora     |
|*O cliente é cobrado pela Microsoft*    |  *$1.14 por hora*       |

* Nesse cenário, a Microsoft cobra $1.14 por hora para uso de sua imagem de VM publicada.

|Listas da Microsoft  | $1.14 por hora  |
|---------|---------|
|A Microsoft paga a você 80% do seu custo de licença|   $0.80 por hora     |
|A Microsoft mantém 20% do seu custo de licença  |  $0.20 por hora       |
|A Microsoft mantém 100% do custo de uso do Azure | $0.14 por hora |

**BYOL (traga sua própria licença)**

* Se você habilitar a opção BYOL, terá a seguinte estrutura de custo.

|Seu custo de licença  | Taxa de licença negociada e cobrada por você  |
|---------|---------|
|Custo de uso do Azure (D1/1-núcleo)    |   $0.14 por hora     |
|*O cliente é cobrado pela Microsoft*    |  *$0.14 por hora*       |

* Nesse cenário, a Microsoft cobra $0.14 por hora para uso de sua imagem de VM publicada.

|Listas da Microsoft  | $0.14 por hora  |
|---------|---------|
|A Microsoft mantém o custo de uso do Azure    |   $0.14 por hora     |
|A Microsoft mantém 0% do seu custo de licença   |  $0 por hora       |

**Assinatura de aplicativo SaaS**

Essa opção deve ser configurada para vender pela Microsoft e pode ser cobrada a uma taxa fixa ou por usuário em uma base mensal ou anual.
• Se você habilitar a opção vender por meio da Microsoft para uma oferta de SaaS, você terá a seguinte estrutura de custo.

|Seu custo de licença       | $100 por mês  |
|--------------|---------|
|Custo de uso do Azure (D1/1-núcleo)    | Cobrado diretamente no Publicador, não no cliente |
|*O cliente é cobrado pela Microsoft*    |  *$100 por mês (Observação: o Publicador deve considerar os custos incorridos ou de infraestrutura de passagem na taxa de licença)*  |

* Nesse cenário, a Microsoft cobra $100 pela sua licença de software e paga $80 para o Publicador.
* Os parceiros qualificados para a taxa reduzida de serviço do Marketplace verão uma taxa de transação reduzida nas ofertas de SaaS de maio de 2019 até junho de 2020. Nesse cenário, a Microsoft cobra $100 pela sua licença de software e paga $90 para o Publicador.

|Listas da Microsoft  | $100 por mês  |
|---------|---------|
|A Microsoft paga a você 80% do seu custo de licença <br> \*A Microsoft paga a você 90% do seu custo de licença para qualquer aplicativo SaaS qualificado   |   $80 por mês <br> \*$90 por mês    |
|A Microsoft mantém 20% do seu custo de licença <br> \*A Microsoft mantém 10% do seu custo de licença para qualquer aplicativo SaaS qualificado.  |  $20 por mês <br> \*$10     |

* **Taxa reduzida de serviço do Marketplace:** Para determinados produtos SaaS que você publica em nosso mercado comercial, a Microsoft reduzirá sua taxa de serviço do Marketplace de 20% (conforme descrito no contrato do Microsoft Publisher) para 10%.  Para que seu produto seja qualificado, pelo menos um de seus produtos deve ser designado pela Microsoft como uma das vendas de covenda de IP pronta ou de IP. Para receber essa taxa de serviço do Marketplace reduzida para o mês, a qualificação deve ser atendida pelo menos cinco (5) dias úteis antes do final do mês do calendário anterior. A taxa reduzida de serviço do Marketplace não se aplicará a VMs, aplicativos gerenciados ou quaisquer outros produtos disponibilizados por meio de nosso mercado comercial.  Essa taxa de serviço do Marketplace reduzida estará disponível para ofertas qualificadas, com encargos de licença coletados pela Microsoft entre 1º de maio de 2019 e 30 de junho de 2020.  Após esse período, a taxa de serviço do Marketplace voltará ao seu valor normal.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Faturamento, pagamento, cobrança e coleções de clientes

**Faturamento e pagamento**

O Publicador pode usar o método de faturamento preferido do cliente para fornecer assinatura ou taxas de licença de software PAYGO.

**Contrato Enterprise** 

Se o método de faturamento preferido do cliente for o Microsoft Enterprise Agreement, suas tarifas de licença de software serão cobradas usando esse método de faturamento como um custo discriminado, separados de qualquer custo de uso específico do Azure.

**Cartões de crédito e fatura mensal** 

Os clientes também podem pagar usando um cartão de crédito e uma nota fiscal mensal. Nesse caso, suas taxas de licença de software serão cobradas exatamente como o cenário de Enterprise Agreement, como um custo discriminado, separados de qualquer custo de uso específico do Azure.

Por exemplo, se o cliente comprar usando um cartão de crédito:

|Descrição    |    Date  |
|----------|----------|
|Período do pedido   | 15 de agosto de 2018-30 de agosto de 2018 |
|Final do termo (mês)   | 30 de agosto de 2018 |
|Data de cobrança | 1 de setembro de 2018 |
|Data de pagamento do cliente | 1 de setembro de 2018 |
|Período de caução (somente cartões de crédito, 30 dias) | 1 de setembro de 2018 – 30 de setembro de 2018 |
|Início do período de coleta | 1 de setembro de 2018 |
|Término do período de coleta (máximo, 30 dias) | 30 de setembro de 2018 |
|Data de cálculo do pagamento (mensalmente no dia 15) | 1 de outubro de 2018 |
|Data do pagamento | 15 de outubro de 2018 |

Se o cliente comprar usando uma Enterprise Agreement:

| Descrição |    Date  |
|----------|----------|
|Período do pedido | 15 de agosto de 2018-30 de agosto de 2018 |
|Final do termo (trimestre) | 30 de setembro de 2018 |
|Data de cobrança | 15 de outubro de 2018 |
|Período de caução (somente cartões de crédito, 30 dias) | n/d |
|Início do período de coleta | 15 de outubro de 2018 |
|Término do período de coleta (máximo, 90 dias) | 15 de janeiro de 2019 |
|Data de pagamento do cliente | 30 de dezembro de 2018 |
|Data de cálculo do pagamento (mensalmente no dia 15) | 15 de janeiro de 2019 |
|Data do pagamento | 15 de fevereiro de 2019 |

**Créditos gratuitos e compromisso monetário** 

Alguns clientes optam por pagar o Azure com um compromisso monetário no Enterprise Agreement ou foram fornecidos créditos gratuitos para uso com o Azure. Embora esses créditos possam ser usados para pagar pelo uso do Azure, eles não podem ser usados para pagar pelas taxas de licença de software do Publicador.

**Cobrança e coleções** 

A cobrança de licença de software do Publicador é apresentada usando o método de faturamento selecionado pelo cliente e segue a linha do tempo de faturamento. Os clientes sem um Enterprise Agreement em vigor são cobrados mensalmente por licenças de software do Marketplace. Os clientes com uma Enterprise Agreement são cobrados mensalmente por meio de uma nota fiscal que é apresentada trimestral.

Quando os modelos de preços de assinatura ou pré-pago são selecionados, a Microsoft atua como o agente do Publicador e é responsável por todos os aspectos de cobrança, pagamento e coleção.

### <a name="publisher-payout-and-reporting"></a>Pagamento e relatório do Publicador

* Todas as taxas de licenciamento de software coletadas pela Microsoft como um agente estão sujeitas a uma taxa de transação de 20%, a menos que especificado de outra forma e sejam deduzidos no momento do pagamento do Publicador.

* Os clientes normalmente compram usando o Enterprise Agreement ou um cartão de crédito habilitado para contrato pago conforme o uso. O tipo de contrato determina a cobrança, o faturamento, a coleta e o tempo de pagamento.

>[!NOTE] 
>Todos os relatórios e informações para a opção de publicação de transação estão disponíveis por meio da seção insights da seção Portal do Cloud Partner ou análise do Partner Center.

#### <a name="billing-questions-and-support"></a>Perguntas e suporte de cobrança

Para obter mais informações e políticas legais, consulte o [contrato do editor](https://cloudpartner.azure.com/Content/Unversioned/PublisherAgreement2.pdf) (disponível no portal do Cloud Partner).

Para obter ajuda sobre perguntas de cobrança, entre em contato com o [suporte do editor do Marketplace comercial](https://aka.ms/marketplacepublishersupport).

## <a name="transact-requirements"></a>Requisitos do Transact

Os requisitos de transação para diferentes tipos de oferta são abordados nesta seção.

### <a name="requirements-for-all-offer-types"></a>Requisitos para todos os tipos de oferta

- Uma conta Microsoft e informações financeiras são necessárias para a opção Transact Publishing, independentemente do modelo de preços da oferta.
- As informações financeiras obrigatórias incluem a conta de pagamento e o perfil de imposto.

Para obter mais informações sobre como configurar essas contas, consulte [gerenciar sua conta do Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/manage-account#financial-details).


### <a name="requirements-for-specific-offer-types"></a>Requisitos para tipos de oferta específicos

A opção Transact Publishing só está disponível para uso com os seguintes tipos de oferta do Marketplace: 

**Máquina Virtual** 

Selecione entre os modelos de preços gratuitos, traga sua própria licença ou pagos conforme o uso e apresente as SKUs definidas no nível da oferta. Na fatura do Azure do cliente, a Microsoft apresenta as taxas de licença de software do Publicador separadamente das taxas de infraestrutura subjacentes do Azure. As tarifas de infraestrutura do Azure são orientadas pelo uso do software do Publicador.

**Aplicativos do Azure: Modelo de solução ou aplicativo gerenciado** 

Deve provisionar uma ou mais máquinas virtuais e efetuar pull da soma dos preços da máquina virtual. Para aplicativos gerenciados em um único plano, uma assinatura mensal de taxa fixa pode ser selecionada como o modelo de preços em vez do preço da máquina virtual. Em alguns casos, as tarifas de uso da infraestrutura do Azure são passadas para o cliente separadamente de taxas de licença de software, mas no mesmo demonstrativo de cobrança. No entanto, se você configurar uma oferta de aplicativo gerenciado para encargos de infraestrutura de ISV, os recursos do Azure serão cobrados para o Publicador e o cliente receberá uma tarifa fixa que inclui o custo de infraestrutura, licenças de software e serviços de gerenciamento.

## <a name="next-steps"></a>Passos seguintes

* Reveja os requisitos de elegibilidade nas opções de publicação pela secção de tipo de oferta para finalizar a seleção e a configuração da sua oferta.
* Consulte os padrões de publicação, a loja para obter exemplos sobre como sua solução é mapeada para um tipo de oferta e a configuração.
