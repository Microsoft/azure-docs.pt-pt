---
title: Conheça os codificadores recomendados pela Azure Media Services Microsoft Docs
description: Este artigo lista nas instalações codificadores recomendados pela Azure Media Services.
services: media-services
keywords: codificação;codificadores;meios
author: dbgeorge
manager: johndeu
ms.author: johndeu
ms.date: 03/20/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 89b01a3fb066f181f5ec54b481b71feaa7a6ae08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77131410"
---
# <a name="recommended-on-premises-encoders"></a>Codificadores locais recomendados

Ao transmitir em direto com o Azure Media Services, pode especificar como pretende que o seu canal receba o fluxo de entrada. Se optar por utilizar um codificador no local com um canal de codificação ao vivo, o seu codificador deve empurrar um fluxo de bitrate de alta qualidade como saída. Se optar por utilizar um codificador no local com um canal de passagem, o seu codificador deve empurrar um fluxo multibitado como saída com todas as qualidades de saída desejadas. Para mais informações, consulte [o streaming ao vivo com os codificadores](media-services-live-streaming-with-onprem-encoders.md)no local.

## <a name="encoder-requirements"></a>Requisitos codificadores

Os codificadores devem suportar o TLS 1.2 quando utilizarem protocolos HTTPS ou RTMPS.

## <a name="live-encoders-that-output-rtmp"></a>Codificadores ao vivo que output RTMP 

A Azure Media Services recomenda a utilização de um dos seguintes codificadores ao vivo que tenham RTMP como saída:

- Adobe Flash Media Live Encoder 3.2
- Haivision Makito X HEVC
- Haivision KB
- Telestream Wirecast (versão 13.0.2 ou superior devido ao requisito TLS 1.2)

  Os codificadores devem suportar o TLS 1.2 ao utilizarem protocolos RTMPS.
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- OBS Studio
- VMIX
- xStream
- Switcher Studio (iOS)

## <a name="live-encoders-that-output-fragmented-mp4"></a>Codificadores ao vivo que produzem MP4 fragmentado 

A Azure Media Services recomenda a utilização de um dos seguintes codificadores ao vivo que tenham mp4 fragmentado multibitado fragmentado (Smooth Streaming) como saída:

- Media Excel Hero Live and Hero 4K (UHD/HEVC)
- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live (versão 2.14.15 e superior devido ao requisito TLS 1.2)

  Os codificadores devem suportar o TLS 1.2 ao utilizarem protocolos HTTPS.
- Envivio 4Caster C4 Gen III
- Imagine comunicações Selenio MCP3

> [!NOTE]
> Um codificador ao vivo pode enviar um fluxo de bitrate único para um canal, mas esta configuração não é recomendada porque não permite o streaming de bitrate adaptativo ao cliente.

## <a name="how-to-become-an-on-premises-encoder-partner"></a>Como tornar-se um parceiro codificador no local

Como parceiro de codificador a Azure Media Services, a Media Services promove o seu produto recomendando o seu codificador aos clientes empresariais. Para se tornar um parceiro codificador no local, deve verificar a compatibilidade do seu código no local com os Serviços de Media. Para o fazer, complete as seguintes verificações:

Passar pela verificação do canal
1. Crie ou visite a sua conta Azure Media Services
2. Criar e iniciar um canal **de passagem**
3. Configure o seu codificador para empurrar um fluxo ao vivo multibitável.
4. Criar um evento ao vivo publicado
5. Execute o seu codificador ao vivo durante aproximadamente 10 minutos
6. Pare o evento ao vivo
7. Criar, iniciar um ponto final de Streaming, utilizar um jogador como o [Azure Media Player](https://aka.ms/azuremediaplayer) para ver o ativo arquivado para garantir que a reprodução não tem falhas visíveis para todos os níveis de qualidade (ou, em alternativa, assistir e validar através do URL de pré-visualização durante a sessão ao vivo antes do passo 6)
8. Grave o ID do Ativo, url de streaming publicado para o arquivo ao vivo, e as definições e versão utilizadas a partir do seu codificador ao vivo
9. Redefinir o estado do canal após a criação de cada amostra
10. Repita os passos 3 a 9 para todas as configurações suportadas pelo seu codificador (com e sem sinalização de anúncios/legendas/diferentes velocidades de codificação)

Verificação de canais de codificação ao vivo
1. Crie ou visite a sua conta Azure Media Services
2. Criar e iniciar um canal **de codificação ao vivo**
3. Configure o seu codificador para empurrar um fluxo ao vivo de um só bitrate.
4. Criar um evento ao vivo publicado
5. Execute o seu codificador ao vivo durante aproximadamente 10 minutos
6. Pare o evento ao vivo
7. Criar, iniciar um ponto final de Streaming, utilizar um jogador como o [Azure Media Player](https://aka.ms/azuremediaplayer) para ver o ativo arquivado para garantir que a reprodução não tem falhas visíveis para todos os níveis de qualidade (ou, em alternativa, assistir e validar através do URL de pré-visualização durante a sessão ao vivo antes do passo 6)
8. Grave o ID do Ativo, url de streaming publicado para o arquivo ao vivo, e as definições e versão utilizadas a partir do seu codificador ao vivo
9. Redefinir o estado do canal após a criação de cada amostra
10. Repita os passos 3 a 9 para todas as configurações suportadas pelo seu codificador (com e sem sinalização de anúncios/legendas/várias velocidades de codificação)

Verificação da longevidade
1. Crie ou visite a sua conta Azure Media Services
2. Criar e iniciar um canal **de passagem**
3. Configure o seu codificador para empurrar um fluxo ao vivo multibitável.
4. Criar um evento ao vivo publicado
5. Execute o seu codificador ao vivo por uma semana ou mais
6. Utilize um jogador como [o Azure Media Player](https://aka.ms/azuremediaplayer) para ver o streaming ao vivo de vez em quando (ou ativo arquivado) para garantir que a reprodução não tem falhas visíveis
7. Pare o evento ao vivo
8. Grave o ID do Ativo, url de streaming publicado para o arquivo ao vivo, e as definições e versão utilizadas a partir do seu codificador ao vivo

Por último, envie as suas definições gravadas e amsstreaming@microsoft.comparâmetros de arquivo ao vivo para os Serviços de Media por e-mail . Após a receção, a Media Services realiza testes de verificação das amostras do seu codificador ao vivo. Pode contactar os Serviços de Comunicação Social com quaisquer questões relacionadas com este processo.
