---
title: Configurar uma política de autorização de chave de conteúdo usando o portal do Azure | Microsoft Docs
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
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74968804"
---
# <a name="configure-a-content-key-authorization-policy"></a>Configurar uma política de autorização de chave de conteúdo
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Visão geral
 Você pode usar os serviços de mídia do Azure para fornecer fluxos MPEG-DASH, Smooth Streaming e HTTP Live Streaming (HLS) protegidos com o criptografia AES (AES) usando chaves de criptografia de 128 bits ou o [DRM (gerenciamento de direitos digitais) do PlayReady](https://www.microsoft.com/playready/overview/). Com os serviços de mídia, você também pode fornecer fluxos de traço criptografados com o Widevine DRM. Tanto o PlayReady, como o Widevine, são encriptados de acordo com a especificação de encriptação comum (ISO/IEC 23001 7 CENC).

Os serviços de mídia também fornecem um serviço de entrega de chave/licença do qual os clientes podem obter chaves AES ou licenças PlayReady/Widevine para reproduzir o conteúdo criptografado.

Este artigo mostra como usar o portal do Azure para configurar a política de autorização de chave de conteúdo. A chave pode ser usada posteriormente para criptografar dinamicamente seu conteúdo. No momento, você pode criptografar os formatos HLS, MPEG-DASH e Smooth Streaming. Não é possível criptografar downloads progressivos.

Quando um Player solicita um fluxo definido para ser criptografado dinamicamente, os serviços de mídia usam a chave configurada para criptografar dinamicamente o conteúdo usando a criptografia AES ou DRM. Para desencriptar a transmissão em fluxo, o leitor solicita a chave ao serviço de entrega de chaves. Para determinar se o usuário está autorizado a obter a chave, o serviço avalia as políticas de autorização que você especificou para a chave.

Se você planeja ter várias chaves de conteúdo ou deseja especificar uma URL de serviço de entrega de chave/licença diferente do serviço de distribuição de chaves dos serviços de mídia, use o SDK do .NET dos serviços de mídia ou as APIs REST. Para obter mais informações, veja:

* [Configurar uma política de autorização de chave de conteúdo usando o SDK do .NET dos serviços de mídia](media-services-dotnet-configure-content-key-auth-policy.md)
* [Configurar uma política de autorização de chave de conteúdo usando a API REST dos serviços de mídia](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>Algumas considerações se aplicam
* Quando a conta dos Serviços de Multimédia é criada, é adicionado um ponto final de transmissão em fluxo predefinido à mesma, no estado "Parado". Para começar a transmitir seu conteúdo e aproveitar o empacotamento dinâmico e a criptografia dinâmica, o ponto de extremidade de streaming deve estar no estado "em execução". 
* Seu ativo deve conter um conjunto de MP4s de taxa de bits adaptável ou arquivos Smooth Streaming de taxa de bits adaptável. Para obter mais informações, consulte [codificar um ativo](media-services-encode-asset.md).
* O serviço de distribuição de chaves armazena em cache ContentKeyAuthorizationPolicy e seus objetos relacionados (opções e restrições de política) por 15 minutos. Você pode criar um ContentKeyAuthorizationPolicy e especificar para usar uma restrição de token, testá-lo e, em seguida, atualizar a política para a restrição aberta. Esse processo leva aproximadamente 15 minutos antes que a política alterne para a versão aberta.
* Um ponto de extremidade de streaming dos serviços de mídia define o valor do cabeçalho acesso-controle-permitir-origem CORS na resposta de simulação como o curinga "\*". Esse valor funciona bem com a maioria dos players, incluindo Player de Mídia do Azure, Roku e JWPlayer, entre outros. No entanto, alguns players que usam o Dash. js não funcionam porque, com o modo de credenciais definido como "include", XMLHttpRequest em seu Dash. js não permite o curinga "\*" como o valor de Access-Control-Allow-Origin. Como uma solução alternativa para essa limitação em Dash. js, se você hospedar o cliente de um único domínio, os serviços de mídia poderão especificar esse domínio no cabeçalho de resposta de simulação. Para obter assistência, abra um tíquete de suporte por meio do portal do Azure.

## <a name="configure-the-key-authorization-policy"></a>Configurar a política de autorização de chave
Para configurar a política de autorização de chave, selecione a página **proteção de conteúdo** .

Os serviços de mídia oferecem suporte a várias maneiras de autenticar usuários que fazem solicitações de chave. A política de autorização de chave de conteúdo pode ter restrições de autorização aberta, de token ou de IP. (O IP pode ser configurado com o REST ou o SDK do .NET.)

### <a name="open-restriction"></a>Restrição aberta
A restrição aberta significa que o sistema fornece a chave para qualquer pessoa que faça uma solicitação de chave. Essa restrição pode ser útil para fins de teste.

![OpenPolicy][open_policy]

### <a name="token-restriction"></a>Restrição de token
Para escolher a política restrita de token, selecione o botão **token** .

A política restrita de token deve ser acompanhada por um token emitido por um serviço de token de segurança (STS). Os serviços de mídia oferecem suporte a tokens nos formatos Simple Web token ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) e token Web JSON (JWT). Para obter mais informações, consulte [autenticação JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Os serviços de mídia não fornecem STS. Você pode criar um STS personalizado para emitir tokens. O STS deve ser configurado para criar um token assinado com as declarações de chave e o problema especificadas que especificou na configuração de restrição de token. Se o token for válido e as declarações no token corresponderem àquelas configuradas para a chave de conteúdo, o serviço de distribuição de chaves dos serviços de mídia retornará a chave de criptografia para o cliente.

Ao configurar a política restrita por token, você deve especificar os parâmetros de chave de verificação primária, emissor e público. A chave de verificação primária contém a chave de que o token foi assinado com. O emissor é o STS que emite o token. O público (às vezes chamado de escopo) descreve a intenção do token ou o recurso ao qual o token autoriza o acesso. O serviço de entrega de chave de serviços de multimédia valida que estes valores no token correspondem aos valores no modelo.

### <a name="playready"></a>PlayReady
Quando você protege seu conteúdo com o PlayReady, uma das coisas que você precisa especificar em sua política de autorização é uma cadeia de caracteres XML que define o modelo de licença do PlayReady. Por padrão, a seguinte política é definida:

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

Você pode selecionar o botão **importar XML de política** e fornecer um XML diferente que esteja de acordo com o esquema XML definido na [visão geral do modelo de licença PlayReady dos serviços de mídia](media-services-playready-license-template-overview.md).

## <a name="additional-notes"></a>Notas adicionais

* O Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

