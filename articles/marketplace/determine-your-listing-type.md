---
title: Determine a sua opção de publicação - Mercado comercial da Microsoft
description: Este artigo descreve critérios de elegibilidade e requisitos para a publicação de ofertas ao Microsoft AppSource e ao Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 07/30/2020
ms.openlocfilehash: 1b0846c68f6860b5c7dac9e93808088dac4f6a05
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607434"
---
# <a name="determine-your-publishing-option"></a>Determinar a opção de publicação

A opção de publicação que escolhe para a sua oferta diz-se diretamente com os requisitos de elegibilidade e com os benefícios do mercado comercial GTM. Mais importante ainda, a seleção de opção de publicação e tipo de oferta define como os utilizadores irão interagir com a sua oferta de mercado comercial.

Para configurar a sua oferta, terá de compreender os seguintes conceitos principais do mercado comercial: as opções de publicação, tipos de oferta e configuração, e chamadas para a ação que regem como e onde a sua oferta é apresentada nas lojas online do mercado comercial.

Neste artigo, vai aprender:

- Como determinar a loja online apropriada para a sua solução.
- Quais as opções de publicação e chamadas para a ação estão disponíveis em cada loja online.
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

&#42;&#42; as ofertas da Microsoft 365 são gratuitas e podem ser rentabilizadas através da oferta saaS como um serviço de licenciamento. Para mais informações, consulte [Monetize o seu add-in Office 365 através do mercado comercial da Microsoft.](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace)

## <a name="choose-a-call-to-action"></a>Escolha uma chamada para a ação

As opções de publicação disponíveis oferecem um envolvimento diferenciado do cliente, ao mesmo tempo que lhe dá acesso à partilha de chumbo e [aos benefícios do mercado comercial.](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits) Note as chamadas para a ação que correspondem à opção de publicação:

| **Opção de publicação**    | **Descrição**  |
| :------------------- | :-------------------|
| **Lista** | Simples listagem da sua aplicação ou serviço que permite a um utilizador comercial do mercado solicitar-lhe a ligação com o cliente através da chamada de chamada para ação **do Contact Me.** |
| **Avaliação** | Utilize o mercado comercial para melhorar a descoberta e automatizar o fornecimento da experiência experimental da sua solução, permitindo aos potenciais utilizadores utilizarem a sua experiência de saaS, IaaS ou Microsoft na aplicação sem custos limitados antes de comprarem. As chamadas à ação utilizadas para a opção de publicação do ensaio são **de Teste Gratuito** ou Test **Drive**. |
| **BYOL** | Utilize o mercado comercial para melhorar a descoberta e automatizar o fornecimento da sua solução e concluir a transação financeira separadamente. Os tipos de oferta BYOL são ideais para migrações em nuvem. O call-to-action é **Get it Now**.
| **Transação** | As ofertas transactais são vendidas através do mercado comercial. A Microsoft é responsável pela faturação e cobranças. O call-to-action é **Get it Now**.|

> [!Note]
> Ao utilizar a opção de publicação Da Transact, é importante compreender os preços, faturação, faturação e considerações de pagamento antes de selecionar um tipo de oferta e criar a sua oferta. Para saber mais, reveja o artigo [Capacidades de transação comercial.](./marketplace-commercial-transaction-capabilities-and-considerations.md)

## <a name="selecting-an-online-store"></a>Selecionando uma loja online

Cada loja online serve requisitos exclusivos do cliente e direciona audiências específicas. O seu tipo de oferta, capacidades de transação e categoria determinará onde a sua oferta será publicada. As categorias e subcategorias são mapeadas para cada loja online com base no público-alvo:

**O Microsoft AppSource** tem como alvo utilizadores de negócios que procuram soluções de linha de negócios ou indústria e serviços de consultoria para a Dynamics 365, Microsoft 365 e Power Platform.

**O Azure Marketplace** tem como alvo profissionais de TI e desenvolvedores que procuram soluções construídas para ou no Azure, bem como serviços de consultoria que acelerem o uso do Azure.

Selecione a categoria e a subcategoria que melhor se alinham com o seu público-alvo. Por exemplo, uma firewall de aplicação web deve ser publicada no Azure Marketplace, na categoria De Segurança, uma vez que o público pretendido são profissionais de TI. Em vez disso, uma aplicação de gestão de contratos deve ser publicada para appSource na categoria Vendas, uma vez que o público pretendido são utilizadores de negócios. A seleção da categoria ou subcategoria incorreta pode resultar na publicação da sua oferta na loja online errada.

### <a name="publishing-to-both-online-stores-saas-offers-only"></a>Publicação em ambas as lojas online (só o SaaS oferece)

As ofertas da SaaS podem ser publicadas no Azure Marketplace ou no AppSource. Se a sua oferta SaaS se *destinar tanto* a um público técnico (Azure Marketplace) como a um público de negócios (AppSource), selecione uma categoria e/ou uma subcategoria aplicável a cada loja online. As ofertas publicadas em ambas as lojas online devem ter uma proposta de valor que se estende a profissionais de TI *e* utilizadores de negócios.

> [!IMPORTANT]
> As ofertas do SaaS com faturação medido estão disponíveis através do Azure Marketplace e do portal Azure. As ofertas do SaaS com apenas planos privados estão disponíveis através do portal Azure.

| Faturação medido | Plano público | Plano privado | Disponível em: |
|---|---|---|---|
| Yes             | Sim         | No           | Azure Marketplace e portal Azure |
| Yes             | Yes         | Yes          | Azure Marketplace e portal Azure* |
| Sim             | Não          | Yes          | Apenas portal Azure |
| No              | Não          | Yes          | Apenas portal Azure |

&#42; O plano privado da oferta só estará disponível através do portal Azure

Por exemplo, uma oferta com faturação medido e um plano privado apenas (sem plano público), será adquirida por clientes no portal Azure. Saiba mais sobre [ofertas privadas no mercado comercial da Microsoft.](private-offers.md)

### <a name="categories"></a>Categorias

As categorias e subcategorias são mapeadas para cada loja online com base no público-alvo. Selecione as categorias e subcategorias que melhor se alinham com a sua oferta e o público pretendido. Pode selecionar:

- Pelo menos uma e até duas categorias. Tem a opção de escolher uma categoria primária e secundária.
- Até duas subcategorias para cada categoria primária e/ou secundária. Se não selecionar nenhuma subcategoria, a sua oferta continuará a ser detetável na categoria selecionada.

[!INCLUDE [categories and subcategories](./includes/categories.md)]

## <a name="next-steps"></a>Passos seguintes

- Assim que decidir uma opção de publicação, está pronto para [selecionar o tipo de oferta](./publisher-guide-by-offer-type.md) que será usado para apresentar a sua oferta.
- Reveja os requisitos de elegibilidade nas opções de publicação por secção de tipo oferta para finalizar a seleção e configuração da sua oferta.
- Reveja os padrões de publicação por loja online por exemplos sobre como a sua solução mapeia para um tipo de oferta e configuração.
