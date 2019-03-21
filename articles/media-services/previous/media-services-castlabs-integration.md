---
title: Utilizar castLabs para entregar licenças de Widevine para serviços de multimédia do Azure | Documentos da Microsoft
description: Este artigo descreve como pode utilizar o Azure Media Services (AMS) para fornecer um fluxo dinamicamente criptografada pela AMS com PlayReady e Widevine DRMs. A licença de PlayReady vem do servidor de licenças do PlayReady dos serviços de multimédia e licença do Widevine é entregue pelo servidor de licenças do castLabs.
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
ms.author: Mingfeiy;willzhan;Juliako
ms.openlocfilehash: dfb82e91b0f65b85d34b7e20d57ed9929469321f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57863262"
---
# <a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>Utilização do castLabs para entregar licenças de Widevine para Serviços de Multimédia do Azure 
> [!div class="op_single_selector"]
> * [Axinom](media-services-axinom-integration.md)
> * [castLabs](media-services-castlabs-integration.md)
> 
> 

## <a name="overview"></a>Descrição geral

Este artigo descreve como pode utilizar o Azure Media Services (AMS) para fornecer um fluxo dinamicamente criptografada pela AMS com PlayReady e Widevine DRMs. A licença de PlayReady vem do servidor de licenças do PlayReady dos serviços de suporte de dados e a licença do Widevine é entregue pelo **castLabs** servidor de licenças.

Para reproduzir novamente de transmissão em fluxo conteúdo protegido pelo CENC (PlayReady e/ou Widevine), pode usar [leitor de multimédia do Azure](https://amsplayer.azurewebsites.net/azuremediaplayer.html). Ver [documento AMP](https://amp.azure.net/libs/amp/latest/docs/) para obter detalhes.

O diagrama a seguir demonstra um alto nível serviços de multimédia do Azure e a arquitetura de integração do castLabs.

![Integração](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

## <a name="typical-system-set-up"></a>Sistema típico configurar

* Conteúdo de mídia é armazenado no AMS.
* IDs de chave das chaves de conteúdo são armazenados em castLabs e AMS.
* castLabs e AMS têm a autenticação de token incorporada. As secções seguintes abordam os tokens de autenticação. 
* Quando um cliente pede para transmitir o vídeo, o conteúdo é encriptado dinamicamente com **encriptação comum** (CENC) e dinamicamente em pacote por AMS para transmissão em fluxo uniforme e DASH. Também para fornecer encriptação de stream elementares PlayReady M2TS para o protocolo de transmissão em fluxo HLS.
* Licença de PlayReady é recuperada do servidor de licenças do AMS e licença do Widevine é recuperada do servidor de licenças do castLabs. 
* O leitor de multimédia automaticamente decide que licença para buscar com base na capacidade de plataforma de cliente. 

## <a name="authentication-token-generation-for-getting-a-license"></a>Geração de tokens de autenticação para obter uma licença

CastLabs e AMS suporta o formato do token JWT (JSON Web Token) utilizado para autorizar uma licença. 

### <a name="jwt-token-in-ams"></a>Token JWT em AMS

A tabela seguinte descreve o token JWT no AMS. 

| Emissor | Cadeia de caracteres do emissor do escolhido proteger o serviço de Token (STS) |
| --- | --- |
| Audiência |Cadeia de caracteres do público-alvo do STS utilizado |
| Afirmações |Um conjunto de declarações |
| NotBefore |Começar a validade do token |
| Expira |Fim de validade do token |
| SigningCredentials |A chave que é partilhada entre o servidor de licenças do PlayReady, castLabs servidor de licenças e o STS, poderia ser simétrico ou assimétrico chave. |

### <a name="jwt-token-in-castlabs"></a>Token JWT em castLabs

A tabela seguinte descreve o token JWT em castLabs. 

| Name | Descrição |
| --- | --- |
| optData |Uma cadeia JSON que contém informações sobre si. |
| crt |Uma cadeia de caracteres do JSON que contém informações sobre o elemento, seus direitos de reprodução e as informações de licença. |
| iat |A data/hora atual na época. |
| jti |Um identificador exclusivo sobre este token (cada token só pode ser utilizado uma vez no sistema castLabs). |

## <a name="sample-solution-setup"></a>Configuração da solução de exemplo

O [solução de exemplo](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) consiste em dois projetos:

* Uma aplicação de consola que pode ser utilizada para definir restrições de DRM sobre um recurso já ingerido, para o PlayReady e Widevine.
* Um aplicativo Web que distribui tokens, pelo que podem ser vistos como uma versão muito simplificada de um STS.

Para utilizar a aplicação de consola:

1. Altere o App. config para configurar as credenciais de AMS, castLabs credenciais, configuração de STS e a chave partilhada.
2. Carregar um recurso para o AMS.
3. Obter o UUID do elemento carregado e altere a linha 32 no ficheiro Program.cs:
   
      var objIAsset = _context.Assets.Where(x => x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf").FirstOrDefault();
4. Utilize um AssetId para atribuir nomes o ativos no sistema castLabs (44 de linha no ficheiro Program.cs).
   
   Tem de definir AssetId para **castLabs**; ele precisa ser uma cadeia exclusiva de alfanumérica.
5. Execute o programa.

Para utilizar a aplicação Web (STS):

1. Altere o Web. config para comerciante do programa de configuração castlabs ID, a configuração do STS e a chave partilhada.
2. Implemente Web sites do Azure.
3. Navegue para o Web site.

## <a name="playing-back-a-video"></a>Reproduzir um vídeo

Para reproduzir um vídeo encriptado com encriptação comum (PlayReady e/ou Widevine), pode utilizar o [leitor de multimédia do Azure](https://amsplayer.azurewebsites.net/azuremediaplayer.html). Ao executar a aplicação de consola, são refletido o ID de chave de conteúdo e o URL do manifesto.

1. Abra um novo separador e iniciar o seu STS: http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2. Aceda a [leitor de multimédia do Azure](https://amsplayer.azurewebsites.net/azuremediaplayer.html).
3. Cole o URL de transmissão em fluxo.
4. Clique nas **opções avançadas** caixa de verificação.
5. Na **proteção** lista pendente, selecione PlayReady e/ou Widevine.
6. Cole o token que recebeu do seu STS na caixa de texto do Token. 
   
   O servidor de licenças castLab não precisa do "portador =" prefixo à frente o token. Portanto, remova que antes de submeter o token.
7. Atualize o jogador.
8. Deve ser reproduzir o vídeo.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

