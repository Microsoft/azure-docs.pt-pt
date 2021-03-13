---
title: Utilizar o castLabs para entregar licenças widevine à Azure Media Services | Microsoft Docs
description: Este artigo descreve como pode utilizar a Azure Media Services (AMS) para entregar um stream que é dinamicamente encriptado pela AMS com DRMs PlayReady e Widevine.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 2a9a408a-a995-49e1-8d8f-ac5b51e17d40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: willzhan
ms.openlocfilehash: 576ac636f166e2daebbb9919d6666fea913a17be
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017057"
---
# <a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>Utilização do castLabs para entregar licenças de Widevine para Serviços de Multimédia do Azure

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]
 
> [!div class="op_single_selector"]
> * [Axinom](media-services-axinom-integration.md)
> * [castLabs](media-services-castlabs-integration.md)
> 
> 

## <a name="overview"></a>Descrição Geral

Este artigo descreve como pode utilizar a Azure Media Services (AMS) para entregar um stream que é dinamicamente encriptado pela AMS com DRMs PlayReady e Widevine. A licença PlayReady provém do servidor de licenças PlayReady dos Media Services e a licença Widevine é entregue pelo servidor de licença **castLabs.**

Para reproduzir o conteúdo de streaming protegido pelo CENC (PlayReady e/ou Widevine), pode utilizar  [o Azure Media Player](https://aka.ms/azuremediaplayer). Consulte [o documento da AMP](https://amp.azure.net/libs/amp/latest/docs/) para mais detalhes.

O diagrama seguinte demonstra uma arquitetura de integração de Azure Media Services de alto nível e castLabs.

![integração](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

## <a name="typical-system-set-up"></a>Configuração típica do sistema

* O conteúdo dos meios de comunicação é armazenado na AMS.
* Os IDs-chave das teclas de conteúdo são armazenados tanto em castLabs como em AMS.
* CastLabs e AMS ambos têm autenticação simbólica incorporada. As seguintes secções discutem fichas de autenticação. 
* Quando um cliente solicita a transmissão do vídeo, o conteúdo é dinamicamente encriptado com **Encriptação Comum** (CENC) e dinamicamente embalado pela AMS para Smooth Streaming e DASH. Também entregamos encriptação de fluxo elementar PlayReady M2TS para o protocolo de streaming HLS.
* A licença PlayReady é recuperada do servidor de licença AMS e a licença Widevine é recuperada do servidor de licença do castLabs. 
* O Media Player decide automaticamente qual a licença a obter com base na capacidade da plataforma do cliente. 

## <a name="authentication-token-generation-for-getting-a-license"></a>Geração simbólica de autenticação para obter uma licença

Tanto o formato de token de suporte de castLabs como AMS suporte jWT (JSON Web Token) usado para autorizar uma licença. 

### <a name="jwt-token-in-ams"></a>JWT token na AMS

A tabela seguinte descreve o símbolo JWT na AMS. 

| Emissor | Cadeia emitente do serviço de token seguro escolhido (STS) |
| --- | --- |
| Audiência |Cadeia de audiência do STS usado |
| Afirmações |Um conjunto de reclamações |
| NotBefore |Iniciar a validade do token |
| Validade |Fim da validade do token |
| AssinaturasCredentais |A chave que é partilhada entre o PlayReady License Server, o castLabs License Server e o STS, pode ser a chave simétrica ou assimétrica. |

### <a name="jwt-token-in-castlabs"></a>JWT token em castLabs

A tabela seguinte descreve o símbolo JWT em castLabs. 

| Nome | Descrição |
| --- | --- |
| optData |Uma corda JSON contendo informações sobre si. |
| crt |Uma cadeia JSON contendo informações sobre o ativo, as suas informações de licença e direitos de reprodução. |
| iat |A data atual na época. |
| jti |Um identificador único sobre este símbolo (cada ficha só pode ser usada uma vez no sistema castLabs). |

## <a name="sample-solution-setup"></a>Configuração da solução de amostra

A [solução de amostra](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) consiste em dois projetos:

* Uma aplicação de consola que pode ser usada para definir restrições de DRM num ativo já ingerido, tanto para o PlayReady como para o Widevine.
* Uma Aplicação Web que distribui fichas, que pode ser vista como uma versão muito simplificada de uma STS.

Para utilizar a aplicação da consola:

1. Altere o app.config para configurar credenciais AMS, credenciais de castLabs, configuração STS e chave partilhada.
2. Faça o upload de um ativo para a AMS.
3. Obtenha o UUID do Ativo carregado e altere a Linha 32 no ficheiro Program.cs:
   
      var objIAsset = _context. Ativos.Where(x => x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf"). FirstOrdefault();
4. Utilize um AssetId para nomear o ativo no sistema castLabs (Linha 44 no ficheiro Program.cs).
   
   Tem de definir o AssetId para **os castLabs;** tem de ser uma corda alfanumérica única.
5. Executar o programa.

Para utilizar a Aplicação Web (STS):

1. Altere o web.config para configurar o ID do mercador de lajes, a configuração STS e a chave partilhada.
2. Implementar para websites Azure.
3. Navegue para o site.

## <a name="playing-back-a-video"></a>Reproduzindo um vídeo

Para reproduzir um vídeo encriptado com encriptação comum (PlayReady e/ou Widevine), pode utilizar o [Azure Media Player](https://aka.ms/azuremediaplayer). Ao executar a aplicação da consola, o ID da chave de conteúdo e o URL manifesto são ecoados.

1. Abra um novo separador e lance o seu STS: http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2. Vá ao [Azure Media Player](https://aka.ms/azuremediaplayer).
3. Pasta na URL de streaming.
4. Clique na caixa de verificação **De Opções Avançadas.**
5. No dropdown **Protection,** selecione PlayReady e/ou Widevine.
6. Cole o símbolo que obteve da sua STS na caixa de texto token. 
   
   O servidor de licença castLab não necessita do prefixo "Bearer=" em frente ao token. Por isso, por favor, remova-o antes de enviar o token.
7. Atualize o leitor.
8. O vídeo devia estar a ser reproduzo.

## <a name="additional-notes"></a>Notas adicionais

* Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

