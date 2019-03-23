---
title: Ofertas privadas | Azure
description: Ofertas privadas no Azure Marketplace para editores de aplicações e serviços.
services: Azure, Marketplace, Compute
documentationcenter: ''
author: qianw211
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/1/2018
ms.author: qianw211
ms.openlocfilehash: c3294aaa2b6c88e08028c8958d2ab3a7297aea74
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351818"
---
# <a name="private-offers"></a>Ofertas privadas

Privada ofertas na [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) ativar os publicadores criem SKUs que só são visíveis para os clientes de destinados.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Desbloquear negociações de enterprise com ofertas privadas

Os clientes empresariais usam cada vez mais mercados online para encontrar, experimentar e comprar soluções na cloud. Agora com ofertas privadas, os publicadores podem usar a marketplace em privado partilhar soluções personalizadas com os clientes de destinados com capacidades de que as empresas exigem:

- *Negociado preços* permite que os publicadores estender descontos e desativar-lista de preços de ofertas disponíveis publicamente.
- *Privada termos e condições* ativar onde os editores podem adaptar os termos e condições a um cliente específico.
- *Especializada configurações* permitem que os publicadores adapte suas máquinas virtuais, aplicações do Azure e aplicações SaaS de ofertas para as necessidades de um cliente individual. Esta opção também permite aos editores fornecer acesso de pré-visualização para novas funcionalidades do produto, antes de iniciar mais amplamente a todos os clientes.

Ofertas privadas permitem que os editores a tirar partido do dimensionamento e disponibilidade global do marketplace público, com a flexibilidade e controlo necessários para negociar e fornecer ofertas personalizadas e configurações. Juntos, esses recursos abrem a porta para adoção do enterprise forte de marketplaces de cloud.  As empresas podem agora comprar e vender em formas esperam e a pedido.

Ofertas privadas estão agora disponíveis para a Máquina Virtual, aplicação do Azure (implementado como modelos de soluções ou aplicativos gerenciados) e as aplicações de SaaS oferecem. Como públicas ofertas, ofertas privadas podem ser criadas e geridas através da [Cloud Partner Portal](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus).  Os clientes podem ser concedidos ou revogado acesso a ofertas privadas em minutos.

## <a name="creating-private-offers-using-skus-and-plans"></a>Criar privada oferece a utilizar SKUs e planos

Para *ofertas de novas ou existentes com SKUs públicos ou planos*, os publicadores podem criar facilmente novos e variações privadas através da criação de novos SKUs ou planos e marcar como privada.  [SKUs de privada](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus) e planos são componentes de uma oferta e só estão visíveis e que podem ser comprados pelos clientes visados. Planos e os SKUs de privada podem reutilizar as imagens de base e/ou metadados já publicado para um público SKU ou plano da oferta. Esta opção permite aos editores criar diversas variações privadas de uma oferta pública sem ter de publicar várias versões da mesma imagem base e metadados da oferta. Para máquinas virtuais e o Azure application ofertas de apenas, quando um SKU privado partilha uma imagem de base com um SKU público, quaisquer alterações à imagem de base da oferta propagará em todos os SKUs públicos e privados usando dessa imagem básica.

Para *novas ofertas que incluem apenas privada SKUs ou planos*, os publicadores podem criar suas ofertas como qualquer outra oferta e, em seguida, marcar a SKUs ou planos como particular. As ofertas que têm apenas privada SKUs ou planos não serão Detetáveis ou podem ser acedidos através de [do Azure Marketplace](https://azuremarketplace.microsoft.com) ou o [portal do Azure](https://azure.microsoft.com/features/azure-portal/) pelos clientes que não estão associados a oferta.

## <a name="targeting-customers-with-private-offers"></a>Filtragem de clientes com ofertas privadas
Para ofertas privadas novas e existentes, os publicadores podem direcionar os clientes que utilizam os identificadores de subscrição. Os publicadores com uma oferta de Máquina Virtual ou aplicativo do Azure podem restringir a disponibilidade de um SKU de privada para um ID de subscrição individual do Azure ou carregar um CSV de até 20 000 IDs de subscrição do Azure. Ao utilizar uma oferta de privada de aplicação SaaS, os publicadores podem associar um ID de subscrição do Azure ou um ID de inquilino para restringir a disponibilidade de um plano de privada, com o manual ou uma abordagem de carregamento CSV.

Depois de uma oferta foi certified e publicada, os clientes podem ser atualizados ou removidos do SKU ou do plano de dentro de minutos, ao utilizar a funcionalidade de sincronização privada subscrições. Esta capacidade permite aos editores de forma rápida e fácil atualizar a lista de clientes para que o SKU privada ou o plano é apresentado sem recertifying ou republicar a oferta.

## <a name="deploying-private-offers"></a>Implementação privada oferece

Ofertas privadas são apenas Detetáveis através da [portal do Azure](https://azure.microsoft.com/features/azure-portal/) e não são apresentadas através de [Azure Marketplace](https://azuremarketplace.microsoft.com). Depois de iniciar sessão no portal do Azure, os clientes podem selecionar o elemento de navegação do Marketplace para aceder às suas ofertas privadas. Ofertas privadas também será apresentado nos resultados da pesquisa e pode ser implementadas via linha de comandos e modelos do Azure Resource Manager, como qualquer outras ofertas.

![[Ofertas privadas]](./media/marketplace-publishers-guide/private-offer.png)

Ofertas privadas também serão apresentada nos resultados da pesquisa. Apenas procure do distintivo "Private".

> [!Note]
> Ofertas privadas não são suportadas com subscrições estabelecidas através de um revendedor do programa fornecedor de soluções Cloud (CSP).

## <a name="next-steps"></a>Passos Seguintes

Se gostaria de aproveitar esses novos recursos, pode começar a utilizar vender no [do Azure Marketplace](https://azuremarketplace.microsoft.com/sell).
