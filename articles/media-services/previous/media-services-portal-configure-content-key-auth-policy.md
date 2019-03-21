---
title: Configurar uma política de autorização da chave de conteúdo com o portal do Azure | Documentos da Microsoft
description: Saiba como configurar uma política de autorização para uma chave de conteúdo.
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
ms.openlocfilehash: b046ce5a8647abe601a6327667241d98445ce1e4
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259895"
---
# <a name="configure-a-content-key-authorization-policy"></a>Configurar uma política de autorização da chave de conteúdo
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Descrição geral
 Pode utilizar os serviços de multimédia do Azure para entregar transmissões em fluxo MPEG-DASH, Smooth Streaming e HTTP Live Streaming (HLS) protegidas com encriptação AES (Advanced Standard) utilizando as chaves de encriptação de 128 bits ou [gestão de direitos digitais do PlayReady (DRM)](https://www.microsoft.com/playready/overview/). Com os Media Services, também pode entregar transmissões em fluxo DASH encriptados com Widevine DRM. Tanto o PlayReady, como o Widevine, são encriptados de acordo com a especificação de encriptação comum (ISO/IEC 23001 7 CENC).

Serviços de multimédia também fornecem um serviço de entrega de chaves/licenças partir do qual os clientes podem obter chaves AES ou licenças do PlayReady/Widevine para reproduzir o conteúdo encriptado.

Este artigo mostra como utilizar o portal do Azure para configurar a política de autorização da chave de conteúdo. A chave mais tarde pode ser utilizada para encriptar dinamicamente o seu conteúdo. Atualmente, pode encriptar HLS, MPEG-DASH e formatos de transmissão em fluxo uniforme. Não é possível encriptar transferências progressivas.

Quando um jogador solicita um fluxo que está definido como seja encriptado dinamicamente, serviços de multimédia utiliza a chave configurada para encriptar dinamicamente o seu conteúdo através de encriptação AES ou o DRM. Para desencriptar a transmissão em fluxo, o leitor solicita a chave ao serviço de entrega de chaves. Para determinar se o utilizador está autorizado a obter a chave, o serviço avalia as políticas de autorização que especificou para a chave.

Se pretender ter várias chaves de conteúdo ou pretender especificar um URL de serviço de entrega de chaves/licenças além do serviço de entrega de chave de serviços de multimédia, utilize o SDK .NET dos Media Services ou de REST APIs. Para obter mais informações, consulte:

* [Configurar uma política de autorização da chave de conteúdo com o SDK .NET dos Media Services](media-services-dotnet-configure-content-key-auth-policy.md)
* [Configurar uma política de autorização da chave de conteúdo com a API de REST de serviços de multimédia](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>São aplicáveis algumas considerações
* Quando a conta dos Serviços de Multimédia é criada, é adicionado um ponto final de transmissão em fluxo predefinido à mesma, no estado "Parado". Para iniciar o seu conteúdo de transmissão em fluxo e tirar partido do empacotamento e encriptação dinâmica, tem de ser o ponto final de transmissão em fluxo no estado "Em execução". 
* O elemento tem de conter um conjunto de MP4s de velocidade de transmissão adaptável ou ficheiros de transmissão em fluxo uniforme de velocidade de transmissão adaptável. Para obter mais informações, consulte [codificar um elemento](media-services-encode-asset.md).
* O serviço de entrega de chave coloca em cache ContentKeyAuthorizationPolicy e seus objetos relacionados (opções de política e restrições) durante 15 minutos. Pode criar um ContentKeyAuthorizationPolicy e especificar para utilizar uma restrição de token, testá-lo e, em seguida, atualizar a política para a restrição aberta. Este processo demora cerca de 15 minutos antes dos comutadores de política para a versão aberta.
* Um ponto final de transmissão em fluxo dos serviços de multimédia define o valor do cabeçalho CORS Access-Control-Allow-Origin na resposta de simulação como o caráter universal "\*". Este valor funciona bem com a maioria dos leitores, incluindo o leitor de multimédia do Azure, Roku e JWPlayer e outros. No entanto, alguns leitores que utilizam dash. js não funcionam porque, com o modo de credenciais definido como "include", XMLHttpRequest no respetivo dash. js não permite o caráter universal "\*" como o valor do Access-Control-Allow-Origin. Como solução para essa limitação no dash. js, se alojar o seu cliente a partir de um único domínio, serviços de multimédia pode especificar que o domínio no cabeçalho de resposta de simulação. Para obter ajuda, abra um pedido de suporte através do portal do Azure.

## <a name="configure-the-key-authorization-policy"></a>Configurar a política de autorização da chave
Para configurar a política de autorização da chave, selecione o **PROTEÇÃO de conteúdo** página.

Os Media Services suportam várias formas de autenticar os utilizadores que efetuam pedidos de chave. A política de autorização da chave de conteúdo pode ter aberto, token ou restrições de autorização de IP. (O IP pode ser configurado com REST ou o SDK .NET).

### <a name="open-restriction"></a>Restrição aberta
A restrição aberta significa que o sistema fornece a chave para qualquer pessoa que faz uma solicitação de chave. Esta restrição poderá ser útil para fins de teste.

![OpenPolicy][open_policy]

### <a name="token-restriction"></a>Restrição de token
Para escolher a política de token restrito, selecione o **TOKEN** botão.

A política de token restrito tem de ser acompanhada de um token emitido por um serviço de token de segurança (STS). Serviços de multimédia suportam tokens no token web simples ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) e formatos de JSON Web Token (JWT). Para obter mais informações, consulte [autenticação de JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Serviços de multimédia não fornece o STS. Pode criar um STS personalizado para emitir tokens. O STS deve ser configurado para criar um token assinado com as declarações de chave e o problema especificadas que especificou na configuração de restrição de token. Se o token é válido e as afirmações no token corresponderem às configuradas para a chave de conteúdo, o serviço de entrega de chave de serviços de multimédia devolve a chave de encriptação para o cliente.

Quando configurar a política de token restrito, tem de especificar a chave de verificação primária, emissor e parâmetros de público-alvo. A chave de verificação primária contém a chave de que o token foi assinado com. O emissor é o STS que emite o token. O público-alvo (por vezes denominado âmbito) descreve a intenção do token ou o recurso o token de acesso a autoriza. O serviço de entrega de chave de serviços de multimédia valida que estes valores no token correspondem aos valores no modelo.

### <a name="playready"></a>PlayReady
Quando proteger o seu conteúdo com o PlayReady, uma das coisas que precisa especificar na sua política de autorização é uma cadeia de caracteres XML que define o modelo de licença PlayReady. Por predefinição, está definida a seguinte política:

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

Pode selecionar o **Importar política xml** botão e fornecer um XML diferente que esteja em conformidade com o esquema XML definido no [descrição geral do modelo de licença PlayReady de serviços de multimédia](media-services-playready-license-template-overview.md).

## <a name="next-steps"></a>Passos Seguintes
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

