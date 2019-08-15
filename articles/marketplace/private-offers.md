---
title: Ofertas privadas | Azure Marketplace
description: Ofertas privadas no Azure Marketplace para Publicadores de aplicativos e serviços.
services: Azure, Marketplace, Compute
author: qianw211
manager: pabutler
ms.service: marketplace
ms.topic: article
ms.date: 11/1/2018
ms.author: pabutler
ms.openlocfilehash: 35134d828098af0cff3d585dee600ee1808b00c4
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69013624"
---
# <a name="private-offers"></a>Ofertas privadas

As ofertas privadas em [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) permitem que os editores criem SKUs que só são visíveis para os clientes de destino.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Desbloqueie acordos empresariais com ofertas privadas

Os clientes corporativos usam cada vez mais Marketplaces online para localizar, experimentar e comprar soluções de nuvem. Agora, com ofertas privadas, os editores podem usar o Marketplace para compartilhar soluções personalizadas de forma privada com os clientes de destino com recursos que as empresas precisam:

- Os *preços negociados* permitem que os editores ampliem os descontos e os preços fora da lista das ofertas disponíveis publicamente.
- *Termos e condições particulares* permitem que os editores personalizem os termos e condições para um cliente específico.
- *As configurações especializadas* permitem que os editores personalizem suas máquinas virtuais, aplicativos do Azure e aplicativos SaaS oferecem às necessidades de um cliente individual. Essa opção também permite que os editores forneçam acesso de visualização aos novos recursos do produto, antes de iniciar de forma mais ampla para todos os clientes.

As ofertas privadas permitem que os editores aproveitem a escala e a disponibilidade global de um Marketplace público, com a flexibilidade e o controle necessários para negociar e fornecer configurações e negócios personalizados. Juntos, esses recursos abrem a porta para a adoção forte da empresa de Marketplaces de nuvem.  As empresas agora podem comprar e vender de maneiras que esperam e exigem.

As ofertas privadas agora estão disponíveis para máquinas virtuais, Aplicativo Azure (implementado como modelos de solução ou aplicativos gerenciados) e ofertas de aplicativos SaaS. Como as ofertas públicas, as ofertas privadas podem ser criadas e gerenciadas por meio do [portal do Cloud Partner](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus).  Os clientes podem ter acesso concedido ou revogado a ofertas privadas em minutos.

## <a name="creating-private-offers-using-skus-and-plans"></a>Criando ofertas privadas usando SKUs e planos

Para *ofertas novas ou existentes com SKUs ou planos públicos*, os editores podem criar facilmente novas variações privadas, criando novas SKUs ou planos e marcando-as como particulares.  Os SKUs e os planos [privados](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus) são componentes de uma oferta e só são visíveis e compráveisdos pelos clientes de destino. Os planos e SKUs particulares podem reutilizar as imagens base e/ou oferecer metadados já publicados para um plano ou SKU público. Essa opção permite que os editores criem várias variações privadas de uma oferta pública sem precisar publicar várias versões da mesma imagem base e oferecer metadados. Para as ofertas de máquina virtual e aplicativo do Azure somente, quando uma SKU privada compartilha uma imagem base com um SKU público, todas as alterações na imagem base da oferta serão propagadas em todas as SKUs públicas e privadas usando essa imagem base.

Para *novas ofertas que incluem apenas SKUs particulares ou planos*, os editores podem criar suas ofertas como qualquer outra oferta e, em seguida, marcar os SKUs ou planos como particulares. As ofertas que têm apenas os SKUs ou os planos privados não poderão ser descobertos ou acessíveis por meio do [Azure Marketplace](https://azuremarketplace.microsoft.com) ou pelo [portal do Azure](https://azure.microsoft.com/features/azure-portal/) por clientes que não estão associados à oferta.

## <a name="targeting-customers-with-private-offers"></a>Direcionando clientes com ofertas privadas
Para ofertas privadas novas e existentes, os editores podem direcionar clientes usando identificadores de assinatura. Os Publicadores que usam uma máquina virtual ou Aplicativo Azure oferta podem restringir a disponibilidade de uma SKU privada para uma ID de assinatura individual do Azure ou carregar um CSV de até 20.000 IDs de assinatura do Azure. Ao usar uma oferta privada de aplicativo SaaS, os editores podem associar uma ID de assinatura do Azure ou uma ID de locatário para restringir a disponibilidade de um plano privado, usando a abordagem de carregamento manual ou CSV.

Depois que uma oferta tiver sido certificada e publicada, os clientes poderão ser atualizados ou removidos da SKU ou do plano em minutos usando o recurso Sincronizar assinaturas privadas. Essa funcionalidade permite que os editores atualizem com rapidez e facilidade a lista de clientes para os quais a SKU ou o plano privado é apresentado sem a recertificação ou a republicação da oferta.

## <a name="deploying-private-offers"></a>Implantando ofertas privadas

As ofertas privadas só são descobertas por meio do [portal do Azure](https://azure.microsoft.com/features/azure-portal/) e não são apresentadas por meio do [Azure Marketplace](https://azuremarketplace.microsoft.com). Depois de conectado à portal do Azure, os clientes podem selecionar o elemento de navegação do Marketplace para acessar suas ofertas privadas. As ofertas privadas também aparecerão nos resultados da pesquisa e poderão ser implantadas por meio de modelos de linha de comando e Azure Resource Manager como qualquer outra oferta.

![[Ofertas privadas]](./media/marketplace-publishers-guide/private-offer.png)

Ofertas privadas também serão exibidas nos resultados da pesquisa. Basta procurar o selo "privado".

## <a name="next-steps"></a>Passos seguintes

Se você quiser aproveitar esses novos recursos, você pode começar a vender no [Azure Marketplace](https://azuremarketplace.microsoft.com/sell).
