---
title: Configure uma política de autorização de chave de conteúdo utilizando o portal Azure [ Microsoft Docs
description: Este artigo demonstra como configurar uma política de autorização para uma chave de conteúdo.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ee82a3fa-c34b-48f2-a108-8ba321f1691e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 8580bafd4d68ef6567b09fefcaa01c682ae2cafe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74968804"
---
# <a name="configure-a-content-key-authorization-policy"></a>Configurar uma política de autorização de chave de conteúdo
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Descrição geral
 Pode utilizar os Serviços De Mídia Azure para fornecer streams MPEG-DASH, Smooth Streaming e HTTP Live Streaming (HLS) protegidos com Advanced Encryption Standard (AES) utilizando chaves de encriptação de 128 bits ou gestão de [direitos digitais PlayReady (DRM)](https://www.microsoft.com/playready/overview/). Com os Serviços de Media, também pode entregar streams DASH encriptados com DrM Widevine. Tanto o PlayReady, como o Widevine, são encriptados de acordo com a especificação de encriptação comum (ISO/IEC 23001 7 CENC).

A Media Services também fornece um serviço de entrega de chaves/licenças a partir do qual os clientes podem obter chaves AES ou licenças PlayReady/Widevine para reproduzir o conteúdo encriptado.

Este artigo mostra como usar o portal Azure para configurar a política de autorização chave de conteúdo. A chave pode ser usada mais tarde para encriptar dinamicamente o seu conteúdo. Atualmente, pode encriptar formatos HLS, MPEG-DASH e Smooth Streaming. Não se pode encriptar downloads progressivos.

Quando um jogador solicita um fluxo que está definido para ser encriptado dinamicamente, os Media Services utilizam a chave configurada para encriptar dinamicamente o seu conteúdo utilizando encriptação AES ou DRM. Para desencriptar a transmissão em fluxo, o leitor solicita a chave ao serviço de entrega de chaves. Para determinar se o utilizador está autorizado a obter a chave, o serviço avalia as políticas de autorização que especificou para a chave.

Se pretender ter várias chaves de conteúdo ou pretender especificar um URL de serviço de entrega de chaves/licenças que não seja o serviço de entrega da chave media Services, utilize os Media Services .NET SDK ou REST APIs. Para obter mais informações, consulte:

* [Configure uma política de autorização de chave de conteúdo utilizando o Media Services .NET SDK](media-services-dotnet-configure-content-key-auth-policy.md)
* [Configure uma política de autorização de chave de conteúdo utilizando a API REST Dos Serviços de Media](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>Algumas considerações aplicam-se
* Quando a conta dos Serviços de Multimédia é criada, é adicionado um ponto final de transmissão em fluxo predefinido à mesma, no estado "Parado". Para começar a transmitir o seu conteúdo e tirar partido de embalagens dinâmicas e encriptação dinâmica, o seu ponto final de streaming deve estar no estado "Running". 
* O seu ativo deve conter um conjunto de MP4s bitrate adaptativos ou ficheiros adaptativos de streaming liso de bitrate. Para mais informações, consulte [encode um ativo](media-services-encode-asset.md).
* O serviço de entrega chave caches ContentKeyAuthorizationPolicy e seus objetos relacionados (opções de política e restrições) durante 15 minutos. Pode criar uma Política de Autorização de Autorização de Conteúdo e especificar usar uma restrição simbólica, testá-la e, em seguida, atualizar a política para a restrição aberta. Este processo demora cerca de 15 minutos até que a política mude para a versão aberta.
* Um ponto final de streaming de Serviços de Media define o valor do cabeçalho\*CORS Access-Control-Allow-Origin em resposta pré-voo como wildcard " . Este valor funciona bem com a maioria dos jogadores, incluindo Azure Media Player, Roku e JWPlayer, entre outros. No entanto, alguns jogadores que usam dash.js não funcionam porque, com o modo de credenciais definido\*para "incluir", XMLHttpRequest no seu dash.js não permite o wildcard " como o valor de Access-Control-Allow-Origin. Como suposições para esta limitação em dash.js, se hospedar o seu cliente a partir de um único domínio, a Media Services pode especificar esse domínio no cabeçalho de resposta pré-voo. Para assistência, abra um bilhete de apoio através do portal Azure.

## <a name="configure-the-key-authorization-policy"></a>Configure a política de autorização chave
Para configurar a política de autorização chave, selecione a página **DE PROTEÇÃO DE CONTEÚDO.**

A Media Services suporta várias formas de autenticar os utilizadores que fazem pedidos chave. A política de autorização chave de conteúdo pode ter restrições de autorização abertas, simbólicas ou IP. (IP pode ser configurado com REST ou o .NET SDK.)

### <a name="open-restriction"></a>Restrição aberta
A restrição aberta significa que o sistema fornece a chave a quem fizer um pedido chave. Esta restrição pode ser útil para efeitos de teste.

![Política Aberta][open_policy]

### <a name="token-restriction"></a>Restrição simbólica
Para escolher a política restrita de fichas, selecione o botão **TOKEN.**

A política restrita simbólica deve ser acompanhada por um símbolo emitido por um serviço de token de segurança (STS). Os serviços de mídia suportam tokens nos formatos simples web token[(SWT)](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)e JSON Web Token (JWT). Para mais informações, consulte [a autenticação JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

A Media Services não fornece STS. Pode criar um STS personalizado para emitir fichas. O STS deve ser configurado para criar um símbolo assinado com a chave especificada e emitir alegações que especificaste na configuração de restrição simbólica. Se o token for válido e as reclamações no token corresponderem às configuradas para a chave de conteúdo, o serviço de entrega da chave Media Services devolve a chave de encriptação ao cliente.

Quando configurar a política restrita de fichas, deve especificar a chave de verificação primária, o emitente e os parâmetros do público. A chave de verificação primária contém a chave com a que o símbolo foi assinado. O emitente é o STS que emite o símbolo. O público (às vezes chamado de âmbito) descreve a intenção do símbolo ou o recurso a que o símbolo autoriza o acesso. O serviço de entrega de chaves media services valida que estes valores no token correspondem aos valores do modelo.

### <a name="playready"></a>PlayReady
Quando protege o seu conteúdo com o PlayReady, uma das coisas que precisa de especificar na sua política de autorização é uma cadeia XML que define o modelo de licença PlayReady. Por predefinição, é definida a seguinte política:

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

Pode selecionar o botão **xml da política de importação** e fornecer um XML diferente que esteja em conformidade com o esquema XML definido na visão geral do modelo de licença [PlayReady dos Serviços de Comunicação Social](media-services-playready-license-template-overview.md).

## <a name="additional-notes"></a>Notas adicionais

* A Widevine é um serviço prestado pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

