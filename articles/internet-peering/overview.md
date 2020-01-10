---
title: Configurar o emparelhamento com a Microsoft
titleSuffix: Azure
description: Visão geral do emparelhamento
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: overview
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: c5fd37c532e2abf2697c741e4dab10a945926e2b
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775692"
---
# <a name="internet-peering-overview"></a>Visão geral de emparelhamento de Internet

O emparelhamento é a interconexão entre a rede global da Microsoft (AS8075) e sua rede com a finalidade de trocar tráfego de Internet de/para Microsoft serviços online e serviços de Microsoft Azure. As operadoras ou os provedores de serviços podem solicitar a conexão com a Microsoft em qualquer um de nossos locais de borda. Cada solicitação é revisada por Microsoft Azure rede para garantir que ela atenda à nossa política de emparelhamento. Você pode configurar um emparelhamento com a rede da Microsoft de duas maneiras:

* **Emparelhamento direto:**

    O emparelhamento é estabelecido em conexões físicas diretas entre a rede da Microsoft em um Microsoft Edge e sua rede. As sessões BGP são configuradas entre essas conexões por nossa política de roteamento e usando o contrato previamente negociado. Isso também é chamado de PNI.

* **Emparelhamento do Exchange:**

    Isso se refere a conexões de emparelhamento público padrão em intercâmbios de Internet (IX). As conexões físicas entre a rede da Microsoft e sua rede são por meio do Switch Fabric operado pela IX. As sessões BGP são configuradas usando o espaço IP fornecido pelo IX.

## <a name="benefits-of-peering-with-microsoft"></a>Benefícios do emparelhamento com a Microsoft
* Reduza os custos de trânsito ao entregar o tráfego da Microsoft usando o emparelhamento com a Microsoft.
* Melhore o desempenho para seus clientes reduzindo os saltos de rede e a latência para a rede Microsoft Edge.
* Proteja o tráfego do cliente contra falhas na rede do seu provedor de rede ou de trânsito, passando o emparelhamento com a Microsoft em locais redundantes.
* Aprenda métricas de desempenho sobre suas conexões de emparelhamento e utilize o insights para solucionar problemas de sua rede.

## <a name="benefits-of-using-azure"></a>Benefícios do uso do Azure

Você pode solicitar o emparelhamento com a Microsoft usando Azure PowerShell ou Portal. O emparelhamento configurado dessa maneira é gerenciado como um recurso do Azure e oferece os seguintes benefícios:
* Etapas simplificadas e automatizadas para configurar e gerenciar o emparelhamento com a Microsoft.
* Maneira rápida e fácil de exibir e gerenciar todos os seus emparelhamentos em um único lugar.
* Acompanhe os dados de status e largura de banda de todas as suas conexões.
* Você pode usar a mesma assinatura para acessar os serviços de nuvem do Azure.

Se você já estabeleceu emparelhamentos com a Microsoft, eles são chamados de **emparelhamentos herdados**. Você pode optar por gerenciar tais emparelhamentos como o recurso do Azure para aproveitar os benefícios acima. Para enviar uma nova solicitação de emparelhamento ou converter o emparelhamento herdado para o recurso do Azure, siga os links na seção **próximas etapas** abaixo.

## <a name="peering-policy"></a>Política de emparelhamento
A Microsoft tem uma política de emparelhamento seletiva, mas geralmente aberta. Os colegas são selecionados com base no desempenho, na capacidade e no local em que há um benefício mútuo e estão sujeitos a determinados requisitos técnicos, comerciais e legais. Para obter detalhes, consulte [política de emparelhamento](policy.md).

## <a name="faq"></a>FAQ
Para perguntas frequentes sobre emparelhamento, consulte [emparelhamento da Internet-perguntas frequentes](faqs.md).

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre as etapas para configurar o emparelhamento direto com a Microsoft, siga [a explicação do emparelhamento direto](walkthrough-direct-all.md)
* Para saber mais sobre as etapas para configurar o emparelhamento do Exchange com a Microsoft, siga [a explicação sobre emparelhamento do Exchange](walkthrough-exchange-all.md)
* Saiba mais sobre algumas das outras principais [capacidades de rede](https://docs.microsoft.com/azure/networking/networking-overview) do Azure.
