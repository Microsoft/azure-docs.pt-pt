---
title: Design híbrido dos subsistemas DRM utilizando a Azure Media Services | Microsoft Docs
description: Este tópico discute o design híbrido dos subsistemas DRM utilizando os Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: willzhan
ms.reviewer: juliako
ms.openlocfilehash: a48e761c4fb74802c6b1db63884ee192446720a4
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103016281"
---
# <a name="hybrid-design-of-drm-subsystems"></a>Design híbrido dos subsistemas DRM

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Este tópico discute o design híbrido dos subsistemas DRM utilizando os Azure Media Services.

## <a name="overview"></a>Descrição Geral

A Azure Media Services presta suporte para os seguintes três sistemas DRM:

* PlayReady
* Widevine (Modular)
* FairPlay

O suporte drm inclui encriptação DRM (encriptação dinâmica) e entrega de licença, com o Azure Media Player a suportar todos os 3 DRMs como um leitor de navegador SDK.

Para um tratamento detalhado da conceção e implementação do subsistema DRM/CENC, consulte o documento intitulado [CENC com Multi-DRM e Controlo de Acesso.](media-services-cenc-with-multidrm-access-control.md)

Embora ofereçamos suporte completo para três sistemas DRM, por vezes os clientes precisam de utilizar várias partes da sua própria infraestrutura/subsistemas, além da Azure Media Services para construir um subsistema híbrido de DRM.

Abaixo estão algumas perguntas comuns feitas pelos clientes:

* "Posso usar os meus próprios servidores de licenças DRM?" (Neste caso, os clientes investiram na quinta de servidores de licenças DRM com lógica de negócio incorporada).
* "Posso usar apenas a sua entrega de licenças DRM na Azure Media Services sem hospedar conteúdo na AMS?"

## <a name="modularity-of-the-ams-drm-platform"></a>Modularidade da plataforma AMS DRM

Como parte de uma plataforma de vídeo em nuvem abrangente, a Azure Media Services DRM tem um design com flexibilidade e modularidade em mente. Pode utilizar o Azure Media Services com qualquer uma das seguintes combinações descritas na tabela abaixo (segue-se uma explicação da notação utilizada na tabela). 

|**Conteúdo que hospeda & origem**|**Encriptação de conteúdo**|**Entrega de licença DRM**|
|---|---|---|
|AMS|AMS|AMS|
|AMS|AMS|Terceiros|
|AMS|Terceiros|AMS|
|AMS|Terceiros|Terceiros|
|Terceiros|Terceiros|AMS|

### <a name="content-hosting--origin"></a>Conteúdo que hospeda & origem

* AMS: o ativo de vídeo é hospedado na AMS e o streaming é através de pontos finais de streaming AMS (mas não necessariamente de embalagem dinâmica).
* Terceiros: o vídeo é hospedado e entregue numa plataforma de streaming de terceiros fora da AMS.

### <a name="content-encryption"></a>Encriptação de conteúdo

* AMS: a encriptação de conteúdo é executada dinamicamente/a pedido pela encriptação dinâmica AMS.
* Terceiros: a encriptação de conteúdo é executada fora da AMS utilizando um fluxo de trabalho pré-processamento.

### <a name="drm-license-delivery"></a>Entrega de licença de DRM

* AMS: A licença DRM é entregue pelo serviço de entrega de licenças AMS.
* Terceiros: A licença drm é entregue por um servidor de licenças DRM de terceiros fora da AMS.

## <a name="configure-based-on-your-hybrid-scenario"></a>Configurar com base no seu cenário híbrido

### <a name="content-key"></a>Chave de conteúdo

Através da configuração de uma chave de conteúdo, pode controlar os seguintes atributos tanto da encriptação dinâmica AMS como do serviço de entrega de licenças AMS:

* A chave de conteúdo utilizada para encriptação drm dinâmica.
* Conteúdo da licença DRM a ser entregue por serviços de entrega de licenças: direitos, chave de conteúdo e restrições.
* Tipo de **restrição de política** de autorização de chave de conteúdo: restrição aberta, IP ou token.
* Se for utilizada **a** **restrição** da política de autorização de autorização de dados do tipo simbólico, a **restrição da política de autorização de autorização de conteúdo** deve ser cumprida antes de ser emitida uma licença.

### <a name="asset-delivery-policy"></a>Política de entrega de ativos

Através da configuração de uma política de entrega de ativos, pode controlar os seguintes atributos utilizados pelo embalador dinâmico AMS e encriptação dinâmica de um ponto final de streaming AMS:

* Protocolo de streaming e combinação de encriptação DRM, como DASH em CENC (PlayReady e Widevine), streaming suave em PlayReady, HLS em Widevine ou PlayReady.
* Os URLs de entrega de licença predefinidos/incorporados para cada um dos DRMs envolvidos.
* Se os URLs de aquisição de licença (LA_URLs) na lista de reprodução DE MPD ou HLS contêm cadeia de consulta de ID chave (KID) para Widevine e FairPlay, respectivamente.

## <a name="scenarios-and-samples"></a>Cenários e amostras

Com base nas explicações na secção anterior, os seguintes cinco cenários híbridos utilizam as - respetivas combinações de configuração da política de **entrega de ativos** (as amostras mencionadas na última coluna seguem a tabela):

|**Conteúdo que hospeda & origem**|**Encriptação DRM**|**Entrega de licença DRM**|**Chave de conteúdo de configuração**|**Configure a política de entrega de ativos**|**Sample**|
|---|---|---|---|---|---|
|AMS|AMS|AMS|Yes|Yes|Exemplo 1|
|AMS|AMS|Terceiros|Yes|Yes|Exemplo 2|
|AMS|Terceiros|AMS|Yes|No|Exemplo 3|
|AMS|Terceiros|Lá fora|No|No|Exemplo 4|
|Terceiros|Terceiros|AMS|Yes|No|    

Nas amostras, a proteção PlayReady funciona tanto para o DASH como para o streaming suave. Os URLs de vídeo abaixo são URLs de streaming suaves. Para obter os URLs DASH correspondentes, basta anexar "(formato=mpd-time-csf)". Você poderia usar o leitor [de teste de mídia azul](https://aka.ms/amtest) para testar em um navegador. Permite-lhe configurar qual o protocolo de streaming a utilizar, segundo o qual a tecnologia. IE11 e Microsoft Edge no Suporte do Windows 10 PlayReady através do EME. Para mais informações, consulte [detalhes sobre a ferramenta de teste.](./offline-playready-streaming-windows-10.md)

### <a name="sample-1"></a>Exemplo 1

* URL de origem (base): `https://willzhanmswest.streaming.mediaservices.windows.net/1efbd6bb-1e66-4e53-88c3-f7e5657a9bbd/RussianWaltz.ism/manifest` 
* PlayReady LA_URL (DASH & suave): `https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/` 
* LA_URL de averbar (DASH): `https://willzhanmswest.keydelivery.mediaservices.windows.net/Widevine/?kid=78de73ae-6d0f-470a-8f13-5c91f7c4` 
* FairPlay LA_URL (HLS): `https://willzhanmswest.keydelivery.mediaservices.windows.net/FairPlay/?kid=ba7e8fb0-ee22-4291-9654-6222ac611bd8` 

### <a name="sample-2"></a>Exemplo 2

* URL de origem (base): https://willzhanmswest.streaming.mediaservices.windows.net/1a670626-4515-49ee-9e7f-cd50853e41d8/Microsoft_HoloLens_TransformYourWorld_816p23.ism/Manifest 
* PlayReady LA_URL (DASH & suave): `http://willzhan12.cloudapp.net/PlayReady/RightsManager.asmx` 

### <a name="sample-3"></a>Exemplo 3

* URL de origem: https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (DASH & suave): `https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/` 

### <a name="sample-4"></a>Exemplo 4

* URL de origem: https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (DASH & suave): `https://willzhan12.cloudapp.net/playready/rightsmanager.asmx` 

## <a name="additional-notes"></a>Notas adicionais

* Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="summary"></a>Resumo

Em resumo, os componentes DRM da Azure Media Services são flexíveis, pode usá-los num cenário híbrido configurando adequadamente a chave de conteúdo e a política de entrega de ativos, conforme descrito neste tópico.

## <a name="next-steps"></a>Passos seguintes
Ver caminhos de aprendizagem dos Serviços de Comunicação Social.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]