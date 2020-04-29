---
title: Converter um legado Exchange peering para um recurso Azure usando o portal Azure
titleSuffix: Azure
description: Converter um legado Exchange peering para um recurso Azure usando o portal Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 87a7a6bca608f1748d3b659eabdc3e941b537377
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678565"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Converter um legado Exchange peering para um recurso Azure usando o portal Azure

Este artigo descreve como converter um legado existente Exchange espreitando para um recurso Azure usando o portal Azure.

Se preferir, pode completar este guia utilizando o [PowerShell](howto-legacy-exchange-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Reveja os [pré-requisitos](prerequisites.md) e o ["Exchange peering walkthrough"](walkthrough-exchange-all.md) antes de iniciar a configuração.

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>Converter um legado Exchange peering para um recurso Azure

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Inscreva-se no portal e selecione a sua subscrição
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-legacy-exchange-peering"></a><a name=create></a>Converter o legado Exchange peering

Pode converter ligações de observação de legados utilizando o recurso **Peering.**

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Lançar o recurso e configurar as definições básicas
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configurar ligações e submeter
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name="verify-exchange-peering"></a><a name=get></a>Verificar o peering de troca
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>Recursos adicionais

Para mais informações, consulte [as FAQs de observação da Internet](faqs.md).

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar um persparo de troca utilizando o portal](howto-exchange-portal.md)
