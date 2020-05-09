---
title: Faturação do mercado comercial no Azure Marketplace
description: Conheça as opções de publicação do mercado e os modelos de faturação transacionais para o mercado comercial.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.topic: guide
ms.date: 12/12/2019
ms.openlocfilehash: deb4f3f1bb17bff0b09b2e4f79ceb967d4d7ff59
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744958"
---
# <a name="commercial-marketplace-billing-with-transact-billing-models"></a>Faturação do mercado comercial com modelos de faturação transata

Este artigo aborda temas relacionados com o comércio para o mercado comercial:

- [Opções de publicação do marketplace](#marketplace-publishing-options)
- [Visão geral transata](#transact-general-overview)
- [Modelos de faturação transata](#transact-billing-models)

## <a name="marketplace-publishing-options"></a>Opções de publicação do marketplace

O mercado comercial oferece várias opções de publicação para editores.

### <a name="list-and-trial-publishing-options"></a>Opções de publicação de listas e julgamentos

As editoras podem alavancar a lista, o trial e trazer as suas próprias opções de publicação (BYOL) para fins promocionais e de aquisição de utilizadores. Com estas opções, a Microsoft não participa diretamente nas transações de licenças de software da editora, e não existe nenhuma taxa de transação associada. Os editores são responsáveis por apoiar todos os aspetos da transação de licença de software, incluindo, mas não se limitando a: ordem, realização, medição, faturação, faturação, pagamento e cobrança. Com as opções de publicação da lista e do teste, as editoras mantêm 100% das taxas de licenciamento de software da editora cobradas ao cliente.

### <a name="transact-publishing-option"></a>Opção de publicação Transact

Além das opções de publicação da lista e do teste, a opção de publicação transata está disponível para os editores. Esta opção tira partido das capacidades de comércio globalmente disponíveis da Microsoft e permite que a Microsoft apresente transações de marketplace na nuvem em nome da editora.

## <a name="transact-general-overview"></a>Visão geral transata

Ao utilizar a opção de publicação transata, a Microsoft permite a venda de software de terceiros e a implementação de alguns tipos de oferta para a subscrição do Cliente Azure. A editora deve considerar a faturação das taxas de infraestrutura, e as taxas de licenciamento de software da própria editora, ao selecionar um modelo de faturação e tipo de oferta.

A opção de publicação Transact é atualmente suportada para os seguintes tipos de oferta: máquinas virtuais, aplicações Azure e aplicações SaaS.

![Transact no Azure Marketplace](./media/transact-amp.png)

### <a name="billing-infrastructure-costs"></a>Custos de infraestrutura de faturação

#### <a name="for-virtual-machines-and-azure-applications"></a>Para máquinas virtuais e aplicações Azure

Para máquinas virtuais e aplicações Azure, as taxas de utilização da infraestrutura Azure são faturadas para a subscrição do Azure do cliente. As taxas de utilização da infraestrutura são avaliadas e apresentadas separadamente das taxas de licenciamento do fornecedor de software na fatura do cliente.

#### <a name="for-saas-apps"></a>Para aplicativos SaaS

Para as Aplicações SaaS, a editora deve prestar contas das taxas de utilização da infraestrutura Azure e das taxas de licenciamento de software como um item de custo único. É representado como uma taxa fixa para o cliente. O uso da infraestrutura Azure é gerido e faturado diretamente para o parceiro. As taxas reais de utilização da infraestrutura não são vistas pelo cliente. Normalmente, os editores optam por agregar taxas de utilização da infraestrutura Azure nos seus preços de licença de software. As taxas de licenciamento de software não são medidas ou baseadas no consumo.

## <a name="transact-billing-models"></a>Modelos de faturação transata

Dependendo da opção de transação utilizada, as taxas de licença de software da editora podem ser apresentadas da seguinte forma:

- *Grátis*: Sem custos para licenças de software.
- *Traga a sua própria licença (BYOL)*: Quaisquer encargos aplicáveis para licenças de software são geridos diretamente entre o editor e o cliente. A Microsoft só passa pelas taxas de utilização da infraestrutura Azure. (Apenas máquinas virtuais e aplicações azure.)
- *Pay-as-you-go*: As taxas de licença de software são apresentadas como uma taxa de preços por hora, por núcleo (vCPU) com base na infraestrutura Azure utilizada. Este modelo aplica-se apenas a máquinas virtuais e aplicações Azure.
- *Preços de subscrição*: As taxas de licença de software são apresentadas como uma taxa mensal ou anual, recorrente cobrada como uma taxa fixa ou por assento. Este modelo aplica-se apenas às Aplicações SaaS e aplicações Azure - Aplicações Geridas.
- *Ensaio gratuito*de software : Sem custos para licenças de software durante 30 dias ou 90 dias.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Preços gratuitos e de trazer a sua própria licença (BYOL)

Ao publicar uma oferta de transação gratuita ou de trazer a sua própria licença, a Microsoft não tem um papel a desempenhar na facilitação da transação de vendas para as suas taxas de licença de software. Tal como a lista e as opções de publicação de testes, a editora mantém 100% das taxas de licença de software.

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Preços de pay-as-you-go e subscrição (baseado no site)

Ao publicar uma oferta de transação de pagamento ou subscrição, a Microsoft fornece a tecnologia e serviços para processar compras de licenças de software, devoluções e cobranças de backbacks. Neste cenário, a editora autoriza a Microsoft a agir como um agente para estes fins. A editora permite que a Microsoft facilite a transação de licenciamento de software, mantendo a sua designação como vendedor, fornecedor, distribuidor e licenciante.

A Microsoft permite que os clientes encomendem, licenciem e utilizem o software da editora, submetendo-se aos termos e condições do Marketplace comercial da Microsoft e do contrato de licenciamento de utilizador final da editora. Os editores devem fornecer o seu contrato de licenciamento de utilizador final ou selecionar o [Contrato Padrão](https://docs.microsoft.com/azure/marketplace/standard-contract) ao criar a oferta.

### <a name="free-software-trials"></a>Ensaios de software gratuitos

Para cenários de publicação transata, a editora pode disponibilizar gratuitamente uma licença de software durante 30 dias ou 90 dias. Esta capacidade de desconto não inclui o custo da utilização da infraestrutura Azure que é impulsionada pela utilização da solução parceira.

### <a name="private-offers"></a>Ofertas privadas

Além de utilizar tipos de oferta e modelos de faturação para rentabilizar uma oferta, os editores podem negociar uma oferta privada, completada com preços negociados e específicos do negócio, ou configurações personalizadas. As ofertas privadas são apoiadas por todas as 3 opções de publicação transacionais.

Esta opção permite preços mais altos ou mais baixos do que a oferta publicamente disponível. As ofertas privadas podem ser usadas para descontar ou adicionar um prémio por uma oferta. As ofertas privadas podem ser disponibilizadas a um ou mais clientes através da listagem branca da sua subscrição Azure ao nível da oferta.

### <a name="examples"></a>Exemplos

#### <a name="pay-as-you-go"></a>Pay as you go

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

### <a name="bring-your-own-license-byol"></a>Traga a sua própria licença (BYOL)

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

### <a name="saas-app-subscription"></a>Subscrição da App SaaS

Esta opção deve ser configurada para vender através da Microsoft e pode ser avaliada a uma taxa fixa ou por utilizador numa base mensal ou anual.

• Se ativar a opção Vender através da Microsoft para uma oferta SaaS, terá a seguinte estrutura de custos.

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

**Taxa reduzida de serviço no mercado:** Para certos Produtos SaaS que publica no nosso Mercado Comercial, a Microsoft reduzirá a sua Taxa de Serviço de Marketplace de 20% (conforme descrito no Microsoft Publisher Agreement) para 10%.  Para que o seu Produto se qualifique, pelo menos um dos seus produtos deve ser designado pela Microsoft como co-venda IP pronta ou co-venda IP priorizada. Para receber esta taxa reduzida de serviço ao mercado para o mês, a elegibilidade deve ser satisfeita pelo menos cinco (5) dias úteis antes do final do mês civil anterior. A taxa reduzida do Serviço de Mercado não se aplicará a VMs, Aplicações Geridas ou quaisquer outros produtos disponibilizados através do nosso Mercado Comercial.  Esta Taxa reduzida de serviço ao mercado estará disponível para ofertas qualificadas, com taxas de licença cobradas pela Microsoft entre 1 de maio de 2019 e 30 de junho de 2020.  Após esse período, a Taxa de Serviço do Mercado voltará ao seu valor normal.
