---
title: Proteja seu conteúdo com os serviços de mídia do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral da proteção de conteúdo com os serviços de mídia do Azure v2.
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
ms.openlocfilehash: 4ff4025941e9a77148daa91995ecf182231d1f0b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976286"
---
# <a name="content-protection-overview"></a>Descrição geral da proteção de conteúdo 

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Veja a versão mais recente, [Serviços de Multimédia v3](https://docs.microsoft.com/azure/media-services/latest/). Além disso, consulte [diretrizes de migração de v2 para v3](../latest/migrate-from-v2-to-v3.md)

Pode utilizar os serviços de multimédia do Azure para proteger os seus suportes de dados a partir do momento em que deixa seu computador por meio de armazenamento, processamento e entrega. Com os serviços de multimédia, pode fornecer seu conteúdo ao vivo e sob demanda dinamicamente encriptado com o Advanced Encryption Standard (AES-128) ou qualquer um dos sistemas de gestão (DRM) três direitos digitais principais: Microsoft PlayReady, Widevine da Google e Apple FairPlay. Serviços de multimédia também fornecem um serviço para entrega de chaves AES e o DRM (PlayReady, Widevine e FairPlay) licenças para os clientes autorizados. 

A imagem seguinte ilustra o fluxo de trabalho de proteção de conteúdo de serviços de multimédia: 

![Proteger com PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

Este artigo explica os conceitos e terminologia relevante para compreender a proteção de conteúdo com os Media Services. O artigo também fornece links para artigos que discutem como proteger o conteúdo. 

## <a name="dynamic-encryption"></a>Encriptação dinâmica
 Pode utilizar os serviços de multimédia para distribuir os seus conteúdos encriptados dinamicamente com a chave não encriptada AES ou encriptação de DRM com o PlayReady, Widevine e FairPlay. Atualmente, pode criptografar os formatos de HTTP Live Streaming (HLS), MPEG DASH e Smooth Streaming. Não há suporte para a criptografia em downloads progressivos. Cada método de criptografia dá suporte aos seguintes protocolos de streaming:

- AES: MPEG-DASH, Smooth Streaming e HLS
- PlayReady: MPEG-DASH, Smooth Streaming e HLS
- Widevine: MPEG-DASH
- FairPlay: HLS

Para criptografar um ativo, você precisa associar uma chave de conteúdo de criptografia ao seu ativo e também configurar uma política de autorização para a chave. As chaves de conteúdo podem ser especificadas ou geradas automaticamente pelos serviços de mídia.

Você também precisa configurar a política de entrega do ativo. Se você quiser transmitir um ativo criptografado por armazenamento, certifique-se de especificar como deseja entregá-lo Configurando a política de entrega de ativos.

Quando um fluxo é solicitado por um jogador, serviços de multimédia utiliza a chave especificada para encriptar dinamicamente o seu conteúdo através da utilização de chave não encriptada AES ou encriptação de DRM. Para descriptografar o fluxo, o Player solicita a chave do serviço de distribuição de chaves dos serviços de mídia. Para decidir se o usuário está autorizado ou não a obter a chave, o serviço avalia as políticas de autorização que você especificou para a chave.

## <a name="aes-128-clear-key-vs-drm"></a>Chave de limpeza AES-128 vs. DRM
Os clientes freqüentemente se questionam sobre se deve utilizar encriptação AES ou um sistema DRM. A principal diferença entre os dois sistemas é que, com a encriptação AES, a chave de conteúdo é transmitida para o cliente num formato não encriptado ("em Limpar"). Como resultado, a chave utilizada para encriptar o conteúdo pode ser exibida num rastreio de rede no cliente em texto simples. Encriptação de chave não encriptada AES-128 é adequada para casos de utilização em que o Visualizador é um confiável (por exemplo, encriptar empresariais vídeos distribuídos dentro de uma empresa sejam visualizados por funcionários).

Encriptação de chave não PlayReady, Widevine e FairPlay que todos fornecerem um nível mais elevado de encriptação em comparação comparada a AES-128. A chave de conteúdo é transmitida num formato encriptado. Além disso, a desencriptação é tratada num ambiente seguro ao nível do sistema operativo, onde é mais difícil para um utilizador mal intencionado a ataques. Para casos de utilização em que o Visualizador pode não ser um confiável e requerem o nível mais elevado de segurança, recomenda-se DRM.

## <a name="storage-encryption"></a>Encriptação do armazenamento
Você pode usar a criptografia de armazenamento para criptografar seu conteúdo limpo localmente usando a criptografia AES de 256 bits. Em seguida, você pode carregá-lo no armazenamento do Azure, onde ele é armazenado criptografado em repouso. Os ativos protegidos com criptografia de armazenamento são descriptografados automaticamente e colocados em um sistema de arquivos criptografados antes da codificação. Os ativos são, opcionalmente, criptografados novamente antes de serem carregados novamente como um novo ativo de saída. O caso de uso primário para criptografia de armazenamento é quando você deseja proteger seus arquivos de mídia de entrada de alta qualidade com criptografia forte em repouso no disco.

Para entregar um ativo de armazenamento criptografado, você deve configurar a política de entrega do ativo para que os serviços de mídia saibam como você deseja entregar seu conteúdo. Antes que o ativo possa ser transmitido, o servidor de streaming descriptografa e transmite o conteúdo usando a política de entrega especificada (por exemplo, AES, criptografia comum ou sem criptografia).

## <a name="types-of-encryption"></a>Tipos de criptografia
PlayReady e Widevine utilizam criptografia comum (modo de CTR do AES). O FairPlay utiliza a criptografia do modo CBC do AES. A criptografia de chave não criptografada AES-128 utiliza a criptografia de envelope.

## <a name="licenses-and-keys-delivery-service"></a>Serviço de entrega de licenças e chaves
Os Media Services fornecem um serviço de entrega de chave para entregar licenças DRM (PlayReady, Widevine, FairPlay) e as chaves AES para os clientes autorizados. Você pode usar [o portal do Azure](media-services-portal-protect-content.md), a API REST ou o SDK dos serviços de mídia para .net para configurar políticas de autorização e autenticação para suas licenças e chaves.

## <a name="control-content-access"></a>Controlar o acesso do conteúdo
Você pode controlar quem tem acesso ao seu conteúdo Configurando a política de autorização de chave de conteúdo. A política de autorização de chave de conteúdo dá suporte à restrição de token ou aberta.

### <a name="open-authorization"></a>Abrir autorização
Com uma política de autorização aberta, a chave de conteúdo é enviada para qualquer cliente (sem restrição).

### <a name="token-authorization"></a>Autorização de token
Com uma política de autorização restrita por token, a chave de conteúdo é enviada somente para um cliente que apresenta um token Web JSON (JWT) ou um token Web simples (SWT) válido na solicitação de licença/chave. Este token deve ser emitido por um serviço de token de segurança (STS). Pode utilizar o Azure Active Directory como um STS ou implementar um STS personalizado. O STS deve ser configurado para criar um token assinado com as declarações de chave e o problema especificadas que especificou na configuração de restrição de token. O serviço de entrega de chave de serviços de multimédia devolve a chave/licença pedida para o cliente se o token é válido e as afirmações no token corresponderem às configuradas para a chave/licença.

Ao configurar a política de token restrito, tem de especificar a chave de verificação primária, emissor e parâmetros de público-alvo. A chave de verificação primária contém a chave de que o token foi assinado com. O emissor é o serviço de token seguro que emite o token. O público-alvo, às vezes chamado de âmbito, descreve a intenção do token ou o recurso o token de acesso a autoriza. O serviço de entrega de chave de serviços de multimédia valida que estes valores no token correspondem aos valores no modelo.

## <a name="streaming-urls"></a>URLs de streaming
Se o ativo foi criptografado com mais de um DRM, use uma marca de criptografia na URL de streaming: (Format = M3U8-AAPL ', Encryption = ' xxx ').

As seguintes considerações são aplicáveis:

* Não é possível especificar mais de um tipo de criptografia.
* O tipo de criptografia não precisa ser especificado na URL se apenas uma criptografia foi aplicada ao ativo.
* O tipo de criptografia não diferencia maiúsculas de minúsculas.
* Os seguintes tipos de criptografia podem ser especificados:
  * **Cenc**: para PlayReady ou Widevine (criptografia comum)
  * **CBCs-AAPL**: para Fairplay (criptografia AES CBC)
  * **CBC**: para criptografia de envelope AES

## <a name="additional-notes"></a>Notas adicionais

* O Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="next-steps"></a>Passos seguintes
Os artigos a seguir descrevem as próximas etapas para ajudá-lo a começar a usar a proteção de conteúdo:

* [Proteger com criptografia de armazenamento](media-services-rest-storage-encryption.md)
* [Proteger com encriptação AES](media-services-protect-with-aes128.md)
* [Proteger com PlayReady e/ou Widevine](media-services-protect-with-playready-widevine.md)
* [Proteger com o FairPlay](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Hiperligações relacionadas

* [Autenticação de token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
* [Integrar o aplicativo baseado em MVC do Azure Media Services OWIN com Azure Active Directory e restringir a entrega de chave de conteúdo com base em declarações JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
