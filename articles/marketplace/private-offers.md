---
title: Ofertas privadas no mercado comercial da Microsoft
description: Ofertas privadas no mercado comercial da Microsoft para editores de aplicações e serviços.
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: vikrambmsft
ms.author: vikramb
ms.date: 07/01/2020
ms.openlocfilehash: 0bb5dccfc99a693ec89ed08ac31b77eb16442f73
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129971"
---
# <a name="private-offers-in-the-microsoft-commercial-marketplace"></a>Ofertas privadas no mercado comercial da Microsoft

As ofertas privadas no [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) permitem que as editoras criem planos que só são visíveis para clientes direcionados. Este artigo discute as opções e benefícios das ofertas privadas.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Desbloqueie negócios empresariais com ofertas privadas

Os clientes empresariais usam cada vez mais mercados online para encontrar, tentar e comprar soluções em nuvem. Agora, com ofertas privadas, os editores podem usar o mercado para partilhar soluções personalizadas com clientes direcionados com capacidades que as empresas exigem:

- *Os preços negociados* permitem que os editores aumentem os descontos e os preços fora da lista das ofertas publicamente disponíveis.
- *Os termos e condições privados* permitem aos editores adaptar os termos e condições a um cliente específico.
- *As configurações especializadas* permitem que as editoras adaptem as suas Máquinas Virtuais, Aplicações Azure e Aplicações SaaS oferecem às necessidades de um cliente individual. Esta opção também permite que os editores forneçam acesso pré-visualizado a novas funcionalidades do produto, antes de lançarem de forma mais ampla a todos os clientes.

As ofertas privadas permitem que os editores aproveitem a escala e a disponibilidade global de um mercado público, com a flexibilidade e controlo necessários para negociar e entregar ofertas e configurações personalizadas. Juntos, estas características abrem a porta a uma forte adoção empresarial de mercados em nuvem. As empresas podem agora comprar e vender de forma que esperam e exigem.

As ofertas privadas estão agora disponíveis para Máquina Virtual, Aplicação Azure (implementada como modelos de solução ou aplicações geridas) e ofertas de Apps SaaS.

<!--- Like public offers, private offers can be created and managed via the [Cloud Partner Portal](). Customers can be granted or revoked access to private offers in minutes.
--->

## <a name="creating-private-offers-using-plans"></a>Criação de ofertas privadas usando planos

Para *ofertas novas ou existentes com planos,* as editoras podem facilmente criar novas variações privadas, criando novos planos (anteriormente conhecidos como SKUs) e marcando-os como privados. Cada oferta pode ter até 45 planos privados.

<!--- [Private SKUs]() --->

Os planos privados são componentes de uma oferta e só são visíveis e purciáveis pelos clientes visados. Os planos privados podem reutilizar as imagens base e/ou oferecer metadados já publicados para um plano público. Esta opção permite que os editores criem múltiplas variações privadas de uma oferta pública sem terem de publicar várias versões da mesma imagem base e oferecer metadados. Para a aplicação Virtual Machine e Azure oferece apenas, quando um plano privado partilha uma imagem base com um plano público, quaisquer alterações à imagem base da oferta irão propagar-se em todos os planos públicos e privados usando essa imagem base.

Para *novas ofertas que incluem apenas planos privados,* os editores podem criar as suas ofertas como qualquer outra oferta, e depois marcar os planos como privados. As ofertas que apenas têm planos privados não serão detetáveis ou acessíveis através do mercado comercial da Microsoft ou do [portal Azure](https://azure.microsoft.com/features/azure-portal/) por clientes que não estejam associados à oferta.

>[!NOTE]
>Uma oferta que contenha apenas planos privados não será visível no mercado público de Azure ou AppSource.

## <a name="targeting-customers-with-private-offers"></a>Direcionar clientes com ofertas privadas

Para ofertas privadas novas e existentes, os editores podem direcionar os clientes usando identificadores de subscrição. Os editores que usam uma oferta de aplicação Virtual Machine ou Azure podem limitar a disponibilidade de um plano privado a um ID de subscrição individual do Azure ou carregar um CSV de até 20.000 IDs de subscrição Azure. Ao utilizar uma oferta privada da SaaS App, os editores podem associar um ID de inquilino para limitar a disponibilidade de um plano privado, utilizando a abordagem de upload manual ou CSV.

Uma vez que uma oferta tenha sido certificada e publicada, os clientes podem ser atualizados ou removidos do plano em poucos minutos, utilizando a funcionalidade Sync Private Subscriptions. Esta capacidade permite que os editores atualizem de forma rápida e fácil a lista de clientes aos quais o plano privado é apresentado sem certificar ou publicar novamente a oferta.

## <a name="deploying-private-offers"></a>Implantação de ofertas privadas

As ofertas privadas só são detetáveis através do [portal Azure](https://azure.microsoft.com/features/azure-portal/) e não são mostradas no [Microsoft AppSource](https://appsource.microsoft.com/) ou [no Azure Marketplace.](https://azuremarketplace.microsoft.com) Para saber mais sobre a publicação nas diferentes lojas online do mercado comercial, consulte [Determine a sua opção de publicação.](./determine-your-listing-type.md)

Uma vez assinado no portal Azure, os clientes podem selecionar o elemento de navegação Marketplace para aceder às suas ofertas privadas. As ofertas privadas também aparecerão nos resultados da pesquisa e podem ser implementadas através da linha de comando e dos modelos do Azure Resource Manager como qualquer outra oferta.

![[Ofertas privadas]](./media/marketplace-publishers-guide/private-offer.png)

As ofertas privadas também aparecerão nos resultados da pesquisa. Procura o distintivo **privado.**

>[!Note]
>As ofertas privadas não são suportadas com subscrições estabelecidas através de um revendedor do programa Cloud Solution Provider (CSP).


<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans]() guide.
--->