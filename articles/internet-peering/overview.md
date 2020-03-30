---
title: Configurar o peering com a Microsoft
titleSuffix: Azure
description: Visão geral do olhar
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: overview
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 576bc3e37711851acd7d6c7ac811a10e40080710
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "75908919"
---
# <a name="internet-peering-overview"></a>Visão geral do olho na Internet

Peering é a interligação entre a rede global da Microsoft (AS8075) e a sua rede com o propósito de trocar o tráfego de internet de/para os serviços online da Microsoft e os Serviços Microsoft Azure. As transportadoras ou fornecedores de serviços podem solicitar a ligação com a Microsoft em qualquer uma das nossas localizações edge. Cada pedido é revisto pela Microsoft para garantir que adere à nossa política de peering. Pode configurar um peering com a rede Microsoft de duas formas:

* **Peering direto:**

    O peering é estabelecido sobre ligações físicas diretas entre a rede Microsoft num Microsoft Edge e na sua rede. As sessões de BGP são configuradas através destas ligações de acordo com a nossa política de encaminhamento e usando o acordo pré-negociado. Isto também é referido como PNI.

* **Persque-se:**

    Isto refere-se às ligações de pares públicos padrão na Internet Exchanges (IX). As ligações físicas entre a rede Microsoft e a sua rede são através de tecido comutador operado pelo IX. As sessões de BGP são configuradas utilizando o espaço IP fornecido pelo IX.

## <a name="benefits-of-peering-with-microsoft"></a>Benefícios de espreitar com a Microsoft
* Reduza os seus custos de trânsito, fornecendo tráfego da Microsoft através do peering com a Microsoft.
* Melhore o desempenho dos seus clientes reduzindo o lúpulo da rede e a latência à rede Microsoft Edge.
* Proteja o tráfego do cliente contra falhas na rede ou na rede do fornecedor de trânsito, espreitando com a Microsoft em locais redundantes.
* Aprenda as métricas de desempenho sobre as suas ligações de observação e utilize insights para resolver problemas na sua rede.

## <a name="benefits-of-using-azure-to-set-up-peering"></a>Benefícios de usar o Azure para criar o peering

Pode solicitar o peering com a Microsoft utilizando o Azure PowerShell ou o portal. O peering criado desta forma é gerido como um recurso Azure e proporciona os seguintes benefícios:
* Passos simplificados e autómáticos para configurar e gerir o peering com a Microsoft.
* Forma rápida e fácil de ver e gerir todos os seus pares num só lugar.
* Acompanhe o estado e os dados de largura de banda para todas as suas ligações.
* Pode utilizar a mesma subscrição para aceder aos seus Serviços Azure Cloud.

Se já estabeleceu os pares com a Microsoft, são referidos como **olhares legados.** Você pode optar por gerir tais epeerings como recurso Azure para tirar partido dos benefícios acima referidos. Para submeter um novo pedido de observação, ou converter o legado de olhar para o recurso Azure, siga os links na secção **de passos seguintes** abaixo.

## <a name="peering-policy"></a>Política de peering
A Microsoft tem uma política seletiva, mas geralmente aberta. Os pares são selecionados com base no desempenho, capacidade e quando há benefício mútuo, e estão sujeitos a certos requisitos técnicos, comerciais e legais. Para mais detalhes, consulte [a política de peering](policy.md).

## <a name="faq"></a>FAQ
Para perguntas frequentes sobre o peering, consulte o [peering da Internet - FAQs](faqs.md).

## <a name="next-steps"></a>Passos seguintes

* Para aprender sobre os passos para configurar o direct peering com a Microsoft, siga o [direct peering walkthrough](walkthrough-direct-all.md)
* Para aprender sobre os passos para configurar o Exchange peering com a Microsoft, siga o [exchange peering walkthrough](walkthrough-exchange-all.md)
* Saiba mais sobre algumas das outras principais [capacidades de rede](https://docs.microsoft.com/azure/networking/networking-overview) do Azure.
