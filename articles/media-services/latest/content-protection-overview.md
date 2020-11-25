---
title: Proteja o seu conteúdo com a encriptação dinâmica V3 dos Media Services
titleSuffix: Azure Media Services
description: Saiba mais sobre a proteção de conteúdos com encriptação dinâmica, protocolos de streaming e tipos de encriptação em Azure Media Services.
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
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 5d6530cf7b8d8611ff23a3517112cb0aa7442d6d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95971179"
---
# <a name="protect-your-content-with-media-services-dynamic-encryption"></a>Proteja o seu conteúdo com encriptação dinâmica dos Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Utilize a Azure Media Services para ajudar a proteger os seus meios de comunicação a partir do momento em que deixa o computador durante todo o processo de armazenamento, processamento e entrega. Com os Media Services, pode fornecer o seu conteúdo ao vivo e a pedido encriptado dinamicamente com o Advanced Encryption Standard (AES-128) ou qualquer um dos três principais sistemas de gestão de direitos digitais (DRM): Microsoft PlayReady, Google Widevine e Apple FairPlay. Os Media Services também fornecem um serviço para a entrega de chaves AES e licenças DRM (PlayReady, Widevine e FairPlay) a clientes autorizados. Se o conteúdo for encriptado com uma chave clara AES e for enviado por HTTPS, não é claro até chegar ao cliente.

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

Nos Serviços de Comunicação Social v3, uma chave de conteúdo está associada ao Localizador de Streaming (ver [exemplo).](protect-with-aes128.md) Se utilizar o serviço de entrega de chaves media, pode deixar que a Azure Media Services gere a chave de conteúdo para si. A chave de conteúdo deve ser gerada se estiver a utilizar o seu próprio serviço de entrega de chaves, ou se precisar de lidar com um cenário de alta disponibilidade onde precisa de ter a mesma chave de conteúdo em dois centros de dados.

Quando um stream é solicitado por um jogador, os Media Services utilizam a chave especificada para encriptar dinamicamente o seu conteúdo utilizando a chave clara AES ou encriptação DRM. Para desencriptar o fluxo, o leitor solicita a chave do serviço de entrega de chaves media ou do serviço de entrega chave especificado. Para decidir se o utilizador está autorizado a obter a chave, o serviço avalia a política de chave de conteúdo que especificou para a chave.

Pode utilizar a API REST ou uma biblioteca de clientes dos Media Services para configurar as políticas de autorização e autenticação para as suas licenças e chaves.

A imagem a seguir ilustra o fluxo de trabalho para a proteção de conteúdos dos Serviços de Comunicação Social:

![Fluxo de trabalho para proteção de conteúdos dos Serviços de Mídia](./media/content-protection/content-protection.svg)
  
&#42; a *encriptação Dynamic suporta a chave clara AES-128, CBCS e CENC. Para mais detalhes, consulte a [matriz de suporte.](#streaming-protocols-and-encryption-types)*

Este artigo explica conceitos e terminologia que o ajudam a entender a proteção de conteúdos com os Media Services.

## <a name="main-components-of-a-content-protection-system"></a>Principais componentes de um sistema de proteção de conteúdos

Para completar com sucesso o seu sistema de proteção de conteúdos, é necessário compreender plenamente o alcance do esforço. As secções seguintes dão uma visão geral de três partes que precisa de implementar.

> [!NOTE]
> Recomendamos vivamente que se concentre e teste completamente cada peça nas seguintes secções antes de passar para a próxima parte. Para testar o seu sistema de proteção de conteúdos, utilize as ferramentas especificadas nas secções.

### <a name="media-services-code"></a>Código dos Serviços de Mídia
  
A [amostra drm](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) mostra-lhe como implementar um sistema multi-DRM com Serviços de Mídia v3 através da utilização de .NET. Também mostra como usar a licença/serviço de entrega de serviços de comunicação.
  
Pode encriptar cada elemento com vários tipos de encriptação (AES-128, PlayReady, Widevine, FairPlay). Para ver o que faz sentido combinar, consulte [protocolos de streaming e tipos de encriptação.](#streaming-protocols-and-encryption-types)

O exemplo mostra como:

1. Criar e configurar uma [política chave de conteúdo](content-key-policy-concept.md).

   Cria uma política de chave de conteúdo para configurar como a chave de conteúdo (que fornece acesso seguro aos seus ativos) é entregue aos clientes finais:  

   * Defina a autorização de entrega de licença. Especifique a lógica do controlo de autorização com base em reclamações em JSON Web Token (JWT).
   * Configure as licenças [PlayReady](playready-license-template-overview.md), [Widevine](widevine-license-template-overview.md)e/ou [FairPlay.](fairplay-license-overview.md) Os modelos permitem configurar direitos e permissões para cada um dos DRMs.

     ```
     ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
     ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
     ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
     ```

2. Crie um [localizador de streaming](streaming-locators-concept.md) configurado para transmitir o ativo encriptado.
  
   O localizador de streaming tem de ser associado a uma [política de streaming.](streaming-policy-concept.md) No exemplo, definimos `StreamingLocator.StreamingPolicyName` a política "Predefined_MultiDrmCencStreaming".

   As encriptações PlayReady e Widevine são aplicadas e a chave é entregue ao cliente de reprodução com base nas licenças DE DRM configuradas. Se também pretender encriptar o seu fluxo com o CBCS (FairPlay), utilize a política "Predefined_MultiDrmStreaming".

   O localizador de streaming também está associado à política de chave de conteúdo que definiu.

3. Criar um símbolo de teste.

   O `GetTokenAsync` método mostra como criar um símbolo de teste.
4. Construa a URL de streaming.

   O `GetDASHStreamingUrlAsync` método mostra como construir o URL de streaming. Neste caso, o URL transmite o conteúdo do DASH.

### <a name="player-with-an-aes-or-drm-client"></a>Jogador com um cliente AES ou DRM

Uma aplicação de leitor de vídeo baseada num jogador SDK (nativo ou baseado no navegador) precisa de satisfazer os seguintes requisitos:

* O player SDK suporta os clientes DRM necessários.
* O leitor SDK suporta os protocolos de streaming necessários: Smooth, DASH e/ou HTTP Live Streaming (HLS).
* O jogador SDK pode lidar com a passagem de um token JWT num pedido de aquisição de licença.

Pode criar um leitor utilizando a API do [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/). Utilize o [Azure Media Player ProtectionInfo API](https://amp.azure.net/libs/amp/latest/docs/) para especificar qual a tecnologia DRM a utilizar em diferentes plataformas DE DRM.

Para testar conteúdo encriptado AES ou CENC (Widevine e/ou PlayReady), pode utilizar [o Azure Media Player](https://aka.ms/azuremediaplayer). Certifique-se de que seleciona **opções Avançadas** e verifique as suas opções de encriptação.

Se pretender testar o conteúdo encriptado FairPlay, utilize [este leitor de testes](https://aka.ms/amtest). O jogador suporta DrMs Widevine, PlayReady e FairPlay, juntamente com encriptação clara de chave AES-128.

Escolha o navegador certo para testar diferentes DRMs:

* Chrome, Opera ou Firefox para Widevine.
* Microsoft Edge ou Internet Explorer 11 para PlayReady.
* Safari no macOS para FairPlay.

### <a name="security-token-service"></a>Serviço de ficha de segurança

Um serviço de ficha de segurança (STS) emite jWT como o símbolo de acesso para acesso de recursos back-end. Pode utilizar a licença/serviço de entrega da Azure Media Services como recurso de back-end. Uma STS tem de definir as seguintes coisas:

* Emitente e público (ou âmbito).
* Sinistros, que dependem dos requisitos empresariais na proteção de conteúdos.
* Verificação simétrica ou assimétrica para verificação de assinaturas.
* Suporte de capotamento da chave (se necessário).

Pode utilizar [esta ferramenta STS](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) para testar o STS. Suporta todos os três tipos de chaves de verificação: simétrica, assimétrica ou Azure Ative Directory (Azure AD) com capotamento de chaves.

## <a name="streaming-protocols-and-encryption-types"></a>Protocolos de transmissão em fluxo e tipos de encriptação

Pode utilizar os Serviços de Comunicação social para fornecer o seu conteúdo encriptado dinamicamente com a chave AES clara ou encriptação DRM utilizando PlayReady, Widevine ou FairPlay. Atualmente, pode encriptar os formatos HLS, MPEG DASH e Smooth Streaming. Cada protocolo suporta os seguintes métodos de encriptação.

### <a name="hls"></a>HLS

O protocolo HLS suporta os seguintes formatos de contentores e esquemas de encriptação:

|Formato do recipiente|Esquema de encriptação|Exemplo de URL|
|---|---|---|
|Todos|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2-TS |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF(fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2-TS |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF(fmp4) |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

O HLS/CMAF + FairPlay (incluindo HEVC/H.265) é suportado nos seguintes dispositivos:

* iOS 11 ou posterior.
* iPhone 8 ou mais tarde.
* macOS High Sierra com CPU de 7ª Geração Intel.

### <a name="mpeg-dash"></a>MPEG-DASH

O protocolo MPEG-DASH suporta os seguintes formatos de contentores e esquemas de encriptação:

|Formato do recipiente|Esquema de encriptação|Exemplos de URL
|---|---|---|
|Todos|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF(fmp4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC (Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>Transmissão em Fluxo Uniforme

O protocolo Smooth Streaming suporta os seguintes formatos de contentores e esquemas de encriptação.

|Protocolo|Formato do recipiente|Esquema de encriptação|
|---|---|---|
|fMP4|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|
|fMP4 | PIFF 1.1 (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=piff)`|

> [!NOTE]
> O suporte PIFF 1.1 é fornecido como uma solução compatível para trás para Smart TV (Samsung, LG) que implementou a versão inicial "Silverlight" da Common Encryption. Recomenda-se apenas utilizar o formato PIFF sempre que necessário para suporte de legacey Samsung ou LG Smart TVs enviados entre 2009-2015 que suportavam a versão PIFF 1.1 da encriptação PlayReady. 

### <a name="browsers"></a>Navegadores

Os navegadores comuns suportam os seguintes clientes DRM:

|Browser|Encriptação|
|---|---|
|Chrome|Widevine|
|Microsoft Edge, Internet Explorer 11|PlayReady|
|Firefox|Widevine|
|Ópera|Widevine|
|Safari|FairPlay|

## <a name="controlling-content-access"></a>Controlar o acesso aos conteúdos

Pode controlar quem tem acesso ao seu conteúdo configurando a política de chave de conteúdo. Os Media Services suportam várias formas de autorização de utilizadores que efetuam pedidos de chave. O cliente (jogador) deve cumprir a apólice antes de a chave poder ser entregue ao cliente. A política de chave de conteúdo pode ter restrições *abertas* ou *simbólicas.*

Uma política de chave de conteúdo restrito aberto pode ser usada quando pretender emitir licença a qualquer pessoa sem autorização. Por exemplo, se a sua receita for baseada em anúncios e não baseada em subscrições.  

Com uma política de chave de conteúdo restrito a ficha simbólica, a chave de conteúdo é enviada apenas para um cliente que apresenta um token JWT válido ou um simples token web (SWT) no pedido de licença/chave. Este símbolo deve ser emitido por uma STS.

Pode utilizar o Azure AD como STS ou implementar uma [STS personalizada.](#using-a-custom-sts) O STS deve ser configurado para criar um símbolo assinado com a chave especificada e emitir alegações que especificou na configuração da restrição simbólica. A licença/serviço de entrega de serviços de comunicação devolve a licença ou chave solicitada ao cliente se ambas estas condições existirem:

* O símbolo é válido.
* As reclamações no símbolo correspondem às configuradas para a licença ou chave.

Ao configurar a política restrita de token, deve especificar a chave de verificação primária, o emitente e os parâmetros do público. A chave de verificação primária contém a chave com a que o símbolo foi assinado. O emitente é o STS que emite o símbolo. O público, por vezes chamado de âmbito, descreve a intenção do símbolo ou do recurso a que o símbolo autoriza o acesso. A licença/serviço de entrega de serviços de comunicação valida que estes valores no token correspondem aos valores do modelo.

### <a name="token-replay-prevention"></a>Prevenção de repetição de token

A funcionalidade *De Prevenção de Repetição token* permite que os clientes dos Media Services estabeleçam um limite para quantas vezes o mesmo token pode ser usado para solicitar uma chave ou uma licença. O cliente pode adicionar uma reclamação de tipo `urn:microsoft:azure:mediaservices:maxuses` no token, onde o valor é o número de vezes que o token pode ser usado para adquirir uma licença ou chave. Todos os pedidos subsequentes com o mesmo sinal para a Entrega de Chaves devolverão uma resposta não autorizada. Veja como adicionar a reclamação na [amostra de DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Considerações

* Os clientes devem ter controlo sobre a geração simbólica. A reivindicação tem de ser colocada no próprio símbolo.
* Ao utilizar esta funcionalidade, os pedidos com fichas cujo prazo de validade esteja a mais de uma hora do momento em que o pedido é recebido são rejeitados com uma resposta não autorizada.
* Os tokens são identificados exclusivamente pela sua assinatura. Qualquer alteração na carga útil (por exemplo, atualização para o prazo de validade ou a reclamação) altera a assinatura do token e contará como um novo símbolo que a Entrega da Chave nunca encontrou antes.
* A reprodução falha se o token exceder o `maxuses` valor definido pelo cliente.
* Esta funcionalidade pode ser utilizada para todos os conteúdos protegidos existentes (apenas o token emitido precisa de ser alterado).
* Esta funcionalidade funciona tanto com o JWT como com o SWT.

## <a name="using-a-custom-sts"></a>Usando uma STS personalizada

Um cliente pode optar por usar uma STS personalizada para fornecer fichas. As razões incluem:

* O fornecedor de identidade (IDP) utilizado pelo cliente não suporta STS. Neste caso, uma STS personalizada pode ser uma opção.
* O cliente poderá necessitar de um controlo mais flexível ou mais apertado para integrar o STS com o sistema de faturação de assinantes do cliente.

   Por exemplo, um operador de serviço [OTT](https://en.wikipedia.org/wiki/Over-the-top_media_services) pode oferecer vários pacotes de assinantes, tais como premium, básico e desportivo. O operador pode querer comparar as reclamações num token com o pacote de um assinante para que apenas o conteúdo de um pacote específico seja disponibilizado. Neste caso, uma STS personalizada proporciona a flexibilidade e controlo necessários.

* Para incluir reclamações personalizadas no token para selecionar entre diferentes ContentKeyPolicyOptions com diferentes parâmetros de licença DRM (uma licença de subscrição versus uma licença de aluguer).
* Incluir uma reclamação que represente o identificador chave de conteúdo da chave a que o símbolo concede acesso.

Quando utilizar uma STS personalizada, devem ser feitas duas alterações:

* Ao configurar o serviço de entrega de licenças para um ativo, precisa de especificar a chave de segurança utilizada para a verificação pelo STS personalizado em vez da chave atual do Azure AD.
* Quando um token JTW é gerado, uma chave de segurança é especificada em vez da chave privada do certificado X509 atual em Azure AD.

Existem dois tipos de chaves de segurança:

* Chave simétrica: A mesma chave é utilizada para gerar e verificar um JWT.
* Chave assimétrica: Um par de chaves público-privado num certificado X509 é usado com uma chave privada para encriptar/gerar um JWT e com a chave pública para verificar o token.

Se utilizar o .NET Framework/C# como plataforma de desenvolvimento, o certificado X509 utilizado para uma chave de segurança assimétrica deve ter um comprimento de chave de pelo menos 2048. Este comprimento chave é um requisito do sistema de classe.IdentityModel.Tokens.X509AsymmetricSecurityKey em .NET Framework. Caso contrário, a seguinte exceção é lançada: IDX10630: O 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' para a assinatura não pode ser menor do que as bits de '2048'.

## <a name="custom-key-and-license-acquisition-url"></a>CHAVE personalizada e URL de aquisição de licença

Utilize os seguintes modelos se pretender especificar um serviço de entrega de licença/chave diferente (não serviços de mídia). Os dois campos substituíveis nos modelos estão lá para que possa partilhar a sua política de streaming em muitos ativos em vez de criar uma política de streaming por ativo. 

* `EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate`: Modelo para o URL do serviço personalizado que entrega chaves aos jogadores do utilizador final. Não é necessário quando se está a usar o Azure Media Services para emitir chaves. 

   O modelo suporta fichas substituíveis que o serviço irá atualizar no tempo de execução com o valor específico do pedido.  Os valores simbólicos atualmente suportados são:
   * `{AlternativeMediaId}`, que é substituído pelo valor de StreamingLocatorId.AlternativeMediaId.
   * `{ContentKeyId}`, que é substituído pelo valor do identificador da chave solicitada.
* `StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate`: Modelo para o URL do serviço personalizado que entrega licenças aos jogadores de utilizador final. Não é necessário quando se está a usar a Azure Media Services para emitir licenças.

   O modelo suporta fichas substituíveis que o serviço irá atualizar no tempo de execução com o valor específico do pedido. Os valores simbólicos atualmente suportados são:  
   * `{AlternativeMediaId}`, que é substituído pelo valor de StreamingLocatorId.AlternativeMediaId.
   * `{ContentKeyId}`, que é substituído pelo valor do identificador da chave solicitada. 
* `StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate`: O mesmo que o modelo anterior, apenas para o Widevine. 
* `StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate`: O mesmo que o modelo anterior, apenas para FairPlay.  

Por exemplo:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

`ContentKeyId` tem um valor da chave solicitada. Pode usar `AlternativeMediaId` se quiser mapear o pedido a uma entidade do seu lado. Por exemplo, `AlternativeMediaId` pode ser usado para ajudá-lo a procurar permissões.

Para exemplos REST que usam URLs de aquisição de licença/chave personalizados, consulte [Políticas de Streaming - Criar](/rest/api/media/streamingpolicies/create).

> [!NOTE]
> Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="troubleshoot"></a>Resolução de problemas

Se tiver o `MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY` erro, certifique-se de que especifica a política de streaming apropriada.

Se tiver erros que terminam com `_NOT_SPECIFIED_IN_URL` , certifique-se de que especifica o formato de encriptação no URL. Um exemplo é `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`. Consulte [os protocolos de streaming e os tipos de encriptação.](#streaming-protocols-and-encryption-types)

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Consulte o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Media Services.

## <a name="next-steps"></a>Passos seguintes

* [Proteger com encriptação AES](protect-with-aes128.md)
* [Proteger com DRM](protect-with-drm.md)
* [Conceber sistema de proteção de conteúdos multi-DRM com controlo de acesso](design-multi-drm-system-with-access-control.md)
* [Encriptação lateral do armazenamento](storage-account-concept.md#storage-side-encryption)
* [Perguntas mais frequentes](frequently-asked-questions.md)
* [Manipulador de token web JSON](/dotnet/framework/security/json-web-token-handler)
