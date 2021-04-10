---
title: Configure uma política de autorização chave de conteúdo utilizando o portal Azure | Microsoft Docs
description: Este artigo demonstra como configurar uma política de autorização para uma chave de conteúdo.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ee82a3fa-c34b-48f2-a108-8ba321f1691e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 68bd4bd2472e80a663294745368fb505767a230a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103008319"
---
# <a name="configure-a-content-key-authorization-policy"></a>Configure uma política de autorização chave de conteúdo

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Descrição Geral
 Pode utilizar os serviços de mídia Azure para fornecer streams MPEG-DASH, Smooth Streaming e HTTP Live Streaming (HLS) protegidos com o Advanced Encryption Standard (AES) utilizando chaves de encriptação de 128 bits ou [a gestão de direitos digitais PlayReady (DRM)](https://www.microsoft.com/playready/overview/). Com os Media Services, também pode fornecer streams DASH encriptados com DrM Widevine. Tanto o PlayReady, como o Widevine, são encriptados de acordo com a especificação de encriptação comum (ISO/IEC 23001 7 CENC).

Os Serviços de Mídia também fornecem um serviço de entrega de chaves/licenças a partir do qual os clientes podem obter chaves AES ou licenças PlayReady/Widevine para reproduzir o conteúdo encriptado.

Este artigo mostra como usar o portal Azure para configurar a política de autorização de chave de conteúdo. A chave pode ser usada mais tarde para encriptar dinamicamente o seu conteúdo. Atualmente, pode encriptar os formatos HLS, MPEG-DASH e Smooth Streaming. Não é possível encriptar transferências progressivas.

Quando um jogador solicita um fluxo que está programado para ser encriptado dinamicamente, os Media Services utilizam a chave configurada para encriptar dinamicamente o seu conteúdo utilizando encriptação AES ou DRM. Para desencriptar a transmissão em fluxo, o leitor solicita a chave ao serviço de entrega de chaves. Para determinar se o utilizador está autorizado a obter a chave, o serviço avalia as políticas de autorização que especificou para a chave.

Se pretender ter várias teclas de conteúdo ou quiser especificar um URL de serviço de entrega de chave/licença que não seja o serviço de entrega de chaves dos Serviços de Comunicação Social, utilize os Serviços de Comunicação Social .NET SDK ou REST APIs. Para obter mais informações, consulte:

* [Configure uma política de autorização chave de conteúdo utilizando os Serviços de Comunicação Social .NET SDK](media-services-dotnet-configure-content-key-auth-policy.md)
* [Configure uma política de autorização chave de conteúdo utilizando a API dos Serviços de Mídia](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>Algumas considerações se aplicam
* Quando a conta dos Serviços de Multimédia é criada, é adicionado um ponto final de transmissão em fluxo predefinido à mesma, no estado "Parado". Para começar a transmitir o seu conteúdo e aproveitar a embalagem dinâmica e a encriptação dinâmica, o seu ponto final de streaming deve estar no estado "Running". 
* O seu ativo deve conter um conjunto de mp4s bitrate adaptativos ou ficheiros de streaming suave de bitrate adaptativo. Para mais informações, consulte [Codificar um ativo.](media-services-encode-asset.md)
* O serviço de entrega chave caches ContentKeyAuthorizationPolicy e seus objetos relacionados (opções de política e restrições) durante 15 minutos. Pode criar uma Política de MarketingKeyAuthorization e especificar para utilizar uma restrição simbólica, testá-la e, em seguida, atualizar a política para a restrição aberta. Este processo demora cerca de 15 minutos até que a política mude para a versão aberta.
* Um ponto final de streaming de Serviços de Mídia define o valor do cabeçalho CORS Access-Control-Allow-Origin na resposta de pré-voo como o wildcard \* " Este valor funciona bem com a maioria dos jogadores, incluindo Azure Media Player, Roku e JWPlayer, entre outros. No entanto, alguns jogadores que usam dash.js não funcionam porque, com o modo de credenciais definido para "incluir", o XMLHttpRequest no seu dash.js não permite o wildcard \* " como o valor do Access-Control-Allow-Origin. Como uma solução para esta limitação em dash.js, se hospedar o seu cliente a partir de um único domínio, os Serviços de Comunicação podem especificar esse domínio no cabeçalho de resposta de pré-voo. Para assistência, abra um bilhete de apoio através do portal Azure.

## <a name="configure-the-key-authorization-policy"></a>Configure a política de autorização chave
Para configurar a política de autorização de chave, selecione a página **CONTENT PROTECTION.**

Os Serviços de Comunicação Social suportam múltiplas formas de autenticar os utilizadores que fazem pedidos-chave. A política de autorização de chave de conteúdo pode ter restrições de autorização abertas, simbólicas ou IP. (O IP pode ser configurado com REST ou o .NET SDK.)

### <a name="open-restriction"></a>Restrição aberta
A restrição aberta significa que o sistema fornece a chave para quem faz um pedido chave. Esta restrição pode ser útil para efeitos de teste.

![Política Aberta][open_policy]

### <a name="token-restriction"></a>Restrição simbólica
Para escolher a política restrita simbólica, selecione o botão **TOKEN.**

A política restrita simbólica deve ser acompanhada de um símbolo emitido por um serviço de fichas de segurança (STS). Os Media Services suportam fichas nos formatos web token simples[(SWT)](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_2)e JSON Web Token (JWT). Para mais informações, consulte [a autenticação JWT.](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

Os Serviços de Comunicação Social não fornecem STS. Você pode criar um STS personalizado para emitir fichas. O STS deve ser configurado para criar um símbolo assinado com a chave especificada e emitir alegações que especificou na configuração da restrição simbólica. Se o token for válido e as reclamações no token corresponderem às configuradas para a chave de conteúdo, o serviço de entrega de chaves dos Serviços de Media devolve a chave de encriptação ao cliente.

Ao configurar a política restrita de token, deve especificar a chave de verificação primária, o emitente e os parâmetros do público. A chave de verificação primária contém a chave com a que o símbolo foi assinado. O emitente é o STS que emite o símbolo. O público (às vezes chamado de âmbito) descreve a intenção do símbolo ou o recurso a que o símbolo autoriza o acesso. O serviço de entrega chave dos Serviços de Comunicação social valida que estes valores no símbolo correspondem aos valores do modelo.

### <a name="playready"></a>PlayReady
Quando proteges o teu conteúdo com o PlayReady, uma das coisas que precisas de especificar na tua política de autorização é uma cadeia XML que define o modelo de licença PlayReady. Por predefinição, define-se a seguinte política:

```xml
<PlayReadyLicenseResponseTemplate xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
  <LicenseTemplates>
    <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices>
      <ContentKey i:type="ContentEncryptionKeyFromHeader" />
      <LicenseType>Nonpersistent</LicenseType>
      <PlayRight>
        <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput>
      </PlayRight>
    </PlayReadyLicenseTemplate>
  </LicenseTemplates>
</PlayReadyLicenseResponseTemplate>
```

Pode selecionar o botão **xml** da política de importação e fornecer um XML diferente que está em conformidade com o esquema XML definido na visão geral do [modelo de licença do Media Services PlayReady](media-services-playready-license-template-overview.md).

## <a name="additional-notes"></a>Notas adicionais

* Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png
