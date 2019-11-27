---
title: Contrato Standard | Azure
description: Contrato padrão para o Azure Marketplace e o AppSource
services: Azure, Marketplace, Compute, Storage, Networking
author: ChJenk
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/19/2019
ms.author: v-chjen
ms.openlocfilehash: dc8edf2b6e4a1204e7edd515da9323896049eb13
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228235"
---
# <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Contrato padrão para o Marketplace comercial da Microsoft

Para simplificar o processo de aquisição para clientes e reduzir a complexidade legal para fornecedores de software, a Microsoft oferece um contrato padrão para o Microsoft Commercial Marketplace a fim de ajudar a facilitar as transações no Marketplace. Em vez de criar termos e condições personalizados, os editores de mercado comercial podem optar por oferecer seu software sob o contrato padrão, que os clientes precisam apenas examinaremos e aceitar uma vez. O contrato padrão pode ser encontrado aqui: [https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178).

Os termos e condições de uma oferta são definidos ao criar a oferta no Partner Center ou no Portal do Cloud Partner. Você pode optar por usar o contrato padrão para o Marketplace comercial da Microsoft em vez de fornecer seus próprios termos e condições personalizados.

>[!Note]
>Depois de publicar uma oferta usando o contrato padrão do Microsoft Commercial Marketplace, você não poderá usar seus próprios termos e condições personalizados. É um cenário de "ou". Você pode oferecer sua solução sob o contrato Standard *ou* seus próprios termos e condições. Se você quiser modificar os termos do contrato padrão, poderá fazer isso por meio de emendas de contrato padrão.

## <a name="standard-contract-amendments"></a>Emendas de contrato padrão

As emendas de contrato padrão permitem que os editores selecionem o contrato padrão para simplificar e com termos personalizados para seus produtos ou negócios. Os clientes precisam apenas examinar as emendas ao contrato, caso já tenham revisado e aceito o contrato padrão da Microsoft.

Há dois tipos de emendas disponíveis para editores de mercado comercial:

* Emendas universais: essas emendas são aplicadas universalmente ao contrato padrão para todos os clientes. As emendas universais são mostradas a todos os clientes da oferta no fluxo de compra. Os clientes devem aceitar os termos do contrato padrão e o aditamento antes de poderem usar sua oferta.

* Emendas personalizadas: essas emendas são emendas especiais ao contrato padrão que são direcionados a clientes específicos somente por meio de IDs de locatário do Azure. Os editores podem escolher o locatário que desejam direcionar. Somente os clientes do locatário serão apresentados com os termos personalizados de emenda no fluxo de compra da oferta.  Os clientes devem aceitar os termos do contrato padrão e as emendas antes de poderem usar sua oferta.

>[!Note]
>Esses dois tipos de emendas se empilham um sobre o outro. Os clientes destinados a emendas personalizadas também terão a emenda universal ao contrato padrão durante a compra.

Você pode aproveitar o contrato padrão do Microsoft Commercial Marketplace para os seguintes tipos de oferta: aplicativos do Azure (modelos de solução e aplicativos gerenciados), máquinas virtuais, contêineres, aplicativos de contêiner, módulos de IoT Edge e SaaS .

## <a name="customer-experience"></a>Experiência do cliente

Durante a experiência de descoberta no Azure Marketplace ou no AppSource, os clientes poderão ver os termos associados à oferta como contrato padrão para o Marketplace comercial da Microsoft e quaisquer emendas universais.

![A experiência de descoberta do cliente portal do Azure.](media/marketplace-publishers-guide/azure-discovery-process.png)

Durante o processo de compra no portal do Azure, os clientes poderão ver os termos associados à oferta como o contrato padrão para o Marketplace comercial da Microsoft e quaisquer emendas universais e/ou específicas de locatário.

![A experiência de compra portal do Azure cliente.](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>API

Os clientes podem usar Get-Azurermmarketplacetermsget para recuperar os termos de uma oferta e aceitá-lo. O contrato padrão e as emendas associadas serão retornados na saída do cmdlet.
