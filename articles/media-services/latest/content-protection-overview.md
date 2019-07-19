---
title: Proteger seu conteúdo usando a criptografia dinâmica dos serviços de mídia – Azure | Microsoft Docs
description: Este artigo fornece uma visão geral da proteção de conteúdo com criptografia dinâmica. Ele também fala sobre protocolos de streaming e tipos de criptografia.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 5d31e4a523fdedf9907e33c70638f07a08461ed1
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310310"
---
# <a name="content-protection-with-dynamic-encryption"></a>Proteção de conteúdo com criptografia dinâmica

Pode utilizar os serviços de multimédia do Azure para proteger os seus suportes de dados a partir do momento em que deixa seu computador por meio de armazenamento, processamento e entrega. Com os serviços de mídia, você pode entregar seu conteúdo ao vivo e sob demanda criptografado dinamicamente com criptografia AES (AES-128) ou qualquer um dos três principais sistemas de DRM (gerenciamento de direitos digitais): Microsoft PlayReady, Google Widevine e Apple FairPlay. Serviços de multimédia também fornecem um serviço para entrega de chaves AES e o DRM (PlayReady, Widevine e FairPlay) licenças para os clientes autorizados. 

No Media Services V3, uma chave de conteúdo é associada ao localizador de streaming (consulte [Este exemplo](protect-with-aes128.md)). Se estiver usando o serviço de distribuição de chaves dos serviços de mídia, você poderá permitir que os serviços de mídia do Azure gerem a chave de conteúdo para você. Você mesmo deve gerar a chave de conteúdo se estiver usando o serviço de distribuição de chaves próprio, ou se precisar lidar com um cenário de alta disponibilidade em que você precisa ter a mesma chave de conteúdo em dois data centers.

Quando um fluxo é solicitado por um jogador, serviços de multimédia utiliza a chave especificada para encriptar dinamicamente o seu conteúdo através da utilização de chave não encriptada AES ou encriptação de DRM. Para descriptografar o fluxo, o jogador solicita a chave do serviço de entrega de chave de serviços de multimédia ou o serviço de entrega de chave que especificou. Para decidir se é ou não o utilizador está autorizado a obter a chave, o serviço avalia a política de chave de conteúdo que especificou para a chave.

Pode utilizar a API REST ou uma biblioteca de cliente dos serviços de multimédia para configurar políticas de autorização e autenticação para as suas licenças e chaves.

A imagem seguinte ilustra o fluxo de trabalho de proteção de conteúdo de serviços de multimédia: 

![Proteger conteúdo](./media/content-protection/content-protection.svg)

&#42;*encriptação dinâmica suporta AES-128 "chave não encriptada", CBCS e CENC. Para obter detalhes, consulte a matriz de suporte [aqui](#streaming-protocols-and-encryption-types).*

Este artigo explica os conceitos e terminologia relevante para compreender a proteção de conteúdo com os Media Services.

## <a name="main-components-of-a-content-protection-system"></a>Componentes principais de um sistema de proteção de conteúdo

Para concluir com êxito o design do sistema/aplicativo "proteção de conteúdo", precisa compreender plenamente o âmbito do esforço. A lista seguinte fornece uma visão geral de três partes, que terá de implementar. 

1. Código de serviços de multimédia do Azure
  
   O exemplo de [DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) mostra como implementar o sistema de vários DRM com os serviços de mídia v3 usando o .net. Ele também mostra como usar a licença dos serviços de mídia/serviço de entrega de chave. Pode encriptar cada elemento com vários tipos de encriptação (AES-128, PlayReady, Widevine, FairPlay). Veja [Protocolos de transmissão em fluxo e tipos de encriptação](#streaming-protocols-and-encryption-types), para ver o que faz sentido combinar.
  
   O exemplo mostra como:

   1. Criar e configurar [políticas de chave de conteúdo](content-key-policy-concept.md). Você cria uma **política de chave de conteúdo** para configurar como a chave de conteúdo (que fornece acesso seguro aos seus ativos) é entregue aos clientes finais.    

      * Defina a autorização de entrega de licença, especificando a lógica da verificação de autorização com base em declarações em JWT.
      * Configure as licenças [PlayReady](playready-license-template-overview.md), [Widevine](widevine-license-template-overview.md)e/ou [Fairplay](fairplay-license-overview.md) . Os modelos permitem que você configure direitos e permissões para cada um dos DRMs usados.

        ```
        ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
        ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
        ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
        ```
   2. Crie um [localizador de streaming](streaming-locators-concept.md) configurado para transmitir o ativo criptografado. 
  
      O **localizador de streaming** deve ser associado a uma [política de streaming](streaming-policy-concept.md). No exemplo, definimos StreamingLocator. StreamingPolicyName como a política "Predefined_MultiDrmCencStreaming". As criptografias PlayReady e Widevine são aplicadas, a chave é entregue ao cliente de reprodução com base nas licenças DRM configuradas. Se também quiser encriptar a sua transmissão em fluxo com CBCS (FairPlay), utilize "Predefined_MultiDrmStreaming".
      
      O localizador de streaming também está associado à **política de chave de conteúdo** que foi definida.
    
   3. Crie um token de teste.

      O **GetTokenAsync** método mostra como criar um teste de token.
   4. Crie o URL de transmissão em fluxo.

      O **GetDASHStreamingUrlAsync** método mostra como criar o URL de transmissão em fluxo. Neste caso, os fluxos de URL a **DASH** conteúdo.

2. Jogador com AES ou DRM cliente. Uma aplicação de leitor de vídeo com base num player SDK (nativo ou baseada no browser) tem de cumprir os seguintes requisitos:
   * O SDK player suporta os clientes DRM necessários
   * O SDK do Player dá suporte aos protocolos de streaming necessários: Smooth, DASH e/ou HLS
   * O SDK player tem de ser capaz de lidar com a passagem de um token JWT no pedido de aquisição de licença
  
     Pode criar um leitor, utilizando o [API de leitor de multimédia do Azure](https://amp.azure.net/libs/amp/latest/docs/). Utilizar o [API do Azure Media Player ProtectionInfo](https://amp.azure.net/libs/amp/latest/docs/) para especificar a tecnologia DRM a ser utilizada em diferentes plataformas DRM.

     Para teste AES ou CENC (Widevine e/ou PlayReady) encriptados conteúdo, pode utilizar [leitor de multimédia do Azure](https://aka.ms/azuremediaplayer). Certifique-se de que clicar em "Opções avançadas" e verifique as opções de encriptação.

     Se pretender testar conteúdo do FairPlay encriptado, utilize [player este teste](https://aka.ms/amtest). O jogador suporta Widevine, PlayReady, e encriptação de chave de não FairPlay DRMs, bem como AES-128. 
    
     Você precisa escolher o navegador certo para testar diferentes DRMs: Chrome/Opera/Firefox para Widevine, Microsoft Edge/IE11 para PlayReady, Safari no macOS para FairPlay.

3. Proteger o serviço de Token (STS), que emite o JSON Web Token (JWT) como token de acesso para acesso a recursos back-end. Pode utilizar os serviços de entrega de licença do AMS como o recurso de back-end. Tem um STS para definir o seguinte:

   * Emissor e público-alvo (ou escopo)
   * Afirmações, que são dependentes nos requisitos de negócios na proteção de conteúdo
   * Verificação de simétrica ou assimétrica para verificação da assinatura
   * Suporte de rollover de chave (se necessário)

     Você pode usar [essa ferramenta STS](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) para testar o STS, que dá suporte a todos os três tipos de chave de verificação: simétrico, assimétrico ou Azure AD com substituição de chave. 

> [!NOTE]
> É altamente recomendado para concentrar-se e testar totalmente a cada parte (descrito anteriormente) antes de passar para a parte seguinte. Para testar o seu sistema de "proteção de conteúdo", utilize as ferramentas especificadas na lista acima.  

## <a name="streaming-protocols-and-encryption-types"></a>Protocolos de transmissão em fluxo e tipos de encriptação

Pode utilizar os serviços de multimédia para distribuir os seus conteúdos encriptados dinamicamente com a chave não encriptada AES ou encriptação de DRM com o PlayReady, Widevine e FairPlay. Atualmente, pode criptografar os formatos de HTTP Live Streaming (HLS), MPEG DASH e Smooth Streaming. Cada protocolo suporta os seguintes métodos de encriptação:

### <a name="hls"></a>HLS

O protocolo HLS dá suporte aos seguintes formatos de contêiner e esquemas de criptografia.

|Formato de contêiner|Esquema de encriptação|Exemplo de URL|
|---|---|---|
|Todos|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2-TS |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF(fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2-TS |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF(fmp4) |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

HLS/CMAF + FairPlay (incluindo HEVC/H. 265) tem suporte nos seguintes dispositivos:

* iOS v11 ou superior 
* iPhone 8 ou superior
* MacOS High Sierra com a CPU Intel 7ª Gen

### <a name="mpeg-dash"></a>MPEG-DASH

O protocolo MPEG-DASH dá suporte aos seguintes formatos de contêiner e esquemas de criptografia.

|Formato de contêiner|Esquema de encriptação|Exemplos de URL
|---|---|---|
|Todos|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF(fmp4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC (Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>Transmissão em Fluxo Uniforme

O protocolo Smooth Streaming dá suporte aos seguintes formatos de contêiner e esquemas de criptografia.

|Protocol|Formato de contêiner|Esquema de encriptação|
|---|---|---|
|fMP4|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|

### <a name="browsers"></a>Browsers

Os navegadores comuns dão suporte aos seguintes clientes DRM:

|Browser|Encriptação|
|---|---|
|Chrome|Widevine|
|Microsoft Edge, IE 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="control-content-access"></a>Controlar o acesso do conteúdo

Pode controlar quem tem acesso ao seu conteúdo ao configurar a política de chave de conteúdo. Os Media Services suportam várias formas de autorização de utilizadores que efetuam pedidos de chave. Tem de configurar a política de chave de conteúdo. O cliente (leitor) tem de cumprir a política antes da chave pode ser entregue ao cliente. A política de chave de conteúdo pode ter **abra** ou **token** restrição. 

Com uma conteúdo chave política de token restrito, a chave de conteúdo é enviada apenas para um cliente que apresenta um válido JSON Web Token (JWT) ou simple web tokens (SWT) no pedido de chaves/licenças. Este token deve ser emitido por um serviço de token de segurança (STS). Pode utilizar o Azure Active Directory como um STS ou implementar um STS personalizado. O STS deve ser configurado para criar um token assinado com as declarações de chave e o problema especificadas que especificou na configuração de restrição de token. O serviço de entrega de chave de serviços de multimédia devolve a chave/licença pedida para o cliente se o token é válido e as afirmações no token corresponderem às configuradas para a chave/licença.

Ao configurar a política de token restrito, tem de especificar a chave de verificação primária, emissor e parâmetros de público-alvo. A chave de verificação primária contém a chave de que o token foi assinado com. O emissor é o serviço de token seguro que emite o token. O público-alvo, às vezes chamado de âmbito, descreve a intenção do token ou o recurso o token de acesso a autoriza. O serviço de entrega de chave de serviços de multimédia valida que estes valores no token correspondem aos valores no modelo.

Os clientes geralmente usam um STS personalizado para incluir declarações personalizadas no token para selecionar entre diferentes ContentKeyPolicyOptions com parâmetros de licença de DRM diferentes (uma licença de assinatura em vez de uma licença de aluguel) ou para incluir uma declaração representando a chave de conteúdo identificador da chave à qual o token concede acesso.

### <a name="token-replay-prevention"></a>Prevenção de reprodução de token

O recurso de *prevenção de reprodução de token* permite que os clientes dos serviços de mídia definam um limite de quantas vezes o mesmo token pode ser usado para solicitar uma chave ou uma licença. O cliente pode adicionar uma declaração do tipo `urn:microsoft:azure:mediaservices:maxuses` no token, em que o valor é o número de vezes que o token pode ser usado para adquirir uma licença ou chave. Todas as solicitações subsequentes com o mesmo token para a distribuição de chaves retornarão uma resposta não autorizada. Consulte Como adicionar a declaração no exemplo de [DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Considerações

* Os clientes devem ter controle sobre a geração de tokens. A declaração precisa ser colocada no próprio token.
* Ao usar esse recurso, as solicitações com tokens cuja hora de expiração é mais de uma hora longe da hora em que a solicitação é recebida são rejeitadas com uma resposta não autorizada.
* Os tokens são identificados exclusivamente por sua assinatura. Qualquer alteração na carga (por exemplo, atualizar para a hora de expiração ou a declaração) altera a assinatura do token e ela conta como um novo token que a entrega de chave não foi encontrada antes.
* A reprodução falhará se o token exceder `maxuses` o valor definido pelo cliente.
* Esse recurso pode ser usado para todo o conteúdo protegido existente (somente o token emitido precisa ser alterado).
* Esse recurso funciona com o JWT e o SWT.

## <a name="custom-key-and-license-acquisition-url"></a>URL de aquisição de licença e chave personalizada

Use os modelos a seguir se desejar especificar um serviço de entrega de chave e licença diferente (não serviços de mídia). Os dois campos substituíveis nos modelos existem para que você possa compartilhar sua política de streaming em vários ativos, em vez de criar uma política de streaming por ativo. 

* EnvelopeEncryption. CustomKeyAcquisitionUrlTemplate-template para a URL do serviço personalizado que fornece chaves para os players do usuário final. Não é necessário ao usar os serviços de mídia do Azure para emitir chaves. O modelo oferece suporte a tokens substituíveis que o serviço atualizará em tempo de execução com o valor específico para a solicitação.  Os valores de token com suporte no momento são {AlternativeMediaId}, que é substituído pelo valor de StreamingLocatorId. AlternativeMediaId e {ContentKeyId}, que é substituído pelo valor do identificador da chave que está sendo solicitada.
* StreamingPolicyPlayReadyConfiguration. CustomLicenseAcquisitionUrlTemplate-template para a URL do serviço personalizado que entrega licenças para players do usuário final. Não é necessário ao usar os serviços de mídia do Azure para a emissão de licenças. O modelo oferece suporte a tokens substituíveis que o serviço atualizará em tempo de execução com o valor específico para a solicitação. Os valores de token com suporte no momento são {AlternativeMediaId}, que é substituído pelo valor de StreamingLocatorId. AlternativeMediaId e {ContentKeyId}, que é substituído pelo valor do identificador da chave que está sendo solicitada. 
* StreamingPolicyWidevineConfiguration. CustomLicenseAcquisitionUrlTemplate-o mesmo que acima, somente para Widevine. 
* StreamingPolicyFairPlayConfiguration. CustomLicenseAcquisitionUrlTemplate-o mesmo que acima, somente para FairPlay.  

Por exemplo:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

O `ContentKeyId` tem um valor da chave que está sendo solicitada `AlternativeMediaId` e o pode ser usado se você quiser mapear a solicitação para uma entidade no seu lado. Por exemplo, o `AlternativeMediaId` pode ser usado para ajudá-lo a Pesquisar permissões.

Para obter exemplos de REST que usam URLs de chave personalizada e de aquisição de licença, consulte [políticas de streaming – criar](https://docs.microsoft.com/rest/api/media/streamingpolicies/create)

## <a name="troubleshoot"></a>Resolução de problemas

Se você receber o `MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY` erro, certifique-se de especificar a política de streaming apropriada.

Se você receber erros que terminam `_NOT_SPECIFIED_IN_URL`com o, certifique-se de especificar o formato de criptografia na URL. Por exemplo, `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`. Consulte [protocolos de streaming e tipos de criptografia](#streaming-protocols-and-encryption-types).

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, envie comentários, obtenha atualizações

Confira o artigo [da Comunidade dos serviços de mídia do Azure](media-services-community.md) para ver diferentes maneiras que você pode fazer perguntas, fornecer comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Passos Seguintes

* [Proteger com encriptação AES](protect-with-aes128.md)
* [Proteger com o DRM](protect-with-drm.md)
* [Projetar o sistema de proteção de conteúdo de vários DRM com o controle de acesso](design-multi-drm-system-with-access-control.md)
* [Criptografia do lado do armazenamento](storage-account-concept.md#storage-side-encryption)
* [Perguntas mais frequentes](frequently-asked-questions.md)

