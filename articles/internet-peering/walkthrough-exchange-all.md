---
title: Passo a passos de emparelhamento do Exchange
titleSuffix: Azure
description: Passo a passos de emparelhamento do Exchange
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: bb761afa6d8953b441b6c9541c43b73031719494
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775176"
---
# <a name="exchange-peering-walkthrough"></a>Passo a passos de emparelhamento do Exchange

Esta seção explica as etapas que você precisa seguir para configurar e gerenciar um emparelhamento do Exchange.

## <a name="create-an-exchange-peering"></a>Criar um emparelhamento do Exchange
> [!div class="mx-imgBorder"]
> ![o fluxo de trabalho de emparelhamento do Exchange e os Estados de conexão](./media/exchange-peering.png)

As etapas a seguir devem ser seguidas para provisionar um emparelhamento do Exchange:
1. Examine a [política de emparelhamento](https://peering.azurewebsites.net/peering) da Microsoft para entender os requisitos de emparelhamento do Exchange.
1. Localizar o local de emparelhamento da Microsoft e a ID do recurso de emparelhamento em [PeeringDB](https://www.peeringdb.com/net/694)
1. Solicite o emparelhamento do Exchange para um local de emparelhamento usando as instruções no artigo [criar e modificar um emparelhamento do Exchange usando o PowerShell](howto-exchange-powershell.md) para obter mais detalhes.
1. Depois de enviar uma solicitação de emparelhamento, a Microsoft examinará a solicitação e entrará em contato com você, se necessário.
1. Depois de aprovado, o estado da conexão muda para aprovado
1. Configure a sessão BGP no seu fim e notifique a Microsoft
1. Provisionaremos a sessão BGP com a política negar tudo e validaremos de ponta a ponta.
1. Se for bem-sucedido, você receberá uma notificação informando que o estado da conexão de emparelhamento está ativo.
1. O tráfego será permitido por meio do novo emparelhamento.

Observe que os Estados de conexão não devem ser confundidos com os Estados de sessão [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) padrão.

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Converter um emparelhamento do Exchange herdado para o recurso do Azure
As etapas a seguir devem ser seguidas para converter um emparelhamento do Exchange herdado para o recurso do Azure:
1. Siga as instruções em [converter um emparelhamento do Exchange herdado para o recurso do Azure](howto-legacy-exchange-powershell.md)
1. Depois de enviar a solicitação de conversão, a Microsoft examinará a solicitação e entrará em contato com você, se necessário.
1. Depois de aprovado, você verá o emparelhamento do Exchange com o estado de conexão como ativo.

## <a name="deprovision-exchange-peering"></a>Desprovisionar emparelhamento do Exchange
Entre em contato com [o emparelhamento da Microsoft](mailto:peering@microsoft.com) para desprovisionar o emparelhamento do Exchange.

Quando um emparelhamento do Exchange for definido para desprovisionamento, você verá o estado da conexão como **PendingRemove**

> [!NOTE]
> Se você executar o cmdlet do PowerShell para excluir o emparelhamento do Exchange quando o estado da conexão for ProvisioningStarted ou ProvisioningCompleted, a operação falhará.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre os [pré-requisitos para configurar o emparelhamento com a Microsoft](prerequisites.md).
