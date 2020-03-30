---
title: Ativar o Peering Service num peering Direto com o portal
titleSuffix: Azure
description: Ativar o Peering Service num peering Direto com o portal
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: article
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 87a65826a338f4098ae24c33939ea7f9d4475e36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129863"
---
# <a name="enable-peering-service-on-a-direct-peering-using-the-portal"></a>Ativar o Peering Service num peering Direto com o portal

Este artigo descreve como ativar o [Serviço de Peering](overview-peering-service.md) num desvio direto utilizando o portal.

Se preferir, pode completar este guia utilizando o [PowerShell](howto-peering-service-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Reveja [os pré-requisitos](prerequisites.md) antes de iniciar a configuração.
* Escolha um 'peering' direto na sua subscrição, desejaactivar o Serviço de Peering. Se não tiver um, converta um legado Direct peering ou crie um novo olhar Direto.
    * Para converter um legado de espreitar diretamente, siga as instruções em [Converter um legado Direto espreitando para o recurso Azure usando o portal](howto-legacy-direct-portal.md).
    * Para criar um novo epeering direto, siga as instruções em [Criar ou modifique um desvio direto utilizando o portal](howto-direct-portal.md).

## <a name="enable-peering-service-on-a-direct-peering"></a>Ativar o Peering Service num peering Direto

### <a name="view-direct-peering"></a><a name= get></a>Ver espreitar direto
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Ativar o peering direto para o serviço de peering

Depois de abrir o direct peering no passo anterior, ative-o para o Serviço de Peering.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Modificar uma ligação de peering direto

Se precisar modificar as definições de ligação, consulte modificar uma secção de **peering direto** em [Criar ou modificar um peering direto utilizando o portal](howto-direct-portal.md)

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar o peering de troca utilizando o portal](howto-exchange-portal.md)
* [Converter um peering do Exchange legado para o recurso do Azure com o portal](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Recursos adicionais

Para perguntas frequentes, consulte [o Peering Service FAQ](service-faqs.md).