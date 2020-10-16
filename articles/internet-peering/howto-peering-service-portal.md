---
title: Ativar o Serviço de Observação Azure num espreitamento direto utilizando o portal Azure
titleSuffix: Azure
description: Ativar o Serviço de Observação Azure num espreitamento direto utilizando o portal Azure
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: a52e6038b622c004dc0d133394cd4f53600b2935
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84700047"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-the-azure-portal"></a>Ativar o Serviço de Observação Azure num espreitamento direto utilizando o portal Azure

Este artigo descreve como ativar o [Serviço de Observação Azure](overview-peering-service.md) num espreitamento direto utilizando o portal Azure.

Se preferir, pode completar este guia utilizando [o PowerShell](howto-peering-service-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Reveja os [pré-requisitos](prerequisites.md) antes de iniciar a configuração.
* Escolha um espreitamento direto na sua subscrição para a qual pretende ativar o Serviço de Peering. Se não tiver um, converta um legado espreitando diretamente ou crie um novo olhar direto:
    * Para converter um legado Espreitar diretamente, siga as instruções em [Converter um legado Espreitar diretamente para um recurso Azure utilizando o portal](howto-legacy-direct-portal.md).
    * Para criar um novo espreitamento direto, siga as instruções em [Criar ou modifique um espreitamento direto utilizando o portal](howto-direct-portal.md).

## <a name="enable-peering-service-on-a-direct-peering"></a>Ativar o Peering Service num peering Direto

### <a name="view-direct-peering"></a><a name= get></a>Ver persmo direto
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Ativar o espreitamento direto para o serviço de peering

Depois de abrir um olhar direto no passo anterior, ative-o para o Serviço de Observação.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Modificar uma ligação de persimento direto

Para modificar as definições de ligação, consulte a secção "Modificar um esprevamento direto" em [Criar ou modificar um esprevamento direto utilizando o portal](howto-direct-portal.md).

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar o Persto cambial utilizando o portal](howto-exchange-portal.md)
* [Converter um legado Exchange olhando para um recurso Azure usando o portal](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Recursos adicionais

Para perguntas frequentes, consulte o [Serviço de Observação DE OBSERVAÇÃO .](service-faqs.md)