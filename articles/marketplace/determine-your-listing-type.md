---
title: Introdução às opções de listagem - Mercado comercial da Microsoft
description: Este artigo descreve opções de listagem para ofertas publicadas no Microsoft AppSource e no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 01/14/2021
ms.openlocfilehash: 467cc4a62cd044cb4ad264ec8643728e6db0b10c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479819"
---
# <a name="introduction-to-listing-options"></a>Introdução às opções de listagem

Quando cria um tipo de oferta, escolhe uma ou mais opções de listagem. Estas opções determinam os botões que os clientes vêem na página de listagem de ofertas nas lojas online. As opções de listagem incluem **Teste Gratuito,** **Test Drive,** **Contato comigo** e **Get It Now**.

Esta tabela mostra quais as opções de listagem disponíveis para cada tipo de oferta:

| Tipo de oferta | Avaliação Gratuita | Versão de Teste | Contacte-me | Get It Now `*` |
| ------------ | ------------- | ------------- | ------------- | ------------- |
| Aplicação Azure (Aplicação gerida) |   | &#10004; |   | &#10004; |
| Aplicação Azure (modelo de solução) |  |  |  | &#10004; |
| Serviço de consultoria |  |  | &#10004; |  |
| Recipiente Azure |  |  |  | &#10004; |
| Dinâmica 365 centro de negócios | &#10004; | &#10004; | &#10004; | &#10004; |
| Dinâmica 365 Envolvimento com clientes & PowerApps | &#10004; | &#10004; | &#10004; | &#10004; |
| Dinâmica 365 para operações | &#10004; | &#10004; | &#10004; | &#10004; |
| Módulo IoT Edge |  |  |  | &#10004; |
| Serviço Gerido |  |  |  | &#10004; |
| Aplicação Power BI |  |  |  | &#10004; |
| Máquina Virtual do Azure | &#10004; | &#10004; |  | &#10004; |
| Software como um serviço | &#10004; | &#10004; | &#10004; | &#10004; |
||||||

&#42; a opção de listagem **Get It Now** inclui Get It Now (Grátis), traga a sua própria licença (BYOL), Subscrição e preços baseados em Uso. Para mais informações, consulte [Get It Now](#get-it-now).

## <a name="change-the-offer-type"></a>Alterar o tipo de oferta

[!INCLUDE [change-offer-type](./includes/change-offer-type.md)]

## <a name="free-trial"></a>Avaliação Gratuita

Utilize o mercado comercial para melhorar a descoberta e automatizar o fornecimento da experiência experimental da sua solução. Isto permite que os potenciais clientes utilizem o seu software como um serviço (SaaS), infraestrutura como serviço (IaaS), ou experiência na aplicação da Microsoft sem custos de 30 dias a seis meses, dependendo do tipo de oferta.

Os clientes usam o botão **Teste Gratuito** na página de listagem da sua oferta para experimentar a sua oferta. Se fornecer um teste gratuito em vários planos dentro da mesma oferta, os clientes podem mudar para um teste gratuito em outro plano, mas o período experimental não reinicia.

Para ofertas de máquinas virtuais, os clientes são cobrados custos de infraestrutura Azure para usar a oferta durante um período experimental. Após o termo do período experimental, os clientes são automaticamente cobrados pelo último plano que tentaram com base em tarifas padrão, a menos que cancelem antes do final do período experimental.

## <a name="test-drive"></a>Versão de Teste

Os clientes usam o botão **Test Drive** na página de listagem da sua oferta para ter acesso a um ambiente pré-configurado durante um número fixo de horas. Para saber mais sobre test drives, veja [o que é um test drive?](what-is-test-drive.md)

> [!TIP]
> A opção Test Drive é diferente da Prova Gratuita. Você pode oferecer Test Drive, Teste Gratuito, ou ambos. Ambos fornecem aos seus clientes a sua solução por um período de tempo fixo. No entanto, o Test Drive também inclui uma visita prática e auto-guiada às principais características e benefícios do seu produto sendo demonstrados num cenário de implementação real.

## <a name="contact-me"></a>Contacte-me

Esta opção é uma simples listagem da sua aplicação ou serviço. Os clientes utilizam o botão **Contacte-me** na página de listagem da sua oferta para solicitar que se conecte com eles sobre a sua oferta.

## <a name="get-it-now"></a>Get It Now

Esta opção de listagem inclui ofertas transacionáveis (subscrições ou preços baseados no utilizador), traz as suas próprias ofertas de licença (BYOL) e **Get It Now (Grátis)**. As ofertas transacionáveis são vendidas através do mercado comercial. A Microsoft é responsável pela faturação e cobranças. Os clientes usam o botão **Get It Now** para receber a oferta.

Esta tabela mostra quais os tipos de oferta que suportam as opções de preços que estão incluídas na opção de listagem **Get It Now.**

| Tipo de oferta | Get It Now (Grátis) | BYOL | Subscrição | Preços baseados na utilização |
| ------------ | ------------- | ------------- | ------------- | ------------- |
| Aplicação Azure (Aplicação gerida) |   |   | &#10004; | &#10004; |
| Aplicação Azure (modelo de solução) | &#10004; |   |   |   |
| Serviço de consultoria |   |   |   |   |
| Recipiente Azure | &#10004;<sup>1</sup> | &#10004;<sup>1</sup> |   |   |
| Dinâmica 365 centro de negócios | &#10004; |   |   |   |
| Dinâmica 365 Envolvimento com clientes & PowerApps | &#10004; |   |   |   |
| Dinâmica 365 para operações | &#10004; |   |   |   |
| Módulo IoT Edge | &#10004;<sup>1</sup> | &#10004;<sup>1</sup> |   |   |
| Serviço Gerido |   | &#10004; |   |   |
| Aplicação Power BI | &#10004; |   |   |   |
| Máquina Virtual do Azure |   | &#10004; |   | &#10004;<sup>2</sup> |
| Software como um serviço | &#10004; |   | &#10004; | &#10004; |
||||||

<sup>1</sup> A coluna do modelo de **preços** do separador **Plano** mostra **Livre** ou **BYOL,** mas não é selecionável.

<sup>2</sup> Preço por hora e faturado mensalmente.

### <a name="get-it-now-free"></a>Get It Now (Grátis)

Utilize esta opção de listagem para oferecer gratuitamente a sua candidatura. Os clientes usam o botão **Get It Now** para obter a sua oferta gratuita.

> [!NOTE]
> As ofertas get It Now (Grátis) não são elegíveis para benefícios do Marketplace Rewards para ofertas transacionáveis. Como não há transação através da montra, estas são categorizadas como **Trial**. Ver [Marketplace Rewards](#marketplace-rewards).

### <a name="bring-your-own-license-byol"></a>Traga a Sua Própria Licença (BYOL)

Utilize esta opção de listagem para permitir que os clientes implementem a sua oferta utilizando uma licença adquirida fora do mercado comercial. Esta opção é ideal para migrações no local para nuvem. Os clientes usam o botão **Get It Now** para comprar a sua oferta utilizando uma licença que pré-compraram de si.

> [!NOTE]
> As ofertas da BYOL não são elegíveis para benefícios do Marketplace Rewards para ofertas transacionáveis. Porque estes requerem que um cliente adquira a licença ao parceiro e não há transação através da montra do mercado comercial, estes são categorizados como **List**. Ver [Marketplace Rewards](#marketplace-rewards).

### <a name="subscription"></a>Subscrição

Pode cobrar uma taxa fixa por estes tipos de oferta:

- A Azure Application (Aplicação Gerida) oferece suporte para subscrições mensais.
- O SaaS oferece suporte para assinaturas mensais e anuais.

### <a name="usage-based-pricing"></a>Preços baseados na utilização

Os seguintes tipos de oferta suportam preços baseados na utilização:

- A Azure Application (Aplicação Gerida) oferece suporte para faturação medido. Para obter mais informações, consulte [a faturação de aplicação gerida.](partner-center-portal/azure-app-metered-billing.md)
- O SaaS oferece suporte para a faturação medido e por preço por utilizador (por assento). Para obter mais informações sobre a faturação medido, consulte [a faturação medido para SaaS utilizando o serviço de medição do mercado comercial.](partner-center-portal/saas-metered-billing.md)
- A máquina virtual Azure oferece suporte para **Per core**, Per **core size**, e Per market e preço de **tamanho central.** Estas opções têm preço por hora e são faturadas mensalmente.

Quando cria uma oferta transacionável, é importante compreender os preços, faturação, faturação e considerações de pagamento antes de selecionar um tipo de oferta e criar a sua oferta. Para saber mais, consulte [lojas online de marketplace comercial.](overview.md#commercial-marketplace-online-stores)

## <a name="sample-offer"></a>Oferta de amostras

Após a publicação da sua oferta, as opções de listagem que escolheu aparecem como botões no canto superior esquerdo da página de listagem na loja online. Por exemplo, a seguinte imagem mostra uma página de listagem de ofertas na loja online Microsoft AppSource com os botões **Get It Now** e Test **Drive:**

:::image type="content" source="media/listing-options.png" alt-text="Screenshot que ilustra a página de listagem para uma oferta com os botões Get It Now e Test Drive.":::

## <a name="listing-and-pricing-options-by-online-store"></a>Opções de listagem e preços por loja online

Com base em vários critérios, determinamos se a sua oferta está listada no Azure Marketplace, Microsoft AppSource ou em ambas as lojas online. Para mais informações sobre as diferenças entre as duas lojas online, consulte [lojas online de marketplace comercial.](overview.md#commercial-marketplace-online-stores)

A tabela a seguir mostra as opções que estão disponíveis para diferentes tipos de oferta e add-ins, e quais as lojas online em que a sua oferta pode ser listada.

| Tipos de oferta e add-ins | Contacte-me | Avaliação Gratuita | Get It Now (Grátis) | BYOL | Get It Now (Transact) |
| ------------ | ------------- | ------------- | ------------- | ------------- | ------------- |
| Máquina Virtual do Azure |   |   |   | Azure Marketplace | Azure Marketplace |
| Aplicação do Azure |   |   | Azure Marketplace | Azure Marketplace | Azure Marketplace |
| Recipiente Azure  |   |   | Azure Marketplace | Azure Marketplace |   |
| Módulo IoT Edge |   |   | Azure Marketplace | Azure Marketplace |   |
| Serviço gerido |   |   |   | Azure Marketplace |   |
| Serviço de consultoria | Ambas as lojas online |   |   |   |   |
| SaaS  | Ambas as lojas online | Ambas as lojas online | Ambas as lojas online |   | Ambas as lojas online &#42; |
| Microsoft 365 App | AppSource | AppSource |   |   |  &#42;&#42; AppSource |
| Dynamics 365 Business Central | AppSource | AppSource |   |   |   |
| Dinâmica 365 para Compromissos com o Cliente & PowerApps | AppSource | AppSource |   |   |   |
| Dinâmica 365 Operações | AppSource | AppSource |   |   |   |
| Aplicação Power BI |   |   | AppSource |   |   |
|||||||

&#42; ofertas transatable saaS no AppSource só aceitam cartões de crédito neste momento.

&#42;&#42; os add-ins microsoft 365 são gratuitos para instalar e podem ser rentabilizados usando uma oferta SaaS. Para mais informações, consulte [Monetize a sua app através do mercado comercial.](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace)

## <a name="marketplace-rewards"></a>Marketplace Rewards

Os benefícios do Marketplace Rewards dependem da opção de listagem que escolher. Para saber mais, consulte [os seus benefícios para o mercado comercial.](gtm-your-marketplace-benefits.md)

Se a sua oferta for transacionável, ganhará benefícios à medida que aumenta as suas vendas faturadas.

Ofertas não transacionáveis ganham benefícios com base no facto de um teste gratuito ser ou não anexado.

## <a name="next-steps"></a>Passos seguintes

Para escolher um tipo de oferta para criar, consulte [o guia de publicação por tipo de oferta.](publisher-guide-by-offer-type.md)
