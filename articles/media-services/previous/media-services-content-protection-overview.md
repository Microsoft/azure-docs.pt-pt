---
title: Proteja o seu conteúdo com a Azure Media Services | Microsoft Docs
description: Este artigo dá uma visão geral da proteção de conteúdos com a Azure Media Services v2.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 81bc00e1-dcda-4d69-b9ab-8768b793422b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: edffa2dddd0ec877a4b825a69a76fb158928c89f
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103016768"
---
# <a name="content-protection-overview"></a>Descrição geral da proteção de conteúdo

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](../latest/index.yml). Além disso, consulte [a orientação de migração de v2 para v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Pode utilizar a Azure Media Services para proteger os seus meios de comunicação a partir do momento em que deixa o computador através do armazenamento, processamento e entrega. Com os Media Services, pode fornecer o seu conteúdo ao vivo e a pedido encriptado dinamicamente com o Advanced Encryption Standard (AES-128) ou qualquer um dos três principais sistemas de gestão de direitos digitais (DRM): Microsoft PlayReady, Google Widevine e Apple FairPlay. Os Media Services também fornecem um serviço para a entrega de chaves AES e licenças DRM (PlayReady, Widevine e FairPlay) a clientes autorizados. 

A imagem a seguir ilustra o fluxo de trabalho de proteção de conteúdos dos Serviços de Comunicação Social: 

![Proteger com PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

Este artigo explica conceitos e terminologia relevantes para a compreensão da proteção de conteúdos com os Media Services. O artigo também fornece links para artigos que discutem como proteger o conteúdo. 

## <a name="dynamic-encryption"></a>Encriptação dinâmica

Pode utilizar os Serviços de Comunicação social para fornecer o seu conteúdo encriptado dinamicamente com a chave AES clara ou encriptação DRM utilizando PlayReady, Widevine ou FairPlay. Se o conteúdo for encriptado com uma chave clara AES e for enviado por HTTPS, não é claro até chegar ao cliente. 

Cada método de encriptação suporta os seguintes protocolos de streaming:
 
- AES: MPEG-DASH, Smooth Streaming e HLS
- PlayReady: MPEG-DASH, Smooth Streaming e HLS
- Widevine: MPEG-DASH
- FairPlay: HLS

A encriptação em downloads progressivos não é suportada. 

Para encriptar um ativo, precisa de associar uma chave de conteúdo de encriptação ao seu ativo e também configurar uma política de autorização para a chave. As teclas de conteúdo podem ser especificadas ou geradas automaticamente pelos Media Services.

Também precisa configurar a política de entrega do ativo. Se pretender transmitir um ativo encriptado de armazenamento, certifique-se de especificar como pretende entregá-lo configurando a política de entrega de ativos.

Quando um stream é solicitado por um jogador, os Media Services utilizam a chave especificada para encriptar dinamicamente o seu conteúdo utilizando a chave clara AES ou encriptação DRM. Para desencriptar o fluxo, o jogador solicita a chave do serviço de entrega de chaves media. Para decidir se o utilizador está ou não autorizado a obter a chave, o serviço avalia as políticas de autorização que especificou para a chave.

## <a name="aes-128-clear-key-vs-drm"></a>Chave clara AES-128 vs. DRM
Os clientes muitas vezes perguntam-se se devem usar encriptação AES ou um sistema DRM. A principal diferença entre os dois sistemas é que com a encriptação AES a chave de conteúdo é transmitida ao cliente num formato não encriptado ("a claro"). Como resultado, a chave usada para encriptar o conteúdo pode ser visualizada num traço de rede do cliente em texto simples. A encriptação clara da chave AES-128 é adequada para o uso de casos em que o espectador é uma parte de confiança (por exemplo, encriptar vídeos corporativos distribuídos dentro de uma empresa para serem visualizados por funcionários).

PlayReady, Widevine e FairPlay fornecem um nível mais alto de encriptação em comparação com a encriptação clara de chave AES-128. A chave de conteúdo é transmitida num formato encriptado. Além disso, a desencriptação é tratada num ambiente seguro a nível do sistema operativo, onde é mais difícil para um utilizador malicioso atacar. DrM é recomendado para o uso de casos em que o espectador pode não ser uma parte de confiança e você precisa do mais alto nível de segurança.

## <a name="storage-encryption"></a>Encriptação do armazenamento
Pode utilizar a encriptação de armazenamento para encriptar o seu conteúdo limpo localmente utilizando encriptação AES de 256 bits. Em seguida, pode enviá-lo para o Azure Storage, onde é armazenado encriptado em repouso. Os ativos protegidos com encriptação de armazenamento são automaticamente desencriptados e colocados num sistema de ficheiros encriptados antes da codificação. Os ativos são opcionalmente reencriptados antes de serem carregados como um novo ativo de saída. O caso principal de utilização para encriptação de armazenamento é quando pretende proteger os seus ficheiros de meios de entrada de alta qualidade com uma encriptação forte em repouso no disco.

Para entregar um ativo encriptado de armazenamento, tem de configurar a política de entrega do ativo para que os Serviços de Comunicação saibam como pretende entregar o seu conteúdo. Antes de o seu ativo poder ser transmitido, o servidor de streaming desencripta e transmite o seu conteúdo utilizando a política de entrega especificada (por exemplo, AES, encriptação comum ou nenhuma encriptação).

## <a name="types-of-encryption"></a>Tipos de encriptação
PlayReady e Widevine utilizam encriptação comum (modo AES CTR). FairPlay utiliza encriptação do modo CBC AES. A encriptação clara da chave AES-128 utiliza encriptação do envelope.

## <a name="licenses-and-keys-delivery-service"></a>Serviço de entrega de licenças e chaves
A Media Services fornece um serviço de entrega chave para a entrega de licenças de DRM (PlayReady, Widevine, FairPlay) e chaves AES a clientes autorizados. Pode utilizar [o portal Azure,](media-services-portal-protect-content.md)a API REST ou o SDK dos Serviços de Comunicação Social para .NET para configurar as políticas de autorização e autenticação para as suas licenças e chaves.

## <a name="control-content-access"></a>Controlar o acesso ao conteúdo
Pode controlar quem tem acesso ao seu conteúdo configurando a política de autorização da chave de conteúdo. A política de autorização de conteúdo suporta a restrição aberta ou simbólica.

### <a name="open-authorization"></a>Autorização aberta
Com uma política de autorização aberta, a chave de conteúdo é enviada a qualquer cliente (sem restrição).

### <a name="token-authorization"></a>Autorização simbólica
Com uma política de autorização restrita a ficha simbólica, a chave de conteúdo é enviada apenas para um cliente que apresenta um Token Web JSON válido (JWT) ou um simples token web (SWT) no pedido chave/licença. Este token deve ser emitido por um serviço de símbolo de segurança (STS). Pode utilizar o Azure Ative Directory como STS ou implementar uma STS personalizada. O STS deve ser configurado para criar um símbolo assinado com a chave especificada e emitir alegações que especificou na configuração da restrição simbólica. O serviço de entrega chave dos Serviços de Comunicação social devolve a chave/licença solicitada ao cliente se o token for válido e as reclamações no token corresponderem às configuradas para a chave/licença.

Ao configurar a política restrita simbólica, deve especificar a chave de verificação primária, o emitente e os parâmetros do público. A chave de verificação primária contém a chave com a que o símbolo foi assinado. O emitente é o serviço de fichas seguras que emite o símbolo. O público, por vezes chamado de âmbito, descreve a intenção do símbolo ou o recurso a que o símbolo autoriza o acesso. O serviço de entrega chave dos Serviços de Comunicação social valida que estes valores no símbolo correspondem aos valores do modelo.

### <a name="token-replay-prevention"></a>Prevenção de repetição de token

A funcionalidade *De Prevenção de Repetição token* permite que os clientes dos Media Services estabeleçam um limite para quantas vezes o mesmo token pode ser usado para solicitar uma chave ou uma licença. O cliente pode adicionar uma reclamação de tipo `urn:microsoft:azure:mediaservices:maxuses` no token, onde o valor é o número de vezes que o token pode ser usado para adquirir uma licença ou chave. Todos os pedidos subsequentes com o mesmo sinal para a Entrega de Chaves devolverão uma resposta não autorizada. Veja como adicionar a reclamação na [amostra de DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Considerações

* Os clientes devem ter controlo sobre a geração simbólica. A reivindicação tem de ser colocada no próprio símbolo.
* Ao utilizar esta funcionalidade, os pedidos com fichas cujo prazo de validade esteja a mais de uma hora do momento em que o pedido é recebido são rejeitados com uma resposta não autorizada.
* Os tokens são identificados exclusivamente pela sua assinatura. Qualquer alteração na carga útil (por exemplo, atualização para o prazo de validade ou a reclamação) altera a assinatura do token e contará como um novo símbolo que a Entrega da Chave nunca encontrou antes.
* A reprodução falha se o token exceder o `maxuses` valor definido pelo cliente.
* Esta funcionalidade pode ser utilizada para todos os conteúdos protegidos existentes (apenas o token emitido precisa de ser alterado).
* Esta funcionalidade funciona tanto com o JWT como com o SWT.

## <a name="streaming-urls"></a>Streaming URLs
Se o seu ativo foi encriptado com mais de um DRM, use uma etiqueta de encriptação no URL de streaming: (formato='m3u8-aapl', encriptação='xxx').

As seguintes considerações são aplicáveis:

* Não pode ser especificado mais do que um tipo de encriptação.
* O tipo de encriptação não tem de ser especificado no URL se apenas uma encriptação foi aplicada ao ativo.
* O tipo de encriptação é insensível ao caso.
* Podem ser especificados os seguintes tipos de encriptação:

  * **cenc**: Para PlayReady ou Widevine (encriptação comum)
  * **cbcs-aapl**: Para FairPlay (encriptação AES CBC)
  * **cbc**: Para encriptação do envelope AES

## <a name="additional-notes"></a>Notas adicionais

* Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="next-steps"></a>Passos seguintes
Os seguintes artigos descrevem os próximos passos para ajudá-lo a começar com a proteção de conteúdos:

* [Proteger com encriptação de armazenamento](media-services-rest-storage-encryption.md)
* [Proteger com encriptação AES](media-services-protect-with-aes128.md)
* [Proteger com PlayReady e/ou Widevine](media-services-protect-with-playready-widevine.md)
* [Proteger com FairPlay](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Ligações relacionadas

* [Autenticação simbólica JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
* [Integre a app baseada em Azure Media Services OWIN MVC com o Azure Ative Directory e restringir a entrega de chaves de conteúdo com base em alegações de JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
