---
title: Limites de sistema do StorSimple 8000 Series | Microsoft Docs
description: Descreve os limites do sistema e os tamanhos recomendados para componentes e conexões do StorSimple 8000 Series.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: c7392678-0924-46c6-9c59-1665cb9b6586
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/28/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 70f2d9542082ddf7ecf1d1e7361b0ecdb14c5ef8
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68963376"
---
# <a name="what-are-storsimple-8000-series-system-limits"></a>O que são os limites do sistema da série StorSimple 8000?

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Descrição geral

O StorSimple fornece armazenamento escalonável e flexível para seu datacenter. No entanto, há alguns limites que você deve ter em mente ao planejar, implantar e operar sua solução StorSimple. A tabela a seguir descreve esses limites e fornece algumas recomendações para que você possa obter o máximo de sua solução StorSimple.

| Identificador de limite | Limite | Comentários |
| --- | --- | --- |
| Número máximo de credenciais da conta de armazenamento |64 | |
| Número máximo de contêineres de volume |64 | |
| Número máximo de volumes |255 | |
| Número máximo de volumes fixados localmente |32 | |
| Número máximo de agendas por modelo de largura de banda |168 |Uma agenda para cada hora, todos os dias da semana (24 * 7). |
| Tamanho máximo de um volume em camadas em dispositivos físicos |64 TB para 8100 e 8600 |8100 e 8600 são dispositivos físicos. |
| Tamanho máximo de um volume em camadas em dispositivos virtuais no Azure |30 TB para 8010 <br></br> 64 TB para 8020 |8010 e 8020 são dispositivos virtuais no Azure que usam armazenamento Standard e Premium, respectivamente. |
| Tamanho máximo de um volume fixado localmente em dispositivos físicos |8,5 TB para 8100 <br></br> 22,5 TB para 8600 |8100 e 8600 são dispositivos físicos. |
| Número máximo de conexões iSCSI |512 | |
| Número máximo de conexões iSCSI de iniciadores |512 | |
| Número máximo de registros de controle de acesso por dispositivo |64 | |
| Número máximo de volumes por política de backup |20 | |
| Número máximo de backups retidos por agenda (em uma política de backup) |64 | |
| Número máximo de agendas por política de backup |10 | |
| Número máximo de instantâneos de qualquer tipo que podem ser retidos por volume |256 |Esse número inclui instantâneos locais e instantâneos de nuvem. |
| Número máximo de instantâneos que podem estar presentes em qualquer dispositivo |10,000 | |
| Número máximo de volumes que podem ser processados em paralelo para backup, restauração ou clonagem |16 |<ul><li>Se houver mais de 16 volumes, eles serão processados sequencialmente conforme os slots de processamento se tornarem disponíveis.</li><li>Novos backups de um volume clonado ou restaurado em camadas não podem ocorrer até que a operação seja concluída. No entanto, para um volume local, os backups são permitidos depois que o volume está online.</li></ul> |
| Restauração e clonagem do tempo de recuperação para volumes em camadas |< 2 minutos |<ul><li>O volume é disponibilizado em 2 minutos após a restauração ou operação de clonagem, independentemente do tamanho do volume.</li><li>O desempenho do volume inicialmente pode ser mais lento do que o normal, pois a maioria dos dados e metadados ainda residem na nuvem. O desempenho pode aumentar à medida que os dados fluem da nuvem para o dispositivo StorSimple.</li><li>O tempo total para baixar metadados depende do tamanho do volume alocado. Os metadados são automaticamente colocados no dispositivo em segundo plano na taxa de 5 minutos por TB de dados de volume alocados. Essa taxa pode ser afetada pela largura de banda da Internet para a nuvem.</li><li>A operação de restauração ou clonagem é concluída quando todos os metadados estão no dispositivo.</li><li>As operações de backup não podem ser executadas até que a operação de restauração ou clonagem esteja totalmente concluída. |
| Tempo de recuperação de restauração para volumes fixados localmente |< 2 minutos |<ul><li>O volume é disponibilizado dentro de 2 minutos da operação de restauração, independentemente do tamanho do volume.</li><li>O desempenho do volume inicialmente pode ser mais lento do que o normal, pois a maioria dos dados e metadados ainda residem na nuvem. O desempenho pode aumentar à medida que os dados fluem da nuvem para o dispositivo StorSimple.</li><li>O tempo total para baixar metadados depende do tamanho do volume alocado. Os metadados são automaticamente colocados no dispositivo em segundo plano na taxa de 5 minutos por TB de dados de volume alocados. Essa taxa pode ser afetada pela largura de banda da Internet para a nuvem.</li><li>Ao contrário dos volumes em camadas, para volumes fixados localmente, os dados do volume também são baixados localmente no dispositivo. A operação de restauração é concluída quando todos os dados de volume foram trazidos para o dispositivo.</li><li>As operações de restauração podem ser longas. O tempo total para concluir a restauração depende do tamanho do volume local provisionado, da largura de banda da Internet e dos dados existentes no dispositivo. As operações de backup no volume fixado localmente são permitidas enquanto a operação de restauração está em andamento. |
| Taxa de processamento para instantâneos de nuvem |15 minutos/TB |<ul><li>Tempo mínimo para que o instantâneo de nuvem esteja pronto para carregamento, por TB de dados de volume alocados no backup. </li><li> O tempo total do instantâneo de nuvem é calculado adicionando esse tempo ao tempo de carregamento do instantâneo, que é afetado pela largura de banda da Internet para a nuvem. |
| Taxa de transferência máxima de leitura/gravação do cliente (quando servida da camada SSD) * |920/720 MB/s com uma única interface de rede de 10 GbE |Até 2x com MPIO e duas interfaces de rede. |
| Taxa de transferência máxima de leitura/gravação do cliente (quando servida da camada de HDD) * |120/250 MB/s | |
| Taxa de transferência máxima de leitura/gravação do cliente (quando servida da camada de nuvem) * para a atualização 3 e posterior * * |40/60 MB/s para volumes em camadas<br><br>60/80 MB/s para volumes em camadas com a opção de arquivamento selecionada durante a criação do volume |A taxa de transferência de leitura depende de clientes gerando e mantendo profundidade de fila de e/s suficiente. <br><br>A velocidade alcançada depende da velocidade da conta de armazenamento subjacente usada. |

&#42;A taxa de transferência máxima por tipo de e/s foi medida com 100% de leitura e 100% de cenários de gravação. A taxa de transferência real pode ser menor e depende da combinação de e/s e das condições de rede.

&#42;&#42;Os números de desempenho anteriores à atualização 3 podem ser menores.

## <a name="next-steps"></a>Passos Seguintes
Examine os [requisitos de sistema do StorSimple](storsimple-8000-system-requirements.md).

