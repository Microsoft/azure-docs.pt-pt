---
title: Ofertas privadas no mercado comercial da Microsoft
description: Ofertas privadas no mercado comercial da Microsoft para editores de aplicações e serviços.
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: vikrambmsft
ms.author: vikramb
ms.date: 01/28/2021
ms.openlocfilehash: 786ecbf553ace6a90515347e8138eeb6e022589b
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063101"
---
# <a name="private-offers-in-the-microsoft-commercial-marketplace"></a>Ofertas privadas no mercado comercial da Microsoft

As ofertas privadas, também chamadas de planos privados, permitem aos editores criar planos que só são visíveis para clientes direcionados. Este artigo discute as opções e benefícios das ofertas privadas.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Desbloqueie negócios empresariais com ofertas privadas

Ao criar ofertas privadas, os editores podem oferecer soluções personalizadas a clientes direcionados com capacidades que as empresas exigem:

- _Os preços negociados_ permitem que os editores aumentem os descontos e os preços fora da lista das ofertas publicamente disponíveis.
- _Os termos e condições privados_ permitem aos editores adaptar os termos e condições a um cliente específico.
- _As configurações especializadas_ permitem que os editores adaptem as suas Máquinas Virtuais, Aplicações Azure e software como um serviço (SaaS) às necessidades de um cliente individual. Esta opção também permite que os editores forneçam acesso pré-visualizado a novas funcionalidades do produto, antes de as lançarem a todos os clientes.

As ofertas privadas permitem que os editores aproveitem a escala e a disponibilidade global de um mercado público, com a flexibilidade e controlo necessários para negociar e entregar ofertas e configurações personalizadas. As empresas podem agora comprar e vender de forma a esperar 

## <a name="create-private-offers-using-plans"></a>Criar ofertas privadas usando planos

Para *ofertas novas ou existentes com planos,* as editoras podem facilmente criar novas variações privadas, criando novos planos (anteriormente conhecidos como SKUs) e marcando-os como privados. Cada oferta pode ter até 45 planos privados.

<!--- [Private SKUs]() --->

Os planos privados estão disponíveis para os seguintes tipos de oferta:

- Máquina Virtual do Azure
- Aplicação Azure (implementada como modelos de solução ou aplicações geridas)
- Serviço Gerido
- Ofertas de SaaS

Os planos privados são componentes de uma oferta e só são visíveis e purciáveis pelos clientes visados. Os planos privados só são visíveis e puráveis pelos clientes visados.  Os planos privados podem ser disponibilizados aos clientes tanto da Azure Global como do Governo Azure.

Os planos privados podem reutilizar as imagens base e/ou oferecer metadados já publicados para um plano público. Esta opção permite que os editores criem múltiplas variações privadas de uma oferta pública sem terem de publicar várias versões da mesma imagem base e oferecer metadados. Para a Azure Virtual Machine e a aplicação Azure oferece apenas, quando um plano privado partilha uma imagem base com um plano público, quaisquer alterações à imagem base da oferta irão propagar-se em todos os planos públicos e privados usando essa imagem base.

Para *novas ofertas que incluem apenas planos privados,* os editores podem criar as suas ofertas como qualquer outra oferta, e depois marcar os planos como privados. As ofertas que apenas têm planos privados não serão detetáveis ou acessíveis no [portal Azure](https://azure.microsoft.com/features/azure-portal/) por clientes que não estejam associados à oferta.

>[!NOTE]
>Uma oferta que contenha apenas planos privados não será visível no mercado público de Azure ou AppSource.

## <a name="target-customers-with-private-offers"></a>Clientes-alvo com ofertas privadas

Para ofertas privadas novas e existentes, os editores podem direcionar os clientes usando identificadores de subscrição. Para ofertas de Máquina Virtual Azure, Aplicação Azure e Serviço Gerido, os editores podem limitar a disponibilidade de um plano privado a um ID de subscrição individual do Azure ou carregar um CSV de até 10.000 IDs de subscrição Azure. Para ofertas saaS, os editores podem associar um ID de inquilino do Azure Ative Directory para limitar a disponibilidade de um plano privado, usando a abordagem de upload manual ou CSV.

Uma vez que uma oferta tenha sido certificada e publicada, os clientes podem ser atualizados ou removidos do plano utilizando a funcionalidade Sync Private Subscriptions. Esta capacidade permite que os editores atualizem de forma rápida e fácil a lista de clientes aos quais o plano privado é apresentado sem certificar ou publicar novamente a oferta.

## <a name="deploying-private-offers"></a>Implantação de ofertas privadas

Uma vez assinado no portal Azure, os clientes podem seguir estes passos para selecionar as suas ofertas privadas.

1. Faça login [no portal Azure](https://ms.portal.azure.com/).
1. Nos **serviços Azure,** selecione **Criar um recurso**.
1. Na página **Nova,** ao lado do **Azure Marketplace,** selecione **Ver tudo.** A página marketplace aparece.
1. Na navegação à esquerda, selecione **Ofertas Privadas.**

> [!NOTE]
> As ofertas privadas só são detetáveis no [portal Azure.](https://azure.microsoft.com/features/azure-portal/) Não são apresentados no [Microsoft AppSource](https://appsource.microsoft.com/) ou [no Azure Marketplace.](https://azuremarketplace.microsoft.com) Para saber mais sobre a publicação nas diferentes lojas online do mercado comercial, consulte [Introdução às opções de listagem.](./determine-your-listing-type.md)

As ofertas privadas também aparecerão nos resultados da pesquisa e podem ser implementadas através da linha de comando e dos modelos do Azure Resource Manager, como qualquer outra oferta.

![[Ofertas privadas]](./media/marketplace-publishers-guide/private-offer.png)

As ofertas privadas também aparecerão nos resultados da pesquisa. Procura o distintivo **privado.**

>[!Note]
>As ofertas privadas não são suportadas com subscrições estabelecidas através de um revendedor do programa Cloud Solution Provider (CSP).


<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans]() guide.
--->
