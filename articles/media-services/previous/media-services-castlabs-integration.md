---
title: Usando o castLabs para fornecer licenças Widevine para os serviços de mídia do Azure | Microsoft Docs
description: Este artigo descreve como você pode usar o AMS (serviços de mídia do Azure) para fornecer um fluxo que é criptografado dinamicamente pelo AMS com o PlayReady e o Widevine DRMs. A licença do PlayReady vem do servidor de licença do PlayReady dos serviços de mídia e a licença do Widevine é fornecida pelo servidor de licença do castLabs.
services: media-services
documentationcenter: ''
author: Mingfeiy
manager: femila
editor: ''
ms.assetid: 2a9a408a-a995-49e1-8d8f-ac5b51e17d40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: Juliako
ms.reviewer: willzhan
ms.openlocfilehash: 9c61fad333037074f392b019ae61c161673e4008
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/17/2019
ms.locfileid: "69016680"
---
# <a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>Utilização do castLabs para entregar licenças de Widevine para Serviços de Multimédia do Azure 
> [!div class="op_single_selector"]
> * [Axinom](media-services-axinom-integration.md)
> * [castLabs](media-services-castlabs-integration.md)
> 
> 

## <a name="overview"></a>Descrição geral

Este artigo descreve como você pode usar o AMS (serviços de mídia do Azure) para fornecer um fluxo que é criptografado dinamicamente pelo AMS com o PlayReady e o Widevine DRMs. A licença do PlayReady vem do servidor de licença do PlayReady dos serviços de mídia e a licença do Widevine é fornecida pelo servidor de licença do **castLabs** .

Para reproduzir o conteúdo de streaming protegido pelo CENC (PlayReady e/ou Widevine), você pode usar [player de mídia do Azure](https://aka.ms/azuremediaplayer). Consulte [amp Document](https://amp.azure.net/libs/amp/latest/docs/) para obter detalhes.

O diagrama a seguir demonstra uma arquitetura de integração do castLabs e dos serviços de mídia do Azure de alto nível.

![integrar](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

## <a name="typical-system-set-up"></a>Configuração típica do sistema

* O conteúdo de mídia é armazenado no AMS.
* As IDs de chave de chaves de conteúdo são armazenadas em castLabs e AMS.
* o castLabs e o AMS têm autenticação de token interna. As seções a seguir discutem os tokens de autenticação. 
* Quando um cliente solicita o fluxo do vídeo, o conteúdo é criptografado dinamicamente com **criptografia comum** (Cenc) e empacotado DINAMICAMENTE pelo AMS para Smooth streaming e Dash. Também fornecemos a criptografia de fluxo elementar M2TS do PlayReady para o protocolo de streaming HLS.
* A licença do PlayReady é recuperada do servidor de licença do AMS e a licença do Widevine é recuperada do servidor de licença do castLabs. 
* O player de mídia decide automaticamente qual licença deve ser buscada com base na capacidade da plataforma do cliente. 

## <a name="authentication-token-generation-for-getting-a-license"></a>Geração de token de autenticação para obter uma licença

O castLabs e o AMS dão suporte ao formato de token JWT (token Web JSON) usado para autorizar uma licença. 

### <a name="jwt-token-in-ams"></a>Token JWT no AMS

A tabela a seguir descreve o token JWT no AMS. 

| Emissor | Cadeia de caracteres do emissor do serviço de token de segurança (STS) escolhido |
| --- | --- |
| Audiência |Cadeia de caracteres de público do STS usado |
| Afirmações |Um conjunto de declarações |
| NotBefore |Iniciar validade do token |
| Expira |Validade final do token |
| SigningCredentials |A chave que é compartilhada entre o servidor de licença do PlayReady, o servidor de licença castLabs e o STS, pode ser uma chave simétrica ou assimétrica. |

### <a name="jwt-token-in-castlabs"></a>Token JWT em castLabs

A tabela a seguir descreve o token JWT em castLabs. 

| Nome | Descrição |
| --- | --- |
| optData |Uma cadeia de caracteres JSON que contém informações sobre você. |
| CRT |Uma cadeia de caracteres JSON que contém informações sobre o ativo, suas informações de licença e seus direitos de reprodução. |
| IAT |A data e hora atual em época. |
| jti |Um identificador exclusivo sobre esse token (cada token só pode ser usado uma vez no sistema castLabs). |

## <a name="sample-solution-setup"></a>Configuração da solução de exemplo

A [solução de exemplo](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) consiste em dois projetos:

* Um aplicativo de console que pode ser usado para definir restrições de DRM em um ativo já ingerido, para PlayReady e Widevine.
* Um aplicativo Web que distribui tokens, que podem ser vistos como uma versão muito SIMPLIFICAda de um STS.

Para usar o aplicativo de console:

1. Altere o app. config para configurar as credenciais do AMS, as credenciais do castLabs, a configuração do STS e a chave compartilhada.
2. Carregar um ativo no AMS.
3. Obtenha o UUID do ativo carregado e altere a linha 32 no arquivo Program.cs:
   
      var objIAsset = _context.Assets.Where(x => x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf").FirstOrDefault();
4. Use um AssetID para nomear o ativo no sistema castLabs (linha 44 no arquivo Program.cs).
   
   Você deve definir AssetID para **castLabs**; Ele precisa ser uma cadeia de caracteres alfanumérica exclusiva.
5. Execute o programa.

Para usar o aplicativo Web (STS):

1. Altere o Web. config para configurar a ID de comerciante do castlabs, a configuração do STS e a chave compartilhada.
2. Implante nos sites do Azure.
3. Navegue até o site.

## <a name="playing-back-a-video"></a>Reproduzindo um vídeo

Para reproduzir um vídeo criptografado com criptografia comum (PlayReady e/ou Widevine), você pode usar o [player de mídia do Azure](https://aka.ms/azuremediaplayer). Ao executar o aplicativo de console, a ID da chave de conteúdo e a URL do manifesto são ecoadas.

1. Abra uma nova guia e inicie seu STS: http://[yourStsName]. azurewebsites. NET/API/token/AssetID/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2. Vá para [player de mídia do Azure](https://aka.ms/azuremediaplayer).
3. Cole a URL de streaming.
4. Clique na caixa de seleção **Opções avançadas** .
5. Na lista suspensa **proteção** , selecione PlayReady e/ou Widevine.
6. Cole o token obtido de seu STS na caixa de texto token. 
   
   O servidor de licença castLab não precisa do prefixo "portador =" na frente do token. Portanto, remova-o antes de enviar o token.
7. Atualize o Player.
8. O vídeo deve ser reproduzido.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

