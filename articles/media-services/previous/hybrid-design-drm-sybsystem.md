---
title: Design híbrido do subsistema DRM utilizando o Azure Media Services [ Microsoft Docs
description: Este tema discute o design híbrido do subsistema DRM utilizando o Azure Media Services.
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
editor: ''
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: willzhan
ms.reviewer: juliako
ms.openlocfilehash: 44095cb85c62fd40032263d96ad678bdeb5effc0
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "82159408"
---
# <a name="hybrid-design-of-drm-subsystems"></a>Design híbrido dos subsistemas DRM 

Este tema discute o design híbrido do subsistema DRM utilizando o Azure Media Services.

## <a name="overview"></a>Descrição geral

A Azure Media Services presta suporte para o seguinte sistema de DRM:

* PlayReady
* Widevine (Modular)
* FairPlay

O suporte a DRM inclui encriptação DRM (encriptação dinâmica) e entrega de licença, com o Azure Media Player a suportar todos os 3 DRMs como um leitor de navegador SDK.

Para um tratamento detalhado da conceção e implementação do subsistema DRM/CENC, consulte o documento intitulado [CENC com Multi-DRM e Controlo](media-services-cenc-with-multidrm-access-control.md)de Acesso .

Embora ofereçamos apoio total a três sistemas DEDRM, por vezes os clientes precisam de utilizar várias partes da sua própria infraestrutura/subsistemas, além dos Serviços De Mídia Azure para construir um subsistema híbrido de DRM.

Abaixo estão algumas perguntas comuns colocadas pelos clientes:

* "Posso usar os meus próprios servidores de licença de DRM?" (Neste caso, os clientes investiram na exploração de servidores de licenças DRM com lógica de negócio incorporada).
* "Posso usar apenas a sua entrega de licença de DRM na Azure Media Services sem hospedar conteúdo na AMS?"

## <a name="modularity-of-the-ams-drm-platform"></a>Modularidade da plataforma AMS DRM

Como parte de uma plataforma abrangente de vídeo em nuvem, a Azure Media Services DRM tem em mente um design com flexibilidade e modularidade. Pode utilizar o Azure Media Services com qualquer uma das seguintes combinações diferentes descritas na tabela abaixo (segue-se uma explicação da notação utilizada na tabela). 

|**Hospedagem de conteúdos & origem**|**Encriptação de conteúdo**|**Entrega de licença DRM**|
|---|---|---|
|AMS|AMS|AMS|
|AMS|AMS|Terceiros|
|AMS|Terceiros|AMS|
|AMS|Terceiros|Terceiros|
|Terceiros|Terceiros|AMS|

### <a name="content-hosting--origin"></a>Hospedagem de conteúdos & origem

* AMS: o ativo de vídeo está hospedado em AMS e o streaming é através de pontos finais de streaming AMS (mas não necessariamente de embalagem dinâmica).
* Terceiros: o vídeo é hospedado e entregue numa plataforma de streaming de terceiros fora da AMS.

### <a name="content-encryption"></a>Encriptação de conteúdo

* AMS: a encriptação do conteúdo é realizada dinamicamente/a pedido pela encriptação dinâmica AMS.
* Terceiros: a encriptação do conteúdo é realizada fora da AMS utilizando um fluxo de trabalho pré-processamento.

### <a name="drm-license-delivery"></a>Entrega de licença de DRM

* AMS: A licença DE DRM é entregue pelo serviço de entrega de licenças AMS.
* Terceiro: A licença DRM é entregue por um servidor de licença DRM de terceiros fora da AMS.

## <a name="configure-based-on-your-hybrid-scenario"></a>Configure com base no seu cenário híbrido

### <a name="content-key"></a>Chave de conteúdo

Através da configuração de uma chave de conteúdo, pode controlar os seguintes atributos tanto da encriptação dinâmica AMS como do serviço de entrega de licenças AMS:

* A chave de conteúdo utilizada para encriptação dDRD dinâmica.
* Conteúdo da licença DRM a ser entregue por serviços de entrega de licenças: direitos, chave de conteúdo e restrições.
* Tipo de restrição da política de autorização de chaves de **conteúdo: restrição**aberta, IP ou simbólica.
* Se **token** **for utilizada uma restrição**da política de autorização de chave de conteúdo, a **restrição** da política de autorização de conteúdo deve ser cumprida antes da emissão de uma licença.

### <a name="asset-delivery-policy"></a>Política de entrega de ativos

Através da configuração de uma política de entrega de ativos, pode controlar os seguintes atributos utilizados pelo pacote dinâmico AMS e encriptação dinâmica de um ponto final de streaming AMS:

* Protocolo de streaming e combinação de encriptação DRM, como o DASH sob cenc (PlayReady e Widevine), streaming suave em PlayReady, HLS em Widevine ou PlayReady.
* Os URLs de entrega de licença sinuosos/incorporados para cada uma das DDR envolvidas.
* Se a aquisição de licençauris (LA_URLs) em DASH MPD ou HLS playlist contém uma cadeia de consulta de ID chave (KID) para Widevine e FairPlay, respectivamente.

## <a name="scenarios-and-samples"></a>Cenários e amostras

Com base nas explicações na secção anterior, os seguintes cinco cenários híbridos utilizam as respetivas combinações de**configuração** da política de entrega de ativos de **conteúdo**-(as amostras mencionadas na última coluna seguem a tabela):

|**Hospedagem de conteúdos & origem**|**Encriptação DRM**|**Entrega de licença DRM**|**Configurar a chave de conteúdo**|**Configurar a política de entrega de ativos**|**Amostra**|
|---|---|---|---|---|---|
|AMS|AMS|AMS|Sim|Sim|Exemplo 1|
|AMS|AMS|Terceiros|Sim|Sim|Exemplo 2|
|AMS|Terceiros|AMS|Sim|Não|Exemplo 3|
|AMS|Terceiros|Lá fora|Não|Não|Exemplo 4|
|Terceiros|Terceiros|AMS|Sim|Não|    

Nas amostras, a proteção PlayReady funciona tanto para o DASH como para o streaming suave. Os URLs de vídeo abaixo são URLs de streaming suaves. Para obter os URLs DASH correspondentes, basta anexar "(formato=mpd-time-csf)". Você poderia usar o leitor de teste de [mídia azul](https://aka.ms/amtest) para testar em um navegador. Permite-lhe configurar qual o protocolo de streaming a utilizar, sob o qual tecnologia. IE11 e Microsoft Edge no suporte do Windows 10 PlayReady através de EME. Para mais informações, consulte [detalhes sobre a ferramenta de teste](https://blogs.msdn.microsoft.com/playready4/2016/02/28/azure-media-test-tool/).

### <a name="sample-1"></a>Exemplo 1

* URL de origem (base):`https://willzhanmswest.streaming.mediaservices.windows.net/1efbd6bb-1e66-4e53-88c3-f7e5657a9bbd/RussianWaltz.ism/manifest` 
* PlayReady LA_URL (DASH & suave):`https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/` 
* LA_URL de grande nitida (DASH):`https://willzhanmswest.keydelivery.mediaservices.windows.net/Widevine/?kid=78de73ae-6d0f-470a-8f13-5c91f7c4` 
* FairPlay LA_URL (HLS):`https://willzhanmswest.keydelivery.mediaservices.windows.net/FairPlay/?kid=ba7e8fb0-ee22-4291-9654-6222ac611bd8` 

### <a name="sample-2"></a>Exemplo 2

* URL de origem (base):https://willzhanmswest.streaming.mediaservices.windows.net/1a670626-4515-49ee-9e7f-cd50853e41d8/Microsoft_HoloLens_TransformYourWorld_816p23.ism/Manifest 
* PlayReady LA_URL (DASH & suave):`http://willzhan12.cloudapp.net/PlayReady/RightsManager.asmx` 

### <a name="sample-3"></a>Exemplo 3

* URL de origem:https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (DASH & suave):`https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/` 

### <a name="sample-4"></a>Exemplo 4

* URL de origem:https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (DASH & suave):`https://willzhan12.cloudapp.net/playready/rightsmanager.asmx` 

## <a name="additional-notes"></a>Notas adicionais

* A Widevine é um serviço prestado pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="summary"></a>Resumo

Em resumo, os componentes DRM da Azure Media Services são flexíveis, pode utilizá-los num cenário híbrido configurando adequadamente a chave de conteúdo e a política de entrega de ativos, como descrito neste tópico.

## <a name="next-steps"></a>Passos seguintes
Ver caminhos de aprendizagem dos Serviços de Media.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

