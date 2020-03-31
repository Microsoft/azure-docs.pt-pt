---
title: Ativar o serviço de peering num peering direto usando powerShell
titleSuffix: Azure
description: Ativar o serviço de peering num peering direto usando powerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: ac843fa5440f7ba085d33cd897bcd4a1722f77ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774188"
---
# <a name="enable-peering-service-on-a-direct-peering-using-powershell"></a>Ativar o serviço de peering num peering direto usando powerShell

Este artigo descreve como ativar o [Serviço de Peering](overview-peering-service.md) num peering direto utilizando cmdlets PowerShell e o modelo de implementação do Gestor de Recursos.

Se preferir, pode completar este guia utilizando o [portal](howto-peering-service-portal.md).

## <a name="before-you-begin"></a>Antes de começar
* Reveja [os pré-requisitos](prerequisites.md) antes de iniciar a configuração.
* Escolha um 'peering' direto na sua subscrição, desejaactivar o Serviço de Peering. Se não tiver um, converta um legado Direct peering ou crie um novo olhar Direto.
    * Para converter um legado de espreitar diretamente, siga as instruções em Converter um legado Direto espreitando para o [recurso Azure usando powerShell](howto-legacy-direct-powershell.md).
    * Para criar um novo epeering direto, siga as instruções em [Criar ou modifique um peering direto utilizando powerShell](howto-direct-powershell.md).

### <a name="working-with-azure-powershell"></a>Trabalhar com a Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Ativar o Peering Service num peering Direto

### <a name="view-direct-peering"></a><a name= get></a>Ver espreitar direto
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Ativar o peering direto para o serviço de peering

Depois de obter o peering direto no passo anterior, ative-o para o Serviço de Peering.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Modificar uma ligação de peering direto

Se precisar modificar as definições de ligação, consulte modificar uma secção de **peering direto** em [Criar ou modificar um peering direto usando powerShell](howto-direct-powershell.md)

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar o peering de troca utilizando o PowerShell](howto-exchange-powershell.md)
* [Converter um legado Exchange peering para o recurso Azure usando powerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Recursos adicionais
Pode obter descrições detalhadas de todos os parâmetros executando o seguinte comando:

```powershell
Get-Help Get-AzPeering -detailed
```

Para perguntas frequentes, consulte [o Peering Service FAQ](service-faqs.md).