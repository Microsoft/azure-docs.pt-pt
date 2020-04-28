---
title: Instruções de peering Direto
titleSuffix: Azure
description: Instruções de peering Direto
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d88fcfc4d3e073bf544f2ca0f4d01dbe305b45da
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75775514"
---
# <a name="direct-peering-walkthrough"></a>Instruções de peering Direto

Esta secção explica os passos que precisa de seguir para configurar e gerir um olhar direto.

## <a name="create-a-direct-peering"></a>Criar um olhar direto
> [!div class="mx-imgBorder"]
> ![Estados de fluxo de trabalho e conexão de peering direto](./media/direct-peering.png)

Devem ser seguidos os seguintes passos, a fim de prever um olhar direto:
1. Reveja a política de [peering da](https://peering.azurewebsites.net/peering) Microsoft para compreender os requisitos para o peering direto.
1. Siga as instruções em [Criar ou modifique um olhar direto](howto-direct-powershell.md) para submeter um pedido de peering.
1. Depois de submeter um pedido de peering, a Microsoft entrará em contacto com o seu endereço de e-mail registado para fornecer LOA (Carta de Autorização) ou para outras informações.
1. Uma vez aprovado o pedido de peering, as alterações do Estado de ligação ao ProvisioningStarted.
1. Precisa:
    1. cablagem completa de acordo com a LOA
    1. (opcionalmente) executar o teste de ligação utilizando 169.254.0.0.0/16
    1. configurar a sessão de BGP e, em seguida, notificar-nos.
1. A Microsoft prevê a sessão de BGP com a política DENY ALL e valida o fim a ponta.
1. Se for bem sucedido, receberá uma notificação de que o estado de ligação de pares está Ativo.
1. O tráfego será então permitido através do novo peering.

Note que os estados de ligação não devem ser confundidos com os estados padrão da sessão [de BGP.](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)

## <a name="convert-a-legacy-direct-peering-to-azure-resource"></a>Converter um peering Direto legado para o recurso do Azure
Devem ser seguidos os seguintes passos para converter um legado direto para o recurso Azure:
1. Siga as instruções em [Converter um legado de espreitar diretamente para](howto-legacy-direct-powershell.md) o recurso Azure
1. Depois de submeter o pedido de conversão, a Microsoft irá rever o pedido e contactá-lo se necessário.
1. Uma vez aprovado, verá o seu Direct peering com um estado de conexão como Ativo.

## <a name="deprovision-direct-peering"></a>Desprovisionamento Deprovisionamento Desvendação Direta
Contacte a equipa [de peering da Microsoft](mailto:peering@microsoft.com) para desprovisionar o peering direto.

Quando um epeering direto estiver definido para a desprovisionamento, você verá o estado de ligação como **Pendente Remove**

> [!NOTE]
> Se executar o cmdlet PowerShell para eliminar o peering direto quando o Estado de Ligação estiver provisioningStarted ou ProvisioningCompletea, a operação falhará.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [os pré-requisitos para configurar o peering com a Microsoft](prerequisites.md).
