---
title: Saiba mais sobre codificadores recomendados pelos serviços de mídia do Azure | Microsoft Docs
description: Este artigo lista os codificadores locais recomendados pelos serviços de mídia do Azure.
services: media-services
keywords: codificação; codificadores; mídia
author: dbgeorge
manager: johndeu
ms.author: johndeu
ms.date: 03/20/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 4a0af9d040c801c125d04a5af72b2ea53322ccdb
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74886576"
---
# <a name="recommended-on-premises-encoders"></a>Codificadores locais recomendados
Ao fazer streaming ao vivo com os serviços de mídia do Azure, você pode especificar como deseja que seu canal receba o fluxo de entrada. Se você optar por usar um codificador local com um canal de codificação ativa, o codificador deverá enviar por push um fluxo de taxa de bits única de alta qualidade como saída. Se você optar por usar um codificador local com um canal de passagem, o codificador deverá enviar por push um fluxo de múltiplas taxas de bits como saída com todas as qualidades de saída desejadas. Para obter mais informações, consulte [transmissão ao vivo com codificadores locais](media-services-live-streaming-with-onprem-encoders.md).

Os serviços de mídia do Azure recomendam o uso de um dos seguintes codificadores ao vivo que têm RTMP como saída:
- Adobe Flash Media Live Encoder 3.2
- Haivision Makito X HEVC
- Haivision KB
- Telestream Wirecast 8.1+
- Telestream Wirecast S
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- OBS Studio
- VMIX
- xStream
- Switcher Studio (iOS)

Os serviços de mídia do Azure recomendam o uso de um dos seguintes codificadores ao vivo com múltiplas taxas de bits fragmentadas-MP4 (Smooth Streaming) como saída:
- Media Excel Hero Live and Hero 4K (UHD/HEVC)
- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 Gen III
- Imagine Communications selenio MCP3

> [!NOTE]
> Um codificador ao vivo pode enviar um fluxo de taxa de bits única para um canal de passagem, mas essa configuração não é recomendada porque não permite o streaming de taxa de bits adaptável para o cliente.

## <a name="how-to-become-an-on-premises-encoder-partner"></a>Como se tornar um parceiro de codificador local
Como um parceiro de codificador local dos serviços de mídia do Azure, os serviços de mídia promovem seu produto, recomendando o codificador para clientes corporativos. Para se tornar um parceiro de codificador local, você deve verificar a compatibilidade do codificador local com os serviços de mídia. Para fazer isso, conclua as seguintes verificações:

Verificação de canal de passagem
1. Criar ou visitar sua conta dos serviços de mídia do Azure
2. Criar e iniciar um canal **de passagem**
3. Configure seu codificador para enviar por push um fluxo ao vivo com múltiplas taxas de bits.
4. Criar um evento ao vivo publicado
5. Execute o codificador ao vivo por aproximadamente 10 minutos
6. Parar o evento ao vivo
7. Crie, inicie um ponto de extremidade de streaming, use um player como [player de mídia do Azure](https://aka.ms/azuremediaplayer) para assistir ao ativo arquivado para garantir que a reprodução não tenha nenhum problema visível para todos os níveis de qualidade (ou observe e valide de forma alternativa por meio da URL de visualização durante a sessão ativa antes da etapa 6)
8. Registre a ID do ativo, a URL de streaming publicada para o arquivo em tempo real e as configurações e a versão usadas no codificador ao vivo
9. Redefinir o estado do canal depois de criar cada amostra
10. Repita as etapas de 3 a 9 para todas as configurações com suporte no seu codificador (com e sem sinalização/legendas do AD/velocidades de codificação diferentes)

Verificação de canal de codificação ativa
1. Criar ou visitar sua conta dos serviços de mídia do Azure
2. Criar e iniciar um canal de **codificação ativa**
3. Configure seu codificador para enviar por push um fluxo ao vivo de taxa de bits única.
4. Criar um evento ao vivo publicado
5. Execute o codificador ao vivo por aproximadamente 10 minutos
6. Parar o evento ao vivo
7. Crie, inicie um ponto de extremidade de streaming, use um player como [player de mídia do Azure](https://aka.ms/azuremediaplayer) para assistir ao ativo arquivado para garantir que a reprodução não tenha nenhum problema visível para todos os níveis de qualidade (ou observe e valide de forma alternativa por meio da URL de visualização durante a sessão ativa antes da etapa 6)
8. Registre a ID do ativo, a URL de streaming publicada para o arquivo em tempo real e as configurações e a versão usadas no codificador ao vivo
9. Redefinir o estado do canal depois de criar cada amostra
10. Repita as etapas de 3 a 9 para todas as configurações com suporte no seu codificador (com e sem sinalização/legendas do AD/várias velocidades de codificação)

Verificação de longevidade
1. Criar ou visitar sua conta dos serviços de mídia do Azure
2. Criar e iniciar um canal **de passagem**
3. Configure seu codificador para enviar por push um fluxo ao vivo com múltiplas taxas de bits.
4. Criar um evento ao vivo publicado
5. Execute seu codificador ao vivo por uma semana ou mais
6. Use um player como [player de mídia do Azure](https://aka.ms/azuremediaplayer) para observar a transmissão ao vivo de tempos em tempos (ou ativo arquivado) para garantir que a reprodução não tenha nenhum problema visível
7. Parar o evento ao vivo
8. Registre a ID do ativo, a URL de streaming publicada para o arquivo em tempo real e as configurações e a versão usadas no codificador ao vivo

Por fim, envie as configurações gravadas e os parâmetros de arquivamento dinâmico para os serviços de mídia enviando amsstreaming@microsoft.compor email. Após o recebimento, os serviços de mídia executam testes de verificação nos exemplos de seu codificador ao vivo. Você pode entrar em contato com os serviços de mídia com qualquer dúvida sobre esse processo.
