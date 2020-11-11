---
title: Determine a sua opção de publicação - Mercado comercial da Microsoft
description: Este artigo descreve critérios de elegibilidade e requisitos para a publicação de ofertas ao Microsoft AppSource e ao Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 09/04/2020
ms.openlocfilehash: 17b53d656d0344e4178b3034a972e26d6aa0bc15
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94492111"
---
# <a name="determine-your-publishing-option"></a>Determinar a opção de publicação

A opção de publicação que escolhe para a sua oferta diz-se diretamente com os requisitos de elegibilidade e com os benefícios do mercado comercial GTM. Mais importante ainda, a seleção de opção de publicação e tipo de oferta define como os utilizadores irão interagir com a sua oferta de mercado comercial.

Para configurar a sua oferta, terá de compreender os seguintes conceitos principais do mercado comercial: as opções de publicação, tipos de oferta e configuração, e opções de listagem que regem como e onde a sua oferta é apresentada nas lojas online do mercado comercial.

Neste artigo, vai aprender:

- Como determinar a loja online apropriada para a sua solução.
- Quais opções de publicação e de listagem estão disponíveis em cada loja online.
- Quais os tipos de oferta disponíveis para cada opção de publicação.

## <a name="commercial-marketplace-publishing-options"></a>Opções de publicação de mercado comercial

A tabela seguinte mostra as opções de publicação para os tipos de oferta no Microsoft AppSource e no Azure Marketplace.

|   | **Lista (Contacto)**  | **Lista (Julgamento)**  | **Gratuito** | **BYOL** | **Transação**|
| :--------- | :----------- | :------------ | :----------- | :---------- |:---------- |
| **Máquina Virtual** |  |  |  | Azure Marketplace |  Azure Marketplace |
| **Aplicativos Azure (multi-VM)** |  |  | Azure Marketplace | Azure Marketplace | Azure Marketplace  |
| **Imagem de recipiente** |  |  | Azure Marketplace | Azure Marketplace |   |
| **Módulo IoT Edge** |  |  | Azure Marketplace | Azure Marketplace |   |
| **Serviços geridos** |  |  |  | Azure Marketplace |   |
| **Serviços de consultadoria** | Ambas as lojas online |  |  |  |   |
| **App SaaS** | Ambas as lojas online | Ambas as lojas online | Ambas as lojas online |  | Ambas as lojas online* |
| **Microsoft 365 App** | AppSource | AppSource |  |  | AppSource**  |
| **Dynamics 365 add-in** |  AppSource | AppSource |  |  |   |
| **PowerApps** | AppSource |AppSource  |  |  |   |

&#42; aplicação SaaS As ofertas transact no Microsoft AppSource são atualmente apenas cartão de crédito.

&#42;&#42; as ofertas da Microsoft 365 são gratuitas e podem ser rentabilizadas através da oferta saaS como um serviço de licenciamento. Para mais informações, consulte [Monetize o seu add-in Microsoft 365 através do mercado comercial da Microsoft.](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace)

## <a name="choose-a-listing-option"></a>Escolha uma opção de listagem

As opções de listagem disponíveis oferecem um envolvimento diferenciado do cliente, ao mesmo tempo que lhe dá acesso à partilha de chumbo e [benefícios comerciais do mercado.](./gtm-your-marketplace-benefits.md) Note as opções de listagem que correspondem à opção de publicação:

| **Opção de publicação**    | **Descrição**  |
| :------------------- | :-------------------|
| **Lista** | Simples listagem da sua aplicação ou serviço que permite a um utilizador comercial do mercado solicitar-lhe a ligação com o cliente através das opções de listagem **Contacte-me.** |
| **Avaliação** | Utilize o mercado comercial para melhorar a descoberta e automatizar o fornecimento da experiência experimental da sua solução, permitindo aos potenciais utilizadores utilizarem a sua experiência de saaS, IaaS ou Microsoft na aplicação sem custos limitados antes de comprarem. As opções de listagem utilizadas para a opção de publicação de ensaio são **free trial** ou **test drive**. |
| **BYOL** | Utilize o mercado comercial para melhorar a descoberta e automatizar o fornecimento da sua solução e concluir a transação financeira separadamente. Os tipos de oferta BYOL são ideais para migrações em nuvem. A opção de listagem é **Get it Now**.
| **Transação** | As ofertas transactais são vendidas através do mercado comercial. A Microsoft é responsável pela faturação e cobranças. A opção de listagem é **Get it Now**.|

> [!Note]
> Ao utilizar a opção de publicação Da Transact, é importante compreender os preços, faturação, faturação e considerações de pagamento antes de selecionar um tipo de oferta e criar a sua oferta. Para saber mais, reveja o artigo [Capacidades de transação comercial.](./marketplace-commercial-transaction-capabilities-and-considerations.md)

## <a name="selecting-an-online-store"></a>Selecionando uma loja online

Cada loja online atende diferentes requisitos do cliente para soluções de negócios e TI. O seu tipo de oferta, capacidades de transação e categoria determinará onde a sua oferta será publicada. As categorias e subcategorias são mapeadas para cada loja online com base no tipo de solução que publica:

**O Microsoft AppSource** oferece soluções de negócio, como soluções da indústria e serviços de consultoria, para a Dynamics 365, Microsoft 365 e Power Platform.

**O Azure Marketplace** oferece soluções de TI construídas para ou em Azure, bem como serviços de consultoria que aceleram o uso do Azure pelos seus clientes.

Selecione a categoria e a subcategoria que melhor se alinham com o seu tipo de solução. Por exemplo, uma firewall de aplicação web é uma solução de TI que deve ser publicada no Azure Marketplace, na categoria De Segurança. Uma aplicação de gestão de contratos é uma solução de negócio que deve ser publicada para appSource na categoria Vendas. A seleção da categoria ou subcategoria incorreta pode resultar na publicação da sua oferta na loja online errada.

### <a name="publishing-to-both-online-stores-saas-offers-only"></a>Publicação em ambas as lojas online (só o SaaS oferece)

As ofertas da SaaS podem ser publicadas no Azure Marketplace ou no AppSource. Se a sua oferta SaaS for *simultaneamente* uma solução de TI (Azure Marketplace) e uma solução de negócio (AppSource), selecione uma categoria e/ou uma subcategoria aplicável a cada loja online. As ofertas publicadas em ambas as lojas online devem ter uma proposta de valor como solução de TI *e* uma solução de negócio.

> [!IMPORTANT]
> As ofertas do SaaS com [faturação medido](partner-center-portal/saas-metered-billing.md) estão disponíveis através do Azure Marketplace e do portal Azure. As ofertas do SaaS com apenas planos privados estão disponíveis através do portal Azure.

| Faturação medido | Plano público | Plano privado | Disponível em: |
|---|---|---|---|
| Yes             | Yes         | No           | Azure Marketplace e portal Azure |
| Yes             | Yes         | Yes          | Azure Marketplace e portal Azure* |
| Yes             | No          | Yes          | Apenas portal Azure |
| No              | No          | Yes          | Apenas portal Azure |

&#42; O plano privado da oferta só estará disponível através do portal Azure

Por exemplo, uma oferta com faturação medido e um plano privado apenas (sem plano público), será adquirida por clientes no portal Azure. Saiba mais sobre [ofertas privadas no mercado comercial da Microsoft.](private-offers.md)

### <a name="categories"></a>Categorias

As categorias e subcategorias são mapeadas para cada loja online com base no tipo de solução. Selecione as categorias e subcategorias que melhor se alinham com a sua solução. Pode selecionar:

- Pelo menos uma e até duas categorias. Pode escolher uma categoria primária e secundária.
- Até duas subcategorias para cada categoria primária e/ou secundária. Se não selecionar nenhuma subcategoria, a sua oferta continuará a ser detetável na categoria selecionada.

[!INCLUDE [categories and subcategories](./includes/categories.md)]

## <a name="next-steps"></a>Passos seguintes

- Assim que decidir uma opção de publicação, reveja o [guia de publicação por tipo de oferta](./publisher-guide-by-offer-type.md).