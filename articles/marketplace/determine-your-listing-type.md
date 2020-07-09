---
title: Determine a sua opção de publicação - Mercado comercial da Microsoft
description: Este artigo descreve critérios de elegibilidade e requisitos para a publicação de ofertas ao Microsoft AppSource e ao Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 06/22/2020
ms.openlocfilehash: 3d8692d3180e4164bff544f71a1216097a390773
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86103658"
---
# <a name="determine-your-publishing-option"></a>Determinar a opção de publicação

A opção de publicação que escolhe para a sua oferta diz-se diretamente com os requisitos de elegibilidade e com os benefícios do mercado comercial GTM. Mais importante ainda, a seleção de opção de publicação e tipo de oferta define como os utilizadores irão interagir com a sua oferta de mercado comercial.

Para configurar a sua oferta, terá de compreender os seguintes conceitos principais do mercado comercial: as opções de publicação, tipos de oferta e configuração, e chamadas à ação que regem como e onde a sua oferta é apresentada nas lojas de marketplace comercial.

Neste artigo, vai aprender:

- Como determinar a montra adequada para a sua solução
- Quais opções de publicação e chamadas para a ação estão disponíveis em cada montra
- Quais os tipos de oferta disponíveis para cada opção de publicação

## <a name="commercial-marketplace-publishing-options"></a>Opções de publicação de mercado comercial

A tabela seguinte mostra as opções de publicação para os tipos de oferta no Microsoft AppSource e no Azure Marketplace.

|   | **Lista (Contacto)**  | **Lista (Julgamento)**  | **Gratuito** | **BYOL** | **Transação**|
| :--------- | :----------- | :------------ | :----------- | :---------- |:---------- |
| **Máquina Virtual** |  |  |  | Azure Marketplace |  Azure Marketplace |
| **Aplicativos Azure (multi-VM)** |  |  | Azure Marketplace | Azure Marketplace | Azure Marketplace  |
| **Imagem de recipiente** |  |  | Azure Marketplace | Azure Marketplace |   |
| **Módulo IoT Edge** |  |  | Azure Marketplace | Azure Marketplace |   |
| **Serviços geridos** |  |  |  | Azure Marketplace |   |
| **Serviços de consultadoria** | Ambas as montras |  |  |  |   |
| **App SaaS** | Ambas as montras | Ambas as montras | Ambas as montras |  | Ambas as montras* |
| **Microsoft 365 App** | AppSource | AppSource |  |  | AppSource**  |
| **Dynamics 365 add-in** |  AppSource | AppSource |  |  |   |
| **PowerApps** | AppSource |AppSource  |  |  |   |

&#42; aplicação SaaS As ofertas transact no Microsoft AppSource são atualmente apenas cartão de crédito.

&#42;&#42; as ofertas da Microsoft 365 são gratuitas e podem ser rentabilizadas através da oferta saaS como um serviço de licenciamento. Para mais informações, consulte [Monetize o seu add-in Office 365 através do mercado comercial da Microsoft.](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace)

## <a name="selecting-a-storefront"></a>Selecionando uma montra

Antes de selecionar uma opção de publicação, é importante compreender os requisitos de elegibilidade da montra para soluções, apps e serviços de marketplace comercial. Cada montra serve requisitos exclusivos do cliente e visa públicos específicos. O seu tipo de oferta, capacidades de transação e categoria ou indústria determinarão onde publicar a sua oferta.

As aplicações **do Microsoft AppSource** são soluções de linha de negócio que podem ser construídas em Azure ou incorporadas para: Dynamics 365, Office 365, Power BI ou Power Apps. Os serviços de consultoria AppSource são ofertas de serviços profissionais que ajudam os clientes a começar ou acelerar o uso da Dynamics 365 e power BI.

As aplicações **do Azure Marketplace** são soluções técnicas de "bloco de construção" construídas ou construídas para o Azure e destinadas a um público de TI ou desenvolvedor. Os serviços de consultoria Azure Marketplace são ofertas de serviços profissionais que ajudam os clientes a começar ou acelerar o uso do Azure.

>[!Note]
>"Cross-listing" (apenas para aplicações SaaS): se a sua oferta SaaS se destina a um público técnico (Azure Marketplace) e a um público de negócios (AppSource), pode selecionar uma categoria e/ou subcategoria aplicável a qualquer uma das Storefronts. Tenha em atenção que a intenção de "cruzar" a sua oferta deve basear-se numa proposta de valor que se estende a ambos os públicos. Clique [aqui](./gtm-offer-listing-best-practices.md#categories) para ver categorias aplicáveis a cada Montra.

## <a name="choose-a-publishing-option"></a>Escolha uma opção de publicação

As opções de publicação disponíveis oferecem um envolvimento diferenciado do cliente, ao mesmo tempo que lhe dá acesso à partilha de chumbo e [aos benefícios do mercado comercial.](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits) Note as chamadas para a ação que correspondem à opção de publicação:

| **Opção de publicação**    | **Descrição**  |
| :------------------- | :-------------------|
| **Lista** | Simples listagem da sua aplicação ou serviço que permite a um utilizador comercial do mercado solicitar-lhe a ligação com o cliente através da chamada de chamada para ação **do Contact Me.** |
| **Avaliação** | Utilize o mercado comercial para melhorar a descoberta e automatizar o fornecimento da experiência experimental da sua solução, permitindo aos potenciais utilizadores utilizarem a sua experiência de saaS, IaaS ou Microsoft na aplicação sem custos limitados antes de comprarem. As chamadas à ação utilizadas para a opção de publicação do ensaio são **de Teste Gratuito** ou Test **Drive**. |
| **BYOL** | Utilize o mercado comercial para melhorar a descoberta e automatizar o fornecimento da sua solução e concluir a transação financeira separadamente. Os tipos de oferta BYOL são ideais para migrações em nuvem. O call-to-action é **Get it Now**.
| **Transação** | As ofertas transactais são vendidas através do mercado comercial. A Microsoft é responsável pela faturação e cobranças. O call-to-action é **Get it Now**.|

> [!Note]
> Ao utilizar a opção de publicação Da Transact, é importante compreender os preços, faturação, faturação e considerações de pagamento antes de selecionar um tipo de oferta e criar a sua oferta. Para saber mais, reveja o artigo [Capacidades de transação comercial.](./marketplace-commercial-transaction-capabilities-and-considerations.md)

## <a name="next-steps"></a>Próximos passos

- Assim que decidir uma opção de publicação, está pronto para [selecionar o tipo de oferta](./publisher-guide-by-offer-type.md) que será usado para apresentar a sua oferta.
- Reveja os requisitos de elegibilidade nas opções de publicação por secção de tipo oferta para finalizar a seleção e configuração da sua oferta.
- Reveja os padrões de publicação por montra, por exemplo, sobre como a sua solução mapeia para um tipo de oferta e configuração.
