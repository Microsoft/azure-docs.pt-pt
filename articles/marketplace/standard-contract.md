---
title: Contrato Standard | Azure
description: Contrato padrão no Azure Marketplace e no AppSource
services: Azure, Marketplace, Compute, Storage, Networking
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/05/2019
ms.author: ellacroi
ms.openlocfilehash: 17c1bf9d20b6f2e3ec450ff7bfb54fe61494ff09
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819498"
---
# <a name="standard-contract"></a>Contrato Standard

Para simplificar o processo de aquisição para clientes e reduzir a complexidade legal para fornecedores de software, a Microsoft oferece um modelo de contrato padrão para ajudar a facilitar uma transação no Marketplace. Em vez de criar termos e condições personalizados, os editores do Azure Marketplace podem optar por oferecer seu software sob o contrato padrão, que os clientes precisam apenas examinaremos e aceitar uma vez. O contrato padrão pode ser encontrado aqui: [https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178). 

Os termos e condições de uma oferta são definidos na guia Marketplace ao criar uma oferta no Portal do Cloud Partner. A opção de contrato padrão é habilitada alterando a configuração para Sim.

![Habilitando opção de contrato padrão](media/marketplace-publishers-guide/standard-contract.png)

>[!Note] 
>Se você optar por usar o contrato padrão, os termos e condições separados ainda serão necessários para o canal do [provedor de soluções na nuvem](./cloud-solution-providers.md) .

## <a name="standard-contract-amendments"></a>Emendas de contrato padrão

As emendas de contrato padrão permitem que os editores selecionem o contrato padrão para simplificar e com termos personalizados para seus produtos ou negócios.  Os clientes precisam apenas examinar as emendas ao contrato, caso já tenham revisado e aceito o contrato padrão da Microsoft.

Há dois tipos de emendas disponíveis para Publicadores do Azure Marketplace:

* Emendas universais: essas emendas são aplicadas universalmente ao contrato padrão para todos os clientes. As emendas universais são mostradas a todos os clientes do produto no fluxo de compra.

![Emendas universais](media/marketplace-publishers-guide/universal-amendaments.png)

* Emendas personalizadas: o Azure Marketplace também tem um provisionamento para emendas personalizadas direcionadas a locatários. Eles são emendas especiais ao contrato padrão que são destinados a determinados clientes apenas. Os editores podem escolher o locatário que desejam direcionar. Os clientes desse locatário comprarão o produto sob contrato padrão e as emendas de destino.

![Emendas personalizadas](media/marketplace-publishers-guide/custom-amendaments.png)

>[!Note] 
>Clientes direcionados a emendas personalizadas também obterão a emenda universal aos termos padrão durante a compra.

>[!Note]
>Os seguintes tipos de oferta dão suporte a emendas de contrato padrão: aplicativos do Azure (modelos de solução e aplicativos gerenciados), máquinas virtuais, contêineres, aplicativos de contêiner.

### <a name="customer-experience"></a>Experiência do cliente

Durante o processo de compra no portal do Azure, os clientes poderão ver os termos associados ao produto como contrato padrão da Microsoft e as emendas.

![O portal do Azure experiência do cliente.](media/marketplace-publishers-guide/ibiza-customer-experience.png)

### <a name="api"></a>API

Os clientes podem usar `Get-AzureRmMarketplaceTerms` para recuperar os termos de uma oferta e aceitá-la. O contrato padrão e as emendas associadas serão retornados na saída do cmdlet.

---
