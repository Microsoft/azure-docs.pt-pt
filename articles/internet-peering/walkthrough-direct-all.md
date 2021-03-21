---
title: Instruções de peering Direto
titleSuffix: Azure
description: Instruções de peering Direto
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 6a1fa7f582511a2089bab2694f9f5ec47634ff54
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97592386"
---
# <a name="direct-peering-walkthrough"></a>Instruções de peering Direto

Esta secção explica os passos que precisa de seguir para configurar e gerir um olhar direto.

## <a name="create-a-direct-peering"></a>Criar um espreitamento direto
> [!div class="mx-imgBorder"]
> ![Estados de trabalho e conexão de observação direta](./media/direct-peering.png)

Devem ser seguidos os seguintes passos para prever um espreguio direto:
1. Reveja [a política de observação da](https://peering.azurewebsites.net/peering) Microsoft para compreender os requisitos para o persimento direto.
1. Siga as instruções em [Criar ou modificar um esprevamento direto](howto-direct-powershell.md) para apresentar um pedido de espreitar.
1. Depois de submeter um pedido de observação, a Microsoft entrará em contacto com o seu endereço de e-mail registado para fornecer LOA (Carta de Autorização) ou para outras informações.
1. Uma vez aprovado o pedido de espremia, o estado de ligação altera-se ao ProvisioningStarted.
1. Tem de:
    1. cablagem completa de acordo com a LOA
    1. (opcionalmente) realizar teste de ligação utilizando 169.254.0.0/16
    1. configurar sessão de BGP e, em seguida, notificar-nos.
1. Microsoft prevê sessão de BGP com política DENY ALL e validar de ponta a ponta.
1. Se for bem sucedido, receberá uma notificação de que o estado de ligação de espreitar é Ativo.
1. O tráfego será então permitido através do novo olhar.

Note que os estados de ligação não devem ser confundidos com estados de sessão [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) padrão.

## <a name="convert-a-legacy-direct-peering-to-azure-resource"></a>Converter um peering Direto legado para o recurso do Azure
Devem ser seguidos os seguintes passos para converter um legado espreitando diretamente para o recurso Azure:
1. Siga as instruções em [Converter um legado Espreitar diretamente para o recurso Azure](howto-legacy-direct-powershell.md)
1. Depois de submeter o pedido de conversão, a Microsoft irá rever o pedido e contactá-lo se necessário.
1. Uma vez aprovado, verá o seu olhar direto com um estado de ligação como Ativo.

## <a name="deprovision-direct-peering"></a>Deprovision Direct peering
Contacte a equipa [de observação da Microsoft](mailto:peering@microsoft.com) para desprovisionar o espreitamento direto.

Quando um espreitamento direto estiver definido para desprovisionamento, você verá o estado de ligação como **PendenteRemove**

> [!NOTE]
> Se executar o cmdlet PowerShell para eliminar o espreitamento direto quando o Estado de Ligação estiver Provisionado Ou ProvisioningCompleted, a operação falhará.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [pré-requisitos para configurar olhando com a Microsoft](prerequisites.md).
