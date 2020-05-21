---
title: Contrato Padrão para o mercado comercial da Microsoft
description: Contrato Padrão para Azure Marketplace e AppSource no Partner Center
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: dsindona
ms.openlocfilehash: 53a22140fe62ce0db18c1d02d816eb31b12a381b
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714156"
---
# <a name="standard-contract-for-microsoft-commercial-marketplace"></a>Contrato Padrão para o mercado comercial da Microsoft

A Microsoft oferece um Contrato Padrão para o mercado comercial da Microsoft. Isto ajuda a simplificar o processo de aquisição para os clientes, a reduzir a complexidade jurídica para os fornecedores de software e a facilitar as transações no mercado. Em vez de elaborar termos e condições personalizados, como editor de marketplace comercial, pode optar por oferecer o seu software ao abrigo do [Contrato Padrão](https://go.microsoft.com/fwlink/?linkid=2041178), que os clientes só precisam de vetar e aceitar uma vez.

Os termos e condições para uma oferta são definidos na criação da oferta no Partner Center. Pode selecionar para utilizar o Contrato Padrão para o mercado comercial da Microsoft em vez de fornecer os seus próprios termos e condições personalizados.

>[!Note]
>Uma vez publicada uma oferta utilizando o contrato Standard para o mercado comercial da Microsoft, não poderá utilizar os seus próprios termos e condições personalizados. É um cenário de "ou". Ou oferece a sua solução ao abrigo do Contrato Padrão *ou* dos seus próprios termos e condições. Se quiser modificar os termos do Contrato Padrão, pode fazê-lo através de Alterações De Contrato Padrão.

## <a name="standard-contract-amendments"></a>Alterações-padrão do contrato

As Alterações-Padrão do Contrato permitem aos editores selecionar o Contrato Padrão para a simplicidade e com termos personalizados para o seu produto ou negócio. Os clientes só precisam de rever as alterações ao contrato, caso já tenham revisto e aceite o Contrato Padrão da Microsoft.

Existem dois tipos de alterações disponíveis para editores de mercado comercial:

* Alterações Universais: Estas alterações são aplicadas universalmente ao Contrato Padrão para todos os clientes. As alterações universais são mostradas a todos os clientes da oferta no fluxo de compra. Os clientes devem aceitar os termos do Contrato Padrão e a alteração antes de poderem utilizar a sua oferta.

* Alterações Personalizadas: Estas alterações são alterações especiais ao Contrato Padrão que são dirigidas a clientes específicos apenas através de IDs de inquilinos Azure. Os editores podem escolher o inquilino que querem atingir. Apenas os clientes do inquilino serão apresentados com os termos de emenda personalizados no fluxo de compra da oferta.  Os clientes devem aceitar os termos do Contrato Padrão e as alterações antes de poderem utilizar a sua oferta.

>[!Note]
>Estes dois tipos de emendas empilham-se em cima uns dos outros. Os clientes visados com alterações personalizadas também receberão a alteração universal ao Contrato Padrão durante a compra.

Pode aproveitar o Contrato Padrão para o mercado comercial da Microsoft para os seguintes tipos de oferta: Aplicações Azure (Modelos de Solução e Aplicações Geridas), Máquinas Virtuais e SaaS.

## <a name="customer-experience"></a>Experiência do cliente

Durante a experiência de descoberta no Azure Marketplace ou appSource, os clientes poderão ver os termos associados à oferta como o Contrato Padrão para o mercado comercial da Microsoft e quaisquer alterações universais.

![A experiência de descoberta de clientes do portal Azure.](media/marketplace-publishers-guide/azure-discovery-process.png)

Durante o processo de compra no portal Azure, os clientes poderão ver os termos associados à oferta como o Contrato Padrão para o mercado comercial da Microsoft e quaisquer alterações universais e/ou específicas do arrendatário.

![A experiência de compra de clientes do portal Azure.](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>API

Os clientes podem utilizar o Get-AzureRmMarketplaceTerms para recuperar os termos de uma oferta e aceitá-la. O Contrato-Tipo e as alterações associadas serão devolvidos na produção do cmdlet.
