---
title: Limites do sistema da série StorSimple 8000 [ StorSimple 8000 system limit ] Microsoft Docs
description: Descreve os limites do sistema e os tamanhos recomendados para componentes e ligações da série StorSimple 8000.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "68963376"
---
# <a name="what-are-storsimple-8000-series-system-limits"></a>Quais são os limites do sistema da série StorSimple 8000?

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Descrição geral

O StorSimple fornece armazenamento escalável e flexível para o seu datacenter. No entanto, existem alguns limites que deve ter em mente à medida que planeia, implementa e opera a sua solução StorSimple. A tabela seguinte descreve estes limites e fornece algumas recomendações para que possa tirar o máximo partido da sua solução StorSimple.

| Identificador de limite | Limite | Comentários |
| --- | --- | --- |
| Número máximo de credenciais de conta de armazenamento |64 | |
| Número máximo de contentores de volume |64 | |
| Número máximo de volumes |255 | |
| Número máximo de volumes fixados localmente |32 | |
| Número máximo de horários por modelo de largura de banda |168 |Horário para cada hora, todos os dias da semana (24*7). |
| Tamanho máximo de um volume hierárquico em dispositivos físicos |64 TB para 8100 e 8600 |8100 e 8600 são dispositivos físicos. |
| Tamanho máximo de um volume hierárquico em dispositivos virtuais em Azure |30 TB para 8010 <br></br> 64 TB para 8020 |8010 e 8020 são dispositivos virtuais em Azure que utilizam o Armazenamento Standard e o Armazenamento Premium, respectivamente. |
| Tamanho máximo de um volume fixado localmente em dispositivos físicos |8,5 TB para 8100 <br></br> 22,5 TB para 8600 |8100 e 8600 são dispositivos físicos. |
| Número máximo de ligações iSCSI |512 | |
| Número máximo de ligações iSCSI de iniciadores |512 | |
| Número máximo de registos de controlo de acesso por dispositivo |64 | |
| Número máximo de volumes por política de backup |20 | |
| Número máximo de backups retidos por horário (numa política de backup) |64 | |
| Número máximo de horários por política de backup |10 | |
| Número máximo de instantâneos de qualquer tipo que possa ser retido por volume |256 |Este número inclui instantâneos locais e imagens em nuvem. |
| Número máximo de instantâneos que podem estar presentes em qualquer dispositivo |10,000 | |
| Número máximo de volumes que podem ser processados paralelamente para cópia de segurança, restauro ou clone |16 |<ul><li>Se houver mais de 16 volumes, são processados sequencialmente à medida que as faixas horárias de processamento ficam disponíveis.</li><li>Novas cópias de segurança de um volume clonado ou de um volume de camadas restaurado não podem ocorrer até que a operação esteja terminada. No entanto, para um volume local, as cópias de segurança são permitidas após o volume estar online.</li></ul> |
| Restaurar e clonar tempo de recuperação para volumes hierárquicos |< 2 minutos |<ul><li>O volume é disponibilizado no prazo de 2 minutos após a operação de restauro ou clone, independentemente do tamanho do volume.</li><li>O desempenho do volume pode inicialmente ser mais lento do que o normal, uma vez que a maioria dos dados e metadados ainda residem na nuvem. O desempenho pode aumentar à medida que os dados fluem da nuvem para o dispositivo StorSimple.</li><li>O tempo total para descarregar metadados depende do tamanho do volume atribuído. Os metadados são automaticamente introduzidos no dispositivo em segundo plano à taxa de 5 minutos por Tuberculose dos dados de volume atribuídos. Esta taxa pode ser afetada pela largura de banda da Internet para a nuvem.</li><li>A operação de restauro ou clone fica completa quando todos os metadados estão no dispositivo.</li><li>As operações de backup não podem ser efetuadas até que a operação de restauro ou clone esteja completa. |
| Restaurar o tempo de recuperação para volumes fixados localmente |< 2 minutos |<ul><li>O volume é disponibilizado no prazo de 2 minutos após a operação de restauro, independentemente do tamanho do volume.</li><li>O desempenho do volume pode inicialmente ser mais lento do que o normal, uma vez que a maioria dos dados e metadados ainda residem na nuvem. O desempenho pode aumentar à medida que os dados fluem da nuvem para o dispositivo StorSimple.</li><li>O tempo total para descarregar metadados depende do tamanho do volume atribuído. Os metadados são automaticamente introduzidos no dispositivo em segundo plano à taxa de 5 minutos por Tuberculose dos dados de volume atribuídos. Esta taxa pode ser afetada pela largura de banda da Internet para a nuvem.</li><li>Ao contrário dos volumes hierárquicos, para volumes fixados localmente, os dados de volume também são descarregados localmente no dispositivo. A operação de restauro fica completa quando todos os dados de volume foram trazidos para o dispositivo.</li><li>As operações de restauro podem ser longas. O tempo total para completar a restauração depende do tamanho do volume local previsto, da largura de banda da Internet e dos dados existentes no dispositivo. As operações de backup no volume fixado localmente são permitidas enquanto a operação de restauro está em curso. |
| Taxa de processamento para instantâneos em nuvem |15 minutos/TB |<ul><li>Tempo mínimo para tornar o instantâneo em nuvem pronto para upload, por TB de dados de volume atribuídos em backup. </li><li> O tempo total de instantâneo em nuvem é calculado adicionando desta vez o tempo de upload instantâneo, que é afetado pela largura de banda da Internet à nuvem. |
| A maior leitura/escrita do cliente (quando servida a partir do nível SSD)* |920/720 MB/s com uma única interface de rede de 10 GbE |Até 2x com MPIO e duas interfaces de rede. |
| A maior leitura/escrita do cliente (quando servida a partir do nível HDD)* |120/250 MB/s | |
| A entrada máxima de leitura/escrita do cliente (quando servida a partir do nível de nuvem)* para Atualização 3 e posterior** |40/60 MB/s para volumes hierárquicos<br><br>60/80 MB/s para volumes hierárquicos com opção de arquivo selecionada durante a criação de volume |A leitura da produção depende da geração e manutenção da profundidade suficiente da fila de I/S. <br><br>A velocidade alcançada depende da velocidade da conta de armazenamento subjacente utilizada. |

&#42; a potência máxima por tipo De/S foi medida com 100% de leitura e 100% de cenários de escrita. A produção real pode ser mais baixa e depende da mistura de I/S e das condições de rede.

&#42;&#42; os números de desempenho antes da Atualização 3 podem ser inferiores.

## <a name="next-steps"></a>Passos seguintes
Reveja os requisitos do [sistema StorSimple](storsimple-8000-system-requirements.md).

