---
title: Proteja o seu conteúdo com encriptação dinâmica V3 dos Media Services
titleSuffix: Azure Media Services
description: Conheça a proteção de conteúdos com encriptação dinâmica, protocolos de streaming e tipos de encriptação nos Serviços De Mídia Azure.
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
ms.openlocfilehash: 18e80383bfcbebc6a442663c141100faa56fd061
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393473"
---
# <a name="protect-your-content-with-media-services-dynamic-encryption"></a>Proteja o seu conteúdo com encriptação dinâmica de Media Services

Utilize o Azure Media Services para ajudar a proteger os seus meios de comunicação a partir do momento em que deixa o computador até ao armazenamento, processamento e entrega. Com os serviços de multimédia, pode fornecer seu conteúdo ao vivo e sob demanda dinamicamente encriptado com o Advanced Encryption Standard (AES-128) ou qualquer um dos sistemas de gestão (DRM) três direitos digitais principais: Microsoft PlayReady, Widevine da Google e Apple FairPlay. Serviços de multimédia também fornecem um serviço para entrega de chaves AES e o DRM (PlayReady, Widevine e FairPlay) licenças para os clientes autorizados.  

Nos Serviços de Media v3, uma chave de conteúdo está associada ao Localizador de Streaming (ver [exemplo).](protect-with-aes128.md) Se utilizar o serviço de entrega de chaves media services, pode permitir que o Azure Media Services gere a chave de conteúdo para si. A chave de conteúdo deve ser gerada se estiver a utilizar o seu próprio serviço de entrega de chaves, ou se precisar de lidar com um cenário de alta disponibilidade onde precisa de ter a mesma chave de conteúdo em dois centros de dados.

Quando um fluxo é solicitado por um jogador, serviços de multimédia utiliza a chave especificada para encriptar dinamicamente o seu conteúdo através da utilização de chave não encriptada AES ou encriptação de DRM. Para descriptografar o fluxo, o jogador solicita a chave do serviço de entrega de chave de serviços de multimédia ou o serviço de entrega de chave que especificou. Para decidir se o utilizador está autorizado a obter a chave, o serviço avalia a política de chave de conteúdo que especificou para a chave.

Pode utilizar a API REST ou uma biblioteca de cliente dos serviços de multimédia para configurar políticas de autorização e autenticação para as suas licenças e chaves.

A imagem seguinte ilustra o fluxo de trabalho para a proteção de conteúdos dos Serviços de Media:

![Fluxo de trabalho para proteção de conteúdos dos Serviços de Media](./media/content-protection/content-protection.svg)
  
&#42;*A encriptação dinâmica suporta a chave clara AES-128, CBCS e CENC. Para mais detalhes, consulte a [matriz](#streaming-protocols-and-encryption-types)de suporte .*

Este artigo explica conceitos e terminologia que o ajudam a entender a proteção de conteúdos com os Serviços de Media.

## <a name="main-components-of-a-content-protection-system"></a>Principais componentes de um sistema de proteção de conteúdos

Para completar com sucesso o seu sistema de proteção de conteúdos, precisa de compreender plenamente o âmbito do esforço. As seguintes secções dão uma visão geral de três partes que precisa de implementar.

> [!NOTE]
> Recomendamos vivamente que se concentre e teste totalmente cada peça nas seguintes secções antes de passar para a próxima parte. Para testar o seu sistema de proteção de conteúdos, utilize as ferramentas especificadas nas secções.

### <a name="media-services-code"></a>Código dos Serviços de Media
  
A [amostra de DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) mostra-lhe como implementar um sistema multi-DRM com Media Services v3 utilizando .NET. Também mostra como usar o serviço de entrega de licença/chave dos Serviços de Media.
  
Pode encriptar cada elemento com vários tipos de encriptação (AES-128, PlayReady, Widevine, FairPlay). Para ver o que faz sentido combinar, consulte protocolos de streaming e tipos de [encriptação.](#streaming-protocols-and-encryption-types)

O exemplo mostra como:

1. Criar e configurar uma [política de chave de conteúdo.](content-key-policy-concept.md)

   Cria uma política chave de conteúdo para configurar como a chave de conteúdo (que proporciona acesso seguro aos seus ativos) é entregue aos clientes finais:  

   * Defina a autorização de entrega da licença. Especifique a lógica do controlo de autorização com base em reclamações em JSON Web Token (JWT).
   * Configure as licenças [PlayReady,](playready-license-template-overview.md) [Widevine](widevine-license-template-overview.md)e/ou [FairPlay.](fairplay-license-overview.md) Os modelos permitem configurar direitos e permissões para cada uma das DM.

     ```
     ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
     ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
     ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
     ```

2. Crie um localizador de [streaming](streaming-locators-concept.md) configurado para transmitir o ativo encriptado.
  
   O localizador de streaming tem de ser associado a uma política de [streaming.](streaming-policy-concept.md) No exemplo, definimos `StreamingLocator.StreamingPolicyName` à política "Predefined_MultiDrmCencStreaming".

   As encriptações PlayReady e Widevine são aplicadas, e a chave é entregue ao cliente de reprodução com base nas licenças de DRM configuradas. Se também quiser encriptar o seu stream com CBCS (FairPlay), utilize a política "Predefined_MultiDrmStreaming".

   O localizador de streaming também está associado à política chave de conteúdo que definiu.

3. Crie um token de teste.

   O método `GetTokenAsync` mostra como criar um símbolo de teste.
4. Crie o URL de transmissão em fluxo.

   O método `GetDASHStreamingUrlAsync` mostra como construir o URL de streaming. Neste caso, o URL transmite o conteúdo DASH.

### <a name="player-with-an-aes-or-drm-client"></a>Jogador com cliente AES ou DRM

Uma aplicação de leitor de vídeo com base num player SDK (nativo ou baseada no browser) tem de cumprir os seguintes requisitos:

* O Player SDK suporta os clientes DRM necessários.
* O Leitor SDK suporta os protocolos de streaming necessários: Smooth, DASH e/ou HTTP Live Streaming (HLS).
* O jogador SDK pode lidar com a passagem de um símbolo JWT num pedido de aquisição de licença.

Pode criar um leitor utilizando a API do [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/). Utilize a [API Azure Media Player ProtectionInfo](https://amp.azure.net/libs/amp/latest/docs/) para especificar qual a tecnologia DRM a utilizar em diferentes plataformas de DRM.

Para testar o conteúdo encriptado AES ou CENC (Widevine e/ou PlayReady), pode utilizar o [Azure Media Player](https://aka.ms/azuremediaplayer). Certifique-se de que seleciona **opções Avançadas** e verifique as suas opções de encriptação.

Se quiser testar conteúdo encriptado fairPlay, utilize [este leitor de teste](https://aka.ms/amtest). O jogador suporta drMs Widevine, PlayReady e FairPlay, juntamente com encriptação de chave clara AES-128.

Escolha o navegador certo para testar diferentes DM:

* Cromo, Ópera ou Firefox para Widevine.
* Microsoft Edge ou Internet Explorer 11 para PlayReady.
* Safari no macOS para FairPlay.

### <a name="security-token-service"></a>Serviço de fichas de segurança

Um serviço de token de segurança (STS) emite jWT como o sinal de acesso para acesso a recursos de back-end. Pode utilizar o serviço de entrega de licença/chave azure Media Services como recurso de back-end. Um STS tem que definir as seguintes coisas:

* Emitente e público (ou âmbito).
* Reclamações, que dependem dos requisitos empresariais na proteção de conteúdos.
* Verificação simétrica ou assimétrica para verificação de assinaturas.
* Suporte de capotamento de chave (se necessário).

Pode utilizar [esta ferramenta STS](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) para testar o STS. Suporta os três tipos de teclas de verificação: simétrica, assimétrica ou Azure Ative Directory (Azure AD) com capotamento de chaves.

## <a name="streaming-protocols-and-encryption-types"></a>Protocolos de transmissão em fluxo e tipos de encriptação

Pode utilizar os serviços de multimédia para distribuir os seus conteúdos encriptados dinamicamente com a chave não encriptada AES ou encriptação de DRM com o PlayReady, Widevine e FairPlay. Atualmente, pode encriptar os formatos HLS, MPEG DASH e Smooth Streaming. Cada protocolo suporta os seguintes métodos de encriptação.

### <a name="hls"></a>HLS

O protocolo HLS suporta os seguintes formatos de contentores e esquemas de encriptação:

|Formato de contêiner|Esquema de encriptação|Exemplo de URL|
|---|---|---|
|Todos|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2-TS |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF(fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2-TS |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF(fmp4) |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

HLS/CMAF + FairPlay (incluindo HEVC/H.265) é suportado nos seguintes dispositivos:

* iOS 11 ou mais tarde.
* iPhone 8 ou mais tarde.
* MacOS High Sierra com CPU de 7ª Geração Intel.

### <a name="mpeg-dash"></a>MPEG-DASH

O protocolo MPEG-DASH suporta os seguintes formatos de contentores e esquemas de encriptação:

|Formato de contêiner|Esquema de encriptação|Exemplos de URL
|---|---|---|
|Todos|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF(fmp4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC (Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>Transmissão em Fluxo Uniforme

O protocolo Smooth Streaming suporta os seguintes formatos de contentores e esquemas de encriptação.

|Protocolo|Formato de contêiner|Esquema de encriptação|
|---|---|---|
|fMP4|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|

### <a name="browsers"></a>Browsers

Os navegadores comuns suportam os seguintes clientes DRM:

|Browser|Encriptação|
|---|---|
|Chrome|Widevine|
|Microsoft Edge, Internet Explorer 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="controlling-content-access"></a>Controlar o acesso aos conteúdos

Pode controlar quem tem acesso ao seu conteúdo ao configurar a política de chave de conteúdo. Os Media Services suportam várias formas de autorização de utilizadores que efetuam pedidos de chave. O cliente (leitor) tem de cumprir a política antes da chave pode ser entregue ao cliente. A política chave de conteúdo pode ter restrição *aberta* ou *simbólica.*

Uma política de chave de conteúdo restrita pode ser usada quando quiser emitir licença a qualquer pessoa sem autorização. Por exemplo, se as suas receitas forem baseadas em anúncios e não baseadas em subscrições.  

Com uma política de chave de conteúdo restrita a fichas, a chave de conteúdo é enviada apenas a um cliente que apresenta um símbolo JWT válido ou um simples token web (SWT) no pedido de licença/chave. Este símbolo deve ser emitido por uma STS.

Pode utilizar o Azure AD como STS ou implementar um [STS personalizado](#using-a-custom-sts). O STS deve ser configurado para criar um token assinado com as declarações de chave e o problema especificadas que especificou na configuração de restrição de token. O serviço de entrega de serviços de mídia/chave devolve a licença ou chave solicitada ao cliente se ambas estas condições existirem:

* O símbolo é válido.
* As alegações no símbolo coincidem com as configuradas para a licença ou chave.

Quando configurar a política restrita de fichas, deve especificar a chave de verificação primária, o emitente e os parâmetros do público. A chave de verificação primária contém a chave de que o token foi assinado com. O emitente é o STS que emite o símbolo. O público, por vezes chamado de âmbito, descreve a intenção do símbolo ou o recurso a que o símbolo autoriza o acesso. O serviço de entrega /chave media Services valida que estes valores no token correspondem aos valores do modelo.

### <a name="token-replay-prevention"></a>Prevenção de reprodução de token

A funcionalidade *de Prevenção* de Repetição de Token permite que os clientes dos Media Services estabeleçam um limite para quantas vezes o mesmo símbolo pode ser usado para solicitar uma chave ou uma licença. O cliente pode adicionar uma reclamação de tipo `urn:microsoft:azure:mediaservices:maxuses` no token, onde o valor é o número de vezes que o token pode ser usado para adquirir uma licença ou chave. Todos os pedidos subsequentes com o mesmo símbolo da Entrega da Chave devolverão uma resposta não autorizada. Veja como adicionar a reclamação na [amostra DEDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Considerações

* Os clientes devem ter controlo sobre a geração simbólica. A reivindicação tem de ser colocada no próprio símbolo.
* Ao utilizar esta funcionalidade, os pedidos com fichas cujo prazo de validade está a mais de uma hora do momento em que o pedido é recebido são rejeitados com uma resposta não autorizada.
* Os tokens são identificados exclusivamente pela sua assinatura. Qualquer alteração na carga útil (por exemplo, atualizar para o tempo de validade ou a reclamação) altera a assinatura do token e contará como um novo símbolo que a Key Delivery não encontrou antes.
* A reprodução falha se o símbolo tiver excedido o valor `maxuses` definido pelo cliente.
* Esta função pode ser utilizada para todos os conteúdos protegidos existentes (apenas o símbolo emitido precisa de ser alterado).
* Esta funcionalidade funciona tanto com jWT como SWT.

## <a name="using-a-custom-sts"></a>Usando um STS personalizado

Um cliente pode optar por usar um STS personalizado para fornecer fichas. Os motivos incluem:

* O fornecedor de identidade (IDP) utilizado pelo cliente não suporta STS. Neste caso, um STS personalizado pode ser uma opção.
* O cliente poderá ter um controlo para integrar o STS com o subscritor do cliente, sistema de faturação mais flexível ou maior.

   Por exemplo, um operador de serviço [OTT](https://en.wikipedia.org/wiki/Over-the-top_media_services) pode oferecer vários pacotes de subscritores, tais como premium, básico e desporto. O operador deve-se de acordo com as afirmações num token com o pacote de um subscritor para que apenas o conteúdo num pacote específico é disponibilizado. Neste caso, um STS personalizado fornece a flexibilidade necessária e o controle.

* Para incluir reclamações personalizadas no token para selecionar entre diferentes ContentKeyPolicyOptions com diferentes parâmetros de licença DEDRM (uma licença de subscrição versus uma licença de aluguer).
* Incluir uma reclamação que represente o identificador chave de conteúdo da chave a que o símbolo dá acesso.

Quando utilizar um STS personalizado, duas alterações devem ser feitas:

* Quando configurar o serviço de entrega de licença para um recurso, tem de especificar a chave de segurança utilizada para verificação pelo STS personalizado em vez da chave atual do Azure AD.
* Quando é gerado um token JTW, é especificada uma chave de segurança em vez da chave privada do atual X509 certificado no Azure AD.

Existem dois tipos de chaves de segurança:

* Chave simétrica: A mesma chave é utilizada para gerar e verificar um JWT.
* Chave assimétrica: um par de chaves públicas-privadas uma X509 certificado é utilizado com uma chave privada para encriptar/gerar um JWT e com a chave pública para verificar o token.

Se usar o .NET Framework / c# como sua plataforma de desenvolvimento, X509 certificado utilizado para uma chave assimétrica segurança tem de ter um comprimento de chave de, pelo menos, 2048. Este comprimento chave é um requisito da classe System.IdentityModel.Tokens.X509AsymmetricSecurityKey in .NET Framework. Caso contrário, é lançada a seguinte exceção: IDX10630: O 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' para a assinatura não pode ser menor do que bits '2048'.

## <a name="custom-key-and-license-acquisition-url"></a>URL de chave personalizada e aquisição de licença

Utilize os seguintes modelos se pretender especificar um serviço de entrega de licença/chave diferente (não serviços de media). Os dois campos substituíveis nos modelos estão lá para que possa partilhar a sua política de streaming em muitos ativos em vez de criar uma política de streaming por ativo. 

* `EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate`: Modelo para o URL do serviço personalizado que entrega chaves aos jogadores de utilizador final. Não é necessário quando se está a usar a Azure Media Services para emitir chaves. 

   O modelo suporta fichas substituíveis que o serviço irá atualizar no prazo de execução com o valor específico do pedido.  Os valores token atualmente suportados são:
   * `{AlternativeMediaId}`, que é substituído pelo valor de StreamingLocatorId.AlternativeMediaId.
   * `{ContentKeyId}`, que é substituído pelo valor do identificador da chave solicitada.
* `StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate`: Modelo para o URL do serviço personalizado que entrega licenças a jogadores de utilizador final. Não é necessário quando se está a usar a Azure Media Services para emitir licenças.

   O modelo suporta fichas substituíveis que o serviço irá atualizar no prazo de execução com o valor específico do pedido. Os valores token atualmente suportados são:  
   * `{AlternativeMediaId}`, que é substituído pelo valor de StreamingLocatorId.AlternativeMediaId.
   * `{ContentKeyId}`, que é substituído pelo valor do identificador da chave solicitada. 
* `StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate`: O mesmo que o modelo anterior, apenas para a Widevine. 
* `StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate`: O mesmo que o modelo anterior, apenas para fairPlay.  

Por exemplo:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

`ContentKeyId` tem um valor da chave solicitada. Pode usar `AlternativeMediaId` se quiser mapear o pedido para uma entidade do seu lado. Por exemplo, `AlternativeMediaId` pode ser usado para ajudá-lo a procurar permissões.

Para exemplos REST que usam URLs de aquisição de licença/chave personalizadas, consulte [Políticas de Streaming - Criar](https://docs.microsoft.com/rest/api/media/streamingpolicies/create).

> [!NOTE]
> A Widevine é um serviço prestado pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="troubleshoot"></a>Resolução de problemas

Se tiver o erro `MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY`, certifique-se de especificar a política de streaming adequada.

Se tiver erros que terminem com `_NOT_SPECIFIED_IN_URL`, certifique-se de que especifica o formato de encriptação no URL. Um exemplo é `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`. Consulte [protocolos de streaming e tipos](#streaming-protocols-and-encryption-types)de encriptação .

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Confira o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Serviços de Media.

## <a name="next-steps"></a>Passos seguintes

* [Proteger com encriptação AES](protect-with-aes128.md)
* [Proteger com ADRM](protect-with-drm.md)
* [Conceber sistema de proteção de conteúdos multi-DRM com controlo de acesso](design-multi-drm-system-with-access-control.md)
* [Encriptação do lado do armazenamento](storage-account-concept.md#storage-side-encryption)
* [Perguntas mais frequentes](frequently-asked-questions.md)
* [Manipulador de ficha web JSON](https://docs.microsoft.com/dotnet/framework/security/json-web-token-handler)
