---
title: Design híbrido do (s) subsistemas DRM usando os serviços de mídia do Azure | Microsoft Docs
description: Este tópico discute o design híbrido do (s) subsistemas DRM usando os serviços de mídia do Azure.
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
ms.openlocfilehash: d15bfcfbae3b24e1a9b29dc74f9b41a979e63ae9
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "69014698"
---
# <a name="hybrid-design-of-drm-subsystems"></a>Design híbrido de subsistemas DRM 

Este tópico discute o design híbrido do (s) subsistemas DRM usando os serviços de mídia do Azure.

## <a name="overview"></a>Descrição geral

Os serviços de mídia do Azure fornecem suporte para os três sistemas DRM a seguir:

* PlayReady
* Widevine (modular)
* FairPlay

O suporte a DRM inclui criptografia DRM (criptografia dinâmica) e entrega de licença, com Player de Mídia do Azure dando suporte a todos os 3 DRMs como um SDK do Player do navegador.

Para obter um tratamento detalhado do design e da implementação do subsistema DRM/CENC, consulte o documento intitulado [Cenc com multi-DRM e controle de acesso](media-services-cenc-with-multidrm-access-control.md).

Embora ofereçamos suporte completo para três sistemas DRM, às vezes, os clientes precisam usar várias partes de sua própria infraestrutura/subsistemas, além dos serviços de mídia do Azure para criar um subsistema DRM híbrido.

Abaixo estão algumas perguntas comuns solicitadas pelos clientes:

* "Posso usar meus próprios servidores de licença DRM?" (Nesse caso, os clientes investiram no farm de servidores de licença do DRM com a lógica comercial inserida).
* "Posso usar apenas sua entrega de licença do DRM nos serviços de mídia do Azure sem hospedar conteúdo no AMS?"

## <a name="modularity-of-the-ams-drm-platform"></a>Modularidade da plataforma DRM do AMS

Como parte de uma plataforma de vídeo em nuvem abrangente, o DRM dos serviços de mídia do Azure tem um design com flexibilidade e modularidade em mente. Você pode usar os serviços de mídia do Azure com qualquer uma das diferentes combinações descritas na tabela a seguir (uma explicação da notação usada na tabela a seguir). 

|**Origem de & de Hospedagem de conteúdo**|**Criptografia de conteúdo**|**Entrega de licença DRM**|
|---|---|---|
|AMS|AMS|AMS|
|AMS|AMS|Terceiros|
|AMS|Terceiros|AMS|
|AMS|Terceiros|Terceiros|
|Terceiros|Terceiros|AMS|

### <a name="content-hosting--origin"></a>Origem de & de Hospedagem de conteúdo

* AMS: o ativo de vídeo é hospedado no AMS e o streaming é por meio de pontos de extremidade de streaming do AMS (mas não necessariamente empacotamento dinâmico).
* Terceiros: o vídeo é hospedado e entregue em uma plataforma de streaming de terceiros fora do AMS.

### <a name="content-encryption"></a>Criptografia de conteúdo

* AMS: a criptografia de conteúdo é executada dinamicamente/sob demanda pela criptografia dinâmica do AMS.
* Terceiros: a criptografia de conteúdo é executada fora do AMS usando um fluxo de trabalho de pré-processamento.

### <a name="drm-license-delivery"></a>Entrega de licença de DRM

* AMS A licença de DRM é fornecida pelo serviço de entrega de licenças do AMS.
* Terceiros: A licença de DRM é fornecida por um servidor de licença de DRM de terceiros fora do AMS.

## <a name="configure-based-on-your-hybrid-scenario"></a>Configurar com base em seu cenário híbrido

### <a name="content-key"></a>Chave de conteúdo

Por meio da configuração de uma chave de conteúdo, você pode controlar os seguintes atributos de criptografia dinâmica do AMS e serviço de entrega de licença do AMS:

* A chave de conteúdo usada para criptografia de DRM dinâmica.
* Conteúdo de licença do DRM a ser entregue pelos serviços de entrega de licença: direitos, chave de conteúdo e restrições.
* Tipo de **restrição de política de autorização de chave de conteúdo**: restrição de abertura, IP ou token.
* Se o tipo de **token** da **restrição de política de autorização de chave de conteúdo for usado**, a **restrição de política de autorização de chave de conteúdo** deverá ser atendida antes que uma licença seja emitida.

### <a name="asset-delivery-policy"></a>Política de entrega de ativos

Por meio da configuração de uma política de entrega de ativos, você pode controlar os seguintes atributos usados pelo empacotador dinâmico do AMS e criptografia dinâmica de um ponto de extremidade de streaming do AMS:

* Combinação de protocolo de streaming e criptografia DRM, como DASH em CENC (PlayReady e Widevine), Smooth streaming sob PlayReady, HLS em Widevine ou PlayReady.
* As URLs de entrega de licença padrão/inseridas para cada um dos DRMs envolvidos.
* Se as URLs de aquisição de licença (LA_URLs) na lista de reprodução de traço MPD ou HLS contêm cadeia de caracteres de consulta de ID de chave (KID) para Widevine e FairPlay, respectivamente.

## <a name="scenarios-and-samples"></a>Cenários e exemplos

Com base nas explicações na seção anterior, os cinco cenários híbridos a seguir usam combinações de configuração da**política de entrega de ativos** de chave-de **conteúdo**(os exemplos mencionados na última coluna seguem a tabela):

|**Origem de & de Hospedagem de conteúdo**|**Criptografia DRM**|**Entrega de licença DRM**|**Configurar chave de conteúdo**|**Configurar política de entrega de ativos**|**Exemplo**|
|---|---|---|---|---|---|
|AMS|AMS|AMS|Sim|Sim|Exemplo 1|
|AMS|AMS|Terceiros|Sim|Sim|Exemplo 2|
|AMS|Terceiros|AMS|Sim|Não|Exemplo 3|
|AMS|Terceiros|Exterior|Não|Não|Exemplo 4|
|Terceiros|Terceiros|AMS|Sim|Não|    

Nos exemplos, a proteção PlayReady funciona tanto para o DASH quanto para Smooth streaming. As URLs de vídeo abaixo são URLs de Smooth streaming. Para obter as URLs de DASH correspondentes, basta acrescentar "(Format = MPD-time-CSF)". Você pode usar o [player de teste de mídia do Azure](https://aka.ms/amtest) para testar em um navegador. Ele permite que você configure qual protocolo de streaming usar, sob o qual Tech. O IE11 e o Microsoft Edge no Windows 10 dão suporte ao PlayReady por meio do EME. Para obter mais informações, consulte [detalhes sobre a ferramenta de teste](https://blogs.msdn.microsoft.com/playready4/2016/02/28/azure-media-test-tool/).

### <a name="sample-1"></a>Exemplo 1

* URL de origem (base): https://willzhanmswest.streaming.mediaservices.windows.net/1efbd6bb-1e66-4e53-88c3-f7e5657a9bbd/RussianWaltz.ism/manifest 
* PlayReady LA_URL (traço & suave): https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 
* Widevine LA_URL (DASH): https://willzhanmswest.keydelivery.mediaservices.windows.net/Widevine/?kid=78de73ae-6d0f-470a-8f13-5c91f7c4 
* FairPlay LA_URL (HLS): https://willzhanmswest.keydelivery.mediaservices.windows.net/FairPlay/?kid=ba7e8fb0-ee22-4291-9654-6222ac611bd8 

### <a name="sample-2"></a>Exemplo 2

* URL de origem (base): https://willzhanmswest.streaming.mediaservices.windows.net/1a670626-4515-49ee-9e7f-cd50853e41d8/Microsoft_HoloLens_TransformYourWorld_816p23.ism/Manifest 
* PlayReady LA_URL (traço & suave): http://willzhan12.cloudapp.net/PlayReady/RightsManager.asmx 

### <a name="sample-3"></a>Exemplo 3

* URL de origem: https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (traço & suave): https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 

### <a name="sample-4"></a>Exemplo 4

* URL de origem: https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (traço & suave): https://willzhan12.cloudapp.net/playready/rightsmanager.asmx 

## <a name="summary"></a>Resumo

Em resumo, os componentes DRM dos serviços de mídia do Azure são flexíveis, você pode usá-los em um cenário híbrido Configurando adequadamente a chave de conteúdo e a política de entrega de ativos, conforme descrito neste tópico.

## <a name="next-steps"></a>Passos Seguintes
Exibir os roteiros de aprendizagem dos serviços de mídia.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

