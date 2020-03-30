---
title: Contrato Standard [ Contrato Padrão] Azure
description: Contrato Padrão para Azure Marketplace e AppSource
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: dsindona
ms.openlocfilehash: 00a83a1b3005043f317ed49cafa735540cd21793
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284964"
---
# <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Contrato Padrão para o mercado comercial da Microsoft

Para simplificar o processo de aquisição para clientes e reduzir a complexidade jurídica dos fornecedores de software, a Microsoft oferece um Contrato Padrão para o mercado comercial da Microsoft, de forma a ajudar a facilitar as transações no mercado. Em vez de elaborar em condições e condições personalizadas, os editores de marketplace comercial podem optar por oferecer o seu software ao abrigo do Contrato Padrão, que os clientes só precisam de vetar e aceitar uma vez. O Contrato Padrão pode [https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178)ser consultado aqui: .

Os termos e condições para uma oferta são definidos na criação da oferta no Partner Center ou no Portal do Parceiro cloud. Pode selecionar para utilizar o Contrato Padrão para o mercado comercial da Microsoft em vez de fornecer os seus próprios termos e condições personalizados.

>[!Note]
>Uma vez publicada uma oferta utilizando o contrato Standard para o mercado comercial da Microsoft, não poderá utilizar os seus próprios termos e condições personalizados. É um cenário de "ou". Ou oferece a sua solução ao abrigo do Contrato Padrão *ou* dos seus próprios termos e condições. Se quiser modificar os termos do Contrato Padrão, pode fazê-lo através de Alterações De Contrato Padrão.

## <a name="standard-contract-amendments"></a>Alterações-padrão do contrato

As Alterações-Padrão do Contrato permitem aos editores selecionar o Contrato Padrão para a simplicidade e com termos personalizados para o seu produto ou negócio. Os clientes só precisam de rever as alterações ao contrato, caso já tenham revisto e aceite o Contrato Padrão da Microsoft.

Existem dois tipos de alterações disponíveis para editores de mercado comercial:

* Alterações Universais: Estas alterações são aplicadas universalmente ao Contrato Padrão para todos os clientes. As alterações universais são mostradas a todos os clientes da oferta no fluxo de compra. Os clientes devem aceitar os termos do Contrato Padrão e a alteração antes de poderem utilizar a sua oferta.

* Alterações Personalizadas: Estas alterações são alterações especiais ao Contrato Padrão que são dirigidas a clientes específicos apenas através de IDs de inquilinos Azure. Os editores podem escolher o inquilino que querem atingir. Apenas os clientes do inquilino serão apresentados com os termos de emenda personalizados no fluxo de compra da oferta.  Os clientes devem aceitar os termos do Contrato Padrão e as alterações antes de poderem utilizar a sua oferta.

>[!Note]
>Estes dois tipos de emendas empilham-se em cima uns dos outros. Os clientes visados com alterações personalizadas também receberão a alteração universal ao Contrato Padrão durante a compra.

Você pode aproveitar o Contrato Padrão para o mercado comercial da Microsoft para os seguintes tipos de oferta: Aplicações Azure (Modelos de Solução e Aplicações Geridas), Máquinas Virtuais, Contentores, Aplicações de Contentores, Módulos De Borda IoT e SaaS .

## <a name="customer-experience"></a>Experiência do cliente

Durante a experiência de descoberta no mercado Azure ou AppSource, os clientes poderão ver os termos associados à oferta como o Contrato Padrão para o mercado comercial da Microsoft e quaisquer alterações universais.

![A experiência de descoberta de clientes do portal Azure.](media/marketplace-publishers-guide/azure-discovery-process.png)

Durante o processo de compra no portal Azure, os clientes poderão ver os termos associados à oferta como o Contrato Padrão para o mercado comercial da Microsoft e quaisquer alterações universais e/ou específicas do arrendatário.

![A experiência de compra de clientes do portal Azure.](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>API

Os clientes podem utilizar o Get-AzureRmMarketplaceTerms para recuperar os termos de uma oferta e aceitá-la. O Contrato-Tipo e as alterações associadas serão devolvidos na produção do cmdlet.
