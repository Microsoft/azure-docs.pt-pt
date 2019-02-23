---
title: Capacidades de transação comercial do Marketplace e considerações | Azure
description: Este artigo descreve as considerações de preços, faturação, faturação e dividendos Transact para um tipo de oferta.
services: Azure, Marketplace, Compute, Storage, Networking, Transact Offer Type
documentationcenter: ''
author: yijenj
manager: nuno costa
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/29/2018
ms.author: yijenj
ms.openlocfilehash: dba2582b29d1fc81a39387f5297852245233b708
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56731094"
---
# <a name="azure-marketplace-commercial-transaction-capabilities-and-considerations"></a>Capacidades de transações comerciais do Azure Marketplace e considerações

Opções de publicação do Azure Marketplace oferecem formas exclusivas para ligar os fornecedores de software e serviços em nuvem com os clientes. Este artigo abrange os seguintes tópicos relacionados com o comércio no Azure Marketplace:

* Opções de publicação do Marketplace
* Visão geral de Transact
* Transact modelos de faturação
* Transact requisitos

## <a name="marketplace-publishing-options"></a>Opções de publicação do Marketplace

As seguintes opções de publicação estão disponíveis aos editores do Azure Marketplace.

### <a name="list--trial-publishing-options"></a>Lista & de opções de publicação de avaliação

No Azure Marketplace, os publicadores podem tirar partido da lista e opções de publicação de avaliação para promocionais e fins de aquisição de utilizador. Com as opções de publicação de avaliação ou lista, Microsoft não participa diretamente em transações de licença de software do publicador e existe uma taxa de transação associada. Os publicadores são responsáveis por suporte de todos os aspetos da transação de licença de software, incluindo mas não limitado a: ordem, preenchimento, medição, faturação, faturação, pagamento e coleção. Com a lista e opções de publicação de avaliação, os publicadores fique com 100% das taxas de licenciamento de software de publicador recolhidas a partir do cliente. 

### <a name="transact-publishing-option"></a>Transact a opção de publicação

Além da lista e opções de publicação de avaliação, a opção de publicação do transact está disponível para os editores do Azure Marketplace.   Tira partido das capacidades de comércio disponível globalmente da Microsoft. Esta opção permite que a Microsoft para transações de marketplace do anfitrião na cloud em nome do publicador.

## <a name="transact-general-overview"></a>Visão geral de Transact

Quando utilizar a opção de publicação do transact, a Microsoft permite que a venda e a implementação de software de terceiros para a subscrição do cliente do Azure. O publicador tem de considerar a faturação das taxas de infraestrutura do Azure e do publicador licenciamento taxas de software, ao selecionar uma faturação de modelo e tipo no Azure Marketplace de oferta. 

A opção de publicação do Transact no Azure Marketplace atualmente é suportada para os seguintes tipos de oferta: Máquinas virtuais, aplicações do Azure ou aplicações de SaaS.

![[Transacionais Enterprise lida no Azure Marketplace]](./media/marketplace-publishers-guide/Transact-enterprise-deals.png)

### <a name="billing-infrastructure-costs"></a>Custos de infraestrutura de faturação

**Para máquinas virtuais e aplicações do Azure**

Para máquinas virtuais e aplicações do Azure, as tarifas de utilização da infraestrutura do Azure são faturadas para a subscrição do cliente do Azure.  As tarifas de utilização de infraestrutura são um preço e apresentadas em separado de taxas de licenciamento do fornecedor de software na fatura do cliente.

**Para aplicações SaaS**

Para aplicações SaaS, o publicador tem em conta as tarifas de utilização da infraestrutura do Azure e taxas de licenciamento de software como um item único custo.  Este é representado como uma taxa mensal fixa para o cliente. A utilização de infraestrutura do Azure é gerenciada e faturada diretamente para o parceiro.  As tarifas de utilização da infraestrutura real não são vistas pelo cliente.  Os publicadores, normalmente, optar por agrupar as tarifas de utilização da infraestrutura do Azure nos respetivos preços de licença de software.  Não são limitadas de taxas de licenciamento de software ou baseado no consumo.

## <a name="transact-billing-models"></a>Transact modelos de faturação

Consoante a opção de transação utilizada, as taxas de licença de software do publicador podem ser apresentadas da seguinte forma:  

* Gratuito: Sem custos para licenças de software. 

* Traga a sua própria licença (BYOL): Quaisquer custos aplicáveis de licenças de software são geridos diretamente entre o publicador e o cliente. Microsoft apenas passa pelas tarifas de utilização da infraestrutura do Azure. (As máquinas virtuais e apenas as aplicações do Azure).

* Pay as you go: As taxas de licença de software são apresentadas como uma por hora, por núcleo (Vcpus) preços de taxa com base na infraestrutura do Azure utilizada. Isto aplica-se apenas a máquinas virtuais e aplicações do Azure.

* Subscrição de preços (baseado no site): As taxas de licença de software são apresentadas como uma taxa mensal, recorrente.  Isto aplica-se apenas para aplicações SaaS e aplicações do Azure – aplicações geridas.

* Versão de avaliação gratuito software: Sem custos para licenças de software para 30 dias ou 90 dias.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Gratuito e bring-your-own-license (BYOL) de preços

Ao publicar uma oferta de transações gratuitas ou bring-your-own-license, Microsoft não têm uma função em facilitar a transação de vendas de suas taxas de licença de software. Tal como a lista e opções de publicação de avaliação, o publicador mantém 100% das taxas de licença de software. 

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Pay as you go e o preço da subscrição (baseado no site)

Ao publicar uma oferta de transação pay as you go ou subscrição, a Microsoft fornece a tecnologia e os serviços, de aquisições de licenças de software do processo, devolve e estornos. Neste cenário, o publicador autoriza a Microsoft para agir como um agente para essas finalidades. O publicador permite à Microsoft facilitar o transação, retendo suas designações como o vendedor, o fornecedor, o distribuidor e o licenciador de licenciamento de software.

Microsoft permite que os clientes de ordem, licenciar e usar o software do publicador, submeter o departamento para os termos e condições do Azure Marketplace e contrato de licença de utilizador final do Editor (consulte o Portal de parceiros da Cloud). Os publicadores devem fornecer o respetivo contrato de licenciamento do utilizador final na oferta do marketplace.

Pedidos processados através do marketplace são faturados para a subscrição do cliente do Azure numa única fatura, o mesmo método de faturação que os custos de infraestrutura do Azure do cliente. Os clientes podem utilizar o preferencial faturamento método e pagamento instrumento utilizado para a sua faturação de subscrição do Azure.

### <a name="free-software-trials"></a>Versões de avaliação do software gratuito

Para transact cenários de publicação, o publicador pode fazer um software de licença disponíveis gratuitamente durante 30 dias ou 90 dias. Esta capacidade discounting não inclui o custo de utilização de infraestrutura do Azure que é orientada pela utilização da solução de parceiro.

### <a name="private-offers"></a>Ofertas privadas

Além de usar oferecem tipos e modelos para de faturação lucrar com uma oferta, os publicadores podem transacionar uma versão particular de oferta de solução, completa com preços negociado, específicos do negócio e configurações personalizadas usando uma imagem personalizada. Ofertas privadas são suportadas por todos os 3 transact as opções de publicação.

Esta opção de preço pode ser maior ou menor do que os preços apresentados publicamente.  Ofertas privadas podem ser utilizadas para o desconto ou adicionar uma premium para uma oferta. Ofertas privadas podem ser disponibilizadas para os clientes de um ou mais pelo white listagem a sua subscrição do Azure ao nível da oferta.

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

**Subscrição de aplicação SaaS (venda através do Azure)**

Esta opção deve ser configurada para venda através de Microsoft e pode ter preços através de um ou mais planos de taxa fixa mensais definido ao nível da oferta.

* Se ativar o destino de venda por meio da opção do Azure, terá a seguinte estrutura de custos.

|A custo da licença       | US $100,00 por mês  |
|--------------|---------|
|Custo de utilização do Azure (D1/1 núcleo)    | Cobrado diretamente para o publicador, não é o cliente |
|*Ser-lhe cobrado pela Microsoft*    |  *US $100,00 por mês (Nota: publicador deve levar em conta quaisquer custos de infraestrutura incorridos ou pass-through o valor da licença)*  |

* Neste cenário, a Microsoft cobra $100,00 para sua licença de software e paga os us $80.00 ao publicador.

|Listas de Microsoft  | US $100,00 por mês  |
|---------|---------|
|Microsoft presta 80% dos gastos de licenciamento    |   US $80.00 por mês     |
|Microsoft fica com 20% dos gastos de licenciamento   |  US $20,00 por mês       |

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
>Todos os relatórios e informações aprofundados para a opção de publicação de transact estão disponíveis por meio da seção de informações do Portal de parceiro de Cloud.

#### <a name="billing-questions-and-support"></a>Perguntas sobre faturação e suporte

Para obter mais informações de e legais políticas, consulte a [contrato de publicador](https://cloudpartner.azure.com/Content/Unversioned/PublisherAgreement2.pdf) (disponível no Portal de parceiros de nuvem).

Para obter ajuda para perguntas sobre faturação, [criar um incidente de suporte](https://support.microsoft.com/getsupport?wf=0&tenant=classiccommercial&oaspworkflow=start_1.0.0.0&pesid=16230&forceorigin=esmc&ccsid=636764613233453423) e escolha as máquinas virtuais ou aplicações Web (também conhecido como aplicações de SaaS), dependendo do tipo de oferta utilizado.

## <a name="transact-requirements"></a>Transact requisitos

Os requisitos de transact para tipos de oferta diferente são abordados nesta seção.

### <a name="requirements-for-all-offer-types"></a>Requisitos para todos os tipos da oferta

**Conta do Dev Center e Microsoft** 

* Centro de desenvolvimento e uma conta Microsoft são necessários para o transact opção, independentemente da oferta de publicação do modelo de preços.
* A conta do Dev Center possui todos os detalhes financeiros relevantes necessários para a Microsoft a cobrar as tarifas do cliente em nome do publicador e a pagar o publicador.
* Embora possa utilizar a mesma organizacionais ou início de sessão os detalhes da Microsoft em ambas as contas, o Centro de desenvolvimento é uma conta separada da conta do Portal de parceiros da Cloud. Para utilizar a opção de publicação do transact, o publicador tem de concluir o Centro de desenvolvimento conta processo de inscrição, além de inscrever-se no acesso ao Portal de parceiros da Cloud.

*Para obter mais informações sobre como configurar estas contas, consulte [se tornar um publicador de Marketplace da Cloud](https://docs.microsoft.com/azure/marketplace/become-publisher).*

### <a name="requirements-for-specific-offer-types"></a>Requisitos para tipos de oferta específico

A opção de publicação do transact só está disponível para utilização com os seguintes tipos de oferta do marketplace: 

**Máquina Virtual** 

Selecione a partir de modelos gratuitos, bring-your-own-license ou pay-as-you-go de preços e apresentar como SKUs definidos ao nível da oferta. Na fatura do cliente do Azure, Microsoft, apresenta o fabricante de taxas de licença de software em separado das tarifas de infraestrutura do Azure subjacente. Taxas de infraestrutura do Azure são condicionadas pela utilização do software do fabricante.

**Aplicações do Azure: Modelo de solução ou a aplicação gerida** 

Terá de aprovisionar uma ou mais máquinas virtuais e os pedidos através da soma dos preços de máquina virtual. Para obter aplicações geridas num único plano, uma assinatura mensal de taxa fixa pode ser selecionada como os preços de modelo em vez disso, os preços das máquinas virtuais. Em ambos os casos, as tarifas de utilização da infraestrutura do Azure são transmitidas para o cliente separadamente de taxas de licença de software, mas no extrato de faturação mesmo.

## <a name="next-steps"></a>Passos Seguintes

* Reveja os requisitos de elegibilidade nas opções de publicação pela secção de tipo de oferta para finalizar a seleção e a configuração da sua oferta.
* Consulte os padrões de publicação, a loja para obter exemplos sobre como sua solução é mapeada para um tipo de oferta e a configuração.
* Se tornar um publicador de Marketplace e inicie sessão para o [Cloud Partner Portal](https://cloudpartner.azure.com) para criar e configurar a sua oferta.
