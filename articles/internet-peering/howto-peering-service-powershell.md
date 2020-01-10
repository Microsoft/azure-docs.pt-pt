---
title: Habilitar o serviço de emparelhamento em um emparelhamento direto usando o PowerShell
titleSuffix: Azure
description: Habilitar o serviço de emparelhamento em um emparelhamento direto usando o PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: ac843fa5440f7ba085d33cd897bcd4a1722f77ac
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774188"
---
# <a name="enable-peering-service-on-a-direct-peering-using-powershell"></a>Habilitar o serviço de emparelhamento em um emparelhamento direto usando o PowerShell

Este artigo descreve como habilitar o [serviço de emparelhamento](overview-peering-service.md) em um emparelhamento direto usando cmdlets do PowerShell e o modelo de implantação do Gerenciador de recursos.

Se preferir, você pode concluir este guia usando o [portal](howto-peering-service-portal.md).

## <a name="before-you-begin"></a>Antes de começar
* Examine os [pré-requisitos](prerequisites.md) antes de começar a configuração.
* Escolha um emparelhamento direto em sua assinatura em que você deseja habilitar o serviço de emparelhamento. Se você não tiver um, converta um emparelhamento direto herdado ou crie um novo emparelhamento direto.
    * Para converter um emparelhamento direto herdado, siga as instruções em [converter um emparelhamento direto herdado para o recurso do Azure usando o PowerShell](howto-legacy-direct-powershell.md).
    * Para criar um novo emparelhamento direto, siga as instruções em [criar ou modificar um emparelhamento direto usando o PowerShell](howto-direct-powershell.md).

### <a name="working-with-azure-powershell"></a>Trabalhar com o Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Habilitar o serviço de emparelhamento em um emparelhamento direto

### <a name= get></a>Exibir emparelhamento direto
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name= get></a>Habilitar o emparelhamento direto para o serviço de emparelhamento

Depois de obter o emparelhamento direto na etapa anterior, habilite-o para o serviço de emparelhamento.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Modificar uma conexão de emparelhamento direto

Se você precisar modificar as configurações de conexão, consulte a seção **modificar um emparelhamento direto** em [criar ou modificar um emparelhamento direto usando o PowerShell](howto-direct-powershell.md)

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar o emparelhamento do Exchange usando o PowerShell](howto-exchange-powershell.md)
* [Converter um emparelhamento do Exchange herdado para o recurso do Azure usando o PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Recursos adicionais
Para obter descrições detalhadas dos parâmetros de executar o seguinte comando:

```powershell
Get-Help Get-AzPeering -detailed
```

Para perguntas frequentes, consulte [perguntas frequentes sobre o serviço de emparelhamento](service-faqs.md).