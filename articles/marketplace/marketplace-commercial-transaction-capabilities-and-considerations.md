---
title: Capacidades de transação comercial do Marketplace e considerações | Azure
description: Este artigo descreve as considerações de preços, faturação, faturação e dividendos Transact para um tipo de oferta.
services: Azure, Marketplace, Compute, Storage, Networking, Transact Offer Type
author: yijenj
manager: nuno costa
ms.service: marketplace
ms.topic: article
ms.date: 10/29/2018
ms.author: yijenj
ms.openlocfilehash: d266b314f19979578b7e7b8de4e7a7090200c9d2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445443"
---
# <a name="commercial-marketplace-transaction-capabilities-and-considerations"></a>Capacidades de transação de marketplace comerciais e considerações

Este artigo abrange os seguintes tópicos relacionados com o comércio de para o marketplace comercial

* Opções de publicação do Marketplace
* Visão geral de Transact
* Transact modelos de faturação
* Transact requisitos

## <a name="marketplace-publishing-options"></a>Opções de publicação do Marketplace

As seguintes opções de publicação estão disponíveis aos editores do marketplace comercial.

### <a name="list--trial-publishing-options"></a>Lista & de opções de publicação de avaliação

Os publicadores podem aproveitar a lista, avaliação e opções para a publicação de BYOL promocionais e fins de aquisição de utilizador. Com estas opções, Microsoft não participa diretamente em transações de licença de software do publicador e existe uma taxa de transação associada. Os publicadores são responsáveis por suporte de todos os aspetos da transação de licença de software, incluindo mas não limitado a: ordem, preenchimento, medição, faturação, faturação, pagamento e coleção. Com a lista e opções de publicação de avaliação, os publicadores fique com 100% das taxas de licenciamento de software de publicador recolhidas a partir do cliente. 

### <a name="transact-publishing-option"></a>Transact a opção de publicação

Além da lista e opções de publicação de avaliação, a opção de publicação do transact está disponível para os editores. Isso tira partido das capacidades de comércio disponível globalmente da Microsoft e permite que a Microsoft para transações de marketplace do anfitrião na cloud em nome do publicador.

## <a name="transact-general-overview"></a>Visão geral de Transact

Ao utilizar o transact a opção de publicação, a Microsoft permite que a venda de software de terceiros e a implantação de alguns tipos de oferta para a subscrição do cliente do Azure. O publicador tem de considerar a faturação das taxas de infraestrutura e do publicador licenciamento taxas de software, ao selecionar uma faturação de modelo e tipo de oferta.

A opção de publicação do Transact atualmente é suportada para os seguintes tipos de oferta: Máquinas virtuais, aplicações do Azure e aplicações SaaS.


![[Transacionais Enterprise lida no Azure Marketplace]](./media/marketplace-publishers-guide/Transact-enterprise-deals.png)

### <a name="billing-infrastructure-costs"></a>Custos de infraestrutura de faturação

**Para máquinas virtuais e aplicações do Azure**

Para máquinas virtuais e aplicações do Azure, as tarifas de utilização da infraestrutura do Azure são faturadas para a subscrição do cliente do Azure.  As tarifas de utilização de infraestrutura são um preço e apresentadas em separado de taxas de licenciamento do fornecedor de software na fatura do cliente.

**Para aplicações SaaS**

Para aplicações SaaS, o publicador tem em conta as tarifas de utilização da infraestrutura do Azure e taxas de licenciamento de software como um item único custo.  Este é representado como uma tarifa fixa para o cliente. A utilização de infraestrutura do Azure é gerenciada e faturada diretamente para o parceiro.  As tarifas de utilização da infraestrutura real não são vistas pelo cliente.  Os publicadores, normalmente, optar por agrupar as tarifas de utilização da infraestrutura do Azure nos respetivos preços de licença de software.  Não são limitadas de taxas de licenciamento de software ou baseado no consumo.

## <a name="transact-billing-models"></a>Transact modelos de faturação

Consoante a opção de transação utilizada, as taxas de licença de software do publicador podem ser apresentadas da seguinte forma:  

* Gratuito: Sem custos para licenças de software. 

* Traga a sua própria licença (BYOL): Quaisquer custos aplicáveis de licenças de software são geridos diretamente entre o publicador e o cliente. Microsoft apenas passa pelas tarifas de utilização da infraestrutura do Azure. (As máquinas virtuais e apenas as aplicações do Azure).

* Pay as you go: As taxas de licença de software são apresentadas como uma por hora, por núcleo (Vcpus) preços de taxa com base na infraestrutura do Azure utilizada. Isto aplica-se apenas a máquinas virtuais e aplicações do Azure.

* • Preço da subscrição: As taxas de licença de software são apresentadas como um mensais ou anuais recorrente taxa cobrada como uma taxa fixa ou por posto de trabalho. Isto aplica-se apenas para aplicações SaaS e aplicações do Azure – aplicações geridas.

* Versão de avaliação gratuito software: Sem custos para licenças de software para 30 dias ou 90 dias.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Gratuito e bring-your-own-license (BYOL) de preços

Ao publicar uma oferta de transações gratuitas ou bring-your-own-license, Microsoft não têm uma função em facilitar a transação de vendas de suas taxas de licença de software. Tal como a lista e opções de publicação de avaliação, o publicador mantém 100% das taxas de licença de software. 

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Pay as you go e o preço da subscrição (baseado no site)

WPay-como--go e o preço da subscrição quando publicar uma oferta de transação pay as you go ou subscrição, a Microsoft fornece a tecnologia e serviços para processar a licença de software de compras, retorna e estornos. Neste cenário, o publicador autoriza a Microsoft para agir como um agente para essas finalidades. O publicador permite à Microsoft facilitar o transação, retendo suas designações como o vendedor, o fornecedor, o distribuidor e o licenciador de licenciamento de software.

Microsoft permite aos clientes a ordem, licenças e software do fabricante de utilização, a submeter o departamento para os termos e condições de mercado de comercial da Microsoft e o contrato de licença de utilizador final do Editor. Os publicadores têm de fornecer o respetivo contrato de licenciamento do utilizador final ou selecione o [contrato padrão](https://docs.microsoft.com/azure/marketplace/standard-contract) ao criar a oferta.


### <a name="free-software-trials"></a>Versões de avaliação do software gratuito

Para transact cenários de publicação, o publicador pode fazer um software de licença disponíveis gratuitamente durante 30 dias ou 90 dias. Esta capacidade discounting não inclui o custo de utilização de infraestrutura do Azure que é orientada pela utilização da solução de parceiro.

### <a name="private-offers"></a>Ofertas privadas

Além de usar oferecem tipos e modelos para de faturação lucrar com uma oferta, os publicadores podem transacionar uma oferta privada, completa com preços negociado e específicos do negócio, ou configurações personalizadas. Ofertas privadas são suportadas por todos os 3 transact as opções de publicação.

Esta opção permite que os preços de versões superiores ou inferiores do que a oferta disponível ao público. Ofertas privadas podem ser utilizadas para o desconto ou adicionar uma premium para uma oferta. Ofertas privadas podem ser disponibilizadas para os clientes de um ou mais pelo white listagem a sua subscrição do Azure ao nível da oferta.


### <a name="examples"></a>Exemplos

**Pay as you go** 

* Se ativar a opção pay as you go, terá a seguinte estrutura de custos.

|A custo da licença  | US $1,00 por hora  |
|---------|---------|
|Custo de utilização do Azure (D1/1 núcleo)    |   US $0,14 por hora     |
|*Ser-lhe cobrado pela Microsoft*    |  *US $1.14 por hora*       |

* Neste cenário, a Microsoft cobra 1.14 us $ por hora pela utilização da sua imagem de VM publicada.

|Listas de Microsoft  | US $1.14 por hora  |
|---------|---------|
|Microsoft presta 80% dos gastos de licenciamento|   US $0.80 por hora     |
|Microsoft fica com 20% dos gastos de licenciamento  |  US $0.20 por hora       |
|Microsoft mantém a 100% do custo de utilização do Azure | US $0,14 por hora |

**Traga a sua própria licença (BYOL)**

* Se ativar a opção de BYOL, terá a seguinte estrutura de custos.

|A custo da licença  | Valor da licença negociado e são faturadas por si  |
|---------|---------|
|Custo de utilização do Azure (D1/1 núcleo)    |   US $0,14 por hora     |
|*Ser-lhe cobrado pela Microsoft*    |  *US $0,14 por hora*       |

* Neste cenário, a Microsoft cobra 0,14 us $ por hora pela utilização da sua imagem de VM publicada.

|Listas de Microsoft  | US $0,14 por hora  |
|---------|---------|
|Microsoft mantém o custo de utilização do Azure    |   US $0,14 por hora     |
|Microsoft mantém 0% dos gastos de licenciamento   |  0,00 us $ por hora       |

**Subscrição de aplicação SaaS**

Esta opção deve ser configurada para venda através de Microsoft e pode ter preços a uma taxa fixa ou por utilizador numa base mensal ou anual.
• Se ativar o destino de venda por meio da opção da Microsoft para uma oferta de SaaS, terá a seguinte estrutura de custo.

|A custo da licença       | US $100,00 por mês  |
|--------------|---------|
|Custo de utilização do Azure (D1/1 núcleo)    | Cobrado diretamente para o publicador, não é o cliente |
|*Ser-lhe cobrado pela Microsoft*    |  *US $100,00 por mês (Nota: publicador deve levar em conta quaisquer custos de infraestrutura incorridos ou pass-through o valor da licença)*  |

* Neste cenário, a Microsoft cobra $100,00 para sua licença de software e paga os us $80.00 ao publicador.
* Parceiros que qualificaram para a taxa de serviço do Marketplace reduzido irão ver que uma taxa reduzida de transação no SaaS oferece desde Maio de 2019 até de 2020 de Junho. Neste cenário, a Microsoft cobra $100,00 para sua licença de software e paga os us $90.00 ao publicador.

|Listas de Microsoft  | US $100,00 por mês  |
|---------|---------|
|Microsoft presta 80% dos gastos de licenciamento <br> \* Microsoft presta 90% do seu custo de licença para quaisquer aplicações de SaaS qualificadas   |   US $80.00 por mês <br> \* US $90.00 por mês    |
|Microsoft fica com 20% dos gastos de licenciamento <br> \* Microsoft mantém a 10% do seu custo de licença para quaisquer aplicações de SaaS qualificados.  |  US $20,00 por mês <br> \* $10.00     |

* **Tarifa de serviço do Marketplace reduzida:** Para determinados produtos de SaaS que publicar no nosso Marketplace comerciais, Microsoft irá reduzir a tarifa de serviço do Marketplace de 20% (conforme descrito no contrato de publicador do Microsoft) para 10%.  Na ordem do seu produto qualificar, pelo menos um dos seus produtos tem de ser designado pela Microsoft como IP de venda conjunta pronto ou priorizado de venda conjunta de IP. Para receber esta tarifa de serviço do Marketplace reduzida para o mês, elegibilidade cumprir, pelo menos, cinco (5) dias úteis antes do final desse mês de calendário. Reduzido o serviço do Marketplace não será aplicada a taxa para VMs, aplicações geridas ou quaisquer outros produtos disponibilizados através do nosso Marketplace comercial.  Esta tarifa de serviço do Marketplace reduzido estarão disponível para as ofertas qualificadas, com custos de licenciamento recolhidos pela Microsoft, entre 1 de Maio de 2019 e 30 de Junho de 2020.  Após esse tempo, a taxa de serviço do Marketplace será retornado para a quantidade de normal.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Faturação, pagamento, faturação e as coleções de clientes

**Faturação e pagamento**

Publicador pode utilizar o cliente preferencial de faturamento do método para entregar subscrição ou taxas de licença de software em PAYGO.

**Contrato Enterprise** 

Se o método de faturação preferencial do cliente é o contrato Enterprise da Microsoft, suas taxas de licença de software são cobradas isso faturação método como um custo itemized, separado dos custos de utilização específicos do Azure.

**Cartões de crédito e a fatura mensal** 

Os clientes também podem pagar com um cartão de crédito e uma fatura mensal. Neste caso, as taxas de licença de software serão cobradas como o cenário do Enterprise Agreement, como um custo itemized, separado dos custos de utilização específicos do Azure.

Por exemplo, se o cliente compra com um cartão de crédito:

|Descrição    |    Date  |
|----------|----------|
|Período de ordem   | 15 de Agosto de 2018-30 de Agosto de 2018 |
|Fim do termo (mês)   | 30 de Agosto de 2018 |
|Data de faturação | 1 de Setembro de 2018 |
|Data de pagamento de cliente | 1 de Setembro de 2018 |
|Caução período (cartões de crédito apenas, 30 dias) | 1 de Setembro de 2018 – 30 de Setembro de 2018 |
|Início do período de coleção | 1 de Setembro de 2018 |
|Fim de período de coleção (no máximo, 30 dias) | 30 de Setembro de 2018 |
|Data de cálculo de dividendos (mensalmente no dia de 15) | 1 de Outubro de 2018 |
|Data de dividendos | 15 de Outubro de 2018 |

Se o cliente compra com um contrato Enterprise:

| Descrição |    Date  |
|----------|----------|
|Período de ordem | 15 de Agosto de 2018-30 de Agosto de 2018 |
|Fim do termo (trimestre) | 30 de Setembro de 2018 |
|Data de faturação | 15 de Outubro de 2018 |
|Caução período (cartões de crédito apenas, 30 dias) | n/d |
|Início do período de coleção | 15 de Outubro de 2018 |
|Fim de período de coleção (no máximo, 90 dias) | 15 de Janeiro de 2019 |
|Data de pagamento de cliente | 30 de Dezembro de 2018 |
|Data de cálculo de dividendos (mensalmente no dia de 15) | 15 de Janeiro de 2019 |
|Data de dividendos | 15 de Fevereiro de 2019 |

**Créditos gratuitos e de alocação monetária** 

Alguns clientes optar por efetue o pré-pagamento do Azure com um compromisso monetário do contrato Enterprise ou foram fornecidos créditos gratuitos para utilização com o Azure. Embora estes créditos podem ser utilizados para pagar a utilização do Azure, não pode ser utilizados para pagar as taxas de licença de software do publicador.

**Faturação e de coleções** 

Faturação de licença de software do publicador é apresentada a usando o método de cliente selecionado da faturação e segue a linha de tempo de faturação. Os clientes sem um contrato Enterprise em vigor faturados mensalmente por licenças de software do marketplace. Os clientes com um contrato Enterprise são faturados mensalmente por meio de uma nota fiscal que é apresentada trimestralmente.

Quando estão selecionados subscrição ou modelos de preços pay as you go, a Microsoft atua como o agente do publicador e é responsável por todos os aspectos de faturação, pagamento e coleção.

### <a name="publisher-payout-and-reporting"></a>Dividendos do publicador e relatórios

* Quaisquer taxas de licenciamento de software recolhidas pela Microsoft como um agente estão sujeitos a uma taxa de transação de 20%, salvo especificação em contrário e são deduzidas no momento da dividendos do publicador.

* Os clientes comprar, normalmente, através do Enterprise Agreement ou um cartão de crédito ativado contrato pay as you go. O tipo de contrato determina a faturação, faturação, recolha e temporização dividendos.

>[!NOTE] 
>Todos os relatórios e informações aprofundados para a opção de publicação de transact estão disponíveis por meio da seção de informações da secção Portal de parceiros da Cloud ou a análise do Centro de parceiros.

#### <a name="billing-questions-and-support"></a>Perguntas sobre faturação e suporte

Para obter mais informações de e legais políticas, consulte a [contrato de publicador](https://cloudpartner.azure.com/Content/Unversioned/PublisherAgreement2.pdf) (disponível no Portal de parceiros de nuvem).

Para obter ajuda para perguntas sobre faturação, contacte [suporte do publicador de marketplace comerciais](https://aka.ms/marketplacepublishersupport).

## <a name="transact-requirements"></a>Transact requisitos

Os requisitos de transact para tipos de oferta diferente são abordados nesta seção.

### <a name="requirements-for-all-offer-types"></a>Requisitos para todos os tipos da oferta

- Uma conta Microsoft e informações financeiras são necessários para o transact opção, independentemente da oferta de publicação do modelo de preços.
- As informações financeiras obrigatórias incluem o perfil de conta e do imposto sobre dividendos.

Para obter mais informações sobre como configurar estas contas, consulte [gerenciar sua conta do Centro de parceiros](https://docs.microsoft.com/azure/marketplace/partner-center-portal/manage-account#financial-details).


### <a name="requirements-for-specific-offer-types"></a>Requisitos para tipos de oferta específico

A opção de publicação do transact só está disponível para utilização com os seguintes tipos de oferta do marketplace: 

**Máquina Virtual** 

Selecione a partir de modelos gratuitos, bring-your-own-license ou pay-as-you-go de preços e apresentar como SKUs definidos ao nível da oferta. Na fatura do cliente do Azure, Microsoft, apresenta o fabricante de taxas de licença de software em separado das tarifas de infraestrutura do Azure subjacente. Taxas de infraestrutura do Azure são condicionadas pela utilização do software do fabricante.

**Aplicações do Azure: Modelo de solução ou a aplicação gerida** 

Terá de aprovisionar uma ou mais máquinas virtuais e os pedidos através da soma dos preços de máquina virtual. Para obter aplicações geridas num único plano, uma assinatura mensal de taxa fixa pode ser selecionada como os preços de modelo em vez disso, os preços das máquinas virtuais. Em alguns casos, as tarifas de utilização da infraestrutura do Azure são transmitidas para o cliente separadamente de taxas de licença de software, mas no extrato de faturação mesmo. No entanto, se configurar uma aplicação gerida pelo oferta para cobranças de infraestrutura de ISV, os recursos do Azure são cobrados ao publicador e o cliente recebe uma taxa fixa, que inclui o custo da infraestrutura, licenças de software e serviços de gestão.

## <a name="next-steps"></a>Passos Seguintes

* Reveja os requisitos de elegibilidade nas opções de publicação pela secção de tipo de oferta para finalizar a seleção e a configuração da sua oferta.
* Consulte os padrões de publicação, a loja para obter exemplos sobre como sua solução é mapeada para um tipo de oferta e a configuração.
