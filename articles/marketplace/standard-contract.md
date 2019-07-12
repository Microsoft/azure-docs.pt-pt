---
title: Contrato Standard | Azure
description: Contrato padrão do Azure Marketplace e AppSource
services: Azure, Marketplace, Compute, Storage, Networking
author: qianw211
ms.service: marketplace
ms.topic: article
ms.date: 07/05/2019
ms.author: ellacroi
ms.openlocfilehash: 80c157423572d356026f257e81d52650ce01d3e8
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620399"
---
# <a name="standard-contract"></a>Contrato Standard

Para simplificar o processo de aprovisionamento para os clientes e reduzir a complexidade legal para fornecedores de software, a Microsoft oferece um modelo de contrato padrão para ajudar a facilitar uma transação no marketplace. Em vez de criar termos e condições personalizados, os publicadores do Azure Marketplace podem optar por oferecer o respetivo software sob o contrato padrão, o que os clientes só têm de verificar e aceitar uma vez. O contrato padrão pode ser encontrado aqui: [ https://go.microsoft.com/fwlink/?linkid=2041178 ](https://go.microsoft.com/fwlink/?linkid=2041178). 

Os termos e condições para uma oferta são definidas na guia Marketplace durante a criação de uma oferta no Portal de parceiros de nuvem. A opção de contrato padrão é ativada ao alterar a definição como Yes.

![Ativar a opção de contrato padrão](media/marketplace-publishers-guide/standard-contract.png)

>[!Note] 
>Se optar por utilizar o contrato padrão, separe os termos e condições ainda são necessárias para o [fornecedor de soluções Cloud](./cloud-solution-providers.md) canal.

## <a name="standard-contract-amendments"></a>Padrão de contrato Amendments

Padrão de contrato de uma permitem que os publicadores selecionar o contrato padrão para manter a simplicidade e com os termos personalizados para o seu produto ou a empresa.  Os clientes só têm de rever os amendments para o contrato, se tiver já revisto e aceite o contrato de padrão da Microsoft.

Existem dois tipos de amendments disponível para editores do Azure Marketplace:

* Amendments universais: Estes amendments são aplicadas universalmente ao contrato de padrão para todos os clientes. Amendments universais são apresentados para todos os clientes do produto no fluxo de compra.

![Amendments universais](media/marketplace-publishers-guide/universal-amendaments.png)

* Amendments personalizados: O Azure Marketplace também tem uma provisão para amendments personalizados direcionadas para os inquilinos. Eles são amendments especiais para o contrato do padrão que são direcionadas para determinados clientes apenas. Os publicadores podem escolher o inquilino que pretende que seja de destino. Os clientes de inquilino seriam comprar o produto em contrato padrão e os destinados amendments.

![Amendments personalizados](media/marketplace-publishers-guide/custom-amendaments.png)

>[!Note] 
>Os clientes segmentados com amendments personalizados também irão receber a emenda universal os termos de padrão durante a compra.

>[!Note]
>Os seguintes tipos de oferta de suporte padrão uma de contrato: Aplicações do Azure (modelos de soluções e aplicações geridas), as máquinas virtuais, contentores, aplicações de contentor.

### <a name="customer-experience"></a>Experiência do cliente

Durante o processo de compra no portal do Azure, os clientes poderão ver os termos associados ao produto como o contrato de padrão da Microsoft e as amendments.

![A experiência do cliente de portal do Azure.](media/marketplace-publishers-guide/ibiza-customer-experience.png)

### <a name="api"></a>API

Os clientes podem utilizar `Get-AzureRmMarketplaceTerms` para obter os termos de uma oferta e aceitá-lo. O contrato de padrão e amendments associados serão devolvidos no resultado do cmdlet.

---
