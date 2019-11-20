---
title: Codificadores de transmissão ao vivo recomendados pelos serviços de mídia – Azure | Microsoft Docs
description: Saiba mais sobre codificadores locais de transmissão ao vivo recomendados pelos serviços de mídia
services: media-services
keywords: codificação; codificadores; mídia
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 11/18/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 32ff975aa200e51e6a555f892a53b0ab9c73a84e
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186025"
---
# <a name="recommended-live-streaming-encoders"></a>Codificadores de transmissão ao vivo recomendados

Nos serviços de mídia do Azure, um [evento ao vivo](https://docs.microsoft.com/rest/api/media/liveevents) (canal) representa um pipeline para o processamento de conteúdo de streaming ao vivo. O evento ao vivo recebe fluxos de entrada ao vivo de uma das duas maneiras.

* Um codificador ao vivo local envia um fluxo RTMP de múltiplas taxas de bits ou Smooth Streaming (MP4 fragmentado) para o evento ao vivo que não está habilitado para executar a codificação ativa com os serviços de mídia. Os fluxos ingeridos passam por eventos ao vivo sem nenhum processamento adicional. Esse método é chamado **de passagem**. Um codificador ao vivo pode enviar um fluxo de taxa de bits única para um canal de passagem. Não recomendamos essa configuração porque ela não permite o streaming de taxa de bits adaptável para o cliente.

  > [!NOTE]
  > Usar um método de passagem é a maneira mais econômica de realizar a transmissão ao vivo.
 
* Um codificador ao vivo local envia um fluxo de taxa de bits única para o evento ao vivo que está habilitado para executar a codificação ativa com os serviços de mídia em um dos seguintes formatos: RTMP ou Smooth Streaming (MP4 fragmentado). Em seguida, o evento ao vivo executa a codificação ativa do fluxo de taxa de bits única de entrada para um fluxo de vídeo com múltiplas taxas de bits (adaptável).

Para obter informações detalhadas sobre a codificação ativa com os serviços de mídia, consulte [transmissão ao vivo com os serviços de mídia v3](live-streaming-overview.md).

## <a name="live-encoders-that-output-rtmp"></a>Codificadores ao vivo que produzem RTMP

Os Serviços de Multimédia recomendam utilizar um dos codificadores em direto seguintes que têm RTMP como saída. Os esquemas de URL com suporte são `rtmp://` ou `rtmps://`.

> [!NOTE]
> Ao transmitir em fluxo através de RTMP, verifique as definições da firewall e/ou do proxy, para confirmar que as portas TCP de saída 1935 e 1936 estão abertas.

- Adobe Flash Media Live Encoder 3.2
- [Cambria Live 4,3](https://www.capellasystems.net/products/cambria-live/)
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio (iOS)
- Telestream Wirecast 8.1+
- Telestream Wirecast S
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- VMIX
- xStream
- [Ffmpeg](https://www.ffmpeg.org)
- [GoPro](https://gopro.com/help/articles/block/getting-started-with-live-streaming) Hero 7 e Hero 8
- [Restream.io](https://restream.io/)

## <a name="live-encoders-that-output-fragmented-mp4"></a>Codificadores ao vivo que geram MP4 fragmentado

Os serviços de mídia recomendam o uso de um dos seguintes codificadores ao vivo com várias taxas de bits Smooth Streaming (MP4 fragmentado) como saída. Os esquemas de URL com suporte são `http://` ou `https://`.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 Gen III
- Imagine Communications selenio MCP3
- Media Excel Hero Live and Hero 4K (UHD/HEVC)
- [Ffmpeg](https://www.ffmpeg.org)

> [!TIP]
>  Se você estiver transmitindo eventos ao vivo em vários idiomas (por exemplo, uma faixa de áudio em inglês e uma faixa de áudio em espanhol), poderá fazer isso com o Media Excel Live Encoder configurado para enviar o feed ao vivo para um evento ao vivo de passagem.


## <a name="configuring-on-premises-live-encoder-settings"></a>Configurando configurações de codificador dinâmico local

Para obter informações sobre quais configurações são válidas para seu tipo de evento ao vivo, consulte [comparação de tipos de evento ao vivo](live-event-types-comparison.md).

### <a name="playback-requirements"></a>Requisitos de reprodução

Para reproduzir o conteúdo, um fluxo de áudio e de vídeo deve estar presente. Não há suporte para a reprodução do fluxo somente de vídeo.

### <a name="configuration-tips"></a>Dicas de configuração

- Sempre que possível, utilize uma ligação de internet conectada.
- Ao determinar os requisitos de largura de banda, clique duas vezes nas taxas de bits de streaming. Embora não seja obrigatório, essa simples regra ajuda a reduzir o impacto do congestionamento da rede.
- Quando utilizar codificadores baseada em software, feche todos os programas desnecessários.
- Alterar a configuração do codificador depois de começar a enviar por push tem efeitos negativos sobre o evento. As alterações de configuração podem fazer com que o evento se torne instável. 
- Certifique-se de que você tenha bastante tempo para configurar seu evento. Para eventos de alta escala, é recomendável iniciar a configuração uma hora antes do evento.

## <a name="becoming-an-on-premises-encoder-partner"></a>Tornando-se um parceiro de codificador local

Como um parceiro de codificador local dos serviços de mídia do Azure, os serviços de mídia promovem seu produto, recomendando o codificador para clientes corporativos. Para se tornar um parceiro de codificador local, você deve verificar a compatibilidade do codificador local com os serviços de mídia. Para fazer isso, conclua as verificações a seguir.

### <a name="pass-through-live-event-verification"></a>Verificação de evento ao vivo de passagem

1. Em sua conta de serviços de mídia, verifique se o **ponto de extremidade de streaming** está em execução. 
2. Crie e inicie o evento de **passagem** ao vivo. <br/> Para obter mais informações, consulte [Live Event States e Billing](live-event-states-billing.md).
3. Obtenha as URLs de ingestão e configure seu codificador local para usar a URL para enviar uma transmissão ao vivo de múltiplas taxas de bits para os serviços de mídia.
4. Obtenha a URL de visualização e use-a para verificar se a entrada do codificador está realmente sendo recebida.
5. Crie um novo objeto de **ativo** .
6. Crie uma **saída ao vivo** e use o nome do ativo que você criou.
7. Crie um **localizador de streaming** com os tipos de **política de streaming** internos.
8. Liste os caminhos no **localizador de streaming** para obter as URLs a serem usadas.
9. Obtenha o nome do host para o **ponto de extremidade de streaming** do qual você deseja transmitir.
10. Combine a URL da etapa 8 com o nome do host na etapa 9 para obter a URL completa.
11. Execute o codificador ao vivo por aproximadamente 10 minutos.
12. Pare o evento ao vivo. 
13. Use um player como [player de mídia do Azure](https://aka.ms/azuremediaplayer) para assistir ao ativo arquivado para garantir que a reprodução não tenha nenhum problema visível em todos os níveis de qualidade. Ou, Assista e valide por meio da URL de visualização durante a sessão ao vivo.
14. Registre a ID do ativo, a URL de streaming publicada para o arquivo em tempo real e as configurações e a versão usadas no codificador ao vivo.
15. Redefina o estado do evento ao vivo depois de criar cada exemplo.
16. Repita as etapas de 5 a 15 para todas as configurações com suporte no seu codificador (com e sem sinalização do AD, legendas ou velocidades de codificação diferentes).

### <a name="live-encoding-live-event-verification"></a>Verificação de evento ao vivo de codificação ativa

1. Em sua conta de serviços de mídia, verifique se o **ponto de extremidade de streaming** está em execução. 
2. Crie e inicie o evento Live de **codificação** ativa. <br/> Para obter mais informações, consulte [Live Event States e Billing](live-event-states-billing.md).
3. Obtenha as URLs de ingestão e configure seu codificador para enviar por push uma transmissão ao vivo de taxa de bits única para os serviços de mídia.
4. Obtenha a URL de visualização e use-a para verificar se a entrada do codificador está realmente sendo recebida.
5. Crie um novo objeto de **ativo** .
6. Crie uma **saída ao vivo** e use o nome do ativo que você criou.
7. Crie um **localizador de streaming** com os tipos de **política de streaming** internos.
8. Liste os caminhos no **localizador de streaming** para obter as URLs a serem usadas.
9. Obtenha o nome do host para o **ponto de extremidade de streaming** do qual você deseja transmitir.
10. Combine a URL da etapa 8 com o nome do host na etapa 9 para obter a URL completa.
11. Execute o codificador ao vivo por aproximadamente 10 minutos.
12. Pare o evento ao vivo.
13. Use um player como [player de mídia do Azure](https://aka.ms/azuremediaplayer) para assistir ao ativo arquivado para garantir que a reprodução não tenha nenhum problema visível para todos os níveis de qualidade. Ou, Assista e valide por meio da URL de visualização durante a sessão ao vivo.
14. Registre a ID do ativo, a URL de streaming publicada para o arquivo em tempo real e as configurações e a versão usadas no codificador ao vivo.
15. Redefina o estado do evento ao vivo depois de criar cada exemplo.
16. Repita as etapas de 5 a 15 para todas as configurações com suporte no seu codificador (com e sem sinalização do AD, legendas ou velocidades de codificação diferentes).

### <a name="longevity-verification"></a>Verificação de longevidade

Siga as mesmas etapas da [verificação de eventos ao vivo de passagem](#pass-through-live-event-verification) , exceto para a etapa 11. <br/>Em vez de 10 minutos, execute seu codificador ao vivo por uma semana ou mais. Use um player como [player de mídia do Azure](https://aka.ms/azuremediaplayer) para observar a transmissão ao vivo de tempos em tempos (ou um ativo arquivado) para garantir que a reprodução não tenha nenhum problema visível.

### <a name="email-your-recorded-settings"></a>Enviar por email suas configurações gravadas

Por fim, envie por email suas configurações gravadas e os parâmetros de arquivamento dinâmico para os serviços de mídia do Azure em amshelp@microsoft.com como uma notificação de que todas as verificações de verificação automática passaram. Além disso, inclua suas informações de contato para qualquer acompanhamento. Você pode entrar em contato com a equipe dos serviços de mídia do Azure com perguntas sobre esse processo.

## <a name="next-steps"></a>Passos seguintes

[Transmissão ao vivo com os serviços de mídia v3](live-streaming-overview.md)
