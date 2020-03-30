---
title: Capacidades e considerações de transações comerciais do Mercado / Azure
description: Este artigo descreve os preços transact, faturação, faturação e considerações de pagamento para um tipo de oferta.
services: Azure, Marketplace, Compute, Storage, Networking, Transact Offer Type
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: dsindona
ms.openlocfilehash: de93a3c48e0b0aceb447e54e11190f487d1aa2d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279832"
---
# <a name="commercial-marketplace-transaction-capabilities-and-considerations"></a>Funcionalidades e considerações de transação do marketplace comercial

Este artigo aborda os seguintes tópicos relacionados com o comércio para o mercado comercial

* Opções de publicação do marketplace
* Visão geral transata
* Modelos de faturação transata
* Requisitos transatas

## <a name="marketplace-publishing-options"></a>Opções de publicação do marketplace

As seguintes opções de publicação estão disponíveis para editores de marketplace comercial.

### <a name="list--trial-publishing-options"></a>Lista & opções de publicação de julgamento

As editoras podem alavancar as opções de publicação da lista, do trial e da BYOL para fins promocionais e de aquisição de utilizadores. Com estas opções, a Microsoft não participa diretamente nas transações de licenças de software da editora, e não existe nenhuma taxa de transação associada. Os editores são responsáveis por apoiar todos os aspetos da transação de licença de software, incluindo, mas não se limitando a: ordem, realização, medição, faturação, faturação, pagamento e cobrança. Com as opções de publicação da lista e do teste, as editoras mantêm 100% das taxas de licenciamento de software da editora cobradas ao cliente. 

### <a name="transact-publishing-option"></a>Opção de publicação Transact

Além das opções de publicação da lista e do teste, a opção de publicação transata está disponível para os editores. Isto tira partido das capacidades de comércio globalmente disponíveis da Microsoft e permite que a Microsoft apresente transações de marketplace na nuvem em nome da editora.

## <a name="transact-general-overview"></a>Visão geral transata

Ao utilizar a opção de publicação transata, a Microsoft permite a venda de software de terceiros e a implementação de alguns tipos de oferta para a subscrição do Cliente Azure. A editora deve considerar a faturação das taxas de infraestrutura, e as taxas de licenciamento de software da própria editora, ao selecionar um modelo de faturação e tipo de oferta.

A opção de publicação Transact é atualmente suportada para os seguintes tipos de oferta: Máquinas Virtuais, Aplicações Azure e Apps SaaS.


![[Transacting Enterprise Deals in Azure Marketplace]](./media/marketplace-publishers-guide/Transact-enterprise-deals.png)

### <a name="billing-infrastructure-costs"></a>Custos de infraestrutura de faturação

**Para máquinas virtuais e aplicações Azure**

Para Máquinas Virtuais e Aplicações Azure, as taxas de utilização da infraestrutura Azure são faturadas para a subscrição azure do cliente.  As taxas de utilização da infraestrutura são avaliadas e apresentadas separadamente das taxas de licenciamento do fornecedor de software na fatura do cliente.

**Para aplicativos SaaS**

Para as Aplicações SaaS, a editora deve prestar contas das taxas de utilização da infraestrutura Azure e das taxas de licenciamento de software como um item de custo único.  É representado como uma taxa fixa para o cliente. O uso da infraestrutura Azure é gerido e faturado diretamente para o parceiro.  As taxas reais de utilização da infraestrutura não são vistas pelo cliente.  Normalmente, os editores optam por agregar taxas de utilização da infraestrutura Azure nos seus preços de licença de software.  As taxas de licenciamento de software não são medidas ou baseadas no consumo.

## <a name="transact-billing-models"></a>Modelos de faturação transata

Dependendo da opção de transação utilizada, as taxas de licença de software da editora podem ser apresentadas da seguinte forma:  

* Grátis: Sem custo sintetísma para licenças de software. 

* Traga a sua própria licença (BYOL): Quaisquer encargos aplicáveis para licenças de software são geridos diretamente entre o editor e o cliente. A Microsoft só passa pelas taxas de utilização da infraestrutura Azure. (Apenas máquinas virtuais e aplicações azure.)

* Pay-as-you-go: As taxas de licença de software são apresentadas como uma taxa de preços por hora, por núcleo (vCPU) com base na infraestrutura Azure utilizada. Isto aplica-se apenas a Máquinas Virtuais e Aplicações Azure.

* Preços de subscrição: As taxas de licença de software são apresentadas como uma taxa mensal ou anual, recorrente, faturada como uma taxa fixa ou por assento. Isto aplica-se apenas às Aplicações SaaS e aplicações Azure - Aplicações Geridas.

* Ensaio gratuito de software: Sem custos para licenças de software durante 30 dias ou 90 dias.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Preços gratuitos e de trazer a sua própria licença (BYOL)

Ao publicar uma oferta de transação gratuita ou de trazer a sua própria licença, a Microsoft não tem um papel a desempenhar na facilitação da transação de vendas para as suas taxas de licença de software. Tal como a lista e as opções de publicação de testes, a editora mantém 100% das taxas de licença de software. 

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Preços de pay-as-you-go e subscrição (baseado no site)

Ao publicar uma oferta de transação de pagamento ou subscrição, a Microsoft fornece a tecnologia e serviços para processar compras de licenças de software, devoluções e cobranças. Neste cenário, a editora autoriza a Microsoft a agir como um agente para estes fins. A editora permite que a Microsoft facilite a transação de licenciamento de software, mantendo a sua designação como vendedor, fornecedor, distribuidor e licenciante.

A Microsoft permite que os clientes encomendem, licenciem e utilizem o software da editora, submetendo-se aos termos e condições do Marketplace comercial da Microsoft e do contrato de licenciamento de utilizador final da editora. Os editores devem fornecer o seu contrato de licenciamento de utilizador final ou selecionar o [Contrato Padrão](https://docs.microsoft.com/azure/marketplace/standard-contract) ao criar a oferta.


### <a name="free-software-trials"></a>Ensaios de software gratuitos

Para cenários de publicação transata, a editora pode disponibilizar gratuitamente uma licença de software durante 30 dias ou 90 dias. Esta capacidade de desconto não inclui o custo da utilização da infraestrutura Azure que é impulsionada pela utilização da solução parceira.

### <a name="private-offers"></a>Ofertas privadas

Além de utilizar tipos de oferta e modelos de faturação para rentabilizar uma oferta, os editores podem negociar uma oferta privada, completada com preços negociados, específicos do negócio ou configurações personalizadas. As ofertas privadas são apoiadas por todas as 3 opções de publicação transacionais.

Esta opção permite preços mais altos ou mais baixos do que a oferta publicamente disponível. As ofertas privadas podem ser usadas para descontar, ou adicionar um prémio por uma oferta. As ofertas privadas podem ser disponibilizadas a um ou mais clientes através da listagem branca da sua subscrição Azure ao nível da oferta.


### <a name="examples"></a>Exemplos

**"Pay As You Go"** 

* Se ativar a opção Pay-As-You-Go, terá a seguinte estrutura de custos.

|O custo da sua licença  | $1,00 por hora  |
|---------|---------|
|Custo de utilização do Azure (D1/1-Core)    |   $0,14 por hora     |
|*Cliente é cobrado pela Microsoft*    |  *$1,14 por hora*       |

* Neste cenário, a Microsoft cobra 1,14 dólares por hora para uso da sua imagem VM publicada.

|Contas da Microsoft  | $1,14 por hora  |
|---------|---------|
|Microsoft paga-lhe 80% do custo da sua licença|   $0,80 por hora     |
|Microsoft mantém 20% do custo da sua licença  |  $0,20 por hora       |
|Microsoft mantém 100% do custo de utilização do Azure | $0,14 por hora |

**Traga a sua própria licença (BYOL)**

* Se ativar a opção BYOL, terá a seguinte estrutura de custos.

|O custo da sua licença  | Taxa de licença negociada e cobrada por si  |
|---------|---------|
|Custo de utilização do Azure (D1/1-Core)    |   $0,14 por hora     |
|*Cliente é cobrado pela Microsoft*    |  *$0,14 por hora*       |

* Neste cenário, a Microsoft cobra $0,14 por hora para uso da sua imagem VM publicada.

|Contas da Microsoft  | $0,14 por hora  |
|---------|---------|
|Microsoft mantém o custo de utilização do Azure    |   $0,14 por hora     |
|Microsoft mantém 0% do custo da sua licença   |  $0,00 por hora       |

**Subscrição da App SaaS**

Esta opção deve ser configurada para vender através da Microsoft e pode ser avaliada a uma taxa fixa ou por utilizador numa base mensal ou anual.
*    Se ativar a opção Sell através da Microsoft para uma oferta SaaS, então terá a seguinte estrutura de custos.

|O custo da sua licença       | $100,00 por mês  |
|--------------|---------|
|Custo de utilização do Azure (D1/1-Core)    | Faturado diretamente para o editor, não para o cliente |
|*Cliente é cobrado pela Microsoft*    |  *$100,00 por mês (nota: o editor deve prestar contas de quaisquer custos de infraestrutura incorridos ou pass-through na taxa de licença)*  |

* Neste cenário, a Microsoft cobra $100,00 pela sua licença de software e paga $80,00 à editora.
* Os parceiros que se qualificaram para a Taxa de Serviço de Mercado Reduzido verão uma taxa de transação reduzida nas ofertas saaS de maio de 2019 a junho de 2020. Neste cenário, a Microsoft cobra $100,00 pela sua licença de software e paga $90,00 à editora.

|Contas da Microsoft  | $100,00 por mês  |
|---------|---------|
|Microsoft paga-lhe 80% do custo da sua licença <br> \*Microsoft paga-lhe 90% do custo da sua licença por quaisquer aplicações SaaS qualificadas   |   $80,00 por mês <br> \*$90,00 por mês    |
|Microsoft mantém 20% do custo da sua licença <br> \*A Microsoft mantém 10% do custo da sua licença para quaisquer aplicações SaaS qualificadas.  |  $20,00 por mês <br> \*$10,00     |

* **Taxa reduzida de serviço no mercado:** Para certos Produtos SaaS que publica no nosso Mercado Comercial, a Microsoft reduzirá a sua Taxa de Serviço de Marketplace de 20% (conforme descrito no Microsoft Publisher Agreement) para 10%.  Para que o seu Produto se qualifique, pelo menos um dos seus produtos deve ser designado pela Microsoft como co-venda IP pronta ou co-venda IP priorizada. Para receber esta taxa reduzida de serviço ao mercado para o mês, a elegibilidade deve ser satisfeita pelo menos cinco (5) dias úteis antes do final do mês civil anterior. A taxa reduzida do Serviço de Mercado não se aplicará a VMs, Aplicações Geridas ou quaisquer outros produtos disponibilizados através do nosso Mercado Comercial.  Esta Taxa reduzida de serviço ao mercado estará disponível para ofertas qualificadas, com taxas de licença cobradas pela Microsoft entre 1 de maio de 2019 e 30 de junho de 2020.  Após esse período, a Taxa de Serviço do Mercado voltará ao seu valor normal.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Faturação do cliente, pagamento, faturação e cobranças

**Faturação e pagamento**

O Editor pode utilizar o método de faturação preferido do cliente para entregar taxas de licença de subscrição ou de software PAYGO.

**Acordo de empresa** 

Se o método de faturação preferido do cliente for o Microsoft Enterprise Agreement, as suas taxas de licença de software serão faturadas utilizando este método de faturação como um custo item, separado de quaisquer custos de utilização específicos do Azure.

**Cartões de crédito e fatura mensal** 

Os clientes também podem pagar usando um cartão de crédito e uma fatura mensal. Neste caso, as suas taxas de licença de software serão cobradas tal como o cenário do Acordo de Empresa, como um custo item, separado de quaisquer custos de utilização específicos do Azure.

Por exemplo, se o cliente comprar usando um cartão de crédito:

|Descrição    |    Date  |
|----------|----------|
|Período de encomenda   | 15 de agosto de 2018 - 30 ago, 2018 |
|Fim do Prazo (mês)   | 30 de agosto de 2018 |
|Data de faturação | 1 de setembro de 2018 |
|Data de Pagamento do Cliente | 1 de setembro de 2018 |
|Período de caução (apenas cartões de crédito, 30 dias) | 1 de setembro de 2018 - 30 set, 2018 |
|Início do Período de Recolha | 1 de setembro de 2018 |
|Fim do Período de Recolha (máximo, 30 dias) | 30 de setembro de 2018 |
|Data de Cálculo do Pagamento (mensalmente no dia 15) | 1 de outubro de 2018 |
|Data de Pagamento | 15 out, 2018 |

Se o cliente comprar usando um Acordo de Empresa:

| Descrição |    Date  |
|----------|----------|
|Período de encomenda | 15 de agosto de 2018 - 30 ago, 2018 |
|Fim do Prazo (trimestre) | 30 de setembro de 2018 |
|Data de faturação | 15 out, 2018 |
|Período de caução (apenas cartões de crédito, 30 dias) | n/d |
|Início do Período de Recolha | 15 out, 2018 |
|Fim do Período de Recolha (máximo, 90 dias) | 15 de janeiro de 2019 |
|Data de Pagamento do Cliente | 30 de dezembro de 2018 |
|Data de Cálculo do Pagamento (mensalmente no dia 15) | 15 de janeiro de 2019 |
|Data de Pagamento | Fev 15, 2019 |

**Créditos gratuitos e compromisso monetário** 

Alguns clientes optam por pré-pagar o Azure com um compromisso monetário no Acordo empresarial ou receberam créditos gratuitos para uso com o Azure. Embora estes créditos possam ser usados para pagar o uso do Azure, não podem ser usados para pagar as taxas de licença de software da editora.

**Faturação e coleções** 

A faturação da licença de software da editora é apresentada utilizando o método de faturação selecionado pelo cliente e segue a linha temporal de faturação. Os clientes sem um Acordo de Empresa em vigor são cobrados mensalmente para licenças de software de marketplace. Os clientes com Um Acordo Empresarial são cobrados mensalmente através de uma fatura que é apresentada trimestralmente.

Quando são selecionados os modelos de preços pay-as-You-Go, a Microsoft atua como agente da editora e é responsável por todos os aspetos da faturação, pagamento e cobrança.

### <a name="publisher-payout-and-reporting"></a>Pagamento e reportagem da editora

* Quaisquer taxas de licenciamento de software cobradas pela Microsoft como agente estão sujeitas a uma taxa de transação de 20%, salvo especificação em contrário e deduzidas no momento do pagamento do editor.

* Os clientes normalmente compram usando o Acordo de Empresa ou um cartão de crédito habilitado a pagar como você-go acordo. O tipo de acordo determina a faturação, faturação, cobrança e pagamento.

>[!NOTE] 
>Todos os relatórios e insights para a opção de publicação transacte estão disponíveis através da secção Insights do Portal do Parceiro cloud ou da secção Analytics do Partner Center.

#### <a name="billing-questions-and-support"></a>Perguntas de faturação e apoio

Para obter mais informações e políticas legais, consulte o [Acordo de Editor](https://cloudpartner.azure.com/Content/Unversioned/PublisherAgreement2.pdf) (disponível no Portal do Parceiro cloud).

Para obter ajuda em questões de faturação, contacte o suporte da [editora do mercado comercial.](https://aka.ms/marketplacepublishersupport)

## <a name="transact-requirements"></a>Requisitos transatas

Os requisitos de transação para diferentes tipos de oferta estão cobertos nesta secção.

### <a name="requirements-for-all-offer-types"></a>Requisitos para todos os tipos de oferta

- Uma conta Microsoft e informações financeiras são necessárias para a opção de publicação transata, independentemente do modelo de preços da oferta.
- A informação financeira obrigatória inclui conta de pagamento e perfil fiscal.

Para obter mais informações sobre a configuração destas contas, consulte Gerir a [conta do Centro de](https://docs.microsoft.com/azure/marketplace/partner-center-portal/manage-account)Sócios).


### <a name="requirements-for-specific-offer-types"></a>Requisitos para tipos de oferta específicos

A opção de publicação transata só está disponível para utilização com os seguintes tipos de oferta de mercado: 

**Máquina Virtual** 

Selecione entre modelos gratuitos, de trazer a sua própria licença ou de preços pay-as-you-go e apresentar-se como as SKUs definidas ao nível da oferta. Na conta do cliente Azure, a Microsoft apresenta as taxas de licença de software da editora separadamente das taxas de infraestrutura azure subjacentes. As taxas de infraestrutura azure são impulsionadas pelo uso do software da editora.

**Aplicações Azure: Modelo de solução ou app gerida** 

Deve fornecer uma ou mais máquinas virtuais e puxar através da soma dos preços da máquina virtual. Para aplicações geridas num único plano, uma subscrição mensal forfável pode ser selecionada como o modelo de preços, em vez do preço da máquina virtual. Em alguns casos, as taxas de utilização da infraestrutura Azure são passadas ao cliente separadamente das taxas de licença de software, mas na mesma declaração de faturação. No entanto, se configurar uma oferta de App Gerida para taxas de infraestrutura ISV, os recursos Do Azure são faturados para a editora, e o cliente recebe uma taxa fixa que inclui o custo de infraestrutura, licenças de software e serviços de gestão.

## <a name="next-steps"></a>Passos seguintes

* Reveja os requisitos de elegibilidade nas opções de publicação por secção de tipo de oferta para finalizar a seleção e configuração da sua oferta.
* Reveja os padrões de publicação por montra, por exemplo, sobre como a sua solução mapeia um tipo de oferta e configuração.
