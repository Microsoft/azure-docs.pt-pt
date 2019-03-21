---
title: Proteger o seu conteúdo com os serviços de multimédia do Azure | Documentos da Microsoft
description: Este artigo lhe dar uma visão geral da proteção de conteúdo com os Media Services.
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 0d37f0596069899bbeb9dfeafa5d487aee910cd8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58183667"
---
# <a name="content-protection-overview"></a>Descrição geral da proteção de conteúdo 

 Pode utilizar os serviços de multimédia do Azure para proteger os seus suportes de dados a partir do momento em que deixa seu computador por meio de armazenamento, processamento e entrega. Com os serviços de multimédia, pode fornecer seu conteúdo ao vivo e sob demanda dinamicamente encriptado com o Advanced Encryption Standard (AES-128) ou qualquer um dos três sistemas de gestão (DRM) de direitos digitais principais: Microsoft PlayReady, Widevine da Google e Apple FairPlay. Serviços de multimédia também fornecem um serviço para entrega de chaves AES e o DRM (PlayReady, Widevine e FairPlay) licenças para os clientes autorizados. 

A imagem seguinte ilustra o fluxo de trabalho de proteção de conteúdo de serviços de multimédia: 

![Proteger com PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

Este artigo explica os conceitos e terminologia relevante para compreender a proteção de conteúdo com os Media Services. O artigo também fornece ligações para artigos que discutem como proteger o conteúdo. 

## <a name="dynamic-encryption"></a>Encriptação dinâmica
 Pode utilizar os serviços de multimédia para distribuir os seus conteúdos encriptados dinamicamente com a chave não encriptada AES ou encriptação de DRM com o PlayReady, Widevine e FairPlay. Atualmente, pode criptografar os formatos de HTTP Live Streaming (HLS), MPEG DASH e Smooth Streaming. Encriptação em downloads progressivos não é suportada. Cada método de encriptação suporta os seguintes protocolos de transmissão em fluxo:

- AES: MPEG-DASH, Smooth Streaming e HLS
- PlayReady: MPEG-DASH, Smooth Streaming e HLS
- Widevine: MPEG-DASH
- FairPlay: HLS

Para encriptar um elemento, terá de associar o seu elemento uma chave de encriptação de conteúdo e também configurar uma política de autorização da chave. Chaves de conteúdo podem ser especificadas ou geradas automaticamente pelos serviços de multimédia.

Terá também de configurar a política de entrega de elementos. Se quiser transmitir um recurso de armazenamento encriptado, certifique-se especificar como pretende entregá-lo ao configurar a política de entrega de elementos.

Quando um fluxo é solicitado por um jogador, serviços de multimédia utiliza a chave especificada para encriptar dinamicamente o seu conteúdo através da utilização de chave não encriptada AES ou encriptação de DRM. Para descriptografar o fluxo, o jogador solicita a chave do serviço de entrega de chave de serviços de multimédia. Para decidir se é ou não o utilizador está autorizado a obter a chave, o serviço avalia as políticas de autorização que especificou para a chave.

## <a name="aes-128-clear-key-vs-drm"></a>Vs de chaves não encriptada AES-128. DRM
Os clientes freqüentemente se questionam sobre se deve utilizar encriptação AES ou um sistema DRM. A principal diferença entre os dois sistemas é que, com a encriptação AES, a chave de conteúdo é transmitida para o cliente num formato não encriptado ("em Limpar"). Como resultado, a chave utilizada para encriptar o conteúdo pode ser exibida num rastreio de rede no cliente em texto simples. Encriptação de chave não encriptada AES-128 é adequada para casos de utilização em que o Visualizador é um confiável (por exemplo, encriptar empresariais vídeos distribuídos dentro de uma empresa sejam visualizados por funcionários).

Encriptação de chave não PlayReady, Widevine e FairPlay que todos fornecerem um nível mais elevado de encriptação em comparação comparada a AES-128. A chave de conteúdo é transmitida num formato encriptado. Além disso, a desencriptação é tratada num ambiente seguro ao nível do sistema operativo, onde é mais difícil para um utilizador mal intencionado a ataques. Para casos de utilização em que o Visualizador pode não ser um confiável e requerem o nível mais elevado de segurança, recomenda-se DRM.

## <a name="storage-encryption"></a>Encriptação do armazenamento
Pode utilizar a encriptação de armazenamento para encriptar o seu conteúdo transparente localmente utilizando encriptação AES de 256 bits. , Em seguida, pode carregá-lo ao armazenamento do Azure, onde são armazenados encriptados em inatividade. Os elementos protegidos com encriptação de armazenamento são automaticamente sem criptografia e colocados no sistema de ficheiros encriptados antes da codificação. Os ativos são opcionalmente encriptados novamente antes de carregar novamente como um novo elemento de saída. O principal motivo para a encriptação de armazenamento é quando deseja proteger os ficheiros de suporte de dados de entrada de alta qualidade com uma encriptação forte Inativos no disco.

Para fornecer um recurso de encriptação de armazenamento, tem de configurar política de entrega de elementos para que os serviços de multimédia, saiba como deseja distribuir os seus conteúdos. Antes do seu elemento pode ser transmitido em fluxo, o servidor de transmissão em fluxo desencripta e transmite o conteúdo utilizando a política de entrega especificado (por exemplo, AES, encriptação comum ou sem encriptação).

## <a name="types-of-encryption"></a>Tipos de encriptação
PlayReady e Widevine utilizam encriptação comum (modo AES CTR). FairPlay utiliza encriptação do modo CBC-AES. Encriptação de chave não encriptada AES-128 utiliza encriptação de envelope.

## <a name="licenses-and-keys-delivery-service"></a>Serviço de entrega de licenças e chaves
Os Media Services fornecem um serviço de entrega de chave para entregar licenças DRM (PlayReady, Widevine, FairPlay) e as chaves AES para os clientes autorizados. Pode usar [do portal do Azure](media-services-portal-protect-content.md), a API REST ou o SDK de serviços de multimédia para .NET para configurar políticas de autorização e autenticação para as suas licenças e chaves.

## <a name="control-content-access"></a>Controlar o acesso do conteúdo
Pode controlar quem tem acesso ao seu conteúdo ao configurar a política de autorização da chave de conteúdo. A política de autorização da chave de conteúdo oferece suporte a restrição aberta ou token.

### <a name="open-authorization"></a>Autorização aberta
Com uma política de autorização aberto, a chave de conteúdo é enviada para qualquer cliente (sem restrição).

### <a name="token-authorization"></a>Autorização de token
Com uma política de autorização de token restrito, a chave de conteúdo é enviada apenas para um cliente que apresenta um válido JSON Web Token (JWT) ou simple web tokens (SWT) no pedido de chaves/licenças. Este token deve ser emitido por um serviço de token de segurança (STS). Pode utilizar o Azure Active Directory como um STS ou implementar um STS personalizado. O STS deve ser configurado para criar um token assinado com as declarações de chave e o problema especificadas que especificou na configuração de restrição de token. O serviço de entrega de chave de serviços de multimédia devolve a chave/licença pedida para o cliente se o token é válido e as afirmações no token corresponderem às configuradas para a chave/licença.

Ao configurar a política de token restrito, tem de especificar a chave de verificação primária, emissor e parâmetros de público-alvo. A chave de verificação primária contém a chave de que o token foi assinado com. O emissor é o serviço de token seguro que emite o token. O público-alvo, às vezes chamado de âmbito, descreve a intenção do token ou o recurso o token de acesso a autoriza. O serviço de entrega de chave de serviços de multimédia valida que estes valores no token correspondem aos valores no modelo.

## <a name="streaming-urls"></a>URLs de transmissão em fluxo
Se o seu elemento foi encriptado com o DRM mais do que uma, utilize uma marca de encriptação no URL de transmissão em fluxo: (formato ' Format=m3u8-aapl ', encriptação = "xxx").

As seguintes considerações aplicam-se:

* Mais do que um tipo de encriptação pode ser especificado.
* Tipo de encriptação não tem de ser especificado no URL, se apenas uma encriptação foi aplicada ao elemento.
* Tipo de encriptação diferencia maiúsculas de minúsculas.
* Os seguintes tipos de encriptação podem ser especificados:
  * **cenc**: Para PlayReady ou Widevine (encriptação comum)
  * **cbcs-aapl**: Para o FairPlay (encriptação AES CBC)
  * **cbc**: Para a encriptação de envelope AES

## <a name="next-steps"></a>Passos Seguintes
Os seguintes artigos descrevem os passos seguintes para ajudar a começar com a proteção de conteúdo:

* [Proteger com encriptação de armazenamento](media-services-rest-storage-encryption.md)
* [Proteger com encriptação AES](media-services-protect-with-aes128.md)
* [Proteger com PlayReady e/ou Widevine](media-services-protect-with-playready-widevine.md)
* [Proteger com o FairPlay](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Ligações relacionadas

* [Autenticação de token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
* [Integrar aplicações com base em MVC de OWIN de serviços de multimédia do Azure com o Azure Active Directory e restringir a entrega de chave de conteúdo com base em declarações do JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
