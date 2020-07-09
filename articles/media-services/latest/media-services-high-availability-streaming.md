---
title: Azure Media Services streaming de alta disponibilidade
description: Saiba como falhar numa conta secundária dos Serviços de Comunicação Social se ocorrer uma falha ou falha no datacenter regional.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "78899233"
---
# <a name="media-services-high-availability-streaming"></a>Serviços de Mídia alta disponibilidade streaming

Atualmente, a Azure Media Services não presta falhas instantâneas do serviço se houver uma falha regional do datacenter ou falha de componentes ou serviços dependentes subjacentes. Este artigo dá orientações sobre como construir o streaming de regiões cruzadas vídeo-on-demand.

## <a name="prerequisites"></a>Pré-requisitos

Rever [Como construir um sistema de codificação trans-regional](media-services-high-availability-encoding.md)

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>Como construir streaming de região cruzada vídeo-on-demand 

* O streaming de regiões cruzadas vídeo-on-demand envolve a duplicação de [ativos,](assets-concept.md) [políticas chave de conteúdo](content-key-policy-concept.md) (se utilizadas), políticas de [streaming](streaming-policy-concept.md)e [localizadores de streaming.](streaming-locators-concept.md) 
* Terá de criar as políticas em ambas as regiões e mantê-las atualizadas. 
* Quando criar os localizadores de streaming, irá querer utilizar o mesmo valor de ID do Localizador, o valor de ID ContentKey e o valor ContentKey.  
* Se estiver a codificar o conteúdo, é aconselhável codificar o conteúdo da região A e publicá-lo, em seguida, copiar o conteúdo codificado para a região B e publicá-lo usando os mesmos valores da região A.
* Pode utilizar o Traffic Manager nos nomes dos anfitriões para a origem e o serviço de entrega de chaves (na configuração dos Serviços de Mídia isto será um URL de servidor de chaves personalizado).

## <a name="next-steps"></a>Próximos passos

Fazer Check-out:

* [Tutorial: Codificar um ficheiro remoto com base no URL e transmitir o vídeo](stream-files-dotnet-quickstart.md)
* [amostras de código](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
