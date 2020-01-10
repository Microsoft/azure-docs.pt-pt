---
title: Cobrança do Marketplace comercial | Azure Marketplace
description: Este artigo aborda os tópicos relacionados ao comércio para o Marketplace comercial.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: guide
ms.date: 12/12/2019
ms.openlocfilehash: 3f610dbc2c5cf052729857c09de1d437e52ac20a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480583"
---
# <a name="commercial-marketplace-billing"></a>Cobrança do Marketplace comercial 

Este artigo aborda os tópicos relacionados ao commerce para o Marketplace comercial:

- [Opções de publicação do Marketplace](#marketplace-publishing-options) 
- [Visão geral do Transact](#transact-general-overview)
- [Modelos de cobrança de transação](#transact-billing-models)

## <a name="marketplace-publishing-options"></a>Opções de publicação do Marketplace 

O Marketplace comercial oferece várias opções de publicação para Publicadores.

### <a name="list-and-trial-publishing-options"></a>Opções de publicação de lista e de avaliação

Os editores podem aproveitar as opções de publicação lista, avaliação e BYOL (traga sua própria licença) para fins de aquisição promocional e de usuário. Com essas opções, a Microsoft não participa diretamente das transações de licença de software do editor e não há nenhuma taxa de transação associada. Os Publicadores são responsáveis por dar suporte a todos os aspectos da transação de licença de software, incluindo, entre outros: ordem, preenchimento, medição, cobrança, faturamento, pagamento e coleção. Com as opções de publicação de lista e de avaliação, os editores mantêm 100% das taxas de licenciamento de software do Publicador coletadas do cliente.

### <a name="transact-publishing-option"></a>Opção de publicação Transact

Além das opções de publicação de lista e de avaliação, a opção Transact Publishing está disponível para editores. Essa opção aproveita os recursos de comércio globalmente disponíveis da Microsoft e permite que a Microsoft hospede transações de Marketplace de nuvem em nome do Publicador.

## <a name="transact-general-overview"></a>Visão geral do Transact

Ao usar a opção Transact Publishing, a Microsoft permite a venda de software de terceiros e a implantação de alguns tipos de oferta na assinatura do Azure do cliente. O Publicador deve considerar a cobrança de taxas de infraestrutura e as próprias tarifas de licenciamento de software do editor, ao selecionar um modelo de cobrança e um tipo de oferta. 

Atualmente, a opção Transact Publishing tem suporte para os seguintes tipos de oferta: máquinas virtuais, aplicativos do Azure e aplicativos SaaS. 

![Transact-lo no Azure Marketplace](./media/transact-amp.png)

### <a name="billing-infrastructure-costs"></a>Custos de infraestrutura de cobrança

#### <a name="for-virtual-machines-and-azure-applications"></a>Para máquinas virtuais e aplicativos do Azure

Para máquinas virtuais e aplicativos do Azure, as tarifas de uso da infraestrutura do Azure são cobradas na assinatura do Azure do cliente. As tarifas de uso de infraestrutura são cobradas e apresentadas separadamente das tarifas de licenciamento do provedor de software na fatura do cliente.

#### <a name="for-saas-apps"></a>Para aplicativos SaaS

Para aplicativos SaaS, o Publicador deve considerar as tarifas de uso da infraestrutura do Azure e as taxas de licenciamento de software como um único item de custo. Ele é representado como uma taxa fixa para o cliente. O uso da infraestrutura do Azure é gerenciado e cobrado diretamente para o parceiro. As tarifas de uso de infraestrutura reais não são vistas pelo cliente. Os editores normalmente optam por agrupar taxas de uso de infraestrutura do Azure em seus preços de licença de software. As taxas de licenciamento de software não são limitadas ou com base no consumo.

## <a name="transact-billing-models"></a>Modelos de cobrança de transação

Dependendo da opção de transação usada, as taxas de licença de software do Publicador podem ser apresentadas da seguinte maneira:

- *Gratuito*: sem custos para licenças de software.
- *BYOL (traga sua própria licença)* : quaisquer encargos aplicáveis para licenças de software são gerenciados diretamente entre o Publicador e o cliente. A Microsoft passa apenas pelas tarifas de uso de infraestrutura do Azure. (Somente máquinas virtuais e aplicativos do Azure.)
- Pré- *pago*: as taxas de licença de software são apresentadas como uma taxa de preço por hora, por núcleo (vCPU) com base na infraestrutura do Azure usada. Esse modelo se aplica somente a máquinas virtuais e aplicativos do Azure.
- *Preços de assinatura*: as taxas de licença de software são apresentadas como uma taxa de recorrência mensal ou anual, cobrada como uma taxa fixa ou por estação. Esse modelo se aplica somente a aplicativos SaaS e aplicativos gerenciados pelo aplicativo do Azure.
- *Avaliação de software gratuita*: sem custos para licenças de software por 30 dias ou 90 dias.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Preços gratuitos e BYOL (traga sua própria licença)

Ao publicar uma oferta de transação gratuita ou traga sua própria licença, a Microsoft não desempenha uma função na facilitação da transação de vendas para suas taxas de licença de software. Assim como as opções de publicação de lista e de avaliação, o Publicador mantém de 100% das taxas de licença de software.

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Preço pago conforme o uso e assinatura (baseado em site)

Ao publicar uma oferta de transação pré-paga ou de assinatura, a Microsoft fornece a tecnologia e os serviços para processar compras de licenças de software, Devoluções e cobranças. Nesse cenário, o Publicador autoriza a Microsoft a atuar como um agente para essas finalidades. O Publicador permite que a Microsoft facilite a transação de licenciamento de software, preservando sua designação como vendedor, provedor, distribuidor e licenciante.

A Microsoft permite aos clientes solicitar, licenciar e usar o software do Publicador, sujeito aos termos e condições do mercado comercial da Microsoft e do contrato de licenciamento do usuário final do editor. Os editores devem fornecer seu contrato de licenciamento de usuário final ou selecionar o [contrato padrão](https://docs.microsoft.com/azure/marketplace/standard-contract) ao criar a oferta.

### <a name="free-software-trials"></a>Avaliações de software gratuitas

Para cenários de publicação de transações, o Publicador pode disponibilizar gratuitamente uma licença de software por 30 dias ou 90 dias. Esse recurso de descontação não inclui o custo do uso de infraestrutura do Azure que é controlado pelo uso da solução de parceiro.

### <a name="private-offers"></a>Ofertas privadas

Além de usar tipos de oferta e modelos de cobrança para monetizar uma oferta, os editores podem realizar transações de uma oferta privada, completa com preços negociados e específicos do negócio ou configurações personalizadas. As ofertas privadas têm suporte de todas as três opções de publicação Transact.

Essa opção permite um preço mais alto ou mais baixo do que a oferta publicamente disponível. As ofertas privadas podem ser usadas para descontos ou adicionar um Premium para uma oferta. As ofertas privadas podem ser disponibilizadas para um ou mais clientes, listando suas assinaturas do Azure no nível da oferta.

### <a name="examples"></a>Exemplos

#### <a name="pay-as-you-go"></a>"Pay-As-You-Go"

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

### <a name="bring-your-own-license-byol"></a>BYOL (traga sua própria licença)

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

### <a name="saas-app-subscription"></a>Assinatura de aplicativo SaaS

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
|A Microsoft paga a você 80% do seu custo de licença <br> \* a Microsoft paga 90% do seu custo de licença para qualquer aplicativo SaaS qualificado   |   $80 por mês <br> \* $90 por mês    |
|A Microsoft mantém 20% do seu custo de licença <br> \* a Microsoft mantém 10% do seu custo de licença para qualquer aplicativo SaaS qualificado.  |  $20 por mês <br> \* $10     |

**Taxa reduzida de serviço do Marketplace:** Para determinados produtos SaaS que você publica em nosso mercado comercial, a Microsoft reduzirá sua taxa de serviço do Marketplace de 20% (conforme descrito no contrato do Microsoft Publisher) para 10%.  Para que seu produto seja qualificado, pelo menos um de seus produtos deve ser designado pela Microsoft como uma das vendas de covenda de IP pronta ou de IP. Para receber essa taxa de serviço do Marketplace reduzida para o mês, a qualificação deve ser atendida pelo menos cinco (5) dias úteis antes do final do mês do calendário anterior. A taxa reduzida de serviço do Marketplace não se aplicará a VMs, aplicativos gerenciados ou quaisquer outros produtos disponibilizados por meio de nosso mercado comercial.  Essa taxa de serviço do Marketplace reduzida estará disponível para ofertas qualificadas, com encargos de licença coletados pela Microsoft entre 1º de maio de 2019 e 30 de junho de 2020.  Após esse período, a taxa de serviço do Marketplace voltará ao seu valor normal.
