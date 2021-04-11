---
title: Saiba mais sobre os codificadores recomendados pela Azure Media Services | Microsoft Docs
description: Este artigo lista em codificadores de instalações recomendados pela Azure Media Services.
services: media-services
keywords: codificação;codificadores;meios de comunicação
author: IngridAtMicrosoft
manager: johndeu
ms.author: johndeu
ms.date: 03/10/2021
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 98d78a105bf06a2e49dee0b8c2be710b220a0023
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103009441"
---
# <a name="recommended-on-premises-encoders"></a>Codificadores locais recomendados

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Ao transmitir em direto com o Azure Media Services, pode especificar como pretende que o seu canal receba o fluxo de entrada. Se optar por utilizar um codificadora nas instalações com um canal de codificação ao vivo, o seu codificante deve empurrar um fluxo de bitrate único de alta qualidade como saída. Se optar por utilizar um codificadora nas instalações com uma passagem pelo canal, o seu codificante deve empurrar um fluxo multi-bitrate como saída com todas as qualidades de saída desejadas. Para mais informações, consulte [o streaming ao vivo com codificadores nas instalações.](media-services-live-streaming-with-onprem-encoders.md)

## <a name="encoder-requirements"></a>Requisitos do codificadores

Os codificadores devem suportar TLS 1.2 quando utilizarem protocolos HTTPS ou RTMPS.

## <a name="live-encoders-that-output-rtmp"></a>Codificadores ao vivo que desempenham RTMP 

A Azure Media Services recomenda a utilização de um dos seguintes codificadores ao vivo que têm RTMP como saída:

- Adobe Flash Media Live Encoder 3.2
- Haivision Makito X HEVC
- Haivision KB
- Telestream Wirecast (versão 13.0.2 ou superior devido ao requisito TLS 1.2)

  Os codificadores devem suportar O TLS 1.2 quando utilizarem protocolos RTMPS.
- Teradek Slice 756
- OBS Studio
- VMIX
- xStream
- Switcher Studio (iOS)

## <a name="live-encoders-that-output-fragmented-mp4"></a>Codificadores ao vivo que desempenaram MP4 

A Azure Media Services recomenda a utilização de um dos seguintes codificadores ao vivo que tenham multi-bitrate fragmentado-MP4 (Smooth Streaming) como saída:

- Media Excel Hero Live and Hero 4K (UHD/HEVC)
- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live (versão 2.14.15 e superior devido ao requisito TLS 1.2)

  Os codificadores devem suportar o TLS 1.2 quando utilizarem os protocolos HTTPS.
- Envivio 4Caster C4 Gen III
- Imagine comunicações Selenio MCP3

> [!NOTE]
> Um codificadora ao vivo pode enviar um fluxo de bitrate único para uma passagem através de canal, mas esta configuração não é recomendada porque não permite o streaming de bitrate adaptativo para o cliente.

## <a name="how-to-become-an-on-premises-encoder-partner"></a>Como se tornar um parceiro de codificação nas instalações

Como parceiro de codificação Azure Media Services, a Media Services promove o seu produto recomendando o seu codificadora aos clientes empresariais. Para se tornar um parceiro de codificação nas instalações, deve verificar a compatibilidade do seu codificadores nas instalações com os Media Services. Para tal, complete as seguintes verificações:

Passar pela verificação do canal
1. Crie ou visite a sua conta Azure Media Services
2. Criar e iniciar um canal **de passagem**
3. Configure o seu codificante para empurrar um fluxo ao vivo multi-bitrate.
4. Criar um evento ao vivo publicado
5. Executar o seu codificar ao vivo por aproximadamente 10 minutos
6. Pare o evento ao vivo
7. Criar, iniciar um ponto final de streaming, utilizar um jogador como [o Azure Media Player](https://aka.ms/azuremediaplayer) para ver o ativo arquivado para garantir que a reprodução não tem falhas visíveis para todos os níveis de qualidade (ou, em alternativa, assistir e validar através do URL de pré-visualização durante a sessão ao vivo antes do passo 6)
8. Grave o ID do ativo, o URL de streaming publicado para o arquivo ao vivo, e as definições e versão utilizadas a partir do seu codificadores ao vivo
9. Repor o estado do canal depois de criar cada amostra
10. Repita os passos 3 a 9 para todas as configurações suportadas pelo seu codificador (com e sem sinalização/legendas de anúncio/diferentes velocidades de codificação)

Verificação de canais de codificação ao vivo
1. Crie ou visite a sua conta Azure Media Services
2. Criar e iniciar um canal **de codificação ao vivo**
3. Configure o seu codificante para empurrar um fluxo ao vivo de uma única bitrate.
4. Criar um evento ao vivo publicado
5. Executar o seu codificar ao vivo por aproximadamente 10 minutos
6. Pare o evento ao vivo
7. Criar, iniciar um ponto final de streaming, utilizar um jogador como [o Azure Media Player](https://aka.ms/azuremediaplayer) para ver o ativo arquivado para garantir que a reprodução não tem falhas visíveis para todos os níveis de qualidade (ou, em alternativa, assistir e validar através do URL de pré-visualização durante a sessão ao vivo antes do passo 6)
8. Grave o ID do ativo, o URL de streaming publicado para o arquivo ao vivo, e as definições e versão utilizadas a partir do seu codificadores ao vivo
9. Repor o estado do canal depois de criar cada amostra
10. Repita os passos 3 a 9 para todas as configurações suportadas pelo seu codificador (com e sem sinalização/legendas de anúncio/várias velocidades de codificação)

Verificação da longevidade
1. Crie ou visite a sua conta Azure Media Services
2. Criar e iniciar um canal **de passagem**
3. Configure o seu codificante para empurrar um fluxo ao vivo multi-bitrate.
4. Criar um evento ao vivo publicado
5. Executar o seu codificar ao vivo por uma semana ou mais
6. Utilize um jogador como [o Azure Media Player](https://aka.ms/azuremediaplayer) para ver o streaming ao vivo de vez em quando (ou ativo arquivado) para garantir que a reprodução não tem falhas visíveis
7. Pare o evento ao vivo
8. Grave o ID do ativo, o URL de streaming publicado para o arquivo ao vivo, e as definições e versão utilizadas a partir do seu codificadores ao vivo

Por último, envie as suas definições gravadas e os parâmetros de arquivo ao vivo para os Serviços de Mídia por e-mail amsstreaming@microsoft.com . Após a receção, a Media Services realiza testes de verificação nas amostras do seu codificante ao vivo. Pode contactar os Serviços de Comunicação Social com quaisquer questões relacionadas com este processo.
