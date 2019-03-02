---
title: Design híbrido do subsystem(s) DRM usando os serviços de multimédia do Azure | Documentos da Microsoft
description: Este tópico aborda o design híbrido do subsystem(s) DRM usando os serviços de multimédia do Azure.
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
ms.date: 02/08/2019
ms.author: willzhan;juliako
ms.openlocfilehash: 86e3e551969ee8d354b2d3e369f6006a77fd7820
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243079"
---
# <a name="hybrid-design-of-drm-subsystems"></a>Design híbrido do subsistemas DRM 

Este tópico aborda o design híbrido do subsystem(s) DRM usando os serviços de multimédia do Azure.

## <a name="overview"></a>Descrição geral

Serviços de multimédia do Azure fornece suporte para o sistema DRM três seguintes:

* PlayReady
* Widevine (Modular)
* FairPlay

O suporte DRM inclui encriptação de DRM (encriptação dinâmica) e entrega de licenças, com o leitor de multimédia do Azure que suporta todas as 3 DRMs como um leitor de navegador SDK.

Para um tratamento detalhado do design de subsistema DRM/CENC e da implementação, consulte o documento intitulado [CENC com múltipla DRM e controlo de acesso](media-services-cenc-with-multidrm-access-control.md).

Embora oferecemos suporte completo para os três sistemas DRM, às vezes, os clientes têm de utilizar várias partes da suas própria infraestrutura subsistemas além dos serviços de multimédia do Azure para criar uma híbrida com o subsistema DRM.

Abaixo são algumas perguntas comuns solicitadas pelos clientes:

* "Posso usar meus próprio servidores de licença DRM?" (Neste caso, os clientes investiram no farm de servidores de licença DRM com lógica de negócio embedded).
* "Posso utilizar apenas a entrega de licenças DRM nos serviços de multimédia do Azure sem a hospedagem de conteúdo no AMS?"

## <a name="modularity-of-the-ams-drm-platform"></a>Modularidade da plataforma do AMS DRM

Como parte de uma plataforma de vídeo na cloud abrangente, o DRM de serviços de multimédia do Azure tem um design com flexibilidade e modularidade em mente. Pode utilizar os serviços de multimédia do Azure com qualquer uma das seguintes combinações diferentes, descritas na tabela abaixo (uma explicação sobre a notação utilizada na tabela a segue). 

|**Alojamento de conteúdo & origem**|**Encriptação de conteúdo**|**Entrega de licença DRM**|
|---|---|---|
|AMS|AMS|AMS|
|AMS|AMS|De terceiros|
|AMS|De terceiros|AMS|
|AMS|De terceiros|De terceiros|
|De terceiros|De terceiros|AMS|

### <a name="content-hosting--origin"></a>Alojamento de conteúdo & origem

* AMS: o elemento de vídeo está hospedado em AMS e transmissão em fluxo é efetuada através de pontos finais de transmissão em fluxo do AMS (mas não necessariamente dynamic packaging).
* De terceiros: vídeo é hospedado e entregue numa plataforma de transmissão em fluxo de terceiros fora do AMS.

### <a name="content-encryption"></a>Encriptação de conteúdo

* AMS: encriptação de conteúdo é efetuada dinamicamente/a pedido, encriptação dinâmica de AMS.
* De terceiros: encriptação de conteúdo é executada fora do AMS através de um fluxo de trabalho de pré-processamento.

### <a name="drm-license-delivery"></a>Entrega de licença de DRM

* AMS: Licença DRM é entregue pelo serviço de entrega de licença do AMS.
* De terceiros: Licença DRM é entregue por um servidor de licenças DRM externos fora do AMS.

## <a name="configure-based-on-your-hybrid-scenario"></a>Configurar com base no seu cenário híbrido

### <a name="content-key"></a>Chave de conteúdo

Através da configuração de uma chave de conteúdo, pode controlar os seguintes atributos de encriptação dinâmica do AMS e serviço de entrega de licença do AMS:

* A chave de conteúdo utilizada para encriptação dinâmica de DRM.
* Conteúdo de licença DRM a entregar dos serviços de entrega de licença: direitos, chave de conteúdo e as restrições.
* O tipo de **restrição de política de autorização da chave de conteúdo**: aberto, IP ou restrição de token.
* Se **token** tipo de **restrição de política de autorização da chave de conteúdo é utilizada**, o **restrição de política de autorização da chave de conteúdo** têm de ser cumpridos antes de uma licença é emitida.

### <a name="asset-delivery-policy"></a>Política de entrega de elemento

Através da configuração de uma política de entrega de elementos, pode controlar os seguintes atributos utilizados pelo empacotador de AMS dinâmica e a encriptação dinâmica de um ponto de final de transmissão em fluxo de AMS:

* Transmissão em fluxo de protocolo e a combinação de encriptação de DRM, como o TRAÇO em CENC (PlayReady e Widevine), transmissão em fluxo uniforme em PlayReady, HLS, sob o Widevine ou o PlayReady.
* O padrão/incorporado de licenças URLs de entrega para cada um dos DRMs envolvidos.
* Se a licença aquisição dos URLs (LA_URLs) DASH MPD ou HLS lista de reprodução contêm a cadeia de consulta da chave de ID (criança) para Widevine e FairPlay, respectivamente.

## <a name="scenarios-and-samples"></a>Cenários e exemplos

Com base nas explicações na secção anterior, os seguintes cenários de cinco híbrida utilizam respectivos **chave de conteúdo**-**política de entrega de elemento** (os exemplos de combinações de configuração mencionado na último siga de coluna da tabela):

|**Alojamento de conteúdo & origem**|**Encriptação de DRM**|**Entrega de licença DRM**|**Configurar a chave de conteúdo**|**Configurar a política de entrega de elemento**|**Exemplo**|
|---|---|---|---|---|---|
|AMS|AMS|AMS|Sim|Sim|Exemplo 1|
|AMS|AMS|De terceiros|Sim|Sim|Exemplo 2|
|AMS|De terceiros|AMS|Sim|Não|Exemplo 3|
|AMS|De terceiros|Fora|Não|Não|Exemplo 4|
|De terceiros|De terceiros|AMS|Sim|Não|    

Nos exemplos, proteção PlayReady funciona para DASH e smooth streaming. Os URLs de vídeo abaixo são URLs de transmissão em fluxo uniforme. Para obter os URLs de TRAÇO correspondente, basta acrescentar "(formato = mpd-time-csf)". Pode utilizar o [player de teste de multimédia do azure](https://aka.ms/amtest) para testar num navegador. Permite-lhe configurar qual protocolo de transmissão em fluxo a utilizar, nas qual tech. IE11 e o Microsoft Edge no Windows 10 suportam o PlayReady, através de EME. Para obter mais informações, consulte [detalhes sobre a ferramenta de teste](https://blogs.msdn.microsoft.com/playready4/2016/02/28/azure-media-test-tool/).

### <a name="sample-1"></a>Exemplo 1

* URL de origem (base): https://willzhanmswest.streaming.mediaservices.windows.net/1efbd6bb-1e66-4e53-88c3-f7e5657a9bbd/RussianWaltz.ism/manifest 
* PlayReady LA_URL (DASH e smooth): https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 
* Widevine LA_URL (DASH): https://willzhanmswest.keydelivery.mediaservices.windows.net/Widevine/?kid=78de73ae-6d0f-470a-8f13-5c91f7c4 
* FairPlay LA_URL (HLS): https://willzhanmswest.keydelivery.mediaservices.windows.net/FairPlay/?kid=ba7e8fb0-ee22-4291-9654-6222ac611bd8 

### <a name="sample-2"></a>Exemplo 2

* URL de origem (base): http://willzhanmswest.streaming.mediaservices.windows.net/1a670626-4515-49ee-9e7f-cd50853e41d8/Microsoft_HoloLens_TransformYourWorld_816p23.ism/Manifest 
* PlayReady LA_URL (DASH e smooth): http://willzhan12.cloudapp.net/PlayReady/RightsManager.asmx 

### <a name="sample-3"></a>Exemplo 3

* URL de origem: https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (DASH e smooth): https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 

### <a name="sample-4"></a>Exemplo 4

* URL de origem: https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (DASH e smooth): https://willzhan12.cloudapp.net/playready/rightsmanager.asmx 

## <a name="summary"></a>Resumo

Em resumo, os componentes de DRM de serviços de multimédia do Azure são flexíveis, pode usá-los num cenário híbrido ao configurar corretamente a chave de conteúdo e a política de entrega de elementos, conforme descrito neste tópico.

## <a name="next-steps"></a>Passos Seguintes
Serviços de multimédia de modo de exibição percursos de aprendizagem.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

