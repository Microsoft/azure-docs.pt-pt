---
title: Configurar olhando com a Microsoft
titleSuffix: Azure
description: Visão geral do persimento
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: overview
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 8f960e58d47963cbb239d498af52e7adfc77caa9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97586871"
---
# <a name="internet-peering-overview"></a>Visão geral do olho da Internet

Peering é a interligação entre a rede global da Microsoft (AS8075) e a sua rede com o objetivo de trocar o tráfego de internet de/para os serviços online da Microsoft e os Serviços Microsoft Azure. As transportadoras ou fornecedores de serviços podem solicitar a ligação com a Microsoft em qualquer uma das nossas localizações edge. Cada pedido é revisto pela Microsoft para garantir que adere à nossa política de observação. Pode configurar um espreitamento com a rede Microsoft de duas formas:

* **Observação direta:**

    O peering é estabelecido sobre ligações físicas diretas entre a rede Microsoft num Microsoft Edge e a sua rede. As sessões de BGP são configuradas através destas ligações de acordo com a nossa política de encaminhamento e utilizando um acordo pré-negociado. Isto também é referido como PNI.

* **Permutando olhando:**

    Isto refere-se às ligações públicas padrão de observação nas Bolsas de Internet (IX). As ligações físicas entre a rede Microsoft e a sua rede são operadas através do tecido switch operado pelo IX. As sessões de BGP são configuradas utilizando o espaço IP fornecido pelo IX.

## <a name="benefits-of-peering-with-microsoft"></a>Benefícios de espreitar com a Microsoft
* Reduza os seus custos de trânsito através da entrega do tráfego da Microsoft utilizando o seu olhar com a Microsoft.
* Melhore o desempenho dos seus clientes reduzindo o lúpulo de rede e a latência para a rede Microsoft Edge.
* Proteja o tráfego do cliente contra falhas na rede da sua rede ou rede de fornecedores de trânsito, observando a Microsoft em locais redundantes.
* Aprenda métricas de desempenho sobre as suas ligações de observação e utilize insights para resolver problemas na sua rede.

## <a name="benefits-of-using-azure-to-set-up-peering"></a>Benefícios da utilização do Azure para criar o seu espremia

Pode solicitar um espreitamento com a Microsoft utilizando o Azure PowerShell ou o portal. O peering criado desta forma é gerido como um recurso Azure e proporciona os seguintes benefícios:
* Passos simplificados e automáveis para configurar e gerir o espreitamento com a Microsoft.
* Maneira rápida e fácil de ver e gerir todos os seus seus olhares num só lugar.
* Rastrear o estado e os dados de largura de banda para todas as suas ligações.
* Pode utilizar a mesma subscrição para aceder aos seus Serviços Azure Cloud.

Se já estabeleceu os seus pares com a Microsoft, são referidos como **olhares legados**. Pode optar por gerir os seus conhecimentos como o recurso Azure para tirar partido dos benefícios acima referidos. Para submeter um novo pedido de espreitar, ou converter o legado que espreita para o recurso Azure, siga os links na secção **etapas seguintes** abaixo.

## <a name="peering-policy"></a>Política de peering
A Microsoft tem uma política de observação seletiva, mas geralmente aberta. Os pares são selecionados com base no desempenho, capacidade e onde há benefício mútuo, e estão sujeitos a determinados requisitos técnicos, comerciais e legais. Para mais detalhes, consulte [a política de observação.](policy.md)

## <a name="faq"></a>FAQ
Para perguntas frequentes sobre espreitar, consulte [olhando para a Internet - PERGUNTAS Frequentes](faqs.md).

## <a name="next-steps"></a>Passos seguintes

* Para aprender sobre os passos para configurar o persigo direto com a Microsoft, siga [o Walkthrough de observação direto](walkthrough-direct-all.md)
* Para aprender sobre os passos para configurar o Exchange peering com a Microsoft, siga [o Exchange peering walkthrough](walkthrough-exchange-all.md)
* Saiba mais sobre algumas das outras principais [capacidades de rede](../networking/networking-overview.md) do Azure.