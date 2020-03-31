---
title: Utilização de castLabs para entregar licenças da Widevine à Azure Media Services [ Serviços De Comunicação Social] Microsoft Docs
description: Este artigo descreve como pode utilizar o Azure Media Services (AMS) para fornecer um stream que é encriptado dinamicamente pela AMS com drMs PlayReady e Widevine.
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
ms.openlocfilehash: 29a344c739d8d99da2e5c81d41a11c601e48022e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74969145"
---
# <a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>Utilização do castLabs para entregar licenças de Widevine para Serviços de Multimédia do Azure 
> [!div class="op_single_selector"]
> * [Axinom](media-services-axinom-integration.md)
> * [castLabs](media-services-castlabs-integration.md)
> 
> 

## <a name="overview"></a>Descrição geral

Este artigo descreve como pode utilizar o Azure Media Services (AMS) para fornecer um stream que é encriptado dinamicamente pela AMS com drMs PlayReady e Widevine. A licença PlayReady provém do servidor de licença PlayReady de Media Services e a licença Widevine é entregue pelo servidor de licença **castLabs.**

Para reproduzir os conteúdos de streaming protegidos pelo CENC (PlayReady e/ou Widevine), pode utilizar [o Azure Media Player](https://aka.ms/azuremediaplayer). Consulte o [documento da AMP](https://amp.azure.net/libs/amp/latest/docs/) para obter mais detalhes.

O diagrama seguinte demonstra uma arquitetura de integração azure media de alto nível e castLabs.

![integração](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

## <a name="typical-system-set-up"></a>Configuração típica do sistema

* Os conteúdos dos meios de comunicação social são armazenados na AMS.
* As principais identificações das chaves de conteúdo são armazenadas tanto em castLabs como ams.
* castLabs e AMS têm autenticação simbólica incorporada. As seguintes secções discutem fichas de autenticação. 
* Quando um cliente pede para transmitir o vídeo, o conteúdo é dinamicamente encriptado com **Encriptação Comum** (CENC) e embalado dinamicamente pela AMS para Smooth Streaming e DASH. Também fornecemos encriptação de fluxo elementar PlayReady M2TS para o protocolo de streaming HLS.
* A licença PlayReady é recuperada do servidor de licença sAn iDa e a licença Widevine é recuperada do servidor de licença castLabs. 
* O Media Player decide automaticamente qual a licença a obter com base na capacidade da plataforma do cliente. 

## <a name="authentication-token-generation-for-getting-a-license"></a>Autenticação geração simbólica para obter uma licença

Ambos os moldes castLabs e AMS suportam o formato de ficha JWT (JSON Web Token) usado para autorizar uma licença. 

### <a name="jwt-token-in-ams"></a>Ficha jWT na AMS

A tabela seguinte descreve o símbolo JWT na AMS. 

| Emissor | Cadeia emitentes do serviço de token seguro escolhido (STS) |
| --- | --- |
| Audiência |Corda de audiência do STS usado |
| Afirmações |Um conjunto de reclamações |
| NotBefore |Iniciar a validade do símbolo |
| Validade |Validade final do símbolo |
| Credenciais de assinatura |A chave que é partilhada entre o PlayReady License Server, o CastLabs License Server e o STS, pode ser uma chave simétrica ou assimétrica. |

### <a name="jwt-token-in-castlabs"></a>Ficha jWT em castLabs

A tabela seguinte descreve o símbolo JWT em castLabs. 

| Nome | Descrição |
| --- | --- |
| optData |Uma corda JSON contendo informações sobre si. |
| crt |Uma cadeia JSON contendo informações sobre o ativo, a sua informação de licença e direitos de reprodução. |
| iat |A data atual na época. |
| jti |Um identificador único sobre este símbolo (cada token só pode ser usado uma vez no sistema castLabs). |

## <a name="sample-solution-setup"></a>Configuração da solução de amostra

A [solução de amostra](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) consiste em dois projetos:

* Uma aplicação de consola que pode ser usada para definir restrições de DRM num ativo já ingerido, tanto para playReady como Widevine.
* Uma Aplicação Web que distribui fichas, que pode ser vista como uma versão muito simplificada de um STS.

Para utilizar a aplicação da consola:

1. Altere a app.config para configurar credenciais AMS, credenciais castLabs, configuração STS e chave partilhada.
2. Faça upload de um Ativo para a AMS.
3. Obtenha o UUID do Ativo carregado e mude a Linha 32 no ficheiro Program.cs:
   
      var objIAsset = _context. Ativos.Onde (x => x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf"). FirstOrDefault();
4. Utilize um AssetId para nomear o ativo no sistema castLabs (Linha 44 no ficheiro Program.cs).
   
   Tem de definir o AssetId para **castLabs;** tem de ser uma corda alfanumérica única.
5. Execute o programa.

Para utilizar a Aplicação Web (STS):

1. Altere o web.config para configurar o ID do mercador de lajes, a configuração STS e a chave partilhada.
2. Implemente para websites do Azure.
3. Navegue para o site.

## <a name="playing-back-a-video"></a>Reproduzindo um vídeo

Para reproduzir um vídeo encriptado com encriptação comum (PlayReady e/ou Widevine), pode utilizar o [Azure Media Player](https://aka.ms/azuremediaplayer). Ao executar a aplicação da consola, o ID da chave de conteúdo e o URL manifesto são ecoados.

1. Abra um novo separador e lance o seu STS: http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2. Vá ao [Azure Media Player.](https://aka.ms/azuremediaplayer)
3. Pasta no URL de streaming.
4. Clique na caixa de verificação **De Opções Avançadas.**
5. No **dropdown** protection, selecione PlayReady e/ou Widevine.
6. Cola o símbolo que ganhaste do teu STS na caixa de texto token. 
   
   O servidor de licença castLab não precisa do prefixo "Bearer=" em frente ao token. Por isso, por favor, remova-o antes de submeter o símbolo.
7. Atualize o jogador.
8. O vídeo devia estar a ser filmado.

## <a name="additional-notes"></a>Notas adicionais

* A Widevine é um serviço prestado pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

