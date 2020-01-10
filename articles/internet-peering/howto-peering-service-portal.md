---
title: Habilitar o serviço de emparelhamento em um emparelhamento direto usando o portal
titleSuffix: Azure
description: Habilitar o serviço de emparelhamento em um emparelhamento direto usando o portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d6b67c42ef8a5ba5ae98894775d1f56cee39ba8b
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774981"
---
# <a name="enable-peering-service-on-a-direct-peering-using-the-portal"></a>Habilitar o serviço de emparelhamento em um emparelhamento direto usando o portal

Este artigo descreve como habilitar o [serviço de emparelhamento](overview-peering-service.md) em um emparelhamento direto usando o Portal.

Se preferir, você pode concluir este guia usando o [PowerShell](howto-peering-service-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Examine os [pré-requisitos](prerequisites.md) antes de começar a configuração.
* Escolha um emparelhamento direto em sua assinatura em que você deseja habilitar o serviço de emparelhamento. Se você não tiver um, converta um emparelhamento direto herdado ou crie um novo emparelhamento direto.
    * Para converter um emparelhamento direto herdado, siga as instruções em [converter um emparelhamento direto herdado para o recurso do Azure usando o portal](howto-legacy-direct-portal.md).
    * Para criar um novo emparelhamento direto, siga as instruções em [criar ou modificar um emparelhamento direto usando o portal](howto-direct-portal.md).

## <a name="enable-peering-service-on-a-direct-peering"></a>Habilitar o serviço de emparelhamento em um emparelhamento direto

### <a name= get></a>Exibir emparelhamento direto
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name= get></a>Habilitar o emparelhamento direto para o serviço de emparelhamento

Depois de abrir o emparelhamento direto na etapa anterior, habilite-o para o serviço de emparelhamento.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Modificar uma conexão de emparelhamento direto

Se você precisar modificar as configurações de conexão, consulte a seção **modificar um emparelhamento direto** em [criar ou modificar um emparelhamento direto usando o portal](howto-direct-portal.md)

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar o emparelhamento do Exchange usando o portal](howto-exchange-portal.md)
* [Converter um emparelhamento do Exchange herdado para o recurso do Azure usando o portal](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Recursos adicionais

Para perguntas frequentes, consulte [perguntas frequentes sobre o serviço de emparelhamento](service-faqs.md).