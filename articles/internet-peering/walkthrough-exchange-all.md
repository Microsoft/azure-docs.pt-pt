---
title: Instruções de peering do Exchange
titleSuffix: Azure
description: Instruções de peering do Exchange
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 97430d86cba1107ef67264a181925d2a4d7c12c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97586827"
---
# <a name="exchange-peering-walkthrough"></a>Instruções de peering do Exchange

Esta secção explica os passos que precisa de seguir para configurar e gerir um perscruto de Troca.

## <a name="create-an-exchange-peering"></a>Criar um perscruta de intercâmbio
> [!div class="mx-imgBorder"]
> ![Troca de trabalho e estados de conexão](./media/exchange-peering.png)

Devem ser seguidas as seguintes etapas para prever um persto cambial:
1. Reveja [a política de observação da](https://peering.azurewebsites.net/peering) Microsoft para compreender os requisitos para o persto de Exchange.
1. Encontre a localização e o id de instalações de esprevação da Microsoft em [PeeringDB](https://www.peeringdb.com/net/694)
1. Solicite a Troca de Um local de observação utilizando as instruções em [Criar e modifique um peering exchange usando](howto-exchange-powershell.md) o artigo PowerShell para mais detalhes.
1. Depois de submeter um pedido de espreitar, a Microsoft irá rever o pedido e contactá-lo se necessário.
1. Uma vez aprovado, alterações de estado de ligação a Aprovadas
1. Configure a sessão de BGP no seu final e notifique a Microsoft
1. Providenciaremos a sessão de BGP com a política DENY ALL e validaremos de ponta a ponta.
1. Se for bem sucedido, receberá uma notificação de que o estado de ligação de espreitar é Ativo.
1. O tráfego será então permitido através do novo olhar.

Note que os estados de ligação não devem ser confundidos com estados de sessão [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) padrão.

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Converter um peering do Exchange legado para o recurso do Azure
Devem ser seguidos os seguintes passos para converter um legado Exchange peering para recurso Azure:
1. Siga as instruções em [Converter um legado Trocar olhando para o recurso Azure](howto-legacy-exchange-powershell.md)
1. Depois de submeter o pedido de conversão, a Microsoft irá rever o pedido e contactá-lo se necessário.
1. Uma vez aprovado, verá a sua Troca a espreitar com o estado de ligação como Ativo.

## <a name="deprovision-exchange-peering"></a>Deprovision Exchange peering
Contacte [a Microsoft a espreitar](mailto:peering@microsoft.com) para desprovisionar o peering da Exchange.

Quando um perscruto de Troca estiver definido para desprovisionamento, você verá o estado de ligação como **PendenteRemove**

> [!NOTE]
> Se executar o cmdlet PowerShell para apagar o persto de Troca quando o estado de ligação estiver ProvisioningStarted ou ProvisioningCompleted, a operação falhará.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [pré-requisitos para configurar olhando com a Microsoft](prerequisites.md).
