---
title: Contrato Padrão para o mercado comercial da Microsoft
description: Contrato Padrão para Azure Marketplace e AppSource no Partner Center
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 05/20/2020
ms.openlocfilehash: 20a257bde6022249fd7b2ab875b94f356234b490
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94488881"
---
# <a name="standard-contract-for-microsoft-commercial-marketplace"></a>Contrato Padrão para o mercado comercial da Microsoft

A Microsoft oferece um Contrato Padrão para o mercado comercial da Microsoft. Isto ajuda a simplificar o processo de aquisição para os clientes, reduzir a complexidade legal dos fornecedores de software e facilitar as transações no mercado. Em vez de criar termos e condições personalizados, como editor de marketplace comercial, pode optar por oferecer o seu software ao abrigo do [Contrato Padrão](https://go.microsoft.com/fwlink/?linkid=2041178), que os clientes apenas precisam de vetar e aceitar uma vez.

Os termos e condições para uma oferta são definidos ao criar a oferta no Partner Center. Pode selecionar para utilizar o Contrato Padrão para o mercado comercial da Microsoft em vez de fornecer os seus próprios termos e condições personalizados.

>[!Note]
>Assim que publicar uma oferta utilizando o contrato Standard para o mercado comercial da Microsoft, não poderá utilizar os seus próprios termos e condições personalizados. Ou oferece a sua solução nos termos *e* condições padrão. Os termos e condições personalizados são definidos ao nível da oferta e aplicam-se a todos os planos; escreva os seus termos e condições **personalizados** na página Propriedades da sua oferta no Partner Center. Se quiser modificar os termos do Contrato Padrão, pode fazê-lo através de Alterações Padrão contratuais.

## <a name="standard-contract-amendments"></a>Alterações padrão do contrato

As Alterações Padrão do Contrato permitem aos editores selecionar o Contrato Padrão para a simplicidade, e com termos personalizados para o seu produto ou negócio. Os clientes só precisam de rever as alterações ao contrato, caso já tenham revisto e aceite o Contrato Padrão da Microsoft.

Existem dois tipos de alterações disponíveis para os editores de mercado comercial:

* Alterações universais: Estas alterações são aplicadas universalmente ao Contrato-Tipo para todos os clientes. As alterações universais são mostradas a todos os clientes da oferta no fluxo de compra. Os clientes devem aceitar os termos do Contrato Padrão e a alteração antes de poderem utilizar a sua oferta.

* Alterações personalizadas: Estas alterações são alterações especiais ao Contrato Padrão que são dirigidas a clientes específicos apenas através de IDs de inquilinos Azure. Os editores podem escolher o inquilino que querem atingir. Apenas os clientes do arrendatário serão apresentados com os termos de alteração personalizados no fluxo de compra da oferta.  Os clientes devem aceitar os termos do Contrato Padrão e as alterações antes de poderem utilizar a sua oferta.

>[!Note]
>Estes dois tipos de emendas empilham-se em cima uns dos outros. Os clientes visados com alterações personalizadas também receberão a alteração universal ao Contrato Padrão durante a compra. As alterações são limitadas a 4000 caracteres, incluindo espaços.

Pode aproveitar o Contrato Padrão para o mercado comercial da Microsoft para os seguintes tipos de oferta: Aplicações Azure (Modelos de Solução e Aplicações Geridas), Máquinas Virtuais e SaaS.

## <a name="customer-experience"></a>Experiência do cliente

Durante a experiência de descoberta no Azure Marketplace ou AppSource, os clientes poderão ver os termos associados à oferta como o Contrato Padrão para o mercado comercial da Microsoft e quaisquer alterações universais.

![A experiência de descoberta do cliente do portal Azure.](media/marketplace-publishers-guide/azure-discovery-process.png)

Durante o processo de compra no portal Azure, os clientes poderão ver os termos associados à oferta como o Contrato Padrão para o mercado comercial da Microsoft e quaisquer alterações universais e/ou específicas do arrendatário.

![A experiência de compra do cliente do portal Azure.](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>API

Os clientes podem usar Get-AzureRmMarketplaceTerms para recuperar os termos de uma oferta e aceitá-la. O Contrato-Tipo e as alterações associadas serão devolvidos na saída do CMDLET.
