---
title: Usar mudança de tempo e saídas ao vivo para criar reprodução de vídeo sob demanda
titleSuffix: Azure Media Services
description: Saiba como usar as saídas dinâmicas e de mudança de tempo para registrar fluxos ao vivo e criar a reprodução sob demanda.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 08/27/2019
ms.author: juliako
ms.openlocfilehash: acba251a57f39c07d690d0c55665b8914feaf06c
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186235"
---
# <a name="use-time-shifting-and-live-outputs-to-create-on-demand-video-playback"></a>Usar mudança de tempo e saídas ao vivo para criar reprodução de vídeo sob demanda

Nos serviços de mídia do Azure, um objeto de [saída ao vivo](https://docs.microsoft.com/rest/api/media/liveoutputs) é como um gravador de vídeo digital que detectará e registrará sua transmissão ao vivo em um ativo em sua conta de serviços de mídia. O conteúdo gravado é mantido no contêiner definido pelo recurso de [ativo](https://docs.microsoft.com/rest/api/media/assets) (o contêiner está na conta de armazenamento do Azure anexada à sua conta). A saída ao vivo também permite que você controle algumas propriedades da transmissão ao vivo de saída, como quanto do fluxo é mantido na gravação de arquivo morto (por exemplo, a capacidade do DVR de nuvem) ou quando os visualizadores podem começar a assistir à transmissão ao vivo. O arquivo morto em disco é uma "janela" de arquivo circular que mantém apenas a quantidade de conteúdo especificada na propriedade **archiveWindowLength** da saída em tempo real. O conteúdo que está fora desta janela é descartado automaticamente do contêiner de armazenamento e não é recuperável. O valor archiveWindowLength representa uma duração ISO-8601 TimeSpan (por exemplo, PTHH: MM: SS), que especifica a capacidade do DVR. O valor pode ser definido de um mínimo de três minutos para um máximo de 25 horas.

A relação entre um evento ao vivo e suas saídas ao vivo é semelhante à transmissão de TV tradicional, pois um canal (evento ao vivo) representa um fluxo constante de vídeo e uma gravação (saída ao vivo) tem como escopo um segmento de tempo específico (por exemplo, notícias da noite de 6: às 16h30 a 7:13h). Depois que o fluxo fluir para o evento ao vivo, você poderá iniciar o evento de streaming criando um ativo, uma saída ao vivo e um localizador de streaming. A saída ao vivo arquivará o fluxo e o tornará disponível para os visualizadores por meio do [ponto de extremidade de streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints). Você pode criar várias saídas dinâmicas (até três no máximo) em um evento ao vivo com diferentes tamanhos e configurações de arquivo. Para obter informações sobre o fluxo de trabalho de transmissão ao vivo, consulte a seção [etapas gerais](live-streaming-overview.md#general-steps) .

## <a name="using-a-dvr-during-an-event"></a>Usando um DVR durante um evento

Esta seção discute como usar um DVR durante um evento para controlar quais partes do fluxo estão disponíveis para ' retrocesso '.

O valor de `archiveWindowLength` determina quanto tempo um visualizador pode ir da posição dinâmica atual. O valor de `archiveWindowLength` também determina por quanto tempo os manifestos do cliente podem crescer.

Suponha que você esteja transmitindo um jogo de futebol e tenha um `ArchiveWindowLength` de apenas 30 minutos. Um visualizador que começa a assistir seu evento 45 minutos depois que o jogo é iniciado pode voltar para no máximo 15 minutos de marca. Suas saídas ao vivo para o jogo continuarão até que o evento ao vivo seja interrompido. O conteúdo que está fora do archiveWindowLength é continuamente descartado do armazenamento e não é recuperável. Neste exemplo, o vídeo entre o início do evento e a marca de 15 minutos teria sido limpo do DVR e do contêiner no armazenamento de BLOBs para o ativo. O arquivo morto não é recuperável e é removido do contêiner no armazenamento de BLOBs do Azure.

Um evento ao vivo dá suporte a até três saídas dinâmicas em execução simultânea (é possível criar no máximo três gravações/arquivos mortos de uma transmissão ao vivo ao mesmo tempo). Esse suporte permite que você publique e arquive diferentes partes de um evento, conforme necessário. Suponha que você precise transmitir um feed linear ao vivo 24x7 e criar "gravações" dos diferentes programas ao longo do dia para oferecer aos clientes como conteúdo sob demanda para exibição de atualização. Para esse cenário, primeiro você cria uma saída dinâmica principal com uma janela de arquivo curto de 1 hora ou menos – essa é a transmissão ao vivo principal que seus visualizadores ajustarão. Você deve criar um localizador de streaming para essa saída ao vivo e publicá-lo em seu aplicativo ou site como o feed "ao vivo". Enquanto o evento ao vivo está em execução, você pode criar programaticamente uma segunda saída simultânea ao vivo no início de um programa (ou 5 minutos antes de fornecer alguns identificadores para aparar posteriormente). Essa segunda saída ao vivo pode ser excluída cinco minutos após o término do programa. Com esse segundo ativo, você pode criar um novo localizador de streaming para publicar este programa como um ativo sob demanda no catálogo do aplicativo. Você pode repetir esse processo várias vezes para outros limites de programa ou destaques que deseja compartilhar como vídeos sob demanda, tudo enquanto o feed "ao vivo" da primeira saída ao vivo continua a transmitir o feed linear.

## <a name="creating-an-archive-for-on-demand-playback"></a>Criando um arquivo para reprodução sob demanda

O ativo ao qual a saída dinâmica está arquivando se torna automaticamente um ativo sob demanda quando a saída dinâmica é excluída. Você deve excluir todas as saídas ao vivo antes que um evento ao vivo possa ser interrompido. Você pode usar um sinalizador opcional [removeOutputsOnStop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) para remover automaticamente as saídas dinâmicas ao parar.

Mesmo depois de parar e excluir o evento, os usuários podem transmitir o conteúdo arquivado como um vídeo sob demanda, desde que você não exclua o ativo. Um ativo não deve ser excluído se for usado por um evento; o evento deve ser excluído primeiro.

Se você publicou o ativo da sua saída ao vivo usando um localizador de streaming, o evento ao vivo (até o comprimento da janela DVR) continuará a ser exibido até a expiração ou a exclusão do localizador de streaming, o que ocorrer primeiro.

Para obter mais informações, consulte:

- [Visão geral da transmissão ao vivo](live-streaming-overview.md)
- [Tutorial de transmissão ao vivo](stream-live-tutorial-with-api.md)

> [!NOTE]
> Quando você exclui a saída dinâmica, não está excluindo o ativo subjacente e o conteúdo no ativo.

## <a name="next-steps"></a>Passos seguintes

* [Subclipe seus vídeos](subclip-video-rest-howto.md).
* [Defina filtros para seus ativos](filters-dynamic-manifest-rest-howto.md).
