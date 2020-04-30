---
title: Ativar o Serviço de Observação de Pares Azure num desvio direto utilizando o PowerShell
titleSuffix: Azure
description: Ativar o Serviço de Observação de Pares Azure num desvio direto utilizando o PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: c029b822d00ce545e3623a6212421a55b2d1971f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81686977"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-powershell"></a>Ativar o Serviço de Observação de Pares Azure num desvio direto utilizando o PowerShell

Este artigo descreve como ativar o [Serviço de Peering](overview-peering-service.md) Azure num desvio direto utilizando cmdlets PowerShell e o modelo de implementação do Gestor de Recursos Azure.

Se preferir, pode completar este guia utilizando o [portal](howto-peering-service-portal.md)Azure .

## <a name="before-you-begin"></a>Antes de começar
* Reveja os [pré-requisitos](prerequisites.md) antes de iniciar a configuração.
* Escolha um 'peering' direto na sua subscrição para o qual pretende ativar o Serviço de Peering. Se não tiver um, ou converta um legado Direct peering ou crie um novo olhar direto:
    * Para converter um legado de espreitar diretamente, siga as instruções em [Converter um legado Diretamente espreitando para um recurso Azure utilizando powerShell](howto-legacy-direct-powershell.md).
    * Para criar um novo epeering direto, siga as instruções em [Criar ou modifique um epeering direto utilizando powerShell](howto-direct-powershell.md).

### <a name="work-with-azure-powershell"></a>Trabalhar com a Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Ativar o Peering Service num peering Direto

### <a name="view-direct-peering"></a><a name= get></a>Ver espreitar direto
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Ativar o peering direto para o serviço de peering

Depois de obter o olhar direto no passo anterior, ative-o para o Serviço de Peering.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Modificar uma ligação de peering direto

Se precisar modificar as definições de ligação, consulte a secção "Modificar um peering direto" no [Create ou modificar um epeering direto utilizando powerShell](howto-direct-powershell.md).

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar o peering de troca utilizando o PowerShell](howto-exchange-powershell.md)
* [Converter um legado Exchange peering para um recurso Azure usando powerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Recursos adicionais
Pode obter descrições detalhadas de todos os parâmetros executando o seguinte comando:

```powershell
Get-Help Get-AzPeering -detailed
```

Para perguntas frequentes, consulte o [Serviço de Peering FAQ](service-faqs.md).