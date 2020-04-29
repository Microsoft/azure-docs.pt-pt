---
title: Streaming de alta disponibilidade dos Serviços De Mídia Azure
description: Aprenda a falhar numa conta secundária de Serviços de Media se ocorrer uma falha ou falha no centro de dados regional.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: 1492dd392eabc4331f8e3d4604fb245a89dedff5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78899233"
---
# <a name="media-services-high-availability-streaming"></a>Streaming de alta disponibilidade dos Serviços de Media

Atualmente, a Azure Media Services não fornece falhas instantâneas do serviço se houver uma falha regional no datacenter ou falha de componentes ou serviços dependentes subjacentes. Este artigo dá orientações sobre como construir o streaming de regiões transversais de vídeo a pedido.

## <a name="prerequisites"></a>Pré-requisitos

Rever [Como construir um sistema de codificação inter-regional](media-services-high-availability-encoding.md)

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>Como construir o streaming de regiões transversais de vídeo a pedido 

* O streaming de regiões transversais de vídeo a pedido envolve a duplicação de [Ativos,](assets-concept.md) [Políticas chave](content-key-policy-concept.md) de conteúdo (se utilizados), Políticas [de Streaming](streaming-policy-concept.md)e Localizadores de [Streaming.](streaming-locators-concept.md) 
* Terá de criar as políticas em ambas as regiões e mantê-las atualizadas. 
* Quando criar os localizadores de streaming, irá querer utilizar o mesmo valor de ID do Localizador, o valor de ID do ContentKey e o valor do ContentKey.  
* Se estiver a codificar o conteúdo, é aconselhável codificar o conteúdo na região A e publicá-lo, em seguida, copiar o conteúdo codificado para a região B e publicá-lo usando os mesmos valores da região A.
* Pode utilizar o Traffic Manager nos nomes do anfitrião para a origem e o serviço de entrega de chaves (na configuração dos Media Services isto será um URL de servidor de chaves personalizado).

## <a name="next-steps"></a>Passos seguintes

Fazer Check-out:

* [Tutorial: Codificar um ficheiro remoto com base em URL e transmitir o vídeo](stream-files-dotnet-quickstart.md)
* [amostras de código](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
