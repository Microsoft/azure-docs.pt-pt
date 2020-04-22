---
title: Enable Azure Peering Service em um peering direto usando o portal Azure
titleSuffix: Azure
description: Enable Azure Peering Service em um peering direto usando o portal Azure
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: article
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 625a17e5acff00f78c5a19725653eec629936f87
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687064"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-the-azure-portal"></a>Enable Azure Peering Service em um peering direto usando o portal Azure

Este artigo descreve como ativar o [Serviço de Peering](overview-peering-service.md) Azure num desvio direto utilizando o portal Azure.

Se preferir, pode completar este guia utilizando o [PowerShell](howto-peering-service-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Reveja os [pré-requisitos](prerequisites.md) antes de iniciar a configuração.
* Escolha um 'peering' direto na sua subscrição para o qual pretende ativar o Serviço de Peering. Se não tiver um, ou converta um legado Direct peering ou crie um novo olhar direto:
    * Para converter um legado de espreitar diretamente, siga as instruções em [Converter um legado Diretamente espreitando para um recurso Azure utilizando o portal](howto-legacy-direct-portal.md).
    * Para criar um novo epeering direto, siga as instruções em [Criar ou modifique um desvio direto utilizando o portal](howto-direct-portal.md).

## <a name="enable-peering-service-on-a-direct-peering"></a>Ativar o Peering Service num peering Direto

### <a name="view-direct-peering"></a><a name= get></a>Ver espreitar direto
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Ativar o peering direto para o serviço de peering

Depois de abrir um olhar direto no passo anterior, ative-o para o Serviço de Peering.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Modificar uma ligação de peering direto

Para modificar as definições de ligação, consulte a secção "Modificar um peering direto" em [Criar ou modificar um epeering direto utilizando o portal](howto-direct-portal.md).

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar o peering de troca utilizando o portal](howto-exchange-portal.md)
* [Converter um legado Exchange peering para um recurso Azure usando o portal](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Recursos adicionais

Para perguntas frequentes, consulte o [Serviço de Peering FAQ](service-faqs.md).