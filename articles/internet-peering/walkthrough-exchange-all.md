---
title: Instruções de peering do Exchange
titleSuffix: Azure
description: Instruções de peering do Exchange
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: bb761afa6d8953b441b6c9541c43b73031719494
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775176"
---
# <a name="exchange-peering-walkthrough"></a>Instruções de peering do Exchange

Esta secção explica os passos que precisa de seguir para configurar e gerir um "Exchange peering".

## <a name="create-an-exchange-peering"></a>Criar um persparo de troca
> [!div class="mx-imgBorder"]
> ![Intercâmbio de fluxos de trabalho e de conexão](./media/exchange-peering.png)

Devem ser seguidos os seguintes passos, a fim de prever um "Exchange peering":
1. Reveja a política de [peering da](https://peering.azurewebsites.net/peering) Microsoft para compreender os requisitos para o peering de Exchange.
1. Encontre a microsoft a espreitar a localização e a identificação da facilidade de observação em [PeeringDB](https://www.peeringdb.com/net/694)
1. Solicitar perspreite para um local de observação utilizando as instruções em [Create e modifique um persping o permuta utilizando](howto-exchange-powershell.md) o artigo PowerShell para mais detalhes.
1. Depois de submeter um pedido de peering, a Microsoft irá rever o pedido e contactá-lo se necessário.
1. Uma vez aprovado, ligação do estado altera-se a Aprovado
1. Configure a sessão de BGP no seu final e notifique a Microsoft
1. Providenciaremos uma sessão de BGP com a política DENY ALL e validaremos o fim-a-dia.
1. Se for bem sucedido, receberá uma notificação de que o estado de ligação de pares está Ativo.
1. O tráfego será então permitido através do novo peering.

Note que os estados de ligação não devem ser confundidos com os estados padrão da sessão [de BGP.](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Converter um peering do Exchange legado para o recurso do Azure
Devem ser seguidos os seguintes passos para converter um legado De intercâmbio de olhos ao recurso Azure:
1. Siga as instruções em [Converter um legado Exchange peering para o recurso Azure](howto-legacy-exchange-powershell.md)
1. Depois de submeter o pedido de conversão, a Microsoft irá rever o pedido e contactá-lo se necessário.
1. Uma vez aprovado, verá o seu Exchange peering com o estado de conexão como Ativo.

## <a name="deprovision-exchange-peering"></a>Deprovisionamento Troca de Pares
Contacte [a Microsoft](mailto:peering@microsoft.com) a espreitar para desprovisionar o peering exchange.

Quando um epeering de troca estiver definido para a desprovisionamento, você verá o estado de ligação como **Pendente Remove**

> [!NOTE]
> Se executar o cmdlet PowerShell para eliminar o peering de Troca quando o estado de ligação for ProvisioningStarted ou ProvisioningCompletea, a operação falhará.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [os pré-requisitos para configurar o peering com a Microsoft](prerequisites.md).
