---
title: Eventos de Live de serviços de multimédia do Azure e um DVR na cloud | Documentos da Microsoft
description: Este artigo explica o que é o resultado em direto e de como usar um DVR na cloud.
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
ms.date: 01/14/2019
ms.author: juliako
ms.openlocfilehash: 4dd14587ec7e1473953981c1ef8c32c59eb9a1d6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60322340"
---
# <a name="using-a-cloud-dvr"></a>Utilizar um DVR na cloud

R [Live saída](https://docs.microsoft.com/rest/api/media/liveoutputs) permite-lhe controlar as propriedades do fluxo em direto saída, como muito da transmissão em fluxo é registrada (por exemplo, a capacidade de DVR na cloud) e se é ou não podem iniciar os visualizadores assistem à transmissão em direto. A relação entre um **evento em direto** e a respetiva **Live saída**s é semelhante à transmissão de televisão tradicional, no qual um canal (**evento em direto**) representa uma constante fluxo de vídeo e uma gravação (**Live saída**) tem um âmbito para um segmento de tempo específico (por exemplo, noite notícias da 6 17:30, para as 19:00: 00). Pode gravar programas de televisão com um gravador de vídeo Digital (DVR) – a funcionalidade equivalente nos eventos em direto é gerenciada por meio da propriedade ArchiveWindowLength. É uma duração de período de tempo de ISO-8601 (por exemplo, PTHH:MM:SS), que especifica a capacidade do DVR e pode ser definida a partir de um mínimo de 3 minutos até um máximo de 25 horas.

## <a name="live-output"></a>Saída em direto

O **ArchiveWindowLength** valor determina até que ponto volta no tempo, um Visualizador pode recuar a partir da posição atual em direto.  **ArchiveWindowLength** também determina quanto tempo os manifestos dos clientes podem aumentar.

Suponha que estão a transmissão em fluxo num jogo de futebol americano, e ele tem um **ArchiveWindowLength** de apenas 30 minutos. Um visualizador que começa a observar seu evento 45 minutos depois de iniciado o jogo pode buscar no máximo na marca de 15 minutos. Sua **saída Live**s para o jogo vai continuar até a **evento em direto** estiver parado, mas conteúdo que está fora do **ArchiveWindowLength** é continuamente descartado do armazenamento e não é recuperável. Neste exemplo, o vídeo entre o início do evento e a marca de 15 minutos seria ter foi removido da sua DVR e do contentor no armazenamento de BLOBs para o [Asset](https://docs.microsoft.com/rest/api/media/assets). O arquivo não é recuperável e é removido do contentor no armazenamento de Blobs do Azure.

Cada **saída Live** está associado um **Asset**, que utiliza para gravar o vídeo para o contentor de armazenamento de Blobs do Azure associada. Para publicar o resultado no Live, tem de criar uma **localizador de transmissão em fluxo** para que **Asset**. Depois de criar uma [localizador de transmissão em fluxo](https://docs.microsoft.com/rest/api/media/streaminglocators), pode compilar um URL de transmissão em fluxo que pode fornecer aos utilizadores.

R **evento em direto** suporta até três em execução em simultâneo **Live saída**s para que possa criar no máximo de 3 gravações/arquivos de um fluxo em direto. Isto permite publicar e arquivar diferentes partes de um evento, conforme necessário. Suponha que precisa difundir um 24x7 live linear feed e criar "gravações" dos programas diferentes ao longo do dia para oferecer aos clientes como conteúdo sob demanda para visualização catch-up. Para este cenário, crie primeiro uma saída de Live primária, com uma janela de arquivo curtos de 1 hora ou menos – este é o principal em fluxo em direto que os visualizadores têm acesso seriam otimizar em. Criaria uma **localizador de transmissão em fluxo** para que isso **Live saída** e publicá-la para a sua aplicação ou site como o feed de "Live". Enquanto o **evento em direto** está em execução, pode criar programaticamente uma segundo simultâneas **Live saída** no início de um programa (ou 5 minutos desde o início para fornecer alguns identificadores para cortar mais tarde). Este segundo **Live saída** podem ser eliminadas de 5 minutos após o programa é concluído. Com este segundo **Asset**, pode criar uma nova **localizador de transmissão em fluxo** para publicar este programa como um recurso de demanda no catálogo da sua aplicação. Pode repetir esse processo várias vezes para outros limites do programa ou destaques que deseja compartilhar, como vídeos a pedido, enquanto "Live" feed da primeira **Live saída** continua a difundir o feed linear. 

> [!NOTE]
> **Live saída**s início após a criação e param quando eliminado. Ao eliminar a **saída Live**, não está a eliminar subjacentes **Asset** e conteúdo no elemento. 
>
> Se tiver publicado a **saída Live** asset usando um **localizador de transmissão em fluxo**, o **evento em direto** (até o tamanho de janela DVR) irá continuar a ser visualizado até o **Localizador de transmissão em fluxo**da expiração ou eliminação, o que ocorrer primeiro.

## <a name="next-steps"></a>Passos Seguintes

- [Descrição geral de transmissão em fluxo em direto](live-streaming-overview.md)
- [Tutorial de transmissão em fluxo em direto](stream-live-tutorial-with-api.md)

