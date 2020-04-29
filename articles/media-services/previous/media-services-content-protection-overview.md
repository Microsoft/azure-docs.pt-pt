---
title: Proteja o seu conteúdo com a Azure Media Services . Microsoft Docs
description: Este artigo apresenta uma visão geral da proteção de conteúdos com a Azure Media Services v2.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 81bc00e1-dcda-4d69-b9ab-8768b793422b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 88e0e1c18722fd86e79fc1fa7722b59b3cb8966a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79460964"
---
# <a name="content-protection-overview"></a>Descrição geral da proteção de conteúdo 

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Consulte também [a orientação de migração da v2 para a v3](../latest/migrate-from-v2-to-v3.md)

Pode utilizar o Azure Media Services para proteger os seus meios de comunicação a partir do momento em que deixa o computador através do armazenamento, processamento e entrega. Com os Media Services, pode fornecer os seus conteúdos ao vivo e a pedido encriptados dinamicamente com advanced Encryption Standard (AES-128) ou qualquer um dos três principais sistemas de gestão de direitos digitais (DRM): Microsoft PlayReady, Google Widevine e Apple FairPlay. A Media Services também fornece um serviço para a entrega de chaves AES e licenças DRM (PlayReady, Widevine e FairPlay) a clientes autorizados. 

A imagem seguinte ilustra o fluxo de trabalho de proteção de conteúdos dos Serviços de Media: 

![Proteger com PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

Este artigo explica conceitos e terminologia relevantes para a compreensão da proteção de conteúdos com os Serviços de Media. O artigo também fornece links para artigos que discutem como proteger os conteúdos. 

## <a name="dynamic-encryption"></a>Encriptação dinâmica

Pode utilizar os Serviços de Media para fornecer o seu conteúdo encriptado dinamicamente com chave aes clara ou encriptação DRM utilizando playReady, Widevine ou FairPlay. Se o conteúdo for encriptado com uma chave aes clara e for enviado https, não é claro até chegar ao cliente. 

Cada método de encriptação suporta os seguintes protocolos de streaming:
 
- AES: MPEG-DASH, Smooth Streaming e HLS
- PlayReady: MPEG-DASH, Smooth Streaming e HLS
- Widevine: MPEG-DASH
- FairPlay: HLS

A encriptação em downloads progressivos não é suportada. 

Para encriptar um ativo, é necessário associar uma chave de conteúdo de encriptação ao seu ativo e configurar também uma política de autorização para a chave. As chaves de conteúdo podem ser especificadas ou geradas automaticamente pelos Serviços de Media.

Também precisa de configurar a política de entrega do ativo. Se pretender transmitir um ativo encriptado de armazenamento, certifique-se de especificar como pretende entregá-lo configurando a política de entrega de ativos.

Quando um stream é solicitado por um leitor, o Media Services utiliza a chave especificada para encriptar dinamicamente o seu conteúdo utilizando a chave aes clara ou a encriptação DRM. Para desencriptar o fluxo, o leitor solicita a chave do serviço de entrega da chave Media Services. Para decidir se o utilizador está ou não autorizado a obter a chave, o serviço avalia as políticas de autorização que especificou para a chave.

## <a name="aes-128-clear-key-vs-drm"></a>Chave clara AES-128 vs. DRM
Os clientes muitas vezes perguntam-se se devem usar encriptação AES ou um sistema DEDRM. A principal diferença entre os dois sistemas é que com a encriptação AES a chave de conteúdo é transmitida ao cliente num formato não encriptado ("na clara"). Como resultado, a chave usada para encriptar o conteúdo pode ser visualizada num traço de rede no cliente em texto simples. A encriptação clara da chave AES-128 é adequada para casos de utilização em que o espectador é uma parte de confiança (por exemplo, encriptar vídeos corporativos distribuídos dentro de uma empresa para ser visto pelos colaboradores).

PlayReady, Widevine e FairPlay fornecem um nível mais elevado de encriptação em comparação com a encriptação clara da chave AES-128. A chave de conteúdo é transmitida num formato encriptado. Além disso, a desencriptação é tratada num ambiente seguro ao nível do sistema operativo, onde é mais difícil para um utilizador malicioso atacar. A DRM é recomendada para casos de utilização em que o espectador pode não ser uma parte de confiança e você requer o mais alto nível de segurança.

## <a name="storage-encryption"></a>Encriptação do armazenamento
Pode utilizar encriptação de armazenamento para encriptar o seu conteúdo limpo localmente utilizando encriptação AES 256 bits. Em seguida, pode enviá-lo para o Azure Storage, onde é armazenado encriptado em repouso. Os ativos protegidos com encriptação de armazenamento são automaticamente desencriptados e colocados num sistema de ficheiros encriptado antes da codificação. Os ativos são opcionalmente reencriptados antes de serem carregados de volta como um novo ativo de saída. O principal caso de utilização para encriptação de armazenamento é quando pretende proteger os seus ficheiros de mídia de entrada de alta qualidade com encriptação forte em repouso no disco.

Para fornecer um ativo encriptado de armazenamento, deve configurar a política de entrega do ativo para que os Serviços de Media saibam como pretende entregar o seu conteúdo. Antes de o seu ativo poder ser transmitido, o servidor de streaming desencripta e transmite o seu conteúdo utilizando a política de entrega especificada (por exemplo, AES, encriptação comum ou sem encriptação).

## <a name="types-of-encryption"></a>Tipos de encriptação
PlayReady e Widevine utilizam encriptação comum (modo AES CTR). FairPlay utiliza encriptação do modo AES CBC. A encriptação de chave clara AES-128 utiliza encriptação de envelopes.

## <a name="licenses-and-keys-delivery-service"></a>Serviço de entrega de licenças e chaves
A Media Services fornece um serviço de entrega chave para a entrega de licenças DE DRM (PlayReady, Widevine, FairPlay) e chaves AES para clientes autorizados. Pode utilizar [o portal Azure,](media-services-portal-protect-content.md)o REST API ou o Media Services SDK para .NET configurar políticas de autorização e autenticação para as suas licenças e chaves.

## <a name="control-content-access"></a>Controlar o acesso a conteúdos
Pode controlar quem tem acesso ao seu conteúdo configurando a política de autorização de chave de conteúdo. A política de autorização de conteúdo apoia a restrição aberta ou simbólica.

### <a name="open-authorization"></a>Autorização aberta
Com uma política de autorização aberta, a chave de conteúdo é enviada a qualquer cliente (sem restrições).

### <a name="token-authorization"></a>Autorização simbólica
Com uma política de autorização restrita de token, a chave de conteúdo é enviada apenas para um cliente que apresenta um JSON Web Token (JWT) válido ou simples ficha web (SWT) no pedido de chave/licença. Esta ficha deve ser emitida por um serviço de token de segurança (STS). Pode utilizar o Azure Ative Directory como STS ou implementar um STS personalizado. O STS deve ser configurado para criar um símbolo assinado com a chave especificada e emitir alegações que especificaste na configuração de restrição simbólica. O serviço de entrega da chave Media Services devolve a chave/licença solicitada ao cliente se o token for válido e as reclamações no token corresponderem às configuradas para a chave/licença.

Quando configurar a política restrita do símbolo, deve especificar a chave de verificação primária, o emitente e os parâmetros do público. A chave de verificação primária contém a chave com a que o símbolo foi assinado. O emitente é o serviço de fichas seguro que emite o símbolo. O público, por vezes chamado de âmbito, descreve a intenção do símbolo ou o recurso a que o símbolo autoriza o acesso. O serviço de entrega de chaves media services valida que estes valores no token correspondem aos valores do modelo.

### <a name="token-replay-prevention"></a>Prevenção de reprodução de token

A funcionalidade *de Prevenção* de Repetição de Token permite que os clientes dos Media Services estabeleçam um limite para quantas vezes o mesmo símbolo pode ser usado para solicitar uma chave ou uma licença. O cliente pode adicionar `urn:microsoft:azure:mediaservices:maxuses` uma reclamação de tipo no token, onde o valor é o número de vezes que o token pode ser usado para adquirir uma licença ou chave. Todos os pedidos subsequentes com o mesmo símbolo da Entrega da Chave devolverão uma resposta não autorizada. Veja como adicionar a reclamação na [amostra DEDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Considerações

* Os clientes devem ter controlo sobre a geração simbólica. A reivindicação tem de ser colocada no próprio símbolo.
* Ao utilizar esta funcionalidade, os pedidos com fichas cujo prazo de validade está a mais de uma hora do momento em que o pedido é recebido são rejeitados com uma resposta não autorizada.
* Os tokens são identificados exclusivamente pela sua assinatura. Qualquer alteração na carga útil (por exemplo, atualizar para o tempo de validade ou a reclamação) altera a assinatura do token e contará como um novo símbolo que a Key Delivery não encontrou antes.
* A reprodução falha se o token tiver excedido o `maxuses` valor definido pelo cliente.
* Esta função pode ser utilizada para todos os conteúdos protegidos existentes (apenas o símbolo emitido precisa de ser alterado).
* Esta funcionalidade funciona tanto com jWT como SWT.

## <a name="streaming-urls"></a>Streaming URLs
Se o seu ativo foi encriptado com mais de um DRM, utilize uma etiqueta de encriptação no URL de streaming: (formato='m3u8-apl', encriptação='xxx').

As seguintes considerações são aplicáveis:

* Não é possível especificar mais do que um tipo de encriptação.
* O tipo de encriptação não tem de ser especificado no URL se apenas uma encriptação for aplicada ao ativo.
* O tipo de encriptação é um caso insensível.
* Os seguintes tipos de encriptação podem ser especificados:

  * **cenc**: Para PlayReady ou Widevine (encriptação comum)
  * **cbcs-apl**: For FairPlay (encriptação AES CBC)
  * **cbc**: Para encriptação do envelope AES

## <a name="additional-notes"></a>Notas adicionais

* A Widevine é um serviço prestado pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="next-steps"></a>Passos seguintes
Os seguintes artigos descrevem os próximos passos para ajudá-lo a começar com a proteção de conteúdos:

* [Proteger com encriptação de armazenamento](media-services-rest-storage-encryption.md)
* [Proteger com encriptação AES](media-services-protect-with-aes128.md)
* [Proteja com playReady e/ou Widevine](media-services-protect-with-playready-widevine.md)
* [Proteger com FairPlay](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Ligações relacionadas

* [Autenticação simbólica jWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
* [Integrar a aplicação baseada em Azure Media Services OWIN MVC com o Azure Ative Directory e restringir a entrega da chave de conteúdo com base em alegações da JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
