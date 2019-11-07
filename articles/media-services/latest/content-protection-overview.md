---
title: Proteger seu conteúdo com a criptografia dinâmica dos serviços de mídia
titleSuffix: Azure Media Services
description: Saiba mais sobre a proteção de conteúdo com criptografia dinâmica, protocolos de streaming e tipos de criptografia nos serviços de mídia do Azure.
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
ms.date: 10/29/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: eeb1dc44341768984bd6e337a2fe65a277f5aa77
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581220"
---
# <a name="protect-your-content-with-media-services-dynamic-encryption"></a>Proteger seu conteúdo com a criptografia dinâmica dos serviços de mídia

Use os serviços de mídia do Azure para ajudar a proteger sua mídia desde o momento em que ela sai do computador até o armazenamento, o processamento e a entrega. Com os serviços de mídia, você pode entregar seu conteúdo ao vivo e sob demanda criptografado dinamicamente com o criptografia AES (AES-128) ou qualquer um dos três principais sistemas de DRM (gerenciamento de direitos digitais): Microsoft PlayReady, Google Widevine e Apple FairPlay. Os serviços de mídia também fornecem um serviço para a entrega de chaves AES e licenças DRM (PlayReady, Widevine e FairPlay) para clientes autorizados.  

No Media Services V3, uma chave de conteúdo é associada ao localizador de streaming (consulte [Este exemplo](protect-with-aes128.md)). Se estiver usando o serviço de distribuição de chaves dos serviços de mídia, você poderá permitir que os serviços de mídia do Azure gerem a chave de conteúdo para você. A chave de conteúdo deve ser gerada por você mesmo se você estiver usando o serviço de distribuição de chaves próprio ou se precisar lidar com um cenário de alta disponibilidade em que você precisa ter a mesma chave de conteúdo em dois data centers.

Quando um fluxo é solicitado por um player, os serviços de mídia usam a chave especificada para criptografar dinamicamente o conteúdo usando a chave não criptografada AES ou a criptografia DRM. Para descriptografar o fluxo, o Player solicita a chave do serviço de distribuição de chaves dos serviços de mídia ou o serviço de distribuição de chaves que você especificou. Para decidir se o usuário está autorizado a obter a chave, o serviço avalia a política de chave de conteúdo que você especificou para a chave.

Você pode usar a API REST ou uma biblioteca de cliente dos serviços de mídia para configurar políticas de autorização e autenticação para suas licenças e chaves.

A imagem a seguir ilustra o fluxo de trabalho para proteção de conteúdo dos serviços de mídia:

![Fluxo de trabalho para proteção de conteúdo dos serviços de mídia](./media/content-protection/content-protection.svg)
  
&#42;*A criptografia dinâmica dá suporte ao AES-128 Clear Key, CBCs e Cenc. Para obter detalhes, consulte a [matriz de suporte](#streaming-protocols-and-encryption-types).*

Este artigo explica os conceitos e a terminologia que o ajudarão a entender a proteção de conteúdo com os serviços de mídia.

## <a name="main-components-of-a-content-protection-system"></a>Componentes principais de um sistema de proteção de conteúdo

Para concluir com êxito o seu sistema de proteção de conteúdo, você precisa compreender totalmente o escopo do esforço. As seções a seguir fornecem uma visão geral de três partes que você precisa implementar.

> [!NOTE]
> É altamente recomendável que você focalize e teste completamente cada parte das seções a seguir antes de passar para a próxima parte. Para testar seu sistema de proteção de conteúdo, use as ferramentas especificadas nas seções.

### <a name="media-services-code"></a>Código dos serviços de mídia
  
O [exemplo de DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) mostra como implementar um sistema de vários DRM com os serviços de mídia v3 usando o .net. Ele também mostra como usar o serviço de distribuição de chaves/licença dos serviços de mídia.
  
Pode encriptar cada elemento com vários tipos de encriptação (AES-128, PlayReady, Widevine, FairPlay). Para ver o que faz sentido combinar, consulte [protocolos de streaming e tipos de criptografia](#streaming-protocols-and-encryption-types).

O exemplo mostra como:

1. Criar e configurar uma [política de chave de conteúdo](content-key-policy-concept.md).

   Você cria uma política de chave de conteúdo para configurar como a chave de conteúdo (que fornece acesso seguro aos seus ativos) é entregue aos clientes finais:  

   * Defina a autorização de entrega de licença. Especifique a lógica da verificação de autorização com base em declarações no token Web JSON (JWT).
   * Configure as licenças [PlayReady](playready-license-template-overview.md), [Widevine](widevine-license-template-overview.md)e/ou [Fairplay](fairplay-license-overview.md) . Os modelos permitem que você configure direitos e permissões para cada um dos DRMs.

     ```
     ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
     ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
     ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
     ```

2. Crie um [localizador de streaming](streaming-locators-concept.md) configurado para transmitir o ativo criptografado.
  
   O localizador de streaming deve ser associado a uma [política de streaming](streaming-policy-concept.md). No exemplo, definimos `StreamingLocator.StreamingPolicyName` como a política "Predefined_MultiDrmCencStreaming".

   As criptografias PlayReady e Widevine são aplicadas e a chave é entregue ao cliente de reprodução com base nas licenças DRM configuradas. Se você também quiser criptografar seu fluxo com CBCS (FairPlay), use a política "Predefined_MultiDrmStreaming".

   O localizador de streaming também está associado à política de chave de conteúdo que você definiu.

3. Crie um token de teste.

   O método `GetTokenAsync` mostra como criar um token de teste.
4. Crie a URL de streaming.

   O método `GetDASHStreamingUrlAsync` mostra como criar a URL de streaming. Nesse caso, a URL transmite o conteúdo do traço.

### <a name="player-with-an-aes-or-drm-client"></a>Player com um cliente AES ou DRM

Um aplicativo de player de vídeo baseado em um SDK do Player (nativo ou baseado em navegador) precisa atender aos seguintes requisitos:

* O SDK do Player dá suporte aos clientes DRM necessários.
* O SDK do Player dá suporte aos protocolos de streaming necessários: Smooth, DASH e/ou HTTP Live Streaming (HLS).
* O SDK do player pode lidar com a passagem de um token JWT em uma solicitação de aquisição de licença.

Você pode criar um player usando a [API player de mídia do Azure](https://amp.azure.net/libs/amp/latest/docs/). Use a [API player de mídia do Azure ProtectionInfo](https://amp.azure.net/libs/amp/latest/docs/) para especificar qual tecnologia de DRM usar em diferentes plataformas DRM.

Para testar o conteúdo criptografado AES ou CENC (Widevine e/ou PlayReady), você pode usar [player de mídia do Azure](https://aka.ms/azuremediaplayer). Certifique-se de selecionar **Opções avançadas** e verifique suas opções de criptografia.

Se você quiser testar o conteúdo criptografado do FairPlay, use [este player de teste](https://aka.ms/amtest). O Player dá suporte a Widevine, PlayReady e FairPlay DRMs, juntamente com criptografia de chave não criptografada AES-128.

Escolha o navegador certo para testar diferentes DRMs:

* Chrome, Opera ou Firefox para Widevine.
* Microsoft Edge ou Internet Explorer 11 para PlayReady.
* Safari no macOS para FairPlay.

### <a name="security-token-service"></a>Serviço de token de segurança

Um serviço de token de segurança (STS) emite JWT como o token de acesso para acesso a recursos de back-end. Você pode usar o serviço de entrega de chave/licença dos serviços de mídia do Azure como o recurso de back-end. Um STS tem que definir as seguintes coisas:

* Emissor e público (ou escopo).
* Declarações, que dependem de requisitos de negócios na proteção de conteúdo.
* Verificação simétrica ou assimétrica para verificação de assinatura.
* Suporte à substituição de chave (se necessário).

Você pode usar [essa ferramenta STS](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) para testar o STS. Ele dá suporte a todos os três tipos de chaves de verificação: simétrico, assimétrico ou Azure Active Directory (Azure AD) com substituição de chave.

## <a name="streaming-protocols-and-encryption-types"></a>Protocolos de transmissão em fluxo e tipos de encriptação

Você pode usar os serviços de mídia para entregar o conteúdo criptografado dinamicamente com a chave não criptografada AES ou a criptografia DRM usando PlayReady, Widevine ou FairPlay. No momento, você pode criptografar os formatos HLS, MPEG DASH e Smooth Streaming. Cada protocolo dá suporte aos seguintes métodos de criptografia.

### <a name="hls"></a>HLS

O protocolo HLS dá suporte aos seguintes formatos de contêiner e esquemas de criptografia:

|Formato do contêiner|Esquema de criptografia|Exemplo de URL|
|---|---|---|
|Todos|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2-TS |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF (fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2-TS |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF (fmp4) |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

HLS/CMAF + FairPlay (incluindo HEVC/H. 265) tem suporte nos seguintes dispositivos:

* iOS 11 ou posterior.
* iPhone 8 ou posterior.
* MacOS High Sierra com CPU Intel 7 geração.

### <a name="mpeg-dash"></a>MPEG-DASH

O protocolo MPEG-DASH dá suporte aos seguintes formatos de contêiner e esquemas de criptografia:

|Formato do contêiner|Esquema de criptografia|Exemplos de URL
|---|---|---|
|Todos|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF (fmp4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF (fmp4)|CENC (Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>Transmissão em Fluxo Uniforme

O protocolo Smooth Streaming dá suporte aos seguintes formatos de contêiner e esquemas de criptografia.

|Protocolo|Formato do contêiner|Esquema de criptografia|
|---|---|---|
|fMP4|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|

### <a name="browsers"></a>Navigator

Os navegadores comuns dão suporte aos seguintes clientes DRM:

|Browser|Encriptação|
|---|---|
|Chrome|Widevine|
|Microsoft Edge, Internet Explorer 11|PlayReady|
|Janelas|Widevine|
|Vela|Widevine|
|Safari|FairPlay|

## <a name="controlling-content-access"></a>Controlando o acesso ao conteúdo

Você pode controlar quem tem acesso ao seu conteúdo Configurando a política de chave de conteúdo. Os Media Services suportam várias formas de autorização de utilizadores que efetuam pedidos de chave. O cliente (Player) deve atender à política antes que a chave possa ser entregue ao cliente. A política de chave de conteúdo pode ter restrição de *token* ou *aberta* .

Uma política de chave de conteúdo com restrição aberta pode ser usada quando você deseja emitir a licença para qualquer pessoa sem autorização. Por exemplo, se sua receita for baseada no AD e não for baseada em assinatura.  

Com uma política de chave de conteúdo restrita por token, a chave de conteúdo é enviada somente para um cliente que apresenta um token JWT válido ou um token Web simples (SWT) na solicitação de licença/chave. Esse token deve ser emitido por um STS.

Você pode usar o Azure AD como um STS ou implantar um [STS personalizado](#using-a-custom-sts). O STS deve ser configurado para criar um token assinado com a chave especificada e as declarações de problema que você especificou na configuração de restrição de token. O serviço de distribuição de chaves/licença dos serviços de mídia retorna a licença ou chave solicitada ao cliente se ambas as condições existirem:

* O token é válido.
* As declarações no token correspondem àquelas configuradas para a licença ou chave.

Ao configurar a política restrita por token, você deve especificar os parâmetros de chave de verificação primária, emissor e público. A chave de verificação primária contém a chave com a qual o token foi assinado. O emissor é o STS que emite o token. O público, às vezes chamado de escopo, descreve a intenção do token ou o recurso ao qual o token autoriza o acesso. O serviço de distribuição de chaves/licença dos serviços de mídia valida que esses valores no token correspondem aos valores no modelo.

### <a name="token-replay-prevention"></a>Prevenção de reprodução de token

O recurso de *prevenção de reprodução de token* permite que os clientes dos serviços de mídia definam um limite de quantas vezes o mesmo token pode ser usado para solicitar uma chave ou uma licença. O cliente pode adicionar uma declaração do tipo `urn:microsoft:azure:mediaservices:maxuses` no token, em que o valor é o número de vezes que o token pode ser usado para adquirir uma licença ou chave. Todas as solicitações subsequentes com o mesmo token para a distribuição de chaves retornarão uma resposta não autorizada. Consulte Como adicionar a declaração no exemplo de [DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Considerações

* Os clientes devem ter controle sobre a geração de tokens. A declaração precisa ser colocada no próprio token.
* Ao usar esse recurso, as solicitações com tokens cuja hora de expiração é mais de uma hora longe da hora em que a solicitação é recebida são rejeitadas com uma resposta não autorizada.
* Os tokens são identificados exclusivamente por sua assinatura. Qualquer alteração na carga (por exemplo, atualizar para a hora de expiração ou a declaração) altera a assinatura do token e ela conta como um novo token que a distribuição de chaves não chegou antes.
* A reprodução falhará se o token exceder o valor de `maxuses` definido pelo cliente.
* Esse recurso pode ser usado para todo o conteúdo protegido existente (somente o token emitido precisa ser alterado).
* Esse recurso funciona com o JWT e o SWT.

## <a name="using-a-custom-sts"></a>Usando um STS personalizado

Um cliente pode optar por usar um STS personalizado para fornecer tokens. Os motivos incluem:

* O IDP (provedor de identidade) usado pelo cliente não dá suporte a STS. Nesse caso, um STS personalizado pode ser uma opção.
* O cliente pode precisar de um controle mais flexível ou mais rígido para integrar o STS ao sistema de cobrança do assinante do cliente.

   Por exemplo, um operador de serviço [Ott](https://en.wikipedia.org/wiki/Over-the-top_media_services) pode oferecer vários pacotes de assinante, como Premium, básico e esportes. O operador pode querer corresponder as declarações em um token com o pacote de um assinante para que somente o conteúdo em um pacote específico seja disponibilizado. Nesse caso, um STS personalizado fornece a flexibilidade e o controle necessários.

* Para incluir declarações personalizadas no token para selecionar entre diferentes ContentKeyPolicyOptions com parâmetros de licença de DRM diferentes (uma licença de assinatura em vez de uma licença de aluguel).
* Para incluir uma declaração que representa o identificador de chave de conteúdo da chave à qual o token concede acesso.

Quando você usa um STS personalizado, deve-se fazer duas alterações:

* Ao configurar o serviço de entrega de licença para um ativo, você precisa especificar a chave de segurança usada para verificação pelo STS personalizado em vez da chave atual do Azure AD.
* Quando um token JTW é gerado, uma chave de segurança é especificada em vez da chave privada do certificado X509 atual no Azure AD.

Há dois tipos de chaves de segurança:

* Chave simétrica: a mesma chave é usada para gerar e verificar um JWT.
* Chave assimétrica: um par de chaves pública-privada em um certificado X509 é usado com uma chave privada para criptografar/gerar um JWT e com a chave pública para verificar o token.

Se você usar .NET Framework/C# como sua plataforma de desenvolvimento, o certificado X509 usado para uma chave de segurança assimétrica deverá ter um comprimento de chave de pelo menos 2048. Esse comprimento de chave é um requisito da classe System. IdentityModel. Tokens. X509AsymmetricSecurityKey em .NET Framework. Caso contrário, a seguinte exceção será gerada: IDX10630: o ' System. IdentityModel. Tokens. X509AsymmetricSecurityKey ' para assinatura não pode ser menor que ' 2048 ' bits.

## <a name="custom-key-and-license-acquisition-url"></a>URL de aquisição de licença e chave personalizada

Use os modelos a seguir se desejar especificar um serviço de entrega de chave/licença diferente (não serviços de mídia). Os dois campos substituíveis nos modelos existem para que você possa compartilhar sua política de streaming em vários ativos, em vez de criar uma política de streaming por ativo. 

* `EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate`: modelo para a URL do serviço personalizado que fornece chaves para os players do usuário final. Ele não é necessário quando você está usando os serviços de mídia do Azure para emitir chaves. 

   O modelo oferece suporte a tokens substituíveis que o serviço atualizará em tempo de execução com o valor específico para a solicitação.  Os valores de token com suporte no momento são:
   * `{AlternativeMediaId}`, que é substituído pelo valor de StreamingLocatorId. AlternativeMediaId.
   * `{ContentKeyId}`, que é substituído pelo valor do identificador da chave solicitada.
* `StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate`: modelo para a URL do serviço personalizado que entrega licenças para players do usuário final. Ele não é necessário quando você está usando os serviços de mídia do Azure para emitir licenças.

   O modelo oferece suporte a tokens substituíveis que o serviço atualizará em tempo de execução com o valor específico para a solicitação. Os valores de token com suporte no momento são:  
   * `{AlternativeMediaId}`, que é substituído pelo valor de StreamingLocatorId. AlternativeMediaId.
   * `{ContentKeyId}`, que é substituído pelo valor do identificador da chave solicitada. 
* `StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate`: igual ao modelo anterior, somente para Widevine. 
* `StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate`: igual ao modelo anterior, somente para FairPlay.  

Por exemplo:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

`ContentKeyId` tem um valor da chave solicitada. Você pode usar `AlternativeMediaId` se quiser mapear a solicitação para uma entidade no seu lado. Por exemplo, `AlternativeMediaId` pode ser usado para ajudá-lo a Pesquisar permissões.

 Para obter exemplos de REST que usam URLs de licença/chave personalizadas, consulte [políticas de streaming – criar](https://docs.microsoft.com/rest/api/media/streamingpolicies/create).

## <a name="troubleshoot"></a>Resolução de problemas

Se você receber o erro de `MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY`, certifique-se de especificar a política de streaming apropriada.

Se você receber erros que terminem com `_NOT_SPECIFIED_IN_URL`, certifique-se de especificar o formato de criptografia na URL. Um exemplo é `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`. Consulte [protocolos de streaming e tipos de criptografia](#streaming-protocols-and-encryption-types).

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, envie comentários, obtenha atualizações

Confira o artigo [da Comunidade dos serviços de mídia do Azure](media-services-community.md) para ver diferentes maneiras que você pode fazer perguntas, fornecer comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Passos seguintes

* [Proteger com criptografia AES](protect-with-aes128.md)
* [Proteger com DRM](protect-with-drm.md)
* [Projetar o sistema de proteção de conteúdo de vários DRM com o controle de acesso](design-multi-drm-system-with-access-control.md)
* [Criptografia do lado do armazenamento](storage-account-concept.md#storage-side-encryption)
* [Perguntas mais frequentes](frequently-asked-questions.md)
* [Manipulador de token Web JSON](https://docs.microsoft.com/dotnet/framework/security/json-web-token-handler)
