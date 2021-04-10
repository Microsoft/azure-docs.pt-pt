---
title: Disponibilidade regional da Azure Media Services | Microsoft Docs
description: Este artigo é uma visão geral das funcionalidades do Microsoft Azure Media Services e disponibilidade regional do serviço.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: references_regions
ms.openlocfilehash: 125f2be205760618ab6ffa3175f719f5e91e8c05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103012263"
---
# <a name="media-services-regional-availability"></a>Disponibilidade regional dos Serviços de Mídia

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. Confira a versão mais recente, [Media Services v3](../latest/media-services-overview.md). Além disso, consulte [a orientação de migração de v2 para v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Os Serviços de Multimédia do Microsoft Azure (AMS) permitem-lhe carregar, armazenar, codificar e empacotar de forma segura conteúdos de vídeo ou áudio, para a entrega de transmissões em fluxo, tanto a pedido, como em direto, para vários clientes (por exemplo, TV, PC e dispositivos móveis).

A AMS opera em várias regiões do mundo, dando-lhe flexibilidade na escolha de onde construir as suas aplicações. Este artigo é uma visão geral das funcionalidades do Microsoft Azure Media Services e disponibilidade regional do serviço.

Para mais informações sobre toda a infraestrutura global do Azure, consulte [geografias Azure.](https://azure.microsoft.com/global-infrastructure/geographies/)

## <a name="ams-accounts"></a>Contas do AMS

Utilize [produtos Azure por Região](https://azure.microsoft.com/global-infrastructure/services/?products=media-services&regions=all) para determinar se os Serviços de Comunicação Social estão disponíveis numa região específica.

## <a name="streaming-endpoints"></a>Pontos finais de transmissões em fluxo

Os clientes dos Serviços de Multimédia podem escolher um ponto final de transmissão em fluxo **Standard** ou **Premium**.

|Name|Estado|Region
|---|---|---|
|Standard|GA|Todos|
|Premium|GA|Todos|

## <a name="live-encoding"></a>Live Encoding

Disponível em todas as regiões, exceto Alemanha, Brasil Sul, Índia Ocidental, Índia Sul e Índia Central.

## <a name="encoding-media-processors"></a>Processadores de multimédia de codificação

O AMS oferece dois codificadores a pedido, o **Media Encoder Standard** e o **Media Encoder Premium Workflow**. Para obter mais informações, veja [Overview and comparison of Azure on-demand media encoders](media-services-encode-asset.md) (Descrição geral e comparação dos codificadores de multimédia a pedido).

|Nome do processador de mulitmédia|Estado|Regiões
|---|---|---|
|Media Encoder Standard|GA|Todos|
|Fluxo de Trabalho Premium de Codificador de Multimédia|GA|Tudo, exceto a China|

## <a name="analytics-media-processors"></a>Processadores de multimédia de análise

A Análise de Multimédia é um conjunto de componentes de voz e visão que facilitam a derivação de conhecimentos acionáveis por parte das organizações e empresas dos respetivos ficheiros de vídeo. Para obter mais informações, consulte [Descrição Geral da Análise dos Media Services do Azure](./legacy-components.md).

> [!NOTE]
> Alguns processadores de mídia analíticos serão reformados. Para as datas de aposentadoria, consulte o tema dos [componentes do legado.](legacy-components.md)

|Nome do processador de mulitmédia|Estado|Region
|---|---|---|
|Azure Media Face Detector|Pré-visualizar|Todos|
|Azure Media Indexer|GA|Todos|
|Azure Media Motion Detector|Pré-visualizar|Todos|
|Azure Media OCR|Pré-visualizar|Todos|
|Azure Media Redactor|GA|Todos|
|Azure Media Video Thumbnails|Pré-visualizar|Todos|

## <a name="protection"></a>Proteção

Os Serviços de Multimédia do Microsoft Azure permite-lhe proteger os seus elementos multimédia desde o momento em que saem do seu computador para armazenamento, processamento e entrega. Para obter mais informações, veja [Protecting AMS content](media-services-content-protection-overview.md) (Proteger conteúdos do AMS).

|Encriptação|Estado|Regiões|
|---|---|---| 
|Armazenamento|GA|Todos|
|Chaves AES-128|GA|Todos|
|Fairplay|GA|Todos|
|PlayReady|GA|Todos|
|Widevine|GA|Todos exceto a Alemanha, o Governo Federal e a China.

> [!NOTE]
> Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="reserved-units-rus"></a>Unidades reservadas (RUs)

O número de unidades reservadas aprovisionadas determina o número de tarefas de multimédia que podem ser processadas em simultâneo numa determinada conta.

Disponível em todas as regiões.

## <a name="reserved-unit-ru-type"></a>Tipo de unidade reservada (RU)

Uma conta de Serviços de Comunicação está associada a um tipo de unidade reservada que determina a velocidade com que as suas tarefas de processamento de mídia estão concluídas. Pode escolher entre os seguintes tipos de unidade reservada: S1, S2 ou S3.

|Nome do tipo do RU|Estado|Regiões
|---|---|---|
|S1|GA|Todos|
|S2|GA|Todos, exceto Sul do Brasil e Oeste da Índia|
|S3|GA|Todos, exceto Oeste da Índia|

## <a name="next-steps"></a>Passos seguintes

[Migrar para os Serviços de Multimédia v3](../latest/media-services-overview.md)

## <a name="provide-feedback"></a>Enviar comentários

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]